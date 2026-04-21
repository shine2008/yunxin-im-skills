# 集成 Web IM UIKit Vue 3 版本

网易云信 IM UIKit 是基于 网易云信 IM SDK（简称 NIM SDK） 开发的一款 即时通讯 UI 组件库，功能涵盖了聊天、会话、群管理。

本文介绍如何集成基于 Vue 3 框架开发的 Web IM UIKit。

## 前提条件

在开始集成 IM UIKit 前，请确保您已完成以下操作：

- 已在 网易云信控制台 上 创建应用，获取 App Key。
- 已 注册 IM 账号，获取 account_id 和 token。
- 调整本地开发环境，并满足如下要求：

  | 配置项              | 要求                             |
  | ------------------- | -------------------------------- |
  | 集成开发环境        | vuecli/vite                      |
  | Vue.js 版本         | Vue 3                            |
  | 开发语言            | TypeScript                       |
  | Node.js 版本        | v16 及以上                       |
  | JavaScript 包管理器 | NPM（版本请与 Node.js 版本匹配） |

## 实现步骤

### 第一步：创建项目

您可以使用 vuecli 或者 vite 创建项目。

如果您已创建项目，请忽略本步骤。

### 第二步：下载组件

1. 手动下载 [NEUIKit.zip](https://yx-web-nosdn.netease.im/common/6d8100101156ad9c0728efaca1b540d6/NEUIKit.zip) IM UIKit 组件。

2. 在下载的资源包中找到 **NEUIKit** 文件夹，将该文件夹复制到您项目的 **components** 目录下，例如：

   ![源码集成下载目录.png](https://yx-web-nosdn.netease.im/common/717ffa68a294a23c0dbbec62eaae29f6/源码集成下载目录.png)

3. 执行以下命令安装 NEUIKit 需要的依赖。

   ```bash
   npm i nim-web-sdk-ng@10.9.30 @xkit-yx/im-store-v2@0.8.8 @xkit-yx/utils@0.7.2 vue-virtual-scroller@2.0.0-beta.8  dayjs@1.11.7 mobx@6.6.1 mitt@3.0.1 --save
   ```

   | 依赖                    | 说明                                                                                                                                                                                                                                                                                                             |
   | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | `nim-web-sdk-ng`        | 网易云信 NIM SDK，提供了一整套即时通讯基础能力。                                                                                                                                                                                     |
   | `@xkit-yx/im-store-v2`  | store 用于 **全局状态管理**，基于 MobX 和 NIM SDK 封装的全局上下文对象，它提供了数据和 UI 之间的双向绑定能力。<br>包含多个子模块，每个子模块负责管理不同的数据领域，如会话、消息、联系人、群组等。通过 store，您可以方便地获取数据、响应数据变化，并更新 UI。<!--更多 store 相关信息，请参见 [全局上下文]()。--> |
   | `@xkit-yx/utils`        | 项目中的工具库，提供 getFileType、parseFileSize 等函数。                                                                                                                                                                                                                                                         |
   | `@vue-virtual-scroller` | 虚拟列表库。                                                                                                                                                                                                                                                                                                     |

下载组件成功后，可根据您的业务需求对源码进行自定义修改。

### 第三步：集成并初始化组件

1. 在您项目的 **App.vue** 文件中引入 NEUIKit 组件，参考以下 **initIMUiKit** 函数初始化 nim 和 store。

   ```vue
   <template>
   <div v-if="showUiKit" class="app-container">
       <router-view></router-view>
   </div>
   </template>

   <script lang="ts">
   import { V2NIMConst } from "nim-web-sdk-ng/dist/esm/nim";
   import RootStore from "@xkit-yx/im-store-v2";
   import V2NIM from "nim-web-sdk-ng/dist/v2/NIM_BROWSER_SDK";
   import type { V2NIMMessage } from "nim-web-sdk-ng/dist/esm/nim/src/V2NIMMessageService";
   import { getCurrentInstance } from "vue";

   export default {
   name: "App",
   methods: {
       initIMUiKit(opts: { appkey: string; account: string; token: string }) {
       const init = () => {
           //初始化nim
           const nim = V2NIM.getInstance(
           {
               appkey: opts.appkey,
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
           // 初始化store
           const store = new RootStore(
           // @ts-ignore
           //参数一：NIMSDK，必填
           nim,
           // 本地化配置，必填，详细参数说明请参考全局上下文的底部常见问题
           {
               // 添加好友是否需要验证
               addFriendNeedVerify: false,
               // 是否需要显示 p2p 消息、p2p会话列表消息已读未读，默认 false
               p2pMsgReceiptVisible: true,
               // 是否需要显示群组消息已读未读，默认 false
               teamMsgReceiptVisible: true,
               // 群组被邀请模式，默认需要验证
               teamAgreeMode:
               V2NIMConst.V2NIMTeamAgreeMode.V2NIM_TEAM_AGREE_MODE_NO_AUTH,
               // 发送消息前回调, 可对消息体进行修改，添加自定义参数
               sendMsgBefore: async (options: {
               msg: V2NIMMessage;
               conversationId: string;
               serverExtension?: Record<string, unknown>;
               }) => {
               return { ...options };
               },
           },
           //参数三：平台，可选
           "Web"
           );
           return {
           nim,
           store,
           };
       };

       const { nim, store } = init();
       const app = getCurrentInstance();

       // 将nim和store 挂载，供组件内部访问
       if (app) {
           app.appContext.app.config.globalProperties.$NIM = nim;
           app.appContext.app.config.globalProperties.$UIKitStore = store;
       }

       nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
           // IM 登录成功后跳转到会话页面
           this.$router.push("/conversation");
           this.showUiKit = true;
       });
       },
   },
   mounted() {
       this.initIMUiKit({
           appkey: 'xxx',
           account: 'xxx',
           token: 'xxxx'
       });
   },
   };
   </script>
   ```

2. 在 **main.ts** 中注册虚拟列表。

   ```ts
   import VueVirtualScroller from 'vue-virtual-scroller'

   //...
   app.use(VueVirtualScroller)
   //...
   ```

### 第四步：运行项目

根据您的业务需求，在合适的时机，通过 `npm run start`，或者您项目的自定义运行命令启动聊天页面。

## 下一步

为保障通信安全，如果您在调试环境中的使用的是网易云信控制台生成的测试用 IM 账号 和 `token`，请确保在后续的正式生产环境中，将其替换为通过 IM 服务端 API 生成的正式 IM 账号（`account_id`）和 `token`。
