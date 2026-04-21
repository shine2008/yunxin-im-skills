# 集成源码

> 更新时间：2025/12/29 11:42:53

IM HTML5 Demo（简称 IM H5 Demo）是基于网易云信 IM UIKit（NEUIKit）打造的一款 Demo，托管在 GitHub 开源代码仓库 `nim-uikit-H5`。该 Demo 提供了会话、聊天、群组等通用功能，您可以基于源码搭建自己的即时通讯业务逻辑。

---

## 前提条件

开始跑通 Demo 之前，请确保您已完成以下操作：

- 在**网易云信控制台**上创建应用，并获取 App Key。
- 注册 IM 账号，获取账号 ID 和 token。
- 在**网易云信控制台**上，为应用开通**消息漫游**开关（开启步骤请参考《控制台文档》配置应用）。
- 调整本地开发环境，满足如下要求：

| 配置项              | 要求                             |
| ------------------- | -------------------------------- |
| 集成开发环境        | vuecli / vite                    |
| Vue.js 版本         | Vue 3                            |
| 开发语言            | TypeScript                       |
| Node.js 版本        | v16 及以上                       |
| JavaScript 包管理器 | NPM（版本请与 Node.js 版本匹配） |
| Android             | 8.1.0 及以上                     |
| iOS                 | 14 及以上                        |

---

## 操作步骤

### 第一步：创建项目

您可以使用 vuecli 或者 vite 创建项目，详细步骤请参考 **Vue 官网文档**。

> 如果您已创建项目，请忽略本步骤。

### 第二步：下载组件

1. 手动下载 `NEUIKit.zip` IM UIKit 组件。
2. 在下载的资源包中找到 **NEUIKit** 文件夹，将其复制到您项目的 `components` 目录下。
3. 执行以下命令安装 NEUIKit 所需依赖：

```bash
npm i nim-web-sdk-ng@10.9.30 @xkit-yx/im-store-v2@0.8.8 @xkit-yx/utils@0.7.2 dayjs@1.11.7 mobx@6.6.1 mitt@3.0.1 --save
```

| 依赖                   | 说明                                                                                                                                                                              |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nim-web-sdk-ng`       | 网易云信 NIM SDK，提供了一整套即时通讯基础能力。                                                                                                                                  |
| `@xkit-yx/im-store-v2` | store 用于全局状态管理，基于 MobX 和 NIM SDK 封装的全局上下文对象，提供数据和 UI 之间的双向绑定能力。包含多个子模块，分别管理会话、消息、通讯录、群组等数据。详见**全局上下文**。 |
| `@xkit-yx/utils`       | 项目中的工具库，提供 `getFileType`、`parseFileSize` 等函数。                                                                                                                      |

下载成功后，可根据业务需求对源码进行修改，具体目录结构请参考**工程结构**。

### 第三步：集成并初始化组件

在您的项目 `App.vue` 文件中引入 NEUIKit 组件，参考以下代码初始化 `nim` 和 `store`：

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
        );
        // 初始化 store
        const store = new RootStore(
          // @ts-ignore
          // 参数一：NIMSDK，必填
          nim,
          // 参数二：本地化配置，必填，详细参数说明请参考全局上下文底部常见问题
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
              return { ...options };
            },
          },
          // 参数三：平台，可选
          "H5"
        );
        return { nim, store };
      };

      const { nim, store } = init();
      const app = getCurrentInstance();

      // 将 nim 和 store 挂载到全局，供组件内部访问
      if (app) {
        app.appContext.app.config.globalProperties.$NIM = nim;
        app.appContext.app.config.globalProperties.$UIKitStore = store;
      }

      nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
        this.$router.push("/conversation");
        this.showUiKit = true;
      });
    },
  },
  mounted() {
    this.initIMUiKit({ account: "xxx", token: "xxxx" });
  },
};
</script>
```

### 第四步：配置路由

在项目的路由文件中配置所需页面，并将路由同步至 `/NEUIKit/utils/uikitRouter.ts`（UIKit 内部也需要根据路由跳转）。

**1. 在 `router.ts` 中添加路由：**

```typescript
import { createRouter, createWebHashHistory } from "vue-router";

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: "/NEUIKit/chat",
      name: "Chat",
      component: () => import("../views/chat/ChatPage.vue"),
    },
  ],
});

export default router;
```

**2. 在 `ChatPage.vue` 中引入 Chat 组件：**

```vue
<template>
  <Chat></Chat>
</template>

<script lang="ts" setup>
import Chat from "@/components/NEUIKit/Chat/index.vue";
</script>
```

