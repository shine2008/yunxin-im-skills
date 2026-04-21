# 切换账号登录

> 更新时间：2025/09/23 11:44:43

集成 IM UIKit 后，用户业务侧的账号登录需要结合网易云信 IM 的登录实现。本文介绍如何切换账号登录。

---

## 实现方式

以账号登录场景为例，用户在登录页登录账号后，您需要将业务服务器返回的 `account` 和 `token` 传入 NIM SDK 的登录方法（`login`）。待 IM 初始化、连接完成后，再重定向到对应的用户界面。具体实现请参考 GitHub 中 `app.vue` 的 `initIMUIKit` 方法。

---

## 实现流程

### 第一步：在 `App.vue` 中检查登录状态

打开页面时，判断用户是否已经登录账号：

- **未登录**：重定向到登录页面。
- **已登录**：初始化 IM SDK 及 RootStore，初始化完成后展示 IM UIKit。

```vue
<template>
  <div v-if="showUiKit" class="app-container">
    <router-view></router-view>
  </div>
</template>

<script lang="ts">
import { init } from "./components/NEUIKit/utils/init";
import { getCurrentInstance } from "vue";

export default {
  name: "App",
  data() {
    return {
      showUiKit: false,
    };
  },
  mounted() {
    this.checkLoginStatus();
  },
  methods: {
    initIMUiKit(opts: { account: string; token: string }) {
      const { nim, store } = init();
      const app = getCurrentInstance();

      if (app) {
        app.appContext.app.config.globalProperties.$NIM = nim;
        app.appContext.app.config.globalProperties.$UIKitStore = store;
      }

      nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
        this.$router.push("/chat");
        this.showUiKit = true;
      });
    },
    checkLoginStatus() {
      // 根据您的实际业务调整登录信息获取方式
      const loginInfo = sessionStorage.getItem(STORAGE_KEY);
      if (!loginInfo) {
        // 未登录，跳转到登录页
        this.$router.push("/login");
        this.showUiKit = true;
        return;
      }

      try {
        const { account, token } = JSON.parse(loginInfo);
        this.initIMUiKit({ account, token });
      } catch (error) {
        console.error("解析登录信息失败", error);
        // 登录信息无效，清除并跳转到登录页
        sessionStorage.removeItem(STORAGE_KEY);
        this.$router.push("/login");
      }
    },
  },
};
</script>
```

### 第二步：在登录页完成 IM 登录

完成业务服务器登录后，调用 `init` 方法初始化 NIM SDK 和 store，并将账号 ID（`account`）和密钥（`token`）传入登录方法：

```typescript
// 登录
async function submitLoginForm() {
  try {
    const res = await loginRegisterByCode(loginForm);

    // 存储登录信息到 sessionStorage
    sessionStorage.setItem(
      STORAGE_KEY,
      JSON.stringify({
        account: res.imAccid,
        token: res.imToken,
      }),
    );

    const { nim, store } = init();
    if (app) {
      app.appContext.app.config.globalProperties.$NIM = nim;
      app.appContext.app.config.globalProperties.$UIKitStore = store;
    }

    nim.V2NIMLoginService.login(res.imAccid, res.imToken).then(() => {
      // IM 登录成功后跳转到聊天页面
      router.push("/chat");
    });
  } catch (error: any) {
    let msg = error.errMsg || error.msg || error.message || i18n.smsCodeFailMsg;
    if (msg.startsWith("request:fail")) {
      msg = i18n.loginNetworkErrorMsg;
    }
    showToast({ message: msg, type: "info" });
  }
}
```

**`init` 方法**（封装在 `/NEUIKit/utils/init.ts` 中）：

```typescript
import { V2NIMConst } from "nim-web-sdk-ng/dist/esm/nim";
import RootStore from "@xkit-yx/im-store-v2";
import V2NIM from "nim-web-sdk-ng/dist/v2/NIM_BROWSER_SDK";
import type { V2NIMMessage } from "nim-web-sdk-ng/dist/esm/nim/src/V2NIMMessageService";

export const init = () => {
  const nim = V2NIM.getInstance(
    {
      appkey: "",
      needReconnect: true,
      debugLevel: "debug",
      apiVersion: "v2",
    },
    {
      V2NIMLoginServiceConfig: {
        lbsUrls: ["https://lbs.netease.im/lbs/webconf.jsp"],
        linkUrl: "weblink.netease.im",
      },
    },
  );

  const store = new RootStore(
    // @ts-ignore
    nim,
    {
      addFriendNeedVerify: false,
      p2pMsgReceiptVisible: true,
      teamMsgReceiptVisible: true,
      teamAgreeMode:
        V2NIMConst.V2NIMTeamAgreeMode.V2NIM_TEAM_AGREE_MODE_NO_AUTH,
      aiVisible: false,
      sendMsgBefore: async (options: {
        msg: V2NIMMessage;
        conversationId: string;
        serverExtension?: Record<string, unknown>;
      }) => {
        return { ...options };
      },
    },
    "H5",
  );

  return { nim, store };
};
```

### 第三步：退出登录

调用 `store.destroy()` 和 `nim.V2NIMLoginService.logout()` 完成退出。可参考 GitHub 中的 `logout` 方法：

```typescript
const { proxy } = getCurrentInstance()!;
const store = proxy?.$UIKitStore;
const nim = proxy?.$NIM;

const logout = () => {
  sessionStorage.removeItem(STORAGE_KEY);
  store?.destroy();
  nim.V2NIMLoginService.logout();
  router.push("/login");
};
```
