网易云信 IM 支持对客户端本地和网易云信服务器存储历史消息进行查询、删除操作。

本文介绍如何调用 NIM SDK 的接口实现历史消息管理。

## 准备工作

使用该功能前，请在 [网易云信控制台](https://app.yunxin.163.com/global/home) 开通 **历史消息** 功能，具体请参考 [配置基础功能/全局功能](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#全局功能)。

::: note note
网易云信 IM 支持云端历史消息，历史消息的存储时长在不同的套餐包中不同，各套餐包中的限制具体请参考 [增值服务](https://yunxin.163.com/price/im)。
:::

## 监听消息相关事件

在进行历史消息相关操作前，您可以提前注册监听相关事件。注册成功后，当对应事件发生时，SDK 会触发对应回调通知。

- **相关回调**：

    - **`onMessageDeletedNotifications`**：消息删除成功回调。当本地端或多端同步删除消息成功时会触发该回调。
    - **`onClearHistoryNotifications`**：消息清空成功回调。当本地端或多端同步清空消息成功时会触发该回调。

- **示例代码**：

提示：若需要确认示例中使用的类/接口的方法签名、参数类型或返回结构，可使用 `nim_sdk_search_symbols` 与 `nim_sdk_list_members` 查询。

    :::::: div linked-codes
    ::: code 安卓
    调用 [`addMessageListener`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#addMessageListener) 方法注册消息相关监听器，监听消息删除回调事件和消息清空回调事件。
    ```Java
    V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

    V2NIMMessageListener messageListener = new V2NIMMessageListener() {

        @Override
        public void onMessageDeletedNotifications(List<V2NIMMessageDeletedNotification> messageDeletedNotifications) {

        }

        @Override
        public void onClearHistoryNotifications(List<V2NIMClearHistoryNotification> clearHistoryNotifications) {

        }
    };
    v2MessageService.addMessageListener(messageListener);
    ```
    如需移除消息相关监听器，可调用 [`removeMessageListener`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#removeMessageListener)。
    ```Java
    V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

    v2MessageService.removeMessageListener(messageListener);
    ```
    :::
    ::::::

## 查询历史消息

### 分页查询会话内所有历史消息

调用 `getMessageListEx` 或 `getMessageList` 方法分页查询指定会话的所有历史消息数据。

可以通过设置查询条件查询指定时间范围内具体消息类型的历史消息数据。

:::::: div linked-codes
::: code Android
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

V2NIMConversationType conversationType = V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P;
String conversationId = V2NIMConversationIdUtil.conversationId("xxx", conversationType);

V2NIMMessageListOption listOption = V2NIMMessageListOption.V2NIMMessageListOptionBuilder.builder(conversationId)
        // TODO: 根据实际情况配置
//                .withAnchorMessage()
//                .withBeginTime()
//                .withDirection()
//                .withEndTime()
//                .withLimit()
//                .withMessageTypes()
//                .withStrictMode()
        .build();

v2MessageService.getMessageListEx(listOption,
        new V2NIMSuccessCallback<V2NIMMessageListResult>() {
            @Override
            public void onSuccess(V2NIMMessageListResult result) {
                // TODO: 成功
            }
        },
        new V2NIMFailureCallback() {
            @Override
            public void onFailure(V2NIMError error) {
                // TODO: 失败
            }
        });
```
:::
::::::

<!--getMessageList 示例
:::::: div linked-codes
::: code 安卓
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

V2NIMConversationType conversationType = V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P;
String conversationId = V2NIMConversationIdUtil.conversationId("xxx", conversationType);

// 根据实际情况配置
V2NIMMessageListOption listOption = V2NIMMessageListOption.V2NIMMessageListOptionBuilder.builder(conversationId)
        .withAnchorMessage()
        .withBeginTime()
        .withDirection()
        .withEndTime()
        .withLimit()
        .withMessageTypes()
        .withStrictMode()
        .build();

v2MessageService.getMessageList(listOption, new V2NIMSuccessCallback<List<V2NIMMessage>>() {
    @Override
    public void onSuccess(List<V2NIMMessage> v2NIMMessages) {
        // TODO: 成功
    }
}, new V2NIMFailureCallback() {
    @Override
    public void onFailure(V2NIMError error) {
        // TODO: 失败
    }
});
```
:::
::::::
-->

### 分页查询会话内所有云端历史消息

调用 `getCloudMessageList` 方法按条件分页获取会话内的云端历史消息数据。

可以通过设置查询条件查询指定时间范围内具体消息类型的历史消息数据。

**示例代码**

:::::: div linked-codes
::: code Android
```Java
// 构建云端消息查询参数
String conversationId = "xxxx";
long beginTime = System.currentTimeMillis() - 7 * 24 * 60 * 60 * 1000L; // 7天前
long endTime = System.currentTimeMillis(); // 当前时间
int limit = 20; // 查询20条消息

V2NIMCloudMessageListOption option = V2NIMCloudMessageListOption.V2NIMCloudMessageListOptionBuilder
    .builder(conversationId)
    .withBeginTime(beginTime)
    .withEndTime(endTime)
    .withLimit(limit)
    .withDirection(V2NIMMessageQueryDirection.V2NIM_QUERY_DIRECTION_DESC)
    .build();

// 调用接口
NIMClient.getService(V2NIMMessageService.class).getCloudMessageList(option,
    new V2NIMSuccessCallback<V2NIMMessageListResult>() {
        @Override
        public void onSuccess(V2NIMMessageListResult result) {
            System.out.println("查询云端消息成功");
            
            List<V2NIMMessage> messages = result.getMessages();
            V2NIMMessage anchorMessage = result.getAnchorMessage();
            
            System.out.println("获取到 " + messages.size() + " 条消息");
            
            for (V2NIMMessage message : messages) {
                System.out.println("消息ID: " + message.getMessageClientId());
                System.out.println("消息内容: " + message.getText());
                System.out.println("发送时间: " + message.getCreateTime());
                System.out.println("---");
            }
            
            if (anchorMessage != null) {
                System.out.println("还有更多消息，下次查询锚点: " + anchorMessage.getMessageClientId());
            } else {
                System.out.println("没有更多消息了");
            }
        }
    },
    new V2NIMFailureCallback() {
        @Override
        public void onFailure(V2NIMError error) {
            System.out.println("查询云端消息失败, code: " + error.getCode() + 
                             ", message: " + error.getDesc());
        }
    }
);
```
:::
<!--开发未提供
-->
<!--暂不支持
-->
::::::

### 批量查询指定的历史消息列表

调用 `getMessageListByIds` 方法根据消息客户端 ID 查询指定的历史消息。

::: note note
- 该接口只在本地数据库中查询。
- Web 端不支持该接口。
:::

:::::: div linked-codes
::: code 安卓
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

List<String> messageClientIds = new ArrayList<>();
// TODO
// messageClientIds.add("xxx");

v2MessageService.getMessageListByIds(messageClientIds,
        new V2NIMSuccessCallback<List<V2NIMMessage>>() {
            @Override
            public void onSuccess(List<V2NIMMessage> v2NIMMessages) {

            }
        },
        new V2NIMFailureCallback() {
            @Override
            public void onFailure(V2NIMError error) {

            }
        });
```
:::
::::::

### 分页查询云端 Thread 历史消息

调用 `getThreadMessageList` 方法根据 Thread 根消息分页获取所有 Thread 子消息。

::: note note
为避免触发请求频控，若云端会话数据同步已完成（收到 `onSyncFinished`），建议您改用 [`getLocalThreadMessageList`](#getLocalThreadMessageList) 方法获取本地 Thread 历史消息列表。
:::

示例代码如下：

:::::: div linked-codes
::: code 安卓
```Java
V2NIMThreadMessageListOption option = new V2NIMThreadMessageListOption();
// messageRefer field is mandatory and is used to determine a root message. It can be filled directly with a message body or a V2NIMMessageRefer object can be constructed based on the message body's fields.
option.setMessageRefer(rootMessageRefer);
// The following fields are optional
// beginTime and endTime define the time range of the query. Fill with 0 to indicate no restriction.
option.setBeginTime(0L);
option.setEndTime(0L);
// For the first page, leave this field blank. When paging, fill in the serverId of the message closest to the target page's current page. For example, anchorMessage represents the earliest message on the current page, allowing the current request to page backward (earlier events). Query 20 messages earlier than anchorMessage (excluding itself).
option.setExcludeMessageServerId("123456");
// Maximum number of messages to be retrieved
option.setLimit(20);
// Query direction, default is V2NIM_QUERY_DIRECTION_DESC
option.setDirection(V2NIMQueryDirection.V2NIM_QUERY_DIRECTION_DESC);

NIMClient.getService(V2NIMMessageService.class).getThreadMessageList(option, v2NIMThreadMessageListResult -> {
    //Query successful
}, error -> {
    //Query failed
});
```
:::
::::::

### 查询本地 Thread 历史消息

调用 `getLocalThreadMessageList` 方法根据 Thread 根消息获取所有本地 Thread 子消息。

示例代码如下：

:::::: div linked-codes
::: code 安卓
```Java
// messageRefer 字段用于确定一条根消息。可以直接填消息体，也可以根据消息体的字段，构造一个 V2NIMMessageRefer 对象
V2NIMMessageRefer messageRefer = rootMessage;
NIMClient.getService(V2NIMMessageService.class).getLocalThreadMessageList(messageRefer, v2NIMThreadMessageListResult -> {
    //Query successful
}, error -> {
    //Query failed
});
```
:::
::::::

## 删除历史消息

网易云信 IM 的删除消息接口，可通过配置 `onlyDeleteLocal` 参数选择是否只删除本地消息。

- 只删除本地，本地会将该消息标记为删除，后续查询本地消息时会过滤该消息，界面不展示，卸载重装会再次显示。
- 删除本地的同时删除云端对应的消息，删除后消息无法恢复。

    :::note note
    删除云端消息功能需要在 [网易云信控制台](https://app.yunxin.163.com/global/home) 单独开通，只有开通后，该功能才能使用。若未开通，请参考 [开启功能项](https://doc.yunxin.163.com/console/concept/TQ2NzE5MzQ?platform=console) 进行开通。
    :::

### 删除指定单条消息

调用 `deleteMessage` 方法删除指定的单条消息。

删除指定消息后，若消息还未读，则应用总消息未读数会减 1。

若需要删除的消息处于未发送成功状态，则只删除本地消息，不涉及云端消息，因此不会触发多端同步。删除本地消息的同时删除对应的云端消息后，会多端同步该操作。

本地或云端删除消息成功后，SDK 会返回删除成功回调 `onMessageDeletedNotifications`。

:::::: div linked-codes
::: code 安卓
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

// V2NIMMessage deleteMessage = ; // 被删除的消息

v2MessageService.deleteMessage(deleteMessage, "xxx", false,
        new V2NIMSuccessCallback<Void>() {
            @Override
            public void onSuccess(Void unused) {

            }
        },
        new V2NIMFailureCallback() {
            @Override
            public void onFailure(V2NIMError error) {

            }
        });
```
:::
::::::

### 批量删除指定消息列表

调用 `deleteMessages` 方法删除指定会话内的消息列表。

删除指定消息列表后，若消息还未读，则应用总消息未读数会减去对应的删除的消息数量。

若需要删除的消息处于未发送成功状态，则只删除本地消息，不涉及云端消息，因此不会触发多端同步。删除本地消息的同时删除对应的云端消息后，会多端同步该操作。

本地或云端删除消息成功后，SDK 会返回删除成功回调 `onMessageDeletedNotifications`。

:::::: div linked-codes
::: code 安卓
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

// List<V2NIMMessage> messages = ; // 被删除的消息

v2MessageService.deleteMessages(messages, "xxx", false,
        new V2NIMSuccessCallback<Void>() {
            @Override
            public void onSuccess(Void unused) {

            }
        },
        new V2NIMFailureCallback() {
            @Override
            public void onFailure(V2NIMError error) {

            }
        });
```
:::
::::::

## 清空历史消息

调用 `clearHistoryMessage` 方法清空指定会话内的所有消息，支持指定是否同步删除漫游消息以及是否多端同步清空操作。您也可以自由选择清空 **本地和云端** 或 **仅本地** 的历史消息。

本地或云端清空消息成功后，SDK 会返回清空成功回调 `onClearHistoryNotifications`。

:::::: div linked-codes
::: code 安卓
```Java
V2NIMMessageService v2MessageService = NIMClient.getService(V2NIMMessageService.class);

V2NIMConversationType conversationType = V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P;
String conversationId = V2NIMConversationIdUtil.conversationId("xxx", conversationType);

V2NIMClearHistoryMessageOption clearOption = V2NIMClearHistoryMessageOption.V2NIMClearHistoryMessageOptionBuilder.builder(conversationId)
   // TODO: 根据实际情况配置
   //                .withDeleteRoam()
   //                .withExtension()
   //                .withOnlineSync()
   //                .withClearMode()
   .build();

v2MessageService.clearHistoryMessage(clearOption,
   new V2NIMSuccessCallback<Void>() {
    @Override
    public void onSuccess(Void unused) {

    }
   },
   new V2NIMFailureCallback() {
    @Override
    public void onFailure(V2NIMError error) {

    }
   });
```
:::
::::::

## 清空指定的漫游会话消息

调用 `clearRoamingMessage` 方法清空指定的漫游会话消息，建议单次最多删除 50 个漫游会话。

清空的漫游会话越多，请求耗时越长，因此若数据量打，建议分次操作。

:::::: div linked-codes
::: code 安卓
```Java
List<String> conversationIds = new ArrayList<>();
conversationIds.add("xxx1|1|xxx2");
conversationIds.add("xxx1|1|xxx3");
NIMClient.getService(V2NIMMessageService.class).clearRoamingMessage(
        conversationIds,
        new V2NIMSuccessCallback<Void>() {
            @Override
            public void onSuccess(Void result) {
                System.out.println("clearRoamingMessage success");
            }
        },
        new V2NIMFailureCallback() {
            @Override
            public void onFailure(V2NIMError error) {
                System.out.println("clearRoamingMessage failed");
            }
        }
);
```
:::
<!--暂未提供
-->
<!--暂不支持
-->
::::::

### 相关接口

:::::: div linked-codes
::: code Android
API | 说明
--- | ---
[`addMessageListener`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#addMessageListener) | 注册消息相关监听器
[`removeMessageListener`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#removeMessageListener) | 取消注册消息相关监听器
[`getMessageList`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getMessageList) | 按消息查询配置项分页获取所有历史消息
[`getMessageListEx`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getMessageListEx) | 按消息查询配置项分页获取所有历史消息
[`getMessageListByIds`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getMessageListByIds) | 根据消息客户端 ID 获取历史消息
[`deleteMessage`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#deleteMessage) | 删除单条消息
[`deleteMessages`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#deleteMessages) | 批量删除消息列表
[`clearHistoryMessage`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#clearHistoryMessage) | 清空会话内历史消息
[`getThreadMessageList`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getThreadMessageList) | 分页获取云端 Thread 历史消息列表
[`getLocalThreadMessageList`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getLocalThreadMessageList) | 获取本地 Thread 历史消息列表
[`getCloudMessageList`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#getCloudMessageList) | 按消息查询配置项分页获取云端消息
[`clearRoamingMessage`](https://doc.yunxin.163.com/messaging2/client-apis/zIwODM2NTM?platform=client#clearRoamingMessage) | 清空指定的漫游会话消息
:::
::::::