**3. 同步路由至 `/NEUIKit/utils/uikitRouter.ts`：**

```typescript
export const neUiKitRouterPath = {
  chat: "/chat",
};
```

**完整路由配置**（参考 Demo 中的 `router.ts`）：

```typescript
import { createRouter, createWebHashHistory } from "vue-router";
import { neUiKitRouterPath } from "@/components/NEUIKit/utils/uikitRouter";

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    { path: "/", redirect: neUiKitRouterPath.conversation },
    {
      path: neUiKitRouterPath.chat,
      name: "Chat",
      component: () => import("../views/chat/ChatPage.vue"),
    },
    {
      path: neUiKitRouterPath.messageReadInfo,
      name: "MessageReadInfo",
      component: () => import("../views/chat/MessageReadInfoPage.vue"),
    },
    {
      path: neUiKitRouterPath.p2pSetting,
      name: "P2pSetting",
      component: () => import("../views/chat/P2pSettingPage.vue"),
    },
    {
      path: neUiKitRouterPath.conversation,
      name: "Conversation",
      component: () => import("../views/conversation/ConversationPage.vue"),
    },
    {
      path: neUiKitRouterPath.conversationSearch,
      name: "ConversationSearch",
      component: () =>
        import("../views/conversation/ConversationSearchPage.vue"),
    },
    {
      path: neUiKitRouterPath.contacts,
      name: "Contacts",
      component: () => import("../views/contacts/ContactsPage.vue"),
    },
    {
      path: neUiKitRouterPath.blacklist,
      name: "Blacklist",
      component: () => import("../views/contacts/BlackListPage.vue"),
    },
    {
      path: neUiKitRouterPath.validlist,
      name: "ValidList",
      component: () => import("../views/contacts/ValidListPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamlist,
      name: "TeamList",
      component: () => import("../views/contacts/TeamListPage.vue"),
    },
    {
      path: neUiKitRouterPath.my,
      name: "My",
      component: () => import("../views/user/MyPage.vue"),
    },
    {
      path: neUiKitRouterPath.userSetting,
      name: "UserSetting",
      component: () => import("../views/user/SettingPage.vue"),
    },
    {
      path: neUiKitRouterPath.myDetail,
      name: "MyDetail",
      component: () => import("../views/user/MyDetailPage.vue"),
    },
    {
      path: neUiKitRouterPath.myDetailEdit,
      name: "MyDetailEdit",
      component: () => import("../views/user/MyDetailEditPage.vue"),
    },
    {
      path: neUiKitRouterPath.aboutNetease,
      name: "AboutNetease",
      component: () => import("../views/user/AboutNeteasePage.vue"),
    },
    {
      path: neUiKitRouterPath.friendCard,
      name: "FriendCard",
      component: () => import("../views/friend/FriendCardPage.vue"),
    },
    {
      path: neUiKitRouterPath.addFriend,
      name: "AddFriend",
      component: () => import("../views/friend/AddFriendPage.vue"),
    },
    {
      path: neUiKitRouterPath.friendInfoEdit,
      name: "FriendInfoEdit",
      component: () => import("../views/friend/FriendInfoEditPage.vue"),
    },
    {
      path: "/team/create",
      name: "TeamCreate",
      component: () => import("../views/team/TeamCreatePage.vue"),
    },
    {
      path: neUiKitRouterPath.teamSetting,
      name: "TeamSetting",
      component: () => import("../views/team/TeamSetPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamMember,
      name: "TeamMMember",
      component: () => import("../views/team/TeamMemberPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamInfoEdit,
      name: "TeamInfoEdit",
      component: () => import("../views/team/TeamInfoEditPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamAvatarEdit,
      name: "TeamAvatarEdit",
      component: () => import("../views/team/TeamAvatarEditPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamIntroduceEdit,
      name: "TeamNameEdit",
      component: () => import("../views/team/TeamIntroduceEditPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamAdd,
      name: "TeamAdd",
      component: () => import("../views/team/TeamAddPage.vue"),
    },
    {
      path: neUiKitRouterPath.teamNick,
      name: "TeamNick",
      component: () => import("../views/team/TeamNickPage.vue"),
    },
    {
      path: neUiKitRouterPath.login,
      name: "Login",
      component: () => import("../views/login/LoginPage.vue"),
    },
  ],
});

export default router;
```

### 第五步：运行项目

通过 `npm run start` 或您项目的自定义运行命令，在合适的时机展示聊天页面。

---

## 自定义开发

### API 参考

IM H5 Demo 基于 `UIKitStore` 开发，相关 API 详情请参考 **UIKitStore**。
