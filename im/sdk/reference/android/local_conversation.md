网易云信 IM 支持本地会话管理功能，包括创建、更新、删除本地会话等基础操作，以及置顶会话等进阶操作。本地会话列表由 SDK 维护并提供查询、监听变化的接口，当会话变更时，SDK 会自动更新会话列表并通知，您无需手动更新。

本文介绍如何调用 NIM SDK 的接口实现本地会话管理。

## 技术原理

网易云信 IM 支持本地（客户端）会话，在客户端进行存储和维护，支持存储和查询用户全量的会话历史消息。

当用户发送消息时，SDK 会自动创建本地会话并更新最近本地会话列表，但在特定场景下需要您手动创建会话，例如：创建一个空本地会话占位。

SDK 会自动同步会话数据，如果您注册了会话相关监听，数据同步开始和同步结束均有回调通知。如果数据同步已开始，建议您在数据同步结束后再进行会话其他操作。在新设备登录 IM 后，SDK 会根据当前的漫游、离线消息自动生成最近会话列表。

## 监听相关事件

在进行本地会话相关操作前，您可以提前注册监听相关事件。注册成功后，当本地会话相关事件发生时，SDK 会触发对应回调通知。

### 注册监听

本地会话相关回调：

- `onSyncStarted`：数据同步开始回调。建议在回调完成后操作数据，如果在此期间操作数据，会出现数据不全，只能操作部分数据的情况。
- `onSyncFinished`：数据同步结束回调。此回调后需要用户重新获取本地会话列表。回调后可任意操作相关的本地会话数据。
- `onSyncFailed`：数据同步失败回调。此回调后可以操作本地会话数据，但由于同步失败，本地只能操作已有数据，数据可能不全。在相关错误恢复后，SDK 会逐步按需重建会话数据。
- `onConversationCreated`：本地会话创建回调，返回创建的本地会话对象。当主动创建本地会话成功时会触发该回调。
- `onConversationDeleted`：本地会话删除回调，返回被删除的本地会话 ID 列表。当主动删除本地会话成功时会触发该回调。
- `onConversationChanged`：本地会话变更回调，返回变更后的会话列表。当调用会话置顶、免打扰、更新消息内容成功时会触发该回调。
- `onTotalUnreadCountChanged`：本地会话消息总未读数变更回调，返回变更后的消息未读数。
- `onUnreadCountChangedByFilter`：指定过滤条件的会话消息未读数变更，根据过滤条件返回变更后的消息未读数。调用 `subscribeUnreadCountByFilter` 方法订阅后，当过滤后的会话消息未读数变化时会返回该回调。
- `onConversationReadTimeUpdated`：同一账号多端登录会话已读时间戳标记时间变更回调，返回同步标记的会话 ID 和标记的时间戳。

示例代码：

提示：若需要确认示例中使用的类/接口的方法签名、参数类型或返回结构，可使用 `nim_sdk_search_symbols` 与 `nim_sdk_list_members` 查询。

