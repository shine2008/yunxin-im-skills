网易云信 IM 支持云端会话管理功能，包括创建、更新、删除会话等基础操作，以及置顶会话等进阶操作。

本文介绍如何调用 NIM SDK 的接口实现云端会话管理。

## 技术原理

网易云信 IM 支持云端（服务端）会话，在服务器进行存储和维护，支持存储和查询用户全量的会话历史消息。**有关服务端会话及未读数，请参考服务端 API [云端会话管理](https://doc.yunxin.163.com/messaging2/server-apis/DgwMjAzMjM?platform=server)**。

当用户发送消息时，SDK 会自动创建会话并更新最近会话列表，但在特定场景下需要您手动创建会话，例如：创建一个空会话占位。

SDK 会自动同步服务端数据，如果您注册了会话相关监听，数据同步开始和同步结束均有回调通知。如果数据同步已开始，建议您在数据同步结束后再进行会话其他操作。在新设备登录 IM 后，SDK 会根据当前的漫游、离线消息自动生成最近会话列表。

## 前提条件

根据本文操作前，请确保您已经完成了以下设置：

- 已在 [网易云信控制台](https://app.yunxin.163.com/global/home) 为应用开通 **云端会话** 开关。开启步骤请参考《控制台文档》[配置应用](https://doc.yunxin.163.com/console/concept/TQ2NzE5MzQ?platform=console)。

    <img alt="开通云端会话.png" src="https://yx-web-nosdn.netease.im/common/d80e78c7f8d0a5b7f0452e7f5d9c6b47/开通云端会话.png" style="width:50%;border: 1px solid #BFBFBF;">

- 已在初始化时开启云端会话功能。若不开启，将默认使用 [本地会话](https://doc.yunxin.163.com/messaging2/guide/zMyODU1OTM?platform=client) 功能。

    :::::: div linked-codes
    ::: code iOS
    将 [`V2NIMSDKOption.enableV2CloudConversation`](https://doc.yunxin.163.com/messaging2/client-apis/DAxNjk0Mzc?platform=client#v2nimsdkoption仅-ios) 设置为 `YES`，只有设置为 YES 后，才能正常使用云端会话 [`V2NIMConversationService`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#接口类) 服务。具体请请参考 [初始化 SDK](https://doc.yunxin.163.com/messaging2/guide/jU5MTUwMDY?platform=client#第三步调用初始化接口)。
    :::
    ::::::   

- 已实现 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。

## 监听会话相关事件

在进行会话相关操作前，您可以提前注册监听相关事件。注册成功后，当会话相关事件发生时，SDK 会触发对应回调通知。

### 注册监听

云端会话相关回调：

- `onSyncStarted`：数据同步开始回调。
- `onSyncFinished`：数据同步结束回调。如果数据同步已开始，建议在数据同步结束后再进行其他会话操作。
- `onSyncFailed`：数据同步失败回调。
- `onConversationCreated`：会话成功创建回调。
- `onConversationDeleted`：主动删除会话回调。
- `onConversationChanged`：会话变更回调。当置顶会话、会话有新消息、主动更新会话成功时会触发该回调。
- `onTotalUnreadCountChanged`：会话消息总未读数变更回调。
- `onUnreadCountChangedByFilter`：过滤后的未读数变更回调。调用 `subscribeUnreadCountByFilter` 方法订阅监听后，当会话过滤后的未读数变化时会返回该回调。
- `onConversationReadTimeUpdated`：同一账号多端登录后的会话已读时间戳标记的回调。

示例代码：

:::::: div linked-codes
::: code iOS
调用 [`addConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#addConversationListener) 方法注册会话相关监听器，监听数据同步开始结束、会话创建、删除、变更及未读数变化等。
```Objective-C
@interface V2ConversationServiceSample : NSObject <V2NIMConversationListener>
@end
@implementation V2ConversationServiceSample

- (void)onSyncStarted
{
    // sync started
}

- (void)onSyncFinished
{
    // sync finished
}

- (void)onSyncFailed:(V2NIMError *)error
{
    // sync failed
}

- (void)onConversationCreated:(V2NIMConversation *)conversation
{
    // conversation created
}

- (void)onConversationDeleted:(NSArray<NSString *> *)conversationIds
{
    // conversations deleted
}

- (void)onConversationChanged:(NSArray<V2NIMConversation *> *)conversations
{
    // conversations changed
}

- (void)onTotalUnreadCountChanged:(NSInteger)unreadCount
{
    // total unread count changed
}

- (void)onUnreadCountChangedByFilter:(V2NIMConversationFilter *)filter
                        unreadCount:(NSInteger)unreadCount
{
    // filter unread count changed
}

- (void)onConversationReadTimeUpdated:(NSString *)conversationId
                            readTime:(NSTimeInterval)readTime
{
    // ReadTime changed
}

- (void)addConversationListener
{
    [NIMSDK.sharedSDK.v2ConversationService addConversationListener:self];
}

@end
```
:::
::::::

### 移除监听

:::::: div linked-codes
::: code iOS
如需移除会话相关监听器，可调用 [`removeConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#removeConversationListener)。

```Objective-C
id<V2NIMConversationListener> listener;
[NIMSDK.sharedSDK.v2ConversationService removeConversationListener:listener];
```
:::
::::::

### 过滤监听

调用 `subscribeUnreadCountByFilter` 方法订阅指定过滤条件过滤后的会话未读数变化监听：

:::::: div linked-codes
::: code iOS
```Objective-C
V2NIMConversationFilter *filter = [[V2NIMConversationFilter alloc] init];
filter.conversationTypes = @[
    @(V2NIM_CONVERSATION_TYPE_P2P),
    @(V2NIM_CONVERSATION_TYPE_TEAM),
];
filter.conversationGroupId = @"groupId";
filter.ignoreMuted = YES;
[NIMSDK.sharedSDK.v2ConversationService subscribeUnreadCountByFilter:filter];
```
:::
::::::

如需取消订阅可调用 `unsubscribeUnreadCountByFilter` 方法：

:::::: div linked-codes
::: code iOS
```Objective-C
V2NIMConversationFilter *filter = [[V2NIMConversationFilter alloc] init];
filter.conversationTypes = @[
    @(V2NIM_CONVERSATION_TYPE_P2P),
    @(V2NIM_CONVERSATION_TYPE_TEAM),
];
filter.conversationGroupId = @"groupId";
filter.ignoreMuted = YES;
[NIMSDK.sharedSDK.v2ConversationService unsubscribeUnreadCountByFilter:filter];
```
:::
::::::

## 创建会话

一般场景下，当收发消息成功后，SDK 会自动创建一条会话。特殊场景下，例如需要会话占位，您可以调用 `createConversation` 方法创建一条空会话。

创建成功后，SDK 会返回创建成功回调 `onConversationCreated`，并同步缓存和数据库。

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
NSString *conversationId = [V2NIMConversationIdUtil p2pConversationId:@"accountId"];
[NIMSDK.sharedSDK.v2ConversationService createConversation:conversationId success:^(V2NIMConversation *conversation) {
    // conversation created
} failure:^(V2NIMError *error) {
    // create failed, handle error
}];
```
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService getConversationList:0 limit:10 success:^(V2NIMConversationResult *result) {
    // receive result
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

### 获取指定单条会话

调用 `getConversation` 按照会话 ID 获取指定会话。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService getConversation:@"conversationId" success:^(V2NIMConversation *conversation) {
    // success
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

### 根据查询选项分页获取会话列表

调用 `getConversationListByOption` 按照设定的查询选项分页获取会话列表，直到获取全量会话。

获取的会话列表结果中，置顶会话排首位。如有多个置顶会话，则按照时间顺序倒序展示。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话列表。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
V2NIMConversationOption *option = [[V2NIMConversationOption alloc] init];
option.conversationTypes = @[
    @(V2NIM_CONVERSATION_TYPE_P2P),
    @(V2NIM_CONVERSATION_TYPE_TEAM),
];
option.conversationGroupIds = @[
    @"groupIdA",
    @"groupIdB",
];
option.onlyUnread = YES;
[NIMSDK.sharedSDK.v2ConversationService getConversationListByOption:0 limit:10 option:option success:^(V2NIMConversationResult * result) {
    // receive result
} failure:^(V2NIMError * error) {
    // handle error
}];
```
:::
::::::

### 批量获取指定的会话列表

调用 `getConversationListByIds` 按照会话 ID 批量获取会话列表。

获取的会话列表结果中，置顶会话排首位。如有多个置顶会话，则按照时间顺序倒序展示。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话列表。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService getConversationListByIds:@[@"conversationIdA", @"conversationIdB"] success:^    (NSArray<V2NIMConversation *> *conversationList) {
    // receive conversation list
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

## 更新会话

调用 `updateConversation` 更新会话服务端扩展字段。

更新成功后，SDK 会返回会话变更回调 `onConversationChanged`，并同步数据库和缓存。

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
V2NIMConversationUpdate *info = [[V2NIMConversationUpdate alloc] init];
info.localExtension = @"localExtension";
info.serverExtension = @"serverExtension";
[NIMSDK.sharedSDK.v2ConversationService updateConversation:@"conversationId"    updateInfo:info success:^{
    // success
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

## 更新会话本地扩展字段

调用 `updateConversationLocalExtension` 更新会话本地扩展字段。

本地扩展字段更新后，SDK 会返回会话变更回调 `onConversationChanged`。

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService updateConversationLocalExtension:@"conversationId" localExtension:@"localExtension" success:^{
    // success
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

## 删除会话

### 删除指定单条会话

调用 `deleteConversation` 根据会话 ID 删除指定会话（包括本地和云端会话），且支持指定是否删除会话内的历史消息（包括本地和漫游消息）。

删除指定会话后，应用总消息未读数会减去该会话的未读数。

删除成功后，SDK 会返回删除成功回调 `onConversationDeleted`，并同步数据库和缓存。

如果被删除会话中有消息未读，SDK 还会返回 `onTotalUnreadCountChanged` 和 `onUnreadCountChangedByFilter` 回调。

::: note note
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService deleteConversation:@"conversationId" clearMessage:NO success:^{
    // delete succeeded
} failure:^(V2NIMError *error) {
    // delete failed, handle error
}];
```
:::
::::::

### 批量删除指定会话列表

调用 `deleteConversationListByIds` 根据会话 ID 批量删除指定会话列表（包括本地和云端会话），且支持指定是否删除会话内的历史消息（包括本地和漫游消息）。

批量删除成功后会返回删除失败的会话列表及错误信息，应用总消息未读数会减去已删除会话的未读数。

每一条最近会话删除成功后，SDK 均会返回删除成功回调 `onConversationDeleted` 回调，并同步数据库和缓存。

如果被删除会话中有消息未读，SDK 还会返回 `onTotalUnreadCountChanged` 和 `onUnreadCountChangedByFilter` 回调。

::: note notice
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService deleteConversationListByIds:@[@"conversationIdA", @"conversationIdB"] clearMessage:NO     success:^(NSArray<V2NIMConversationOperationResult *> *resultList) {
    if (resultList.count > 0) {
        // delete succeeded, check failed conversation
    }
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

## 消息未读数

### 获取消息未读数

**获取全部会话消息总未读数**

调用 `getTotalUnreadCount` 方法获取全部会话的消息总未读数。

::: note notice
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
NSInteger count = [NIMSDK.sharedSDK.v2ConversationService getTotalUnreadCount];
```
:::
::::::

**获取指定会话列表的消息总未读数**

调用 `getUnreadCountByIds` 方法按照会话 ID 列表获取指定会话列表的消息总未读数。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService getUnreadCountByIds:@[@"conversationIdA", @"conversationIdB"] success:^(NSInteger    unreadCount) {
    // receive unread count
} failure:^(V2NIMError * _Nonnull error) {
    // handle error
}];
```
:::
::::::

**获取过滤后的会话消息总未读数**

调用 `getUnreadCountByFilter` 方法按照会话 ID 列表获取指定会话列表的消息总未读数。

::: note note
如果数据同步已开始（收到 `onSyncStarted` 回调），建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作，否则可能无法获取到完整的会话数据。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
V2NIMConversationFilter *filter = [[V2NIMConversationFilter alloc] init];
filter.conversationTypes = @[
    @(V2NIM_CONVERSATION_TYPE_P2P),
    @(V2NIM_CONVERSATION_TYPE_TEAM)
];
filter.conversationGroupId = @"groupId";
filter.ignoreMuted = YES;

[NIMSDK.sharedSDK.v2ConversationService getUnreadCountByFilter:filter
    success:^(NSInteger unreadCount) {
        // receive unread count
    }
    failure:^(V2NIMError * _Nonnull error) {
        // handle error
    }];
```
:::
::::::

### 未读数清零

网易云信 IM 支持将会话内的消息未读数清零，即标记为已读。

**将全部会话消息总未读数清零**

调用 `clearTotalUnreadCount` 将应用内全部会话的消息总未读数清零。

清零成功后，SDK 返回 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，并同步数据库和缓存。

::: note note
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService clearTotalUnreadCount:^{

} failure:^(V2NIMError * _Nonnull error) {
    // handle error
}];
```
:::
::::::

**将指定会话列表的消息未读数清零**

调用 `clearUnreadCountByIds` 按照会话 ID 批量将指定会话列表的消息未读数清零。

清零成功后，SDK 返回清零失败的会话 ID 列表，并触发 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，同步数据库和缓存。

::: note note
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService clearUnreadCountByIds:@[@"conversationIdA", @"conversationIdB"] success:^(NSArray<V2NIMConversationOperationResult *> *resultList) {
    if (resultList.count > 0) {
        // check failed conversation
    }
} failure:^(V2NIMError * _Nonnull error) {
    // handle error
}];
```
:::
::::::

**将指定分组的会话消息未读数清零**

调用 `clearUnreadCountByGroupId` 按照会话分组 ID 将指定分组的会话消息未读数清零。

清零成功后，SDK 返回 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，并同步数据库和缓存。

::: note note
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService clearUnreadCountByGroupId:@"groupId" success:^{

        } failure:^(V2NIMError * _Nonnull error) {
            // handle error
        }];
```
:::
::::::

**将指定类型的会话消息未读数清零**

调用 `clearUnreadCountByTypes` 方法按照会话类型批量将指定类型的会话消息未读数清零。

清零成功后，SDK 返回 `onTotalUnreadCountChanged`、`onConversationChanged` 和 `onUnreadCountChangedByFilter` 回调，并同步数据库和缓存。

::: note note
建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService clearUnreadCountByTypes:@[
        @(V2NIM_CONVERSATION_TYPE_P2P),
        @(V2NIM_CONVERSATION_TYPE_TEAM),
    ] success:^{
        // success
    } failure:^(V2NIMError * _Nonnull error) {
        // handle error
    }];
```
:::
::::::

### 标记会话已读

**标记会话已读**

调用 `markConversationRead` 方法标记指定会话已读。当前只支持 P2P，高级群，超大群会话类型。

标记成功后，会触发 `onConversationReadTimeUpdated` 回调，返回标记已读的时间戳。SDK 会同步数据库和缓存。

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
- (void)markRead
{
    [NIMSDK.sharedSDK.v2ConversationService markConversationRead:@"conversationId" success:^(NSTimeInterval timestamp) {
        // result
    } failure:^(V2NIMError * _Nonnull error) {
        // error
    }];
}
```
:::
::::::

**获取会话已读时间戳**

调用 `getConversationReadTime` 方法获取指定会话的已读时间戳。

::: note note
- 查询前请确保指定的会话已存在。
- 数据同步完成前，可能查询不到完整数据。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
- (void)getReadTime
{
    [NIMSDK.sharedSDK.v2ConversationService getConversationReadTime:@"conversationId" success:^(NSTimeInterval timestamp) {
        // result
    } failure:^(V2NIMError * _Nonnull error) {
        // error
    }];
}
```
:::
::::::

## 进阶功能

### 会话置顶

调用 `stickTopConversation` 方法按照会话 ID 将指定会话添加为置顶状态。最多支持置顶 100 条会话。

置顶成功后，SDK 会返回会话变更回调 `onConversationChanged`，并同步数据库和缓存。

::: note notice
- 使用会话置顶功能前，您需要 [已开通 IM 套餐包](https://doc.yunxin.163.com/messaging2/quick-start/jU0Mzg0MTU?platform=client#第一步开通-im-套餐包) 并在 [网易云信控制台](https://app.yunxin.163.com/global/home) 的 **应用管理 > 产品功能 > IM 即时通讯 > 全局功能** 路径下开通会话指定功能。
- 建议您在数据同步完成（收到 `onSyncFinished` 回调）后再进行该操作。
:::

**示例代码**：

:::::: div linked-codes
::: code iOS
```Objective-C
[NIMSDK.sharedSDK.v2ConversationService stickTopConversation:@"conversationId" stickTop:YES success:^{
    // success
} failure:^(V2NIMError *error) {
    // handle error
}];
```
:::
::::::

## 查询置顶会话

查询当前全量置顶的云端会话列表。

:::::: div linked-codes
::: code iOS
```Objective-C
[[NIMSDK sharedSDK].v2ConversationService getStickTopConversationList:^(NSArray<V2NIMConversation *> * _Nonnull conversationList) {
    // 获取成功
} failure:^(V2NIMError * _Nonnull error) {
    // 获取失败
}];
```
:::
::::::

## 相关信息

- [云端会话分组管理](https://doc.yunxin.163.com/messaging2/guide/DYzMjEwOTg?platform=client)

- [本地会话管理](https://doc.yunxin.163.com/messaging2/guide/TIyMDA1Nzg?platform=client)

- [会话相关错误码](https://doc.yunxin.163.com/messaging2/guide/DUxNjU3MzU?platform=client#会话错误)

## 相关接口

:::::: div linked-codes
::: code 安卓/iOS/macOS/Windows
API | 说明
--- | ---
[`addConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#addConversationListener) | 注册云端会话相关监听器
[`removeConversationListener`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#removeConversationListener) | 取消注册云端会话相关监听器
[`subscribeUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#subscribeUnreadCountByFilter) | 订阅过滤后的会话未读数变化
[`unsubscribeUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#unsubscribeUnreadCountByFilter) | 取消订阅过滤后的会话未读数变化
[`createConversation`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#createConversation) | 创建一条空会话
[`getConversationList`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getconversationlist) | 获取会话列表
[`getConversation`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getconversation) | 获取单条会话
[`getConversationListByOption`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getconversationlistbyoption) | 获取指定会话列表
[`getConversationListByIds`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getconversationlistbyids) | 根据会话 ID 批量获取会话列表
[`updateConversation`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#updateConversation) | 更新会话服务端扩展字段
[`updateConversationLocalExtension`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#updateConversationLocalExtension) | 更新指定会话的本地扩展字段
[`deleteConversation`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#deleteconversation) | 删除一条会话
[`deleteConversationListByIds`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#deleteconversationlistbyids) | 根据会话 ID 批量删除会话列表
[`getTotalUnreadCount`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#gettotalunreadcount) | 获取全部会话的消息总未读数
[`getUnreadCountByIds`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getunreadcountbyids) | 获取指定会话列表的消息总未读数
[`getUnreadCountByFilter`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getunreadcountbyfilter) | 根据过滤参数获取相应的消息未读数
[`clearTotalUnreadCount`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#cleartotalunreadcount) | 清空所有会话总的未读数
[`clearUnreadCountByIds`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#clearUnreadCountByIds) | 清空指定会话列表的消息总未读数
[`clearUnreadCountByGroupId`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#clearunreadcountbygroupid) | 根据会话分组 ID 清除分组内所有会话的消息总未读数
[`clearUnreadCountByTypes`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#clearunreadcountbytypes) | 根据会话类型清除指定会话类型的所有未读数
[`markConversationRead`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#markConversationRead) | 标记会话已读时间戳
[`getConversationReadTime`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#markConversationRead) | 获取会话已读时间戳
[`stickTopConversation`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#sticktopconversation) | 置顶会话
[`getStickTopConversationList`](https://doc.yunxin.163.com/messaging2/client-apis/zA4NjQzOTQ?platform=client#getStickTopConversationList) | 查询当前置顶的全量云端会话列表
:::
::::::