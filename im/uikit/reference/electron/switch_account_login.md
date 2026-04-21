# 切换账号登录

> 更新时间：2025/12/22 16:50:05

集成 IM UIKit 后，用户业务侧的账号登录需要结合网易云信 IM 的登录实现。本文介绍如何切换账号登录。

---

## 概述

以账号登录场景为例，用户在登录页登录账号后，您需要将业务服务器返回的 `account` 和 `token` 传入 NIM SDK 的登录方法（`login`）。待 IM 初始化、连接完成后，再重定向到对应的用户界面。具体实现请参考 GitHub 中 `app.vue` 的 `initIMUIKit` 方法。

---

## 登录

在打开页面时，判断用户是否已完成业务侧的登录：

- **已登录**：直接从缓存中获取 `appkey`、`account` 以及 `token`，进行 IM UIKit 的初始化和登录。
- **未登录**：重定向到登录页面，完成业务侧登录后，将业务服务器返回的 `appkey`、`account` 和 `token` 用于 IM UIKit 的初始化和登录。

### 已登录业务侧

```javascript
import { initIMUIKit } from "./components/NEUIKit/utils/init";

// 初始化 IM UIKit
const { nim } = initIMUIKit(APP_KEY);

nim?.loginService
  ?.login(opts.account, opts.token, {})
  .then(() => {
    // IM 登录成功后跳转到会话页面
    this.$router.push("/chat");
    this.showUiKit = true;
  })
  .catch((error) => {
    if (error.code === 102422) {
      // 账号被封禁
      showToast({
        message: "当前账号已被封禁",
        type: "info",
      });
      // 登录信息无效，清除并跳转到登录页
      localStorage.removeItem(STORAGE_KEY);
      this.$router.push("/login");
    }
  });
```

---

## 退出登录

调用 `store.destroy()` 以及 `nim.loginService.logout()` 方法完成退出。具体可参考 GitHub 中的 `logout` 方法：

```javascript
import { getContextState } from "../../../components/NEUIKit/utils/init";

const { store, nim } = getContextState();

const logout = () => {
  // 清除缓存信息，根据实际业务调整
  localStorage.removeItem(STORAGE_KEY);
  store?.destroy();
  nim?.loginService?.logout();
  router.push("/login");
};
```
