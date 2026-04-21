# uni-app UIKit 集成指南

## 第一步：创建 uni-app 项目

在 HBuilderX IDE 环境中，创建 uni-app 项目。详细步骤请参考《uni-app 官网文档》通过 HBuilderX 可视化界面。

> 如果您已创建项目，请忽略本步骤。

---

## 第二步：下载 Demo 源码

运行以下命令将 NEUIKit GitHub 源码项目 `nim-uikit-uniapp.git` 下载到您的 uni-app 项目中：

```bash
# 找一个目录，clone 组件项目
git clone https://github.com/netease-kit/nim-uikit-uniapp.git

# 在您的项目根目录下执行以下命令，拷贝组件
mkdir -p ./pages/NEUIKit

# macOS
mv ${组件项目路径}/NEUIKit/* ./pages/NEUIKit

# Windows
move ${组件项目路径}/NEUIKit/* .\pages\NEUIKit
```

下载成功后，可根据您的业务需求对源码进行修改，具体的目录结构请参考**工程结构**。

---

## 第三步：添加依赖和图片引入

### 安装依赖

在您的项目根目录下执行：

```bash
npm init -y
npm i nim-web-sdk-ng@10.9.40 @xkit-yx/im-store-v2@0.8.5 @xkit-yx/utils@0.7.2 dayjs@1.11.7 mobx@6.6.1 pinyin@3.1.0 --save
```

| 依赖                   | 说明                                                                                                                                                                                      |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nim-web-sdk-ng`       | 网易云信 NIM SDK，提供了一整套即时通讯基础能力。                                                                                                                                          |
| `@xkit-yx/im-store-v2` | store 用于全局状态管理，基于 MobX 和 NIM SDK 封装的全局上下文对象，提供数据和 UI 之间的双向绑定能力。包含多个子模块，分别负责管理会话、消息、通讯录、群组等数据领域。详见**全局上下文**。 |
| `@xkit-yx/utils`       | 项目中的工具库，提供 `getFileType`、`parseFileSize` 等函数。                                                                                                                              |
| `pinyin`               | 主要用于好友列表的拼音排序功能。                                                                                                                                                          |

### 复制静态图片资源

在您的项目根目录下执行以下命令，将组件所需图片复制到 `static` 目录下。若命令执行不成功，请按照路径手动复制。

```bash
mkdir -p static

# macOS
cp -r pages/NEUIKit/static static/YX_IMG

# Windows
xcopy /E pages\NEUIKit\static static\YX_IMG
```

> **说明**：当前静态资源放置在网易对象存储（NOS）中，可通过链接方式访问。由于访问频率有限制，建议将静态资源部署到您的**业务服务器**上，然后修改 `/pages/NEUIKit/components/Icon.vue` 组件中的链接即可。

---

## 第四步：集成并初始化组件

### 方式一：正常集成

在您的项目 `App.vue` 文件中引入 NEUIKit 组件，参考以下代码初始化 `nim` 和 `store`：

```typescript
<script lang="ts">
import RootStore from '@xkit-yx/im-store-v2'
import V2NIM, { V2NIMConst } from 'nim-web-sdk-ng/dist/v2/NIM_UNIAPP_SDK'

