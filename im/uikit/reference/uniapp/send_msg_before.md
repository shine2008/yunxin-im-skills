# 发送消息前添加配置参数

> 更新时间：2025/09/11 14:15:56

本文介绍使用 IM UIKit uni-app 版如何在发送消息前配置参数。

---

## 前提条件

根据本文操作前，请确保您已完成 IM UIKit 初始化。

---

## 配置参数

在初始化 `store` 时，您可在 `sendMsgBefore` 回调中进行配置：

- 通过 `options.msg` 可以获取发送前的消息体，在该消息体上挂载其他字段。
- 通过 `options.conversationId` 可以获取当前会话的会话 ID。
- 通过 `sendMsgBefore` 中 `return` 最终挂载字段后的消息体即可完成配置。

```javascript
const store = (uni.$UIKitStore = new RootStore(
  // @ts-ignore
  nim,
  {
    // ...
    // 发送消息前回调，可对消息体进行修改，添加自定义参数
    sendMsgBefore: async (options: {
      msg: V2NIMMessage
      conversationId: string
      serverExtension?: Record<string, unknown>
    }) => {
      const pushConfig = {
        // ...
      }
      return { ...options, pushConfig }
    },
  },
  'UniApp'
))
```

---

## 配置示例

以下示例展示如何在发送消息前，对消息添加推送相关参数：

```javascript
const store = (uni.$UIKitStore = new RootStore(
  // @ts-ignore
  nim,
  {
    // ...
    // 发送消息前回调，可对消息体进行修改，添加自定义参数
    sendMsgBefore: async (options: {
      msg: V2NIMMessage
      conversationId: string
      serverExtension?: Record<string, unknown>
    }) => {
      const pushContent = getMsgContentTipByType({
        text: options.msg.text,
        messageType: options.msg.messageType,
      })

      const yxAitMsg = options.serverExtension
        ? options.serverExtension.yxAitMsg
        : { forcePushIDsList: '[]', needForcePush: false }

      // 如果是 @ 消息，需要走离线强推
      const { forcePushIDsList, needForcePush } = yxAitMsg
        ? // @ts-ignore
          store.msgStore._formatExtAitToPushInfo(yxAitMsg, options.msg.text)
        : { forcePushIDsList: '[]', needForcePush: false }

      console.log('forcePushIDsList: ', forcePushIDsList)

      const { conversationId } = options
      const conversationType =
        nim.V2NIMConversationIdUtil.parseConversationType(conversationId)
      const targetId =
        nim.V2NIMConversationIdUtil.parseConversationTargetId(conversationId)

      const pushPayload = JSON.stringify({
        // OPPO 推送配置
        oppoField: {
          click_action_type: 4, // 参考 OPPO 官网
          click_action_activity: '', // 各端不同，请按需填写
          action_parameters: {
            sessionId: targetId,
            sessionType: conversationType,
          },
        },

        // vivo 推送配置
        vivoField: {
          pushMode: 0, // 推送模式：0 正式推送，1 测试推送，默认为 0
        },

        // 华为推送配置
        hwField: {
          click_action: {
            type: 1,
            action: '', // 各端不同，请按需填写
          },
          androidConfig: {
            category: 'IM',
            data: JSON.stringify({
              sessionId: targetId,
              sessionType: conversationType,
            }),
          },
        },

        // 通用字段
        sessionId: targetId,
        sessionType: conversationType,
      })

      const pushConfig: V2NIMMessagePushConfig = {
        pushEnabled: true,
        pushNickEnabled: true,
        forcePush: needForcePush,
        forcePushContent: pushContent,
        forcePushAccountIds: forcePushIDsList,
        pushPayload: '{}',
        pushContent,
      }

      return { ...options, pushConfig }
    },
  },
  'UniApp'
))
```
