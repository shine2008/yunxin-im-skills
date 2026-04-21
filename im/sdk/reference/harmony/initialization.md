本文介绍如何初始化适配鸿蒙系统的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

初始化 NIM Harmony SDK 前，请确保您已经完成了以下操作：

- [集成 SDK](https://doc.yunxin.163.com/messaging2/guide/DMzMTMxMjM?platform=client)。
- 在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上 [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)，并获取 App Key 和 App Secret。
- [注册网易云信 IM 账号](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#%E7%AC%AC%E4%BA%8C%E6%AD%A5%E6%B3%A8%E5%86%8C-im-%E8%B4%A6%E5%8F%B7)，获取 IM 账号和 token。

## 第一步：注册服务

调用 `NIMSdk.registerCustomServices` 方法注册使用的服务。

### **参数说明**

```TypeScript
static registerCustomServices(serviceType: V2NIMProvidedServiceType, creator: V2ServiceCreator)
```

参数 | 类型 | 说明 |
---- | ---- | ---- |
`serviceType` | `V2NIMProvidedServiceType` | 服务类型 |
`creator` | `V2ServiceCreator` | 服务构造器 |

### **示例代码**

```TypeScript
// 根据业务，选择所需服务进行注册
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_TEAM, (core, serviceName, serviceConfig) => new V2NIMTeamServiceImpl(core, serviceName, serviceConfig))
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_CLIENT_ANTISPAM_UTIL, (core, serviceName, serviceConfig) => new V2NIMClientAntispamUtil(core, serviceName, serviceConfig));

/** 选择“云端会话”或“本地会话”，两者互斥。开通云端会话，则需注册 CONVERSATION 和 CONVERSATION_GROUP；若开通本地会话，则需注册 LOCAL_CONVERSATION **/
// A. 选择云端会话（若开通了会话分组则带上 CONVERSATION_GROUP）
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_CONVERSATION, (core, serviceName, serviceConfig) => new V2NIMConversationServiceImpl(core, serviceName, serviceConfig));
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_CONVERSATION_GROUP, (core, serviceName, serviceConfig) => new V2NIMConversationGroupServiceImpl(core, serviceName, serviceConfig));
// B. 选择本地会话
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_LOCAL_CONVERSATION, (core, serviceName, serviceConfig) => new V2NIMLocalConversationServiceImpl(core, serviceName, serviceConfig))

NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_MESSAGE, (core, serviceName, serviceConfig) => new V2NIMMessageServiceImpl(core, serviceName, serviceConfig));
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_USER, (core, serviceName, serviceConfig) => new V2NIMUserServiceImpl(core, serviceName, serviceConfig));
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_FRIEND, (core, serviceName, serviceConfig) => new V2NIMFriendServiceImpl(core, serviceName, serviceConfig));
NIMSdk.registerCustomServices(V2NIMProvidedServiceType.V2NIM_PROVIDED_SERVICE_SIGNALLING, (core, serviceName, serviceConfig) => new V2NIMSignallingServiceImpl(core, serviceName, serviceConfig))
```

## 第二步：初始化

调用 `newInstance` 方法创建实例并实现初始化。

### **参数说明**

```TypeScript
static newInstance(context: common.Context, initializeOptions: NIMInitializeOptions, serviceOptions: NIMServiceOptions = {}): NIMInterface
```

参数 | 类型 | 说明 |
---- | ---- | ---- |
`context` | common.Context | 应用上下文 |
`initializeOptions` | `NIMInitializeOptions` | SDK 的配置信息 |
`serviceOptions` | `NIMServiceOptions` | 业务服务的配置信息 |

`NIMInitializeOptions` 的配置参数

参数 | 类型 | 是否必选 | 说明 |
---- | ---- | ---- | ---- |
`appkey` | string | 是 | 应用的 App Key，在 [网易云信控制台](https://app.yunxin.163.com/global/home) [创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)后获取。 |
`xhrConnectTimeout` | number | 否，默认为 30000 ms | 建立连接时的 xhr 请求的超时时间 |
`socketConnectTimeout` | number | 否，默认为 30000 ms | 建立 websocket 连接的超时时间 |

`NIMServiceOptions` 的配置参数

参数 | 类型 | 是否必选 | 说明 |
---- | ---- | ---- | ---- |
`loginServiceConfig` | `NIMLoginServiceConfig` | 否 | LoginService 的配置参数 |

`NIMLoginServiceConfig` 的配置参数

参数 | 类型 | 是否必选 | 说明 |
---- | ---- | ---- | ---- |
`lbsUrls` | string[] | 否 | lbs 地址。SDK 连接时会向 lbs 地址请求得到 socket 连接地址 |
`linkUrl` | string | 否 | socket 备用地址，当 lbs 请求失败时，尝试直接连接 socket 备用地址 |
`customClientType` | number | 否 | 自定义客户端类型 |
`customTag` | string | 否 | 自定义客户端标签 |

### **示例代码**

```TypeScript
let initializeOptions: NIMInitializeOptions = {
appkey: "your appkey ",
logLevel: LogLevel.Debug,
isOpenConsoleLog: true // 打开后可以将 nimsdk 的日志输出到控制台
};

let serviceOptions: NIMServiceOptions = {
loginServiceConfig: {
    lbsUrls: ['https://lbs.netease.im/lbs/webconf'],
    linkUrl: 'weblink-harmony-tmp.netease.im:443'
}
}

const nim = NIMSdk.newInstance(context, initializeOptions, serviceOptions)
```

## 下一步

完成初始化后，您可以尝试 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。