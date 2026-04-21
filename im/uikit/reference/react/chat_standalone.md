# 仅集成聊天

若您在集成 IMUIkit 时，只需要集成会话消息组件，不需要集成会话列表、通讯录、搜索等组件，可按照以下方式进行集成。

## 适用场景

仅集成聊天功能而不包括完整社交功能的场景通常有以下几种:

- **客户服务与在线支持**：在这类场景中，聊天功能作为客户与企业沟通的桥梁，例如在线客服、智能客服数字人、售后支持。
- **专业服务咨询**：垂直领域的辅助沟通工具，例如医疗咨询、教育平台、金融顾问、法律咨询。
- **电商与交易平台**：买卖双方沟通，例如订单状态通知、砍价/议价功能。
- **专业协作工具**：项目管理软件，例如设计协作平台、代码协作工具。
- **特定社交场景**：约会应用的初期互动，例如陌生人社交、兴趣小组。
- **嵌入式通讯功能**：游戏内聊天，例如直播平台、共享经济应用。

## 效果预览

单独集成会话消息组件常用在临时会话页面，例如技术支持或者售后服务，这种场景下，用户可能需要与客服代表进行即时沟通以解决具体问题。在这种情况下，单独会话消息允许用户和客服之间进行实时的文本、图片或文件交换，而无需跳转到其他页面或应用。这样的集成不仅提高了用户体验，还增强了服务的响应速度和效率。

![image-netease](https://yx-web-nosdn.netease.im/common/9949c5baff35cd31a334d2338f3fcfc3/%E8%81%8A%E5%A4%A91.gif){: style="width:80%;border: 1px solid #BFBFBF;"}

## 前提条件

根据本文操作前，请确保您已经完成了以下设置：

- 导入组件
- 初始化组件

## 集成步骤

1. 集成会话消息组件。

    ```JSX
    import React, { useMemo } from 'react'
    import { Provider, ChatContainer } from '@xkit-yx/im-kit-ui'
    import '@xkit-yx/im-kit-ui/es/style'
    import './index.less'

    const App = () => {
      const initOptions = useMemo(() => {
        return {
          appkey: '',
          account: '',
          token: '',
          debugLevel: 'debug',
          // ……
        }
      }, [])

      return (
        <Provider initOptions={initOptions} sdkVersion={1}>
          <ChatContainer />
        </Provider>
      )
    }
    ```

2. 在需要展示聊天页面时，动态展示 `ChatContainer` 组件，调用 `store` 中的 `selectConversation` 或 `insertConversationActive`，以及插入会话，传入对应参数。

    ::: note note
    如果你使用的是 **本地会话**，请将示例中的 `store.conversationStore` 替换为 `store.localConversationStore`。
    :::

    ```JSX
    import { Provider, ChatContainer, useStateContext } from '@xkit-yx/im-kit-ui'
    const { store } = useStateContext()
    // ...
    const gotoChat1 = () => {
      const store = this.$uikit.getStateContext()?.store
      const myAccount = store.userStore.myUserInfo.accountId
      const conversationType = 1
      // 用户 ID
      const receiverId = 'xxxx'
      const conversationId = `${myAccount}|${conversationType}|${receiverId}`
      if (store.conversationStore.conversations.get(conversationId)) {
        store.uiStore.selectConversation(conversationId)
      } else {
        const isSelected = true
        store.conversationStore.insertConversationActive(
          conversationType,
          receiverId,
          isSelected,
        )
      }
    }
    const gotoChat2 = () => {
      const store = this.$uikit.getStateContext()?.store
      const myAccount = store.userStore.myUserInfo.accountId
      const conversationType = 2
      // 群 ID
      const receiverId = 'xxxxx'
      const conversationId = `${myAccount}|${conversationType}|${receiverId}`
      if (store.conversationStore.conversations.get(conversationId)) {
        store.uiStore.selectConversation(conversationId)
      } else {
        const isSelected = true
        store.conversationStore.insertConversationActive(
          conversationType,
          receiverId,
          isSelected,
        )
      }
    }
    return (
      <Provider initOptions={initOptions}>
        {<button onClick={gotoChat1}>单聊</button>}
        {<button onClick={gotoChat2}>群聊</button>}
        <ChatContainer />
      </Provider>
    )
    ```
