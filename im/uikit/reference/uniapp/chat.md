# 单独集成聊天页面

> 更新时间：2025/09/11 14:15:56

若您在集成 IM UIKit 时，只需要集成聊天页面（会话消息组件），不需要会话列表、通讯录、搜索等其他页面，可以参考本文实现。

---

## 前提条件

已完成 IM 初始化以及 UIKit 相关配置，具体请参考**快速开始**。

---

## 操作步骤

### 第一步：配置聊天页面路由

参考组件导入和初始化文档，在 UIKit 组件导入并初始化后，在 `pages.json` 中完成聊天（会话消息）页面的路由配置：

```json
{
  "pages": [
    {
      "path": "pages/NEUIKit/pages/Chat/index",
      "style": {
        "navigationBarBackgroundColor": "#F6F8FA",
        "navigationBarTextStyle": "black",
        "navigationStyle": "custom",
        "enablePullDownRefresh": false,
        "app-plus": {
          "softinputNavBar": "none",
          "bounce": "none"
        }
      }
    }
  ]
}
```

### 第二步：跳转至聊天页面

在项目的其他页面跳转至聊天页面时，需要先调用 `uni.$UIKitStore.uiStore.selectConversation` 接口，并传入 `conversationId`。

完整示例代码如下：

```javascript
const navigateToChat = async (account) => {
  /**
   * from：发送方 account
   * V2NIMConversationType：会话类型，单聊为 1，群聊为 2
   * to：接收方 account，如果是群聊则为群 ID
   */
  const conversationId = `${from}|${V2NIMConversationType}|${to}`;

  await uni.$UIKitStore.uiStore.selectConversation(conversationId);

  uni.navigateTo({ url });
};
```