export default {
  onLaunch() {
    const imOptions = {
      appkey: '', // 请填写您的 appkey
      account: '', // 请填写您的 account
      token: '',  // 请填写您的 token
    }
    if (imOptions) {
      this.initNim(imOptions)
    } else {
      // 需要登录，跳转登录页
    }
  },
  methods: {
    initNim(opts) {
      const isWxApp = uni.getSystemInfoSync().uniPlatform == 'mp-weixin'

      // 初始化 NIM SDK
      const nim = (uni.$UIKitNIM = V2NIM.getInstance(
        {
          appkey: opts.appkey,
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

      // 初始化 store
      const store = (uni.$UIKitStore = new RootStore(
        nim,
        {
          addFriendNeedVerify: false,
          // 是否需要显示 p2p 消息已读未读，默认 false
          p2pMsgReceiptVisible: true,
          // 是否需要显示群组消息已读未读，默认 false
          teamMsgReceiptVisible: true,
          teamAgreeMode: V2NIMConst.V2NIMTeamAgreeMode.V2NIM_TEAM_AGREE_MODE_NO_AUTH,
          /**
           * 发送消息前的钩子函数（异步），可配置推送参数、拦截消息发送等。
           * 返回 false 则不发送消息。
           */
          sendMsgBefore: async (options) => {
            const pushConfig = {}
            return { ...options, pushConfig }
          },
        },
        'UniApp'
      ))

      nim.V2NIMLoginService.login(opts.account, opts.token).then(() => {
        // 跳转至您需要展示的页面
      })
    },
    logout() {},
  },
}
</script>

<style>
uni-page-body {
  height: 100%;
}
uni-page-body > uni-view {
  height: 100%;
}
</style>
```

### 方式二：ESM 集成

> 自 **V10.3.0** 版本起，IM uniapp UIKit 支持 ESM 集成方式。

NIM SDK 默认导出 UMD 格式产物，可在多种环境中使用。当应用对包体积有更严格的要求（如小程序），可使用 ESM（ECMAScript Module）格式的 SDK。ESM 是 JavaScript 原生模块系统，配合 webpack 的 tree-shaking 可进一步缩减 SDK 体积。

具体请参考 **ESM 引入**。

1. 下载 `sdk-release_im_uniApp.zip`，该示例演示如何打包生成 ESM 版本的 NIM SDK。
2. 完成 ESM 集成后，修改 `NEUIKit/utils/nim.ts` 文件：

```typescript
// esmNim.js 为您使用示例项目打包出的缩小体积后的 NIM JS 产物
import { V2NIMConst } from "../esmNim.js";
export { V2NIMConst };
```

---

## 第五步：配置路由

### 修改路由前缀

在 `pages/NEUIKit/utils/customNavigate.ts` 中，修改 `preUrl`：

```javascript
const preUrl = "/pages/NEUIKit";
```

### 更新 pages.json

在您项目的 `pages.json` 文件中更新 pages 路由。以下是 IM UIKit 的所有页面路由，可根据项目需要选择适当的路由。具体路由介绍请参考**路由页面介绍**。

```json
{
  "pages": [
    {
      "path": "pages/NEUIKit/pages/Conversation/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/index/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Conversation/conversation-search/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Login/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/index",
      "style": {
        "navigationBarBackgroundColor": "#F6F8FA",
        "navigationBarTextStyle": "black",
        "navigationStyle": "custom",
        "enablePullDownRefresh": false,
        "app-plus": { "softinputNavBar": "none", "bounce": "none" }
      }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/video-play",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/message/p2p-set",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/message/pin-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/forward",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Chat/message-read-info",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-info-edit",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-name-edit",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-intro-edit",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-avatar-edit",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/nick-in-team",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-manage",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/transform-team",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/team-manager-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-set/add-team-manager",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-member/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-create/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Team/team-add/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Contact/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Contact/contact-list/group-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Contact/contact-list/valid-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/Contact/contact-list/black-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/friend/add-friend",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/friend/friend-edit",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/friend/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my/about",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my/setting",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my/collection-card",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my/collection-list",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/my-detail/index",
      "style": { "navigationStyle": "custom" }
    },
    {
      "path": "pages/NEUIKit/pages/User/detail-item/index",
      "style": { "navigationStyle": "custom" }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black"
  },
  "tabBar": {
    "backgroundColor": "#F6F8FA",
    "color": "#999999",
    "selectedColor": "#337EFF",
    "height": "60px",
    "list": [
      {
        "text": "消息",
        "iconPath": "static/YX_IMG/conversation.png",
        "selectedIconPath": "static/YX_IMG/conversation-selected.png",
        "pagePath": "pages/NEUIKit/pages/Conversation/index"
      },
      {
        "text": "通讯录",
        "iconPath": "static/YX_IMG/contact.png",
        "selectedIconPath": "static/YX_IMG/contact-selected.png",
        "pagePath": "pages/NEUIKit/pages/Contact/index"
      },
      {
        "text": "我的",
        "iconPath": "static/YX_IMG/me.png",
        "selectedIconPath": "static/YX_IMG/me-selected.png",
        "pagePath": "pages/NEUIKit/pages/User/my/index"
      }
    ]
  }
}
```

---

## 第六步：运行 Demo

在 uni-app IDE 中运行 Demo 即可。

---

## 自定义开发

### API 参考

IM uni-app Demo 基于 `UIKitStore` 开发，相关 API 详情请参考 **UIKitStore**。

**示例**：获取 IM uni-app UIKit 的会话未读数：

```javascript
uni.$UIKitStore.conversationStore.totalUnreadCount;
```

### 使用原生 SDK 中的方法

如果您需要实现目前 uni-app UIKit 还未提供、但原生 IM uni-app SDK 已支持的能力，可直接调用原生 SDK 的接口。原生 SDK API 请参考 **NIM Web SDK**。

**示例**：对消息历史进行全文检索（按时间分页搜索），调用原生 SDK 的 `searchCloudMessages` 方法：

```javascript
uni.$UIKitNIM.nim.searchCloudMessages({
  keyword: "您好",
});
```

> 以上示例中的 `uni.$UIKitNIM.nim` 为原生 NIM uni-app SDK 的实例。
