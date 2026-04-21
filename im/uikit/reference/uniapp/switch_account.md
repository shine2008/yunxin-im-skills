# 切换账号登录

> 更新时间：2025/12/22 15:02:02

集成 IM UIKit 后，用户业务侧的账号登录需要结合网易云信 IM 的登录实现。本文介绍 uni-app 项目如何结合两种账号登录方式。

---

## 实现方式

以账号登录场景为例，用户在登录页登录账号后，您需要调用 `app.vue` 根组件文件中注册的账号登录方法，将业务服务器返回的 `account` 和 `token` 传入 `app.vue` 里的初始化登录 IM 的方法。待 IM 初始化、连接完成后，再重定向到对应的用户界面。

具体实现请参考 GitHub 中 `app.vue` 的 `initNim` 方法。

---

## 实现示例

### 第一步：在 `app.vue` 中处理登录逻辑

在 `onLaunch` 方法里判断用户是否已经登录账号：

- 如果用户**未登录**，重定向到登录页面。
- 如果用户**已登录**，则初始化 IM SDK 及 `RootStore`。

```typescript
<script lang="ts">
import RootStore from '@xkit-yx/im-store-v2'
import V2NIM from 'nim-web-sdk-ng/dist/v2/NIM_UNIAPP_SDK'
import {
  customRedirectTo,
  customReLaunch,
  customSwitchTab,
} from './utils/customNavigate'
import { STORAGE_KEY } from './utils/constants'
import { isWxApp } from './utils'

export default {
  onLaunch() {
    // 判断您的业务是否已登录
    const imOptions = uni.getStorageSync(STORAGE_KEY)
    if (imOptions) {
      this.initNim(imOptions)
    } else {
      // 需要登录，重定向到登录页
      customRedirectTo({
        url: '/pages/Login/index',
      })
    }
  },
  methods: {
    initNim(opts: { account: string; token: string }) {
      // 保存登录信息
      uni.setStorage({
        key: STORAGE_KEY,
        data: opts,
        success: function () {
          console.log('保存登录信息 success')
        },
      })

      // 初始化 IM SDK
      const nim = (uni.$UIKitNIM = V2NIM.getInstance(
        {
          appkey: '',
          needReconnect: true,
          debugLevel: 'debug',
          apiVersion: 'v2',
        },
        {
          V2NIMLoginServiceConfig: {
            lbsUrls: isWxApp
              ? ['https://lbs.netease.im/lbs/wxwebconf.jsp']
              : ['https://lbs.netease.im/lbs/webconf.jsp'],
            linkUrl: isWxApp ? 'wlnimsc0.netease.im' : 'weblink.netease.im',
            // 使用固定设备 ID
            isFixedDeviceId: true,
          },
        }
      ))

      // 初始化 RootStore
      const store = (uni.$UIKitStore = new RootStore(
        // @ts-ignore
        nim,
        {
          // ...
        },
        'UniApp'
      ))

      nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
        // 登录完成后跳转到会话页，可根据实际需求跳转到指定页面
        customSwitchTab({
          url: '/pages/Conversation/index',
        })
      })
    },
    logout() {
      uni.removeStorageSync(STORAGE_KEY)
      uni.$UIKitNIM.V2NIMLoginService.logout().then(() => {
        uni.$UIKitStore.destroy()
        customReLaunch({
          url: '/pages/Login/index',
        })
      })
    },
  },
}
</script>
```

### 第二步：在登录页调用 IM 登录方法

完成业务服务器登录后，调用 `app.vue` 中注册的 `initNim` 方法，并传入业务服务器返回的账号 ID（`imAccid`）和密钥（`imToken`）。

```typescript
async function submitLoginForm() {
  try {
    // loginRegisterByCode 需结合您的业务需求自行实现
    const res = await loginRegisterByCode(loginForm);
    const app = getApp();
    // 调用 app.vue 中注册的 IM 登录方法
    app.initNim({ account: res.imAccid, token: res.imToken });
  } catch (error: any) {
    uni.showToast({
      title: msg,
      icon: "none",
    });
  }
}
```

### 第三步：退出登录

调用 `app.vue` 中注册的 `logout` 方法完成 IM 登出。

```typescript
const logout = () => {
  const app = getApp();
  // 调用 app.vue 中注册的 IM 登出方法
  app.logout();
};
```
