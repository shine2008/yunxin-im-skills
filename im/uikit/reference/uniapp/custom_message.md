# 收发自定义消息

> 更新时间：2025/09/11 14:15:56

本文介绍网易云信 IM UIKit uni-app 版如何收发自定义消息。

---

## 前提条件

根据本文操作前，请确保您已完成 IM UIKit 初始化。

---

## 发送自定义消息

以**发送一条评价消息**为例，发送时可通过 `createCustomMessage` 创建一条自定义消息，调用 `sendMessage` 进行发送。

```javascript
// 发送评价消息
const sendEvaluationMessage = () => {
  const message = uni.$UIKitNIM.V2NIMMessageCreator.createCustomMessage(
    "this is a custom message" + Date.now(),
    JSON.stringify({
      isEvaluationMessage: true,
    }),
  );

  const store = uni.$UIKitStore;
  const to = store.uiStore.selectedConversation;

  uni.$UIKitNIM.V2NIMMessageService.sendMessage(message, to).then((res) => {
    // 将返回的消息体插入消息列表
    store.msgStore._handleSendMsgSuccess(res.message);
    // 让消息滚动到可视区域
    scrollBottom();
  });
};
```

---

## 接收自定义消息

同样以**接收评价消息**为例，接收方可根据消息体上 `props.attachment` 的自定义字段，判断当前消息是否为评价消息，从而自行实现任意的 UI 展示。

在 `NEUIKit/pages/Chat/message/message-item.vue` 中，添加自定义消息渲染的代码：

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
