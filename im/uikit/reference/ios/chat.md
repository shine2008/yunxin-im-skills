
# IM UIKit iOS 聊天界面集成指南

本文主要介绍如何构建聊天界面。IM UIKit 提供基于 UITableview 实现的聊天界面，其类名为 `ChatViewController`，并提供了两个子类：

- **`TeamChatViewController`**：群聊聊天界面
- **`P2PChatViewController`**：单聊聊天界面
::: note note
IM UIKit 将聊天界面拆分成 P2P 和 Team 两种类型，有利于解耦和添加用户自身的其他逻辑，在使用时，您需要注意区分聊天类型。
:::

## 方法原型

```swift
let p2pChatVC = P2PChatViewController(conversationId: conversationId, anchor: anchor)
let teamVC = TeamChatViewController(conversationId: conversationId, anchor: anchor)
```

## 参数说明

| 参数 | 类型 | 说明 |
| ---- | ---- | ---- |
| conversationId | String | 消息 ID。 |
| anchor | V2NIMMessage | 锚点，用于历史消息搜索。 |

## 代码示例（聊天界面集成）

```swift
    // 1、路由跳转(推荐) - 更推荐使用路由方式进行页面跳转，便于统一管理
    if V2NIMConversationIdUtil.conversationType(conversationId) == .CONVERSATION_TYPE_P2P {
        Router.shared.use(
            PushP2pChatVCRouter,
            parameters: ["nav": navigationController as Any,
                        "conversationId": conversationId as Any,
                        "anchor": anchor],
            closure: nil
        )
    } else if V2NIMConversationIdUtil.conversationType(conversationId) == .CONVERSATION_TYPE_TEAM {
        Router.shared.use(
            PushTeamChatVCRouter,
            parameters: ["nav": navigationController as Any,
                        "conversationId": conversationId as Any,
                        "anchor": anchor],
            closure: nil
        )
    }

    // 2、导航控制器跳转 - 直接创建并推入会话控制器
    if V2NIMConversationIdUtil.conversationType(conversationId) == .CONVERSATION_TYPE_P2P {
        let p2pChatVC = P2PChatViewController(conversationId: conversationId, anchor: anchor)
        navigationController?.pushViewController(p2pChatVC, animated: true)
    } else if V2NIMConversationIdUtil.conversationType(conversationId) == .CONVERSATION_TYPE_TEAM {
        let teamVC = TeamChatViewController(conversationId: conversationId, anchor: anchor)
        navigationController?.pushViewController(teamVC, animated: true)
    }
```

::: note note
使用时，根据不同的消息类型，跳转到对应的界面，开源代码中内部跳转通过 Router 路由实现。路由器的具体使用说明，请参考 界面跳转。
:::
