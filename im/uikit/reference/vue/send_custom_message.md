# 集成自定义消息

本文介绍网易云信 IM UIKit 如何收发自定义消息。

## 前提条件

根据本文操作前，请确保您已经完成 IM UIKit 初始化。

## 发送自定义消息

以 **发送一条评价消息** 为例，发送时，可通过 `createCustomMessage` 创建一条自定义消息，调用 `sendMessage` 进行发送。

::: note note  
以下代码中的 **nim、store、scrollToBottom** 相关获取，可参考 **/NEUIKit/Chat/message/message-input.vue** 中的写法。
:::

```ts
// 发送评价消息
const sendEvaluationMessage = () => {
    const message = nim.V2NIMMessageCreator.createCustomMessage(
    'this is a custom message' + Date.now(),
    //todo 标识为自定义消息的字段
    JSON.stringify({
        isEvaluationMessage: true,
    }),
    );

    const to = store.uiStore.selectedConversation;
    nim.V2NIMMessageService.sendMessage(message, to).then((res) => {
    // 把当前返回的消息体插入消息列表
    store.msgStore._handleSendMsgSuccess(res.message);
    // 让消息滚动到可视区域
    scrollToBottom()
    });
}
```

## 接收自定义消息

同样以 **接收评价消息** 为例，接收方可根据消息体上的 `props.attachment` 上的自定义字段，判断当前消息是否为评价消息，从而自行实现任意的 UI 展示。

在 **NEUIKit** 的 **/NEUIKit/Chat/message/message-item-content.vue** 下，添加自定义消息渲染的代码。

```vue
const raw = props?.attachment?.raw || '{}';
  // 这里根据发送自定义消息时的标记位进行判断
  const isEvaluationMessage = JSON.parse(raw).isEvaluationMessage;

<template>
    <div
      // todo 根据自定义字段 自行判断自定义消息
      v-else-if="isEvaluationMessage"
    >
      // todo自行实现任意的 自定义消息 UI 展示
       <div className='text'>欢迎您进行评价</div>
        </div>
    </div>
</template>
```
