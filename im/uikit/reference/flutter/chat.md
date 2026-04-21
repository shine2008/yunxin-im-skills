# IM UIKit Flutter 会话消息界面集成指南

本文档详细介绍如何在Flutter应用中集成IM UIKit的会话消息界面。

::: note note
会话消息界面 UI 个性化定制相关说明，请参见 自定义会话消息 UI。
:::

## 1. 集成效果

会话消息界面效果图片

## 2. 初始化 nim_chatkit_ui

使用 `nim_chatkit_ui` 之前，需调用 `ChatKitClient.init` 方法对该模块进行初始化，以保证该模块功能的正常使用。初始化方法全局只需要调用一次，建议您在 main.dart 文件中调用。

::: note notice 
初始化需要在 IM UIKit 界面加载之前进行。
:::

示例代码如下：

```dart
// 跳转到单聊界面示例
// 初始化 nim_chatkit_ui 模块
void main() {

  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);


  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    ChatKitClient.init();
    // ...
  }
}
```

## 3. 集成会话消息界面

会话页面在 `ChatPage` 类中实现,接入之前请确保已经引入`nim_chatkit_ui`依赖。

```dart
dependencies:
  nim_chatkit_ui: ^10.0.0
```

### 3.1 命名路由直接跳转

您可以使用命名路由直接跳转到 `ChatPage`，示例代码如下：

```dart
///单聊
Navigator.pushNamed(context, RouterConstants.PATH_CHAT_PAGE,
    arguments: {'conversationId': conversationId,
      'conversationType': NIMConversationType.p2p});
///群聊
Navigator.pushNamed(context, RouterConstants.PATH_CHAT_PAGE,
    arguments: {'conversationId': conversationId,
      'conversationType': NIMConversationType.team});
```

参数说明：

参数 | 类型 | 是否必填 |  说明
---- | ---- | ---- | ---
`context` | BuildContext | 是 | 上下文。
`conversationId` | String | 是 | 会话 ID。
`conversationType` | NIMConversationType | 是 | 会话类型。
`anchor` | NIMMessage | 否 | 定位消息。

也可使用云信已封装的路由方法跳转，示例代码如下：

```dart
import 'package:im_common_ui/router/imkit_router_factory.dart';
///单聊
goToP2pChat(context, userId);
///群聊
goToTeamChat(context, teamId);
```

### 3.2 实例化 ChatPage 跳转

```dart
//跳转到单聊界面
Navigator.push(context, MaterialPageRoute(builder: (context){
      return ChatPage(conversationId: sessionId, conversationType: sessionType)
    }));
```

- **ChatPage 实例化参数**

    ```dart
    ChatPage(
          {Key? key,
          required this.conversationId,
          required this.conversationType,
          this.anchor,
          this.customPopActions,
          this.onTapAvatar,
          this.chatUIConfig,
          this.messageBuilder,
          this.onMessageItemClick,
          this.onMessageItemLongClick})
    ```

- **ChatPage 参数说明**  

参数 | 类型 | 是否必填 |  说明
---- | ---- | ---- | ---
`sessionId` | String  | 是 | 会话 ID。
  `conversationType` | NIMConversationType | 是 | 会话类型。
 `anchor` | NIMMessage | 否 | 定位消息。
  `customPopActions` | PopMenuAction | 否 | 长按弹框操作函数。
  `onTapAvatar` | Function(String? userID, {bool isSelf})  | 否 | 头像点击方法。
  `chatUIConfig` | ChatUIConfig | 否 | 页面配置。
  `messageBuilder` | ChatKitMessageBuilder  | 否 | 自定义消息builder方法。
  `onMessageItemClick` | Function  | 否 | 消息单击回调。
  `onMessageItemLongClick` | Function | 否 | 消息长按回调。

### 3.3 创建自己的 ChatPage

您也可通过如下两个步骤创建自己的 `ChatPage`。

1. 创建自己的 `ChatPage`，并集成我们提供的消息列表（`ChatKitMessageList`）和输入模块（`BottomInputField`）。

    **ChatKitMessageList 参数**

    参数 | 类型 | 是否必填 |  说明
    ---- | ---- | ---- | ---
    `scrollController` | AutoScrollController  | 是 | 滑动控制器。
    `anchor` | NIMMessage | 否 | 定位消息。
    `customPopActions` | PopMenuAction | 否 | 长按弹框操作函数。
    `onTapAvatar` | Function(String? userID, {bool isSelf}) | 否 | 头像点击方法。
    `chatUIConfig` | ChatUIConfig | 否 | 页面配置。
    `messageBuilder` | ChatKitMessageBuilder | 否 | 自定义消息builder方法。
    `teamInfo` | NIMTeam | 群聊必填 | 群组信息。

    **BottomInputField 参数**

    参数 | 类型 | 是否必填 |  说明
    ---- | ---- | ---- | ---
    `scrollController` | AutoScrollController | 是 | 滑动控制器。
    `sessionType` | NIMSessionType | 是| 会话类型。
    `hint` | String | 否 | 默认提示。
    `chatUIConfig` | ChatUIConfig | 否 | 页面配置。

    **示例代码**

    ```dart
      Scaffold(
                  backgroundColor: Colors.white,
                  //your appbar
                  appBar: AppBar(),
                  body: Stack(
                    alignment: Alignment.topCenter,
                    children: [
                      Column(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                            ///消息列表
                          Expanded(
                            child: Listener(
                              onPointerMove: (event) {
                                _inputField.currentState.hideAllPanel();
                              },
                              child: ChatKitMessageList(
                                scrollController: autoController,
                                popMenuAction: widget.customPopActions,
                                anchor: widget.anchor,
                                messageBuilder: widget.messageBuilder ??
                                    widget.chatUIConfig?.messageBuilder ??
                                    ChatKitClient
                                        .instance.chatUIConfig.messageBuilder,
                                onTapAvatar: widget.onTapAvatar ?? defaultAvatarTap,
                                chatUIConfig: widget.chatUIConfig ??
                                    ChatKitClient.instance.chatUIConfig,
                                teamInfo: context.watch<ChatViewModel>().teamInfo,
                              ),
                            ),
                          ),
                          ///输入模块
                          BottomInputField(
                            scrollController: autoController,
                            sessionType: widget.sessionType,
                            hint: S.of(context).chat_message_send_hint(title),
                            chatUIConfig: widget.chatUIConfig ??
                                ChatKitClient.instance.chatUIConfig,
                            key: _inputField,
                          )
                        ],
                      ),
                    ],
                  ));
    ```

2. 将自己实现的 `ChatPage` 注册到路由管理器，方便其他模块跳转。 

    示例代码如下：

    ```dart
    IMKitRouter.instance.registerRouter(
        RouterConstants.PATH_CHAT_PAGE,
        (context) => ChatPage(
              sessionId:
                  IMKitRouter.getArgumentFormMap<String>(context, 'sessionId')!,
              sessionType: IMKitRouter.getArgumentFormMap<NIMSessionType>(
                  context, 'sessionType')!,
              anchor:
                  IMKitRouter.getArgumentFormMap<NIMMessage>(context, 'anchor'),
            ));
    ```