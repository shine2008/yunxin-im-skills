本文介绍如何初始化适配 Web 类型应用的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。初始化的模块主要包括 IM 消息、会话、用户资料、好友关系、用户关系、群组。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

初始化 NIM SDK 前，请确保您已经完成了以下操作：

- [集成 NIM SDK](https://doc.yunxin.163.com/messaging2/guide/DcyMjA1Njk?platform=client)。
- 在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上 [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)，并获取 App Key 和 App Secret。
- [注册网易云信 IM 账号](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#%E7%AC%AC%E4%BA%8C%E6%AD%A5%E6%B3%A8%E5%86%8C-im-%E8%B4%A6%E5%8F%B7)，获取 IM 账号和 Token。

## 实现初始化

调用 [`V2NIM.getInstance`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/classes/src.V2NIM.html#getInstance) 方法创建 NIM 实例，同时配置初始化参数。

- `V2NIM.getInstance` 方法为单例模式，对于同一个账号永远返回同一个实例，即只在第一次调用时初始化一个实例，后续调用该方法会直接返回初始化过的实例。
- 如需更新初始化配置，不建议继续调用 `V2NIM.getInstance` 方法，推荐调用 [`setOptions`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_NIMInterface.NIMInterface.html#setOptions) 方法更新原 SDK 实例的初始化配置。

    :::note note
    如果您需要使用 V10 NIM SDK API，请将 `NIMInitializeOptions.apiVersion` 参数取值设置为 `v2`。
    :::

### 参数说明

```TypeScript
getInstance(_options?: NIMInitializeOptions, _otherOptions?: NIMOtherOptions): NIMInterface
```

| 参数名称 | 类型 | 是否必填 | 默认值 | 说明 |
| ---- | ---- | ---- | ---- | ---- |
| `_options` | [`NIMInitializeOptions`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_NIMInterface.NIMInitializeOptions.html) | 否 | 见下文 | 初始化基础配置项。 |
| `_otherOptions` | [`NIMOtherOptions`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_NIMInterface.NIMOtherOptions.html) | 否 | 见下文 | 初始化其他配置项。 |

`NIMInitializeOptions` 全部参数请参考 [`NIMInitializeOptions`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_NIMInterface.NIMInitializeOptions.html)，**部分重要** 参数说明如下表所示：

| 名称 | 类型 | 是否必填 | 默认值 | 说明 |
| ---- | ---- | ---- | ---- | ---- |
| `appkey` | string | 是 | - | 应用的 App Key，在 [网易云信控制台](https://app.yunxin.163.com/global/home) [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)后获取。 |
| `apiVersion` | string | 否 | `v1` | 使用 API 版本。<br>如需使用 V10 SDK 接口，请设置为 `v2`。 |
| `debugLevel` | string | 否 | `off` | 日志等级。<li>`off`：不输出任何日志<li>`debug`：输出所有日志 `<li>log`：输出 log、warn、error 级别的日志<li>`warn`：输出 warn、error 级别的日志<li>`error`：输出 error 级别的日志。 |
| `enableV2CloudConversation` | boolean | 否 | false | 是否开启云端会话功能，默认为 false，不启用云端会话模式（默认使用本地会话模式）。只有设置为 true 后，才能正常使用 `V2NIMConversationService` 服务。

`NIMOtherOptions` 全部参数请参考 [`NIMOtherOptions`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_NIMInterface.NIMOtherOptions.html)，**部分重要** 参数说明如下表所示：

| 名称 | 类型 | 是否必填 | 默认值 | 说明 |
| ---- | ---- | ---- | ---- | ---- |
| `V2NIMLoginServiceConfig` | [`NIMEModuleParamV2Login`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/modules/src_V2NIMLoginService.html#NIMEModuleParamV2Login) | 否 | - | 使用 V10 SDK 登录的相关配置项。 |
| `cloudStorageConfig` | [`NIMEModuleParamCloudStorageConfig`](https://doc.yunxin.163.com/messaging2/references/web/typedoc/Latest/zh/v2/nim/interfaces/src_CloudStorageServiceInterface.NIMEModuleParamCloudStorageConfig.html) | 否 | - | 云存储配置，如需使用融合存储需要提前引入 AWS SDK，并设置 `s3` 参数。 |

`NIMEModuleParamV2Login` 参数说明：

| 名称 | 类型 | 是否必填 | 默认值 | 说明 |
| ---- | ---- | ---- | ---- | ---- |
| `lbsUrls` | Array<string> | 否 | 网易云信公网提供的链接 | LBS 地址。SDK 连接时会向 LBS 地址请求得到 socket 连接地址。 |
| `linkUrl` | string | 否 | null | socket 备用地址，当 LBS 请求失败时，尝试直接连接 socket 备用地址。 |
| `isFixedDeviceId` | boolean | 否 | false | `deviceId` 是否需要固定。
| `customClientType` | number | 否 | 0 | 自定义客户端类型。 |
| `customTag` | string | 否 | null | 自定义客户端标签。 |

### 示例代码

```TypeScript
const nim = NIM.getInstance(
  /**
   * param1: NIMInitializeOptions
   */
  {
    appkey: "YOUR_APPKEY",
    debugLevel: "debug",
    // 启用 v2 即 v10 API 支持
    apiVersion: "v2",
    // 启用云端会话功能
    //enableV2CloudConversation: true
  }
)
```

## 下一步

完成初始化后，您可以尝试 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。