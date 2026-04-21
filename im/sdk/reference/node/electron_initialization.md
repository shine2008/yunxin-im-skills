本文介绍如何初始化适配 Node.js/Electron 的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

初始化 NIM Electron SDK 前，请确保您已经完成了以下操作：

- [集成 SDK](https://doc.yunxin.163.com/messaging2/guide/DQ3OTM0MTM?platform=client)。
- 在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上 [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)，并获取 App Key 和 App Secret。
- [注册网易云信 IM 账号](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#第二步注册-im-账号)，获取 IM 账号和 token。

## 注意说明

自 10.9.40 版本起，IM Node.js/Electron SDK **不再自动创建全局的 v2、chatroom、qchat、chatroom 变量**，开发者升级后需主动创建 v2 对象，例如：

```
// 引入 node-nim
const NIM = require('node-nim')

// 创建对象
const v2 = new NIM.V2NIMClient()

// 调用方法
v2.init()
v2.getLoginService()
...
```

## 第一步：配置初始化可选项

:::note note
您可按需对初始化的可选项进行配置。本步骤为可选步骤，如果不配置，将使用默认配置。
:::

初始化可选项配置主要分为：

- 基础配置 [`V2NIMBasicOption`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/interfaces/v2_def_v2_nim_struct_def.V2NIMBasicOption.html)：设置日志等级、自定义登录端类型等信息。
- 连接相关配置 [`V2NIMLinkOption`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/interfaces/v2_def_v2_nim_struct_def.V2NIMLinkOption.html)：设置 HTTPS 协议、加密算法等能力。
- 数据库配置 [`V2NIMDatabaseOption`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/interfaces/v2_def_v2_nim_struct_def.V2NIMDatabaseOption.html)：设置数据备份、机密、恢复等能力。
- 融合存储配置 [`V2NIMFCSOption`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/interfaces/v2_def_v2_nim_struct_def.V2NIMFCSOption.html)：开启和设置融合存储认证类型。
- 私有化配置 [`V2NIMPrivateServerOption`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/interfaces/v2_def_v2_nim_struct_def.V2NIMPrivateServerOption.html)：设置私有化 LBS 和 Link 地址等。

## 第二步：调用初始化接口

调用 [`init`](https://doc.yunxin.163.com/messaging2/references/electron/typedoc/Latest/zh/classes/v2_v2_nim_client.V2NIMClient.html#init) 方法初始化 SDK，推荐在应用程序启动时初始化。初始化成功后，即可使用 V10 所有的 API。

### 参数说明

参数 | 类型 | 是否必选 | 说明
--- | ---- | ---- | ---- |
`option` | `V2NIMInitOption` | 是 | V10 初始化配置参数。

`V2NIMInitOption` 参数说明：

参数 | 类型 | 是否必选 | 说明
---- | ---- | ---- | ---- |
`appkey` | string | 是 | 网易云信应用的 AppKey。获取方式请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。 |
`appDataPath` | string | 否 | 应用的数据目录，为空则使用默认目录。<br>默认数据：
`basicOption` | `V2NIMBasicOption` | 否 | 基础配置。<note type=note>若需要开启云端会话功能，可以配置 `V2NIMBasicOption.enableCloudConversation` 为 true。默认为 false，不启用云端会话模式（默认使用本地会话模式）。只有设置为 true 后，才能正常使用 `V2NIMConversationService` 服务。
`linkOption` | `V2NIMLinkOption` | 否 | 连接相关配置。
`databaseOption` | `V2NIMDatabaseOption` | 否 | 数据库配置。
`fcsOption` | `V2NIMFCSOption` | 否 | 融合存储配置。
`privateServerOption` | `V2NIMPrivateServerOption` | 否 | 私有化配置。

### 示例代码

```js
const { v2 } = require('node-nim')
// or
import { v2 } from 'node-nim'

// 初始化 SDK 并校验返回值
const result = v2.init({
    appkey: 'Your appkey here',
    appDataPath: 'Your custom data cache path',
    basicOption: {
        // basic options
    },
    linkOption: {
        // link options
    },
    databaseOption: {
        // database options
    },
    fcsOption: {
        // FCS options
    },
    privateServerOption: {
        // private server options
    }
})
if (result) {
    console.error('Failed to initialize NIM SDK, result:', result)
}

// .....

// 反初始化 SDK
v2.uninit()
```

## 下一步

完成初始化后，您可以尝试 [登录 IM](https://doc.yunxin.163.com/messaging/guide/Dk1MTY4MzA?platform=client)。