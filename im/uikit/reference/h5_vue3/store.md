# 全局上下文

> 更新时间：2025/11/14 18:28:55

在 IM UIKit 中，`store` 是一个基于 MobX 封装的全局上下文对象，它提供了数据和 UI 之间的双向绑定能力。`store` 包含多个子模块，每个子模块负责管理不同的数据领域，如会话、消息、通讯录、群组等。通过 `store`，您可以方便地获取数据、响应数据变化，并更新 UI。

---

## 适用场景

`store` 在 IM UIKit 中的使用场景包括但不限于：

- **消息发送与管理**：使用 `store` 管理会话消息，发送文本、图片、视频等类型的消息。
- **状态同步**：同步用户在线状态、群组信息、好友列表等。
- **数据绑定**：在 UI 组件中使用 `store` 的数据进行数据绑定，实现 UI 的自动更新。
- **自定义消息**：发送自定义消息格式，满足特定业务需求。
- **扩展功能**：在特定场景下（如群聊）添加扩展字段或处理特定业务逻辑。

---

## 前提条件

在使用 IM UIKit `store` 前，请确保您已完成 IM UIKit 的初始化。详情请参考**初始化**。

---

## 第一步：初始化 store

```javascript
// 初始化 NIM SDK
const nim = V2NIM.getInstance(
  {
    appkey: "your appkey",
    needReconnect: true,
    debugLevel: "debug",
    apiVersion: "v2",
    enableV2CloudConversation: enableV2CloudConversation,
  },
  {
    V2NIMLoginServiceConfig: {
      lbsUrls: ["https://lbs.netease.im/lbs/webconf.jsp"],
      linkUrl: "weblink.netease.im",
    },
  }
)

// 初始化 store
const store = new RootStore(
  // @ts-ignore
  // 参数一：NIMSDK，必填
  nim,
  // 参数二：本地化配置，必填，详细参数说明请参考底部常见问题
  {
    addFriendNeedVerify: false,
    p2pMsgReceiptVisible: true,
    teamMsgReceiptVisible: true,
    teamAgreeMode: V2NIMConst.V2NIMTeamAgreeMode.V2NIM_TEAM_AGREE_MODE_NO_AUTH,
    sendMsgBefore: async (options: {
      msg: V2NIMMessage;
      conversationId: string;
      serverExtension?: Record<string, unknown>;
    }) => {
      return { ...options }
    },
  },
  // 参数三：平台，可选
  "H5"
)
```

---

## 第二步：数据渲染

使用 `autorun` 监听 `store` 数据的变化，从而驱动 UI 更新。具体使用说明请参考 **MobX 官方文档**。

> 调用 `store` 相较于直接调用 NIM SDK 方法，具备数据与 UI 双向绑定的能力，无需您额外处理 UI 的增删。

**store 子模块说明：**

| 返回值              | 类型   | 说明                                                              |
| ------------------- | ------ | ----------------------------------------------------------------- |
| `UiStore`           | Object | MobX 可观察对象，负责 UI 会用到的属性的子 store。                 |
| `ConnectStore`      | Object | MobX 可观察对象，负责连接的子 store。                             |
| `MsgStore`          | Object | MobX 可观察对象，负责管理会话消息的子 store。                     |
| `SysMsgStore`       | Object | MobX 可观察对象，负责管理好友申请、群申请相关系统消息的子 store。 |
| `ConversationStore` | Object | MobX 可观察对象，负责管理会话相关的子 store。                     |
| `FriendStore`       | Object | MobX 可观察对象，负责管理好友信息的子 store。                     |
| `UserStore`         | Object | MobX 可观察对象，负责管理用户信息（包含陌生人）的子 store。       |
| `RelationStore`     | Object | MobX 可观察对象，负责管理黑名单和静音列表的子 store。             |
| `TeamStore`         | Object | MobX 可观察对象，负责管理群组的子 store。                         |
| `TeamMemberStore`   | Object | MobX 可观察对象，负责管理群组成员的子 store。                     |

**示例**：创建群聊时添加群扩展字段，调用 `TeamStore` 中的 `createTeamActive` 接口：

```javascript
store.teamStore
  .createTeamActive({
    name: "测试群",
    avatar: "https:xxxxxxxxxxx",
    accounts: ["account1", "account2"],
    serverExtension: JSON.stringify({ test: "test" }),
  })
  .then((res) => {
    console.log("创建群成功", res);
  })
  .catch((err) => {
    console.log("创建群失败", err);
  });
```

---

## 常见问题

### 如何监听 store 的数据变更？

通过 `autorun` 可以获取最新的 `store` 数据。以下示例展示如何监听会话列表的变更：

```javascript
import { autorun } from "mobx";

let conversationListWatch = () => {};

onMounted(() => {
  conversationListWatch = autorun(() => {
    conversationList.value = store?.uiStore?.conversations;
  });
});

// 组件卸载时移除监听
onUnmounted(() => {
  conversationListWatch();
});
```

### 如何进行 store 本地化配置？

参考以下参数进行本地化配置：

```typescript
export interface LocalOptions {
  /** 添加好友模式，默认需要验证 */
  addFriendNeedVerify?: boolean;
  /** 群组更新模式，默认管理员可修改 */
  teamUpdateTeamMode?: V2NIMTeamUpdateInfoMode;
  /** 群组更新自定义字段模式，默认所有人可修改 */
  teamUpdateExtMode?: V2NIMTeamUpdateExtensionMode;
  /** 是否需要 @ 消息提醒，默认 true */
  needMention?: boolean;
  /** 转让群主的同时是否退出群聊，默认 false */
  leaveOnTransfer?: boolean;
  /** 是否允许群主转让，默认 false */
  allowTransferTeamOwner?: boolean;
  /** 是否需要显示单聊消息、单聊会话列表消息已读未读，默认 false */
  p2pMsgReceiptVisible?: boolean;
  /** 是否需要显示群组消息已读未读，默认 false */
  teamMsgReceiptVisible?: boolean;
  /** 是否需要显示群管理员相关主动功能，默认 false */
  teamManagerVisible?: boolean;
  /** 是否开启数字人功能，默认 true */
  aiVisible?: boolean;
  /** 单个群管理员默认数量限制，默认 10 个 */
  teamManagerLimit?: number;
  /** 发送消息前的钩子函数，异步函数，返回 false 则不发送消息 */
  sendMsgBefore?: (params: any) => Promise<V2NIMSendMessageParams | false>;
  /** AI 机器人提供者 */
  aiUserAgentProvider?: AIUserAgentProvider;
  /** 会话列表拉取会话数量，默认 100 */
  conversationLimit?: number;
  /** 是否开启日志打印 */
  debug?: "off" | "debug";
}
```
