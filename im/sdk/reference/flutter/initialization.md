本文介绍如何初始化适配 Flutter 的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。

## 调用时机

集成 NIM Flutter SDK 后，在调用各项即时通讯功能之前需要将其初始化，创建 SDK 实例。

## 前提条件

初始化 NIM SDK 前，请确保您已经完成了以下操作：

- 在项目中 [集成 SDK](https://doc.yunxin.163.com/messaging2/guide/DIyNzI1MDA?platform=client)。
- 在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上 [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)，并获取 App Key 和 App Secret。
- [注册网易云信 IM 账号](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#%E7%AC%AC%E4%BA%8C%E6%AD%A5%E6%B3%A8%E5%86%8C-im-%E8%B4%A6%E5%8F%B7)，获取 IM 账号和 token。

## 初始化 SDK

将 NIM SDK 集成到客户端后，调用 `initialize` 方法进行初始化。

**示例代码：**

```dart
late NIMSDKOptions options;
    if (kIsWeb) {
      var base = NIMInitializeOptions(
        appkey: appKey,
        apiVersion: 'v2',
        debugLevel: 'debug',
      );
      options = NIMWebSDKOptions(
        appKey: appKey,
        initializeOptions: base,
      );
    } else if (Platform.isAndroid) {
      final directory = await getExternalStorageDirectory();
      options = NIMAndroidSDKOptions(
          appKey: appKey,
          shouldSyncStickTopSessionInfos: true,
          //若需要使用云端会话，请提前开启云端会话
          //enableV2CloudConversation: true,
          sdkRootDir:
              directory != null ? '${directory.path}/NIMFlutter' : null);
    } else if (Platform.isIOS) {
      final directory = await getApplicationDocumentsDirectory();
      options = NIMIOSSDKOptions(
        appKey: appKey,
        shouldSyncStickTopSessionInfos: true,
        //若需要使用云端会话，请提前开启云端会话
        //enableV2CloudConversation: true,
        sdkRootDir: '${directory.path}/NIMFlutter',
        apnsCername: 'your apnsCername',
        pkCername: 'your pkCername',
      );
    } else if (Platform.isMacOS || Platform.isWindows) {
      NIMBasicOption basicOption = NIMBasicOption();
      options = NIMPCSDKOptions(basicOption: basicOption, appKey: appKey);
    } 

    NimCore.instance.initialize(options).then((value) async {
      print('initialize result: $value');
    });
```

## 下一步

完成初始化后，可 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。

## 相关参考

### 海外数据中心配置

如果您的应用的服务区域为海外数据中心，为满足海外访问域名合规性，您需要自行通过客户端 SDK 配置海外专属域名，包括 link 域名和 LBS 域名。具体请参考 [海外数据中心](https://doc.yunxin.163.com/messaging2/concept/Dk0Nzk3MDE?platform=client)。

### 初始化配置参数

您需要实现 [`NIMSDKOptions`](https://pub.dev/documentation/nim_core_v2/latest/nim_core_v2/NIMSDKOptions-class.html) 的子类配置初始化参数。`NIMSDKOptions` 包含多个子结构体，用于不同平台的特有配置。

- [`NIMAndroidSDKOptions`](https://pub.dev/documentation/nim_core_v2/latest/nim_core_v2/NIMAndroidSDKOptions-class.html) 在通用参数的基础上添加了 Android 平台独有的参数。
- [`NIMIOSSDKOptions`](https://pub.dev/documentation/nim_core_v2/latest/nim_core_v2/NIMIOSSDKOptions-class.html) 在通用参数的基础上添加了 iOS 平台独有的参数。
- [`NIMPCSDKOptions`](https://pub.dev/documentation/nim_core_v2/latest/nim_core_v2/NIMPCSDKOptions-class.html) 适用于 Windows 和 MacOS 平台，通用参数除 AppKey 之外皆无效。
- [`NIMWebSDKOptions`](https://pub.dev/documentation/nim_core_v2/latest/nim_core_v2/NIMWebSDKOptions-class.html) 适用于 Web 平台，通用参数除 AppKey 之外皆无效。

<!--
#### 初始化通用参数

`NIMSDKOptions` 的参数说明如下：

|                  参数                   | 类型 | 必填 | 说明          |
| :-------------------------------------| :-------|:-------|:--------------------------------- |
|                 `appKey`               |String  | 是| 设置SDK 的 `appKey`                                                                                |
|               `sdkRootDir`      |    String      | 否| 外置存储根目录，用于存放多媒体消息和日志等文件        |
|     `enablePreloadMessageAttachment` |  bool    | 否| 是否需要 SDK 自动预加载多媒体消息的附件             |
|          `shouldSyncUnreadCount`     |bool |否     | 是否开启会话已读多端同步             |
| `shouldTeamNotificationMessageMarkUnread` |bool |否 | 群通知消息是否计入未读数，默认不计入未读         |
|      `enableAnimatedImageThumbnail` |   bool   | 否| 开启动图缩略图，默认为 false，如开启则截取动图的第一帧作为缩略图                 |
|      `enableTeamMessageReadReceipt`   |bool |否    | 是否启用群消息已读功能，默认关闭                                                                      |
| `shouldConsiderRevokedMessageUnreadCount` |bool |否 | 撤回消息时未读数是否减一        |
|             `nosSceneConfig`              |  Map<NIMNosScene, int>| 否|  设置网易对象存储服务（Netease Object Storage，NOS）的存储场景，从而指定 IM 相关资源（如多媒体消息的附件）在 NOS 上的过期时间    |
|     `shouldSyncStickTopSessionInfos`    |bool |否    | 置顶会话是否同步                                                                                      |
|            `cdnTrackInterval`   |int | 否          | CDN 信息上报的回调间隔                                                                                |
|          `enableDatabaseBackup`   | bool | 否       | 是否开启数据库备份功能                                                                                |
|      `enableReportLogAutomatically`   |bool    | 否| 是否开启 IM 日志自动上报，默认关闭                                                                    |



#### Android 平台独有参数

`NIMAndroidSDKOptions` 的参数说明如下：

|   参数            | 类型 | 必填 | 说明                                    |
| :----------------------- | :----|:-----|:----------------------------- |
|    `notificationConfig`     |`NIMStatusBarNotificationConfig` | 否| 消息通知栏配置                 |
| `improveSDKProcessPriority` | bool | 是|  是否提高 SDK 进程优先级（默认提高，可降低 SDK 核心进程被系统回收的概率）            |
|      `preLoadServers`       | bool | 是| 预加载服务，默认 true，不建议设置为 false，预加载连接可以优化登陆流程       |
|         `reducedIM`    |bool | 是    | 是否是弱 IM 场景，默认为 false。如果您的应用仅在部分场景按需使用 IM 能力(不需要在应用启动时就做自动登录)，并不需要保证消息通知、数据的实时性，那么这里可以填 true。弱 IM 场景下，push 进程采用懒启动策略(延迟到用户登录阶段)，启动后其生命周期将跟随 UI 进程，降低弱 IM 场景的应用后台功耗开销 |
|   `checkManifestConfig`   |bool |是 | 是否在 SDK 初始化时检查清单文件配置是否完全，默认为 false，建议在调试阶段打开，上线时关掉      |
|       `mixPushConfig`   | `NIMMixPushConfig`| 否    | 配置第三方推送的`appid`、`appkey`和证书                |
|       `disableAwake`   | bool |是     | 是否禁止后台进程唤醒 UI 进程         |
|  `fetchServerTimeInterval` | int | 是 | 获取服务器时间的连续请求的间隔时间, 最小 1000ms， 默认 2000ms        |
| `customPushContentType`| String | 否 | 离线推送不显示详情时，要显示的文案对应的自定义类型名称 |
|     `databaseEncryptKey `                    |  String  |   否  |   数据库加密秘钥，用于消息数据库加密。<br/> 如果不设置，数据库处于明文状态； 设置后，数据库会加密保存数据，之前明文保存的历史数据会被转为加密保存； 一旦开启过加密功能后，不支持退回明文保存状态 |
| `thumbnailSize`|   int  | 是  |  消息缩略图的尺寸。该值为最长边的大小，下载的缩略图最长边不会超过该值。默认值 350 px |
| `enableFcs` |   bool   |     是   |   是否开启融合存储。融合存储为海外用户提供了新的存储方式。开启融合存储后，SDK 在应用收到文件消息会自动启用 CDN 加速选择最快的节点进行存储，将文件消息存储到 AWS 服务器或网易对象存储（NetEase Object Storage, NOS）服务器上。默认 true  <br/> <font color=red>注意：</font>虽然融合存储默认开启，但需要开通该功能后才能生效。如有需要，请通过云信官网首页提供的微信等联系方式咨询商务经理开通该功能 |
| `enabledQChatMessageCache`| bool  | 否  | 开启圈组的消息缓存，默认不开启 |

#### iOS 平台独有参数

`NIMIOSSDKOptions` 的参数说明如下：

|    参数        | 类型| 必填 | 说明          |
| :---------|:--------------| :------|:---------------------------- |
|    `apnsCername` | String | 否      | 在云信控制台配置的 APNs 推送证书名             |
|     `pkCername`   | String | 否  | 在云信控制台配置的 PushKit 推送证书名             |
|    `disableTraceroute`     | bool | 否                  | 设置禁用 NIM Flutter SDK 的 traceroute 能力，默认为 NO。SDK 会在请求失败时，进行 traceroute ，探测网路中各节点，以判断在哪个节点失去连接                   |
|      `maxUploadLogSize` |int | 否|  日志上传大小上限，默认 0，不限制，单位(byte)           |
| `enableFetchAttachmentAutomaticallyAfterReceivingInChatroom` | bool | 否 | 是否在收到聊天室消息后自动下载附件，默认为 NO           |
|     `enableFileProtectionNone`   |bool | 否               | 是否使用 NSFileProtectionNone 作为云信文件的 `NSProtectionKey`，默认为 NO。只有在应用上层开启了 Data Protection 时才生效       |
|    `enabledHttpsForInfo`     |bool | 否                   | 针对用户信息开启 HTTPS 协议支持，默认为 YES。在默认情况下，用户头像、群头像和聊天室用户头像等信息都默认托管在云信服务端上，所以 SDK 会针对它们自动开启 HTTPS 支持。如果您需要将这些信息都托管在自己的服务器上，需要将该参数设置为 NO，避免 SDK 自动将您的 HTTP URL 自动转换为 HTTPS URL     |
|   `enabledHttpsForMessage`   | bool | 否  | 针对消息内容开启 HTTPS 协议支持，默认为 YES。在默认情况下，消息（包括附带的图片、视频和音频数据）都默认托管在云信服务端，所以 SDK 会针对它们自动开启 HTTPS 支持。如果您需要将这些数据都托管在自己的服务器上，需要将该参数为 NO (强烈不建议)，避免 SDK 自动将您的 HTTP URL 自动转换为 HTTPS URL。 需要注意的是，即使设置了这个参数，通过 SDK 发出去的消息 URL 仍是 HTTPS 的，设置这个参数只影响接收到的消息的 URL 格式转换 |
|    `maxAutoLoginRetryTimes`  | int | 否    | 自动登录重试次数，默认为 0。即默认情况下，自动登录将无限重试。设置成大于 0 的值后，在没有登录成功前，自动登录将重试最多 `maxAutoLoginRetryTimes` 次   |
|   `maximumLogDays` | int | 否   | 本地日志的有效期，默认为 7 天。即超过 7 天的本地日志将被清除。只能设置大于等于 2 的值    |
|   `disableReconnectInBackgroundState`  | bool | 否   | 是否禁止后台重连，默认为 NO。即默认情况下，当程序退到后台断开连接后，如果应用仍能运行，SDK 将继续执行自动重连机制。设置为 YES 后在后台将不自动重连，重连将被推迟到前台进行。只有特殊用户场景才需要此设置，无明确原因请勿设置   |
|   `enableTeamReceipt` | bool | 否    | 是否开启群消息已读回执功能，默认为 NO     |
|     `enableFileQuickTransfer`   | bool | 否                | 是否开启文件快传本地开关，默认 YES      |
|    `enableAsyncLoadRecentSession`    | bool | 否            | 是否开启异步读取最近会话，默认 NO。对于最近会话比较多的用户，初始读取数据库时，可能影响到启动速度，这些用户可以选择开启该选项，querySessionList 会优先返回一部分最近会话，等到全部读取完成时，通过回调通知用户刷新 UI    |
| `enabledQChatMessageCache`| bool  | 否  | 开启圈组的消息缓存，默认不开启 |


#### Windows&MacOS 平台参数

PC 端参数分为以下几个模块:

- `NIMBasicOption` 基础配置。
- `NIMLinkOption` 连接相关配置。
- `NIMDatabaseOption` 数据库配置。
- `NIMFCSOption` 融合存储配置。
- `NIMPrivateServerOption` 私有化配置。

#### Web 平台参数

Web 端参数分为以下两个模块：

- `NIMInitializeOptions` 基础初始化参数。
- `NIMOtherOptions` 其他初始化参数。

`NIMInitializeOptions` 参数如下：

| 名称 | 类型 | 是否必填 | 默认值 | 说明 |
| ---- | ---- | ---- | ---- | ---- |
| `appkey` | string | 是 | null | 云信appkey |
| `apiVersion` | string | 否 | null | api 版本，请传入 'v2' |
| `debugLevel` | string | 否 | off | 日志级别，默认为 off，即不输出任何日志|
| `binaryWebsocket` | boolean | true | | 是否采用二进制的形式传输数据 |
| `loginSDKTypeParamCompat` | boolean | 否 | | loginSDKTypeParamCompat |

-->