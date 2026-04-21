本文介绍如何初始化适配 iOS 应用的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。初始化 SDK 时，可同时配置 APNs 离线推送服务、会话已读多端同步、群消息已读和融合存储等重要功能。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

初始化 NIM SDK 前，请确保您已 [集成 SDK](https://doc.yunxin.163.com/messaging2/guide/zk2MjQ5OTc?platform=client)。

## 第一步：引入头文件

在项目文件中引入头文件。

```Objective-C
#import <NIMSDK/NIMSDK.h>
```

## 第二步：配置初始化可选项

:::note note
您可按需对初始化的可选项进行配置。本步骤为可选步骤，如果不配置，将使用默认配置。
:::

配置可选项主要分为：

- [`NIMSDKConfig`](https://doc.yunxin.163.com/messaging2/references/iOS/doxygen/Latest/zh/d6/d13/interface_n_i_m_s_d_k_config.html)：主要提供自动下载消息附件、撤回消息是否计入未读、是否多端同步未读数、HTTPS 支持、自动登录最大重试次数、本地日志存活期等重要 SDK 初始化配置项。
- [SDK 根目录](https://doc.yunxin.163.com/messaging2/references/iOS/doxygen/Latest/zh/d6/d13/interface_n_i_m_s_d_k_config.html#a22d776a9e90b585742a798b0505acc73)：若不设置，数据默认放置于 `$Document/NIMSDK` 目录下。
- [`NIMServerSetting`](https://doc.yunxin.163.com/messaging2/references/iOS/doxygen/Latest/zh/df/d1d/interface_n_i_m_server_setting.html)：对网易云信服务器进行配置，如 CDN 域名下发、是否是专属云、IM 服务器 LBS 域名等。
- [`NIMQChatConfig`](https://doc.yunxin.163.com/messaging2/references/iOS/doxygen/Latest/zh/d3/d35/interface_n_i_m_q_chat_config.html)：可配置圈组初始化实例，如圈组消息缓存、自动订阅功能。

## 第三步：调用初始化接口

调用 [`registerWithOptionV2`](https://doc.yunxin.163.com/messaging2/references/iOS/doxygen/Latest/zh/de/de3/interface_n_i_m_s_d_k.html#a4140971377bec8212dabd66fdea0bbb3) 方法初始化 SDK，推荐在应用程序启动时初始化，目的是配置 SDK 并准备进行登录和即时通讯功能。初始化成功后，即可使用 V10 所有的 API。

### **参数说明**

参数 | 类型 | 是否必选 | 说明
--- | ---- | ---- | ---- |
`option` | [`NIMSDKOption`](https://doc.yunxin.163.com/messaging2/client-apis/DAxNjk0Mzc?platform=client#NIMSDKOption) | 是 | 应用配置参数。
`v2Option` | [`V2NIMSDKOption`](https://doc.yunxin.163.com/messaging2/client-apis/DAxNjk0Mzc?platform=client#V2NIMSDKOption) | 否 | V10 初始化配置参数。

`V2NIMSDKOption` 参数说明：

参数 | 类型 | 是否必选 | 说明
--- | ---- | ---- | ---- |
`useV1Login` | BOOL | 否 | 是否在开启 V10 API 后，仍使用 V9 的登录 API 登录 IM。<li>默认 NO，即开启 V10 API 后需要使用 V10 的登录 API 登录 IM。<li>设置为 YES，则开启 V10 API 后仍需要使用 V9 的登录 API 登录 IM。<note type=notice>V9 和 V10 版本的登录 API 无法同时使用，只能选择其一。
`enableV2CloudConversation` | BOOL | 否 | 是否开启云端会话功能，默认为 NO，不启用云端会话模式（默认使用本地会话模式）。只有设置为 YES 后，才能正常使用 `V2NIMConversationService` 服务。

### 示例代码

```Objective-C
NIMSDKOption *option = [NIMSDKOption optionWithAppKey:appKey];
option.apnsCername = @"your apns certificate";
option.pkCername = @"your push kit certificate";
// 初始化配置
V2NIMSDKOption *v2Option = [[V2NIMSDKOption alloc] init];
//激活 V10 所有 API，默认使用 V10 的登录接口登录 IM
v2Option.useV1Login = NO;
//若仍使用 V9 的登录接口登录 IM
//v2Option.useV1Login = YES;
//是否开启云端服务功能，默认使用本地会话
v2Option.enableV2CloudConversation = NO;
//若需要使用云端会话
//v2Option.enableV2CloudConversation = YES;
[[NIMSDK sharedSDK] registerWithOptionV2:option v2Option:v2Option];
```

## 下一步

完成初始化后，您可以尝试 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。