:::::: div linked-codes
::: code 安卓
调用 [`addConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/TIyMDA1Nzg?platform=client#addConversationListener) 方法注册本地会话相关监听器，监听数据同步开始结束、会话创建、删除、变更及未读数变化等。

```Java
V2NIMLocalConversationListener listener = new V2NIMLocalConversationListener() {
  @Override
  public void onSyncStarted() {  
  }
  @Override
  public void onSyncFinished() {
  }
  @Override
  public void onSyncFailed(V2NIMError error) {
  }
  @Override
  public void onConversationCreated(V2NIMLocalConversation conversation) {
  }
  @Override
  public void onConversationDeleted(List<String> conversationIds) {
  }
  @Override
  public void onConversationChanged(List<V2NIMLocalConversation> conversationList) {
  }
  @Override
  public void onTotalUnreadCountChanged(int unreadCount) {
  }
  @Override
  public void onUnreadCountChangedByFilter(V2NIMLocalConversationFilter filter, int unreadCount) {
  }
  @Override
  public void onConversationReadTimeUpdated(String conversationId, long readTime) {
  }
};
NIMClient.getService(V2NIMLocalConversationService.class).addConversationListener(listener);
```
:::
::::::

### 移除监听

:::::: div linked-codes
::: code 安卓
如需移除会话相关监听器，可调用 [`removeConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/TIyMDA1Nzg?platform=client#removeConversationListener)。
```Java
NIMClient.getService(V2NIMLocalConversationService.class).removeConversationListener(listener);
```
:::
::::::

### 过滤监听

调用 `subscribeUnreadCountByFilter` 方法订阅指定过滤条件过滤后的本地会话未读数变化：

:::::: div linked-codes
::: code 安卓
```Java
List<V2NIMConversationType> conversationTypes = new ArrayList<>();
conversationTypes.add(V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P);
//是否忽略免打扰
boolean ignoreMuted = true;
V2NIMLocalConversationFilter filter = new V2NIMLocalConversationFilter(conversationTypes,ignoreMuted);
NIMClient.getService(V2NIMLocalConversationService.class).subscribeUnreadCountByFilter(filter);
```
:::
::::::

如需取消订阅可调用 `unsubscribeUnreadCountByFilter` 方法：

:::::: div linked-codes
::: code 安卓
```Java
List<V2NIMConversationType> conversationTypes = new ArrayList<>();
conversationTypes.add(V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P);
//是否忽略免打扰
boolean ignoreMuted = true;
V2NIMLocalConversationFilter filter = new V2NIMLocalConversationFilter(conversationTypes,ignoreMuted);
NIMClient.getService(V2NIMLocalConversationService.class).unsubscribeUnreadCountByFilter(filter);
```
:::
::::::

## 创建本地会话

一般场景下，当本地端收发消息，SDK 会自动创建一条本地会话。特殊场景下，例如需要本地会话占位，您可以调用 `createConversation` 方法创建一条本地空会话。

创建成功后，SDK 会返回创建成功回调 `onConversationCreated`，并同步更新缓存和数据库。

::: note note
如果没有消息收发，该会话仅创建人可见。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
NIMClient.getService(V2NIMLocalConversationService.class).createConversation(conversationId, new V2NIMSuccessCallback<V2NIMLocalConversation>() {
  @Override
  public void onSuccess(V2NIMLocalConversation result) {
   // 创建成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 创建失败
  }
});
```
:::
::::::

## 获取本地会话

网易云信 IM 提供多种方式获取本地会话列表，用于在应用首页显示用户会话。

### 分页获取所有本地会话列表

调用 `getConversationList` 方法从客户端本地分页获取会话数据。

本地历史会话数据无上限，因此建议分页多次获取，直到获取全量会话数据，SDK 会进行数据同步并返回对应回调通知 UI 层。

::: note note
- 查询的本地会话列表结果中，置顶会话排首位。
- 如果会话数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
- 查询云端会话时，当正好查询完所有数据时，下次查询时才会提示查询完毕（finished = true）。但是查询本地会话时，当正好查询完所有本地数据时，当次查询时就会提示查询完毕（finished = true）。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
int offset = 0;
//分页拉取数量，不建议超过100
int limit = 100;
NIMClient.getService(V2NIMLocalConversationService.class).getConversationList(offset, limit, new V2NIMSuccessCallback<V2NIMLocalConversationResult>() {
    @Override
    public void onSuccess(V2NIMLocalConversationResult result) {
        // 获取成功
        //本地会话列表
        List<V2NIMLocalConversation> conversationList = result.getConversationList();
        //是否拉取完毕
        boolean finished = result.isFinished();
        //下一次拉取偏移量，首次传入的offset值，后续拉取采用上一次返回的offset值。如果finished为true，则返回0
        long nextOffset = result.getOffset();
    }
}, new V2NIMFailureCallback() {
    @Override
    public void onFailure(V2NIMError error) {
        //获取失败
    }
});
```
:::
::::::

### 获取指定单条本地会话

调用 `getConversation` 根据会话 ID 获取单条本地会话。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
NIMClient.getService(V2NIMLocalConversationService.class).getConversation(conversationId, new V2NIMSuccessCallback<V2NIMLocalConversation>() {
  @Override
  public void onSuccess(V2NIMLocalConversation result) {
    // 获取成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
    //获取失败   
  }
});
```
:::
::::::

### 根据筛选条件分页查询本地会话列表

调用 `getConversationListByOption` 根据指定的筛选条件分页查询本地会话列表。

该方法根据筛选条件从客户端本地分页获取会话数据，直到获取全量会话，SDK 会进行数据同步并返回对应回调通知 UI 层。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
int offset = 0;
//分页拉取数量，不建议超过100
int limit = 100;
//会话类型列表,为空表示查询所有类型，否则查询指定对话类型
List<V2NIMConversationType> conversationTypes = new ArrayList<>();
//是否只拉取未读会话，默认false
boolean onlyUnread = false;
V2NIMLocalConversationOption option = new V2NIMLocalConversationOption(conversationTypes, onlyUnread);
NIMClient.getService(V2NIMLocalConversationService.class).getConversationListByOption(offset, limit, option, new V2NIMSuccessCallback<V2NIMLocalConversationResult>() {
    @Override
    public void onSuccess(V2NIMLocalConversationResult result) {
        // 获取成功
        //本地会话列表
        List<V2NIMLocalConversation> conversationList = result.getConversationList();
        //是否拉取完毕
        boolean finished = result.isFinished();
        //下一次拉取偏移量，首次传入的offset值，后续拉取采用上一次返回的offset值。如果finished为true，则返回0
        long nextOffset = result.getOffset();
    }
}, new V2NIMFailureCallback() {
    @Override
    public void onFailure(V2NIMError error) {
        //获取失败
    }
});
```
:::
::::::

### 批量获取指定的本地会话列表

调用 `getConversationListByIds` 根据会话 ID 批量查询指定的本地会话列表。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<String> conversationIds = new ArrayList<>();
conversationIds.add("cjl|1|cjl1");
conversationIds.add("cjl|2|43243253");
conversationIds.add("cjl|3|234323432");
NIMClient.getService(V2NIMLocalConversationService.class).getConversationListByIds(conversationIds, new V2NIMSuccessCallback<List<V2NIMLocalConversation>>() {
  @Override
  public void onSuccess(List<V2NIMLocalConversation> result) {
   // 获取成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 获取失败
  }
});
```
:::
::::::

## 更新会话的本地扩展信息

调用 `updateConversationLocalExtension` 更新本地会话的本地扩展信息。

本地扩展字段更新后，SDK 会返回会话变更回调 `onConversationChanged`，并同步更新缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
//本地扩展字段
String localExtension = "localExtension";
NIMClient.getService(V2NIMLocalConversationService.class).updateConversationLocalExtension(conversationId, localExtension, new V2NIMSuccessCallback<Void>() {
  @Override
  public void onSuccess(Void result) {
   // 更新成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 更新失败
  }
});
```
:::
::::::

## 删除本地会话

### 删除指定单条本地会话

调用 `deleteConversation` 根据会话 ID 删除指定的本地会话，且支持指定是否删除会话内的历史消息。

删除指定本地会话后，应用总消息未读数会减去该会话的未读数。

删除成功后，SDK 会返回删除成功回调 `onConversationDeleted`，并同步更新缓存和数据库。

如果被删除的本地会话中有消息未读，SDK 还会返回 `onTotalUnreadCountChanged` 和 `onUnreadCountChangedByFilter` 回调。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
//是否清空消息记录
boolean clearMessage = true;
NIMClient.getService(V2NIMLocalConversationService.class).deleteConversation(conversationId, clearMessage, new V2NIMSuccessCallback<Void>() {
  @Override
  public void onSuccess(Void result) {
   // 删除成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 删除失败
  }
});
```
:::
::::::

### 批量删除指定本地会话列表

调用 `deleteConversationListByIds` 根据会话 ID 批量删除指定的本地会话列表，且支持指定是否删除会话内的历史消息。

批量删除成功后会返回删除失败的会话列表及错误信息，应用总消息未读数会减去已删除会话的未读数。

删除每一条本地会话成功后，SDK 均会返回删除成功回调 `onConversationDeleted` 回调，并同步更新缓存和数据库。

如果被删除的本地会话中有消息未读，SDK 还会返回 `onTotalUnreadCountChanged` 和 `onUnreadCountChangedByFilter` 回调。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<String> conversationIds = new ArrayList<>();
conversationIds.add("cjl|1|cjl1");
conversationIds.add("cjl|2|43243253");
conversationIds.add("cjl|3|234323432");
//是否清空消息记录
boolean clearMessage = true;
NIMClient.getService(V2NIMLocalConversationService.class).deleteConversationListByIds(conversationIds, clearMessage, new V2NIMSuccessCallback<List<V2NIMLocalConversationOperationResult>>() {
  @Override
  public void onSuccess(List<V2NIMLocalConversationOperationResult> result) {
   // 删除成功,返回会话ID操作失败的列表
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 删除失败
  }
});
```
:::
::::::

## 本地会话消息未读数

### 获取消息未读数

**获取所有本地会话消息总未读数**

调用 `getTotalUnreadCount` 方法获取所有本地会话的消息总未读数。

当消息未读数有任何变更，都会触发 `onTotalUnreadCountChanged` 回调。

::: note note
- 如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
- 该接口为同步接口。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
int totalUnreadCount = NIMClient.getService(V2NIMLocalConversationService.class).getTotalUnreadCount();
```
:::
::::::

**获取指定本地会话列表的消息总未读数**

调用 `getUnreadCountByIds` 方法根据会话 ID 获取指定本地会话列表的消息总未读数。

返回的消息总未读数为所有合法会话的消息未读数的总和。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<String> conversationIds = new ArrayList<>();
conversationIds.add("cjl|1|cjl1");
conversationIds.add("cjl|2|43243253");
NIMClient.getService(V2NIMLocalConversationService.class).getUnreadCountByIds(conversationIds, new V2NIMSuccessCallback<Integer>() {
  @Override
  public void onSuccess(Integer unreadCounts) {
   // 获取成功,返回总未读数为所有合法会话加和后的值
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   //获取失败
  }
});
```
:::
::::::

**获取过滤后的本地会话消息总未读数**

调用 `getUnreadCountByFilter` 方法根据过滤条件获取过滤后的所有本地会话列表的消息总未读数。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<V2NIMConversationType> conversationTypes = new ArrayList<>();
//是否忽略免打扰
boolean ignoreMuted = true;
V2NIMLocalConversationFilter filter = new V2NIMLocalConversationFilter(conversationTypes, ignoreMuted);
NIMClient.getService(V2NIMLocalConversationService.class).getUnreadCountByFilter(filter, new V2NIMSuccessCallback<Integer>() {
  @Override
  public void onSuccess(Integer unreadCounts) {
   // 获取成功,返回总未读数为所有合法会话加和后的值
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   //获取失败
  }
});
```
:::
::::::

### 未读数清零

网易云信 IM 支持将本地会话内的消息未读数清零，即标记为已读。

**将所有本地会话消息未读数清零**

调用 `clearTotalUnreadCount` 将应用内所有本地会话的消息未读数清零。

清零成功后，SDK 返回 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，并同步更新缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
NIMClient.getService(V2NIMLocalConversationService.class).clearTotalUnreadCount(new V2NIMSuccessCallback<Void>() {
  @Override
  public void onSuccess(Void unused) {
   // 清除成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 清除失败
  }
});
```
:::
::::::

**将指定本地会话列表的消息未读数清零**

调用 `clearUnreadCountByIds` 方法根据会话 ID 批量将指定本地会话列表的消息未读数清零。

清零成功后，SDK 返回清零失败的会话 ID 列表，并触发 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，同步更新缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<String> conversationIds = new ArrayList<>();
conversationIds.add("cjl|1|cjl1");
conversationIds.add("cjl|2|43243253");
NIMClient.getService(V2NIMLocalConversationService.class).clearUnreadCountByIds(conversationIds, new V2NIMSuccessCallback<List<V2NIMLocalConversationOperationResult>>() {
  @Override
  public void onSuccess(List<V2NIMLocalConversationOperationResult> result) {
   // 清除成功,返回会话ID操作失败的列表
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 清除失败
  }
});
```
:::
::::::

**将指定类型的本地会话消息未读数清零**

调用 `clearUnreadCountByTypes` 方法按照会话类型批量将指定类型的本地会话消息未读数清零。

清零成功后，SDK 返回 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，并同步更新缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
List<V2NIMConversationType> conversationTypes = new ArrayList<>();
conversationTypes.add(V2NIMConversationType.V2NIM_CONVERSATION_TYPE_P2P);
NIMClient.getService(V2NIMLocalConversationService.class).clearUnreadCountByTypes(conversationTypes, new V2NIMSuccessCallback<Void>() {
  @Override
  public void onSuccess(Void unused) {
   // 清除成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 清除失败
  }
});
```
:::
::::::

### 标记本地会话已读

**标记本地会话已读**

调用 `markConversationRead` 方法标记指定本地会话已读。当前只支持 P2P，高级群，超大群会话类型。

标记成功后，会触发 `onConversationReadTimeUpdated` 回调，返回标记已读的时间戳。SDK 会同步更新缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
NIMClient.getService(V2NIMLocalConversationService.class).markConversationRead(conversationId, new V2NIMSuccessCallback<Long>() {
  @Override
  public void onSuccess(Long result) {
   // 标记成功,返回会话已读时间戳，单位毫秒
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 标记失败
  }
});
```
:::
::::::

**获取本地会话已读时间戳**

调用 `getConversationReadTime` 方法获取指定本地会话的已读时间戳。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
NIMClient.getService(V2NIMLocalConversationService.class).getConversationReadTime(conversationId, new V2NIMSuccessCallback<Long>() {
  @Override
  public void onSuccess(Long result) {
   // 获取成功,返回会话已读时间戳，单位毫秒
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {

  }
});
```
:::
::::::

## 会话置顶

### 置顶指定的本地会话

调用 `stickTopConversation` 方法根据本地会话 ID 将指定会话添加为置顶状态。最多支持置顶 100 个本地会话。

置顶成功后，SDK 会返回会话变更回调 `onConversationChanged`，并同步更新缓存和数据库。

::: note notice
- 使用会话置顶功能前，您需要 [已开通 IM 套餐包](https://doc.yunxin.163.com/messaging2/quick-start/jU0Mzg0MTU?platform=client#第一步开通-im-套餐包) 并在 [网易云信控制台](https://app.yunxin.163.com/global/home) **应用管理 > 产品功能 > IM 即时通讯 > 全局功能** 开通会话指定功能。
- 置顶前请确保该本地会话已存在。
- 如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能查询不到完整数据或者查询到的是老数据。
:::

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
//是否置顶,true：置顶, false：取消置顶
boolean stickTop = true;
NIMClient.getService(V2NIMLocalConversationService.class).stickTopConversation(conversationId, stickTop, new V2NIMSuccessCallback<Void>() {
  @Override
  public void onSuccess(Void result) {
   // 置顶成功
  }
}, new V2NIMFailureCallback() {
  @Override
  public void onFailure(V2NIMError error) {
   // 置顶失败
  }
});
```
:::
::::::

### 查询置顶会话

查询当前全量置顶的本地会话列表。

**示例代码**：

:::::: div linked-codes
::: code 安卓
```Java
NIMClient.getService(V2NIMLocalConversationService.class).getStickTopConversationList(
    new V2NIMSuccessCallback<List<V2NIMLocalConversation>>() {
        @Override
        public void onSuccess(List<V2NIMLocalConversation> conversationList) {
            // receive result
        }
    }, new V2NIMFailureCallback() {
        @Override
        public void onFailure(V2NIMError error) {
            int code = error.getCode();
            String desc = error.getDesc();;
            // handle error
        }
    }
);
```
:::
::::::

## 设置正在聊天的会话

设置指定会话为当前正在聊天的会话。设置后，SDK 将接管该会话的本地已读时间记录，以及必要的同步操作。

当前正在聊天的会话数量不超过 1 个，如果多次设置，则当前会话为最后一次设置的会话。设置为空表示没有当前会话。

当用户正处于设置的当前正在聊天会话时，收到的该会话的新消息都不计未读数。

**示例代码：**

:::::: div linked-codes
::: code 安卓
```Java
String conversationId = "cjl|1|cjl1";
V2NIMLocalConversationService conversationService = NIMClient.getService(V2NIMLocalConversationService.class);

V2NIMSyncResult<Void> result = conversationService.setCurrentConversation(conversationId);
if (result.isSuccess()) {
    // 设置当前会话成功
} else {
    // 设置当前会话失败，处理错误
    V2NIMError error = result.getError();
}
```
:::
::::::

## 相关信息

- [会话相关错误码](https://doc.yunxin.163.com/messaging2/guide/DUxNjU3MzU?platform=client#会话错误码)
- [云端会话管理](https://doc.yunxin.163.com/messaging2/guide/zExMDk4ODU?platform=client)

## 相关接口

:::::: div linked-codes
::: code 安卓
API | 说明
--- | ---
[`addConversationListener`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#addConversationListener) | 注册本地会话相关监听 
[`removeConversationListener`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#removeConversationListener) | 取消注册本地会话相关监听 
[`subscribeUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#subscribeUnreadCountByFilter) | 订阅指定过滤条件的本地会话消息未读数变化
[`unsubscribeUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#unsubscribeUnreadCountByFilter) | 取消订阅指定过滤条件的本地会话消息未读数变化
[`createConversation`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#createConversation) | 创建一条空本地会话  
[`getConversation`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getConversation) | 根据会话 ID 获取单条本地会话 
[`getConversationList`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getConversationList) | 获取所有本地会话列表 
[`getConversationListByIds`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getConversationListByIds) | 根据会话 ID 批量获取本地会话列表 
[`getConversationListByOption`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getConversationListByOption) | 根据指定的筛选条件获取本地会话列表 
[`updateConversationLocalExtension`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#updateConversationLocalExtension) | 更新本地会话的本地扩展信息 
[`deleteConversation`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#deleteConversation) | 删除一条本地会话 
[`deleteConversationListByIds`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#deleteConversationListByIds) | 根据会话 ID 批量删除本地会话列表 
[`getTotalUnreadCount`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getTotalUnreadCount) | 获取全部本地会话的消息总未读数 
[`getUnreadCountByIds`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getUnreadCountByIds) | 根据会话 ID 获取指定本地会话的消息总未读数
[`getUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getUnreadCountByFilter) | 根据过滤参数获取相应的本地会话消息未读数 
[`clearTotalUnreadCount`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#clearTotalUnreadCount) | 清除所有本地会话的消息总未读数
[`clearUnreadCountByIds`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#clearUnreadCountByIds) | 根据会话 ID 清除指定本地会话列表的消息未读数 
[`clearUnreadCountByTypes`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#clearUnreadCountByTypes) | 根据会话类型清除指定本地会话类型的消息未读数 
[`markConversationRead`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#markConversationRead) | 标记本地会话已读时间戳
[`getConversationReadTime`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getConversationReadTime) | 获取本地会话已读时间戳
[`stickTopConversation`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#stickTopConversation) | 置顶本地会话
[`getStickTopConversationList`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#getStickTopConversationList) | 查询当前置顶的全量本地会话列表
[`setCurrentConversation`](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client#setCurrentConversation) | 设置当前正在聊天的会话。
:::
::::::
