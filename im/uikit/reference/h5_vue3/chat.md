# 仅集成聊天页面

> 更新时间：2025/09/11 14:15:56

网易云信 IM UIKit 支持仅集成会话消息组件，无需集成会话列表、通讯录、搜索等完整功能组件。本方案适用于在线客服、专业咨询、电商交易平台等只需聊天功能的场景。

---

## 接口介绍

在仅集成会话消息组件场景中，主要使用以下关键接口：

- **`selectConversation`**：选择已有会话。
- **`insertConversationActive`**：创建并选择新会话。

> 会话 ID 格式：`${myAccount}|${conversationType}|${receiverId}`，组装方式请参考 `createConversation`。

---

## 前提条件

根据本文操作前，请确保您已完成以下设置：

- 导入并初始化组件
- 配置路由
- 注册 IM 账号用于用户登录

---

## 操作步骤

### 第一步：初始化与配置

组件导入和初始化后，初始化 IM UIKit 并配置必要参数：

```typescript
<script lang="ts">
import { V2NIMConst } from "nim-web-sdk-ng/dist/esm/nim";
import RootStore from "@xkit-yx/im-store-v2";
import V2NIM from "nim-web-sdk-ng/dist/v2/NIM_BROWSER_SDK";
import type { V2NIMMessage } from "nim-web-sdk-ng/dist/esm/nim/src/V2NIMMessageService";
import { getCurrentInstance } from "vue";

export default {
  name: "App",
  methods: {
    initIMUiKit(opts: { account: string; token: string }) {
      const init = () => {
        // 初始化 NIM SDK
        const nim = V2NIM.getInstance(
          {
            appkey: "your appkey",
            needReconnect: true,
            debugLevel: "debug",
            apiVersion: "v2",
            enableV2CloudConversation: true,
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
          // 参数二：本地化配置，必填
          {
            addFriendNeedVerify: false,
            // 是否需要显示单聊消息已读未读，默认 false
            p2pMsgReceiptVisible: true,
            // 是否需要显示群组消息已读未读，默认 false
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

        return { nim, store }
      }

      const { nim, store } = init()
      const app = getCurrentInstance()

      // 将 nim 和 store 挂载到全局，供组件内部访问
      if (app) {
        app.appContext.app.config.globalProperties.$NIM = nim
        app.appContext.app.config.globalProperties.$UIKitStore = store
      }

      nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
        this.$router.push("/conversation")
        this.showUiKit = true
      })
    },
  },
  mounted() {
    this.initIMUiKit({ account: "xxx", token: "xxxx" })
  },
}
</script>
```

### 第二步：发起会话

提供单聊和群聊两种方式进入聊天页面。在跳转至聊天页面前，请调用 `store` 中的 `selectConversation` 或 `insertConversationActive`，传入对应参数。

```vue
<template>
  <div>
    <button @click="gotoChat">单聊</button>
    <button @click="gotoGroupChat">群聊</button>
  </div>
</template>

<script>
import { getCurrentInstance } from "vue";

export default {
  name: "IMApp",
  methods: {
    gotoChat() {
      const { proxy } = getCurrentInstance();
      const store = proxy?.$UIKitStore;
      const myAccount = store.userStore.myUserInfo.accountId;
      const conversationType = 1; // 单聊类型
      const receiverId = "xxxx"; // 对方用户账号 ID
      const conversationId = `${myAccount}|${conversationType}|${receiverId}`;

      // 以下以本地会话为例，云端会话请将 localConversationStore 替换为 conversationStore
      if (store.localConversationStore.conversations.get(conversationId)) {
        store.uiStore.selectConversation(conversationId);
      } else {
        store.localConversationStore.insertConversationActive(
          conversationType,
          receiverId,
          true, // isSelected
        );
      }

      this.$router.push("/chat");
    },

    gotoGroupChat() {
      const { proxy } = getCurrentInstance();
      const store = proxy?.$UIKitStore;
      const myAccount = store.userStore.myUserInfo.accountId;
      const conversationType = 2; // 群聊类型
      const receiverId = "xxxxx"; // 群组 ID
      const conversationId = `${myAccount}|${conversationType}|${receiverId}`;

      if (store.localConversationStore.conversations.get(conversationId)) {
        store.uiStore.selectConversation(conversationId);
      } else {
        store.localConversationStore.insertConversationActive(
          conversationType,
          receiverId,
          true, // isSelected
        );
      }

      this.$router.push("/chat");
    },
  },
};
</script>
```
