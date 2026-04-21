# 收发自定义消息

> 更新时间：2025/12/22 16:49:47

本文介绍网易云信 IM UIKit Electron 版如何收发自定义消息。

---

## 前提条件

根据本文操作前，请确保您已完成 IM UIKit 初始化。

---

## 发送自定义消息

以**发送一条评价消息**为例，发送时可通过 `createCustomMessage` 创建一条自定义消息，调用 `sendMessage` 进行发送。

> `nim`、`store`、`scrollToBottom` 的获取方式可参考 `/NEUIKit/Chat/message/message-input.vue` 中的写法。

```javascript
import { getContextState } from "./NEUIKit/utils/init";

const { store, nim } = getContextState();

// 发送评价消息
const sendEvaluationMessage = () => {
  const message = nim.V2NIMMessageCreator.createCustomMessage(
    "this is a custom message" + Date.now(),
    // 标识为自定义消息的字段
    JSON.stringify({
      isEvaluationMessage: true,
    }),
  );

  const to = store.uiStore.selectedConversation;
  nim.V2NIMMessageService.sendMessage(message, to).then((res) => {
    // 将返回的消息体插入消息列表
    store.msgStore._handleSendMsgSuccess(res.message);
    // 让消息滚动到可视区域
    scrollToBottom();
  });
};
```

---

## 接收自定义消息

同样以**接收评价消息**为例，接收方可根据消息体上 `props.msg.attachment` 的自定义字段，判断当前消息是否为评价消息，从而自行实现任意的 UI 展示。

在 `NEUIKit/Chat/message/message-item-content.vue` 中，添加自定义消息渲染代码：

```javascript
// 解析自定义消息附件
const raw = props?.msg?.attachment?.raw || "{}";
// 根据发送自定义消息时的标记位进行判断
const isEvaluationMessage = JSON.parse(raw).isEvaluationMessage;
```

```html
<template>
  <!-- 根据自定义字段判断是否为评价消息，自行实现任意 UI 展示 -->
  <div v-else-if="isEvaluationMessage">
    <div class="text">欢迎您进行评价</div>
    <img
      class="star"
      src="https://yx-web-nosdn.netease.im/common/57dea86c692f76efdfecff37ee7e1059/星星.png"
    />
    <img
      class="star"
      src="https://yx-web-nosdn.netease.im/common/57dea86c692f76efdfecff37ee7e1059/星星.png"
    />
    <img
      class="star"
      src="https://yx-web-nosdn.netease.im/common/57dea86c692f76efdfecff37ee7e1059/星星.png"
    />
    <img
      class="star"
      src="https://yx-web-nosdn.netease.im/common/57dea86c692f76efdfecff37ee7e1059/星星.png"
    />
    <img
      class="star"
      src="https://yx-web-nosdn.netease.im/common/57dea86c692f76efdfecff37ee7e1059/星星.png"
    />
  </div>
</template>
```
