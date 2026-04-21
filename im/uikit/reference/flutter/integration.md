# IM UIKit Flutter 快速集成指南


网易云信 IM UIKit 是基于 网易云信 IM SDK（NIM SDK）开发的一款 即时通讯 UI 组件库，功能涵盖了聊天、会话、群管理。本文档详细介绍在 Flutter 项目中集成 10.x.x 版本的 IM UIKit 的完整流程，包括准备工作、组件导入、权限配置、初始化和界面搭建等步骤。

```mermaid
graph LR
classDef default fill:#337EFF,stroke:#337EFF,stroke-width:0px,color:#FFFFFF;
按需导入组件 --> 添加权限 --> 配置会话模式 --> 初始化组件 --> 登录 --> 搭建界面
```

## 1. 准备工作

在开始集成 IM UIKit 前，请确保您已完成以下操作：

1. 在 网易云信控制台上，创建应用，开通即时通讯 IM 服务，获取 App Key。
2. 已 注册 IM 账号，获取账号 ID（`account_id`）和凭证（`token`）。
3. 准备如下开发环境：
    - Dart SDK 要求 3.5 及以上版本。如果需要兼容较低版本，请参考下文 [兼容低版本 Dart SDK](#compatibility)。
    - 支持的 CPU 架构：arm64-v8a、armeabi-v7a。（不支持 x86 模拟器。）
    - 项目开发环境：

        Android | iOS |
        ---- | ---- |
        <ul><li>Android Studio Bumblebee </li><li>Android API 24 及以上版本设备 </li><li>1.6.10 以上版本的 <code>kotlin-gradle-plugin</code></li></ul> | <ul><li>Xcode 12.0 及以上版本</li><li>App 要求 iOS 12.0 以上版本设备</li><li>项目已设置有效的开发者签名</li></ul> |

## 2. 按需导入组件

您可以根据业务需要，选择所需的 IM UIKit 组件。各组件相互独立，添加或删除均不影响项目编译。

- **方式一**：如需添加远程依赖，在您的项目的 `pubspec.yaml` 中，添加相应组件。示例中的 `{LATEST_VERSION}`，建议使用最新版本，请参考 更新日志。

    :::::: div linked-codes
    ::: code 完整功能引入示例
    ```yaml
# pubspec.yaml 完整功能引入示例
dependencies:
    # 通讯录功能组件
    nim_contactkit_ui: ^{LATEST_VERSION}
    # 会话列表功能组件
    nim_conversationkit_ui: ^{LATEST_VERSION}
    # 群组功能组件
    nim_teamkit_ui: ^{LATEST_VERSION}
    # 聊天功能组件
    nim_chatkit_ui: ^{LATEST_VERSION}
    # 搜索功能组件
    nim_searchkit_ui: ^{LATEST_VERSION}
```
    :::
    ::: code 仅引入聊天示例
    ```yaml
# pubspec.yaml 仅引入聊天示例
dependencies:
    # 会话列表功能组件
    nim_conversationkit_ui: ^{LATEST_VERSION}
    # 群组功能组件
    nim_teamkit_ui: ^{LATEST_VERSION}
    # 聊天功能组件
    nim_chatkit_ui: ^{LATEST_VERSION}
```
    :::
    ::::::

- **方式二**：如需添加本地依赖，请在获取对应分支的 nim-uikit-flutter.git 源码后，通过指定路径的形式实现。

    :::::: div linked-codes
    ::: code 完整功能引入示例
    ```yaml
# pubspec.yaml 本地依赖完整功能引入示例
dependencies:
        # 通讯录功能组件
        nim_contactkit_ui:
            path: ../nim_contactkit_ui
        # 会话列表功能组件
        nim_conversationkit_ui:
            path: ../nim_conversationkit_ui
        # 群组功能组件
        nim_teamkit_ui:
            path: ../nim_teamkit_ui
        # 聊天功能组件
        nim_chatkit_ui:
            path: ../nim_chatkit_ui
        # 搜索功能组件
        nim_searchkit_ui:
            path: ../nim_searchkit_ui
```
    :::
    ::: code 仅引入聊天示例
    ```yaml
# pubspec.yaml 本地依赖仅引入聊天示例
dependencies:
        # 会话列表功能组件
        nim_conversationkit_ui:
            path: ../nim_conversationkit_ui
        # 群组功能组件
        nim_teamkit_ui:
            path: ../nim_teamkit_ui
        # 聊天功能组件
        nim_chatkit_ui:
            path: ../nim_chatkit_ui
```
    :::
    ::::::

## 3. 添加权限

由于 IM UIKit 运行，需要拍摄/相册/录音/网络等权限，需要您在 Native 的文件中手动声明，才可正常使用相关能力。

- Android：android/app/src/main/AndroidManifest.xml ，在 `<manifest></manifest>`中，添加如下权限。

    ```
    <uses-permission
        android:name="android.permission.INTERNET"/>
    <uses-permission
        android:name="android.permission.RECORD_AUDIO"/>
    <uses-permission
        android:name="android.permission.FOREGROUND_SERVICE"/>
    <uses-permission
        android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission
        android:name="android.permission.VIBRATE"/>
    <uses-permission
        android:name="android.permission.ACCESS_BACKGROUND_LOCATION"/>
    <uses-permission
        android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission
        android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission
        android:name="android.permission.CAMERA"/>
    <uses-permission 
        android:name="android.permission.READ_MEDIA_IMAGES"/>
    <uses-permission 
        android:name="android.permission.READ_MEDIA_VIDEO"/>
    ```

- iOS：打开 ios/Podfile ，在文件末尾新增如下权限代码。

    ```
    post_install do |installer|
      installer.pods_project.targets.each do |target|
        flutter_additional_ios_build_settings(target)
        target.build_configurations.each do |config|        
              config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'arm64'               
              config.build_settings['ENABLE_BITCODE'] = 'NO'        
              config.build_settings["ONLY_ACTIVE_ARCH"] = "NO"    
            end
        target.build_configurations.each do |config|
              config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
                '$(inherited)',
                'PERMISSION_MICROPHONE=1',
                'PERMISSION_CAMERA=1',
                'PERMISSION_PHOTOS=1',
                'PERMISSION_MEDIA_LIBRARY=1',
              ]
            end
      end
    end
    ```

## 4. 配置会话存储模式

如果您的应用开通了 **高级版** 套餐，在初始化 SDK 之前，您可以选择会话是否存储在云端。入口为 IM UIKit 的核心类 `IMKitClient`，该类提供初始化、登录、获取当前账号信息等能力。

此步骤为可选，默认会话存储在本地。

```Dart
// 如果需要将会话存储在云端，设置为 true。默认为 false（存储在本地）
IMKitClient.setEnableCloudConversation(true);
```

关于云端会话功能的说明：

- **设置方法**：`IMKitClient.setEnableCloudConversation(true/false)`
- **获取当前设置**：`IMKitClient.enableCloudConversation`
- **默认值**：`false`（使用本地存储）

## 5. 初始化 UIKit 和组件

在使用 IM UIKit 功能前，必须先对其进行初始化，但不需要对 IM UIKit 依赖的底层 [NIM SDK]单独初始化。初始化需要完成两部分：IM UIKit 的初始化和各功能组件的初始化。相关配置通过 NIMSDKOptions 添加。

1. 在应用的 `initState` 中初始化。

    ```dart
// 在应用的 initState 中初始化 IM UIKit
@override
void initState() {
  super.initState();

  // 获取之前配置的会话存储模式
  final enableCloudConversation = await IMKitClient.enableCloudConversation;

  // 根据平台创建对应的 SDK 配置
  if (Platform.isAndroid) {
    options = NIMAndroidSDKOptions(
      appKey: "您的 App Key",
      enableV2CloudConversation: enableCloudConversation,
      // 其他配置...
    );
  } else if (Platform.isIOS) {
    options = NIMIOSSDKOptions(
      appKey: "您的 App Key",
      enableV2CloudConversation: enableCloudConversation,
      // 其他配置...
    );
  }

  // 初始化 IM UIKit
  IMKitClient.init(appKey, options).then((success) {
    if (success) {
      // 初始化成功
      print("IM UIKit 初始化成功");
    } else {
      // 初始化失败
      print("IM UIKit 初始化失败");
    }
  });
}
    ```

2. 在应用的根 Widget 中初始化各功能组件。

    ```dart
// 应用根组件中初始化各功能组件示例
class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  // 初始化您需要的功能组件
  void initPlugins() {
    ChatKitClient.init();
    TeamKitClient.init();
    ConversationKitClient.init();
    ContactKitClient.init();
    SearchKitClient.init();
  }

  @override
  Widget build(BuildContext context) {
    // 初始化各功能组件
    initPlugins();

    return MaterialApp(
      title: 'IM UIKit',
      // 配置路由表
      routes: IMKitRouter.instance.routes,
      // 配置支持的语言
      supportedLocales: IMKitClient.supportedLocales,
      // 配置多语言委托
      localizationsDelegates: [
        CommonUILocalizations.delegate,
        ConversationKitClient.delegate,
        ChatKitClient.delegate,
        ContactKitClient.delegate,
        TeamKitClient.delegate,
        SearchKitClient.delegate,
        ...GlobalMaterialLocalizations.delegates,
      ],
      home: const MyHomePage(title: 'IM UIKit'),
    );
  }
}
    ```

    如需了解更多配置方法，请参考 nim-uikit-flutter.git 项目中的 config.dart 文件。

## 6. 登录 IM 服务

在 UIKit 初始化成功后，调用 `IMKitClient.loginIM` 方法进行登录：

```dart
// 登录 IM 服务示例代码
// 将 account_id 和 token 替换为您的 IM 账号和凭证
IMKitClient.loginIM(NIMLoginInfo("account_id", "token")).then((success) {
    if (success) {
        // 登录成功
        print("IM 登录成功");
    } else {
        // 登录失败
        print("IM 登录失败");
    }
});
```

:::note note
如果在初始化时已设置用户信息，初始化成功后还需调用 `getIt<IMLoginService>().syncUserInfo(account)` 方法同步用户信息。
:::

## 7. 搭建界面

以搭建会话列表界面为例，您可以直接创建 `ConversationPage` 组件到您的应用中，还可以选择传入 `onUnreadCountChanged` 接口以获取当前未读消息数量信息。

```dart
// 会话列表界面实现示例
class _MyHomePageState extends State<MyHomePage> {
  int chatUnreadCount = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: ConversationPage(
        // 可选：监听未读消息数量变化
        onUnreadCountChanged: (unreadCount) {
          setState(() {
            chatUnreadCount = unreadCount;
          });
        },
      ),
    );
  }
}
```

会话列表界面最终搭建效果参考图如下：

FlutterUIKit会话列表效果图片

## 8. 下一步

IM UIKit 中提供的常用业务场景界面，创建或者跳转到界面需要传入具体参数，详情请参考 界面跳转，相关详细集成说明如下：

界面类 | 所属组件 | 界面介绍 | 参考文档
---- | ---- | ---- | ----
[`ConversationPage`]| nim_conversationkit_ui | 会话列表界面 | [集成会话列表界面]
[`ContactPage`] | nim_contactkit_ui | 通讯录界面 | [集成通讯录界面]
[`ChatPage`] | nim_chatkit_ui | 单聊/群聊会话界面 | [集成会话消息界面]
