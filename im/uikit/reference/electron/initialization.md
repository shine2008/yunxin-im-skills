# 集成 UIKit（Vue 3）

> 更新时间：2026/03/26 14:05:11

网易云信 IM UIKit 是基于网易云信 IM SDK（简称 NIM SDK）开发的一款即时通讯 UI 组件库，功能涵盖了聊天、会话、群管理等。本文介绍如何集成基于 Vue 3 框架开发的 IM Electron UIKit。

---

## 前提条件

在开始集成 IM UIKit 前，请确保您已完成以下操作：

- 已在**网易云信控制台**上创建应用，获取 App Key。
- 已注册 IM 账号，获取 `account_id` 和 `token`。
- 调整本地开发环境，满足如下要求：

| 配置项              | 要求                             |
| ------------------- | -------------------------------- |
| 集成开发环境        | vuecli / vite                    |
| Vue.js 版本         | Vue 3                            |
| 开发语言            | TypeScript                       |
| Node.js 版本        | v16 及以上                       |
| JavaScript 包管理器 | NPM（版本请与 Node.js 版本匹配） |

---

## 实现步骤

### 第一步：创建项目

您可以使用官方脚手架创建项目，详细步骤请参考 **Electron 官网文档**。

> 如果您已创建项目，请忽略本步骤。

### 第二步：下载组件

1. 手动下载 `NEUIKit.zip` IM UIKit 组件。

2. 在下载的资源包中：
   - 找到 **NEUIKit** 文件夹，将其复制到您项目的 `components` 目录下。
   - 找到 **NEUIKitBridge** 文件夹，放置到 `electron` 目录下。

3. 执行以下命令安装 NEUIKit 所需依赖：

```bash
npm i node-nim@10.9.72 @xkit-yx/utils@0.7.2 vue-virtual-scroller@2.0.0-beta.8 image-size@2.0.2 mobx@6.6.1 mitt@3.0.1 --save
```

| 依赖                   | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `node-nim`             | 网易云信 NIM SDK，提供了一整套即时通讯基础能力。             |
| `@xkit-yx/utils`       | 项目中的工具库，提供 `getFileType`、`parseFileSize` 等函数。 |
| `vue-virtual-scroller` | 虚拟列表库。                                                 |

下载成功后，可根据您的业务需求对源码进行自定义修改。

### 第三步：集成并初始化组件

**1. 在 `App.vue` 中初始化 NIM 并登录**

从 `NEUIKit/utils/init` 引入 `initIMUIKit`，传入您的 AppKey 进行初始化，获取返回的 `nim` 对象。

> 若需进行初始化相关配置（如配置云端会话），可在 `NEUIKit/utils/init.ts` 文件的 `initIMUIKit` 中配置 `nim.init` 的 `basicOption` 配置项，具体文档请参考 **Electron 初始化**。

```vue
<template>
  <div v-if="showUiKit" class="app-container">
    <router-view></router-view>
  </div>
</template>

<script lang="ts">
import { initIMUIKit } from "./components/NEUIKit/utils/init";

export default {
  name: "App",
  data() {
    return {
      showUiKit: false,
    };
  },
  methods: {
    async init() {
      // 初始化 IM UIKit
      const { nim } = initIMUIKit(APP_KEY);
      nim?.loginService
        ?.login(account, token, {})
        .then(() => {
          // IM 登录成功后跳转到会话页面
          this.$router.push("/chat");
          this.showUiKit = true;
        })
        .catch((error) => {
          if (error.code === 102422) {
            // 登录信息无效，清除并跳转到登录页
            localStorage.removeItem(STORAGE_KEY);
            this.$router.push("/login");
          }
        });
    },
  },
  mounted() {
    this.init();
  },
};
</script>
```

**2. 在 `main.ts` 中注册虚拟列表**

```typescript
import VueVirtualScroller from "vue-virtual-scroller";

// ...
app.use(VueVirtualScroller);
// ...
```

**3. 在 `electron/main.ts` 中注册 IPC 处理器**

NEUIKitBridge 是一个可独立拷贝的 Electron 主进程功能模块集合，每个模块提供三类导出：

- `init*()`：初始化函数，在 `app.whenReady()` 后调用。
- `register*Handlers()`：IPC 处理器注册函数，在渲染进程加载前调用。
- 其他工具函数：如 `markAppQuitting()` 等。

可用模块：

| 模块           | 说明                                             |
| -------------- | ------------------------------------------------ |
| `tray`         | 系统托盘功能（图标、菜单、未读数显示）           |
| `window-close` | 窗口关闭行为管理（隐藏到托盘 vs 真正退出）       |
| `app`          | 应用控制（重启、DevTools）                       |
| `version`      | 版本信息获取                                     |
| `notification` | 桌面通知                                         |
| `fs`           | 文件系统操作（文件读写、目录管理、本地文件协议） |
| `navigation`   | 页面导航事件                                     |
| `i18n`         | 国际化支持                                       |

```typescript
import {
  // Tray 模块
  initTray,
  registerTrayHandlers,
  // Window Close 模块
  initWindowClose,
  markAppQuitting,
  registerWindowCloseHandlers,
  // App 模块
  initApp,
  registerAppHandlers,
  // Notification 模块
  initNotificationService,
  registerNotificationHandlers,
  // FS 模块
  registerFsHandlers,
  registerLocalFileProtocol,
  initLocalFileProtocol,
} from "./NEUIKitBridge";

app.whenReady().then(() => {
  registerNEUIKitIpcHandlers();
  // ...
});
```

更多示例请参考完整的 **Demo 源码**。

### 第四步：运行项目

通过 `npm run start` 或您项目的自定义运行命令，在合适的时机启动聊天页面。

---

## 下一步

为保障通信安全，如果您在调试环境中使用的是网易云信控制台生成的测试用 IM 账号和 `token`，请确保在后续的正式生产环境中，将其替换为通过 IM 服务端 API 生成的正式 IM 账号（`account_id`）和 `token`。
