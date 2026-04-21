<!-- keywords: 即时通讯,IM,基本功能,初始化,初始化 SDK,海外数据中心,快速开始 -->

本文介绍如何初始化适配安卓应用的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。初始化 SDK 时，可同时配置第三方离线推送服务、会话已读多端同步、群消息已读和融合存储等重要功能。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

初始化 NIM SDK 前，请确保您已经完成了以下操作：

- 在项目中 [集成 NIM SDK](https://doc.yunxin.163.com/messaging2/guide/DA5NzIyMjc?platform=client)。
- 在 [网易云信控制台](https://app.yunxin.163.com/global/home) 上，[创建应用](https://doc.yunxin.163.com/console/guide/TIzMDE4NTA?platform=console)，获取 App Key 和 App Secret。
    - App Key 需要在调用初始化的方法时，通过 `SDKOptions` 参数传入（具体见下文）。
    - App Secret 可在初始化完成后，用于实现 <a href="https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client#%E5%8A%A8%E6%80%81%20Token%20%E7%99%BB%E5%BD%95">动态 Token 登录</a>。
- [注册网易云信 IM 账号](https://doc.yunxin.163.com/messaging2/guide/jU0Mzg0MTU?platform=client#%E7%AC%AC%E4%BA%8C%E6%AD%A5%E6%B3%A8%E5%86%8C-im-%E8%B4%A6%E5%8F%B7)，获取 IM 账号和 Token。

## 初始化 SDK

NIM SDK 支持在应用代码的任意位置初始化。

调用 [`NIMClient#initV2`](https://doc.yunxin.163.com/messaging2/references/android/doxygen/Latest/zh/classcom_1_1netease_1_1nimlib_1_1sdk_1_1_n_i_m_client.html#aa8e0e524f223d29e8f2499557a2ae2e5) 方法进行初始化。

**参数说明**

提示：若需要确认示例中使用的类/接口的方法签名、参数类型或返回结构，可使用 `nim_sdk_search_symbols` 与 `nim_sdk_list_members` 查询。

```Java
public static void initV2(Context context, SDKOptions options)
```

参数 | 类型 | 说明
---- | ---- | ----
`context` | Context | 应用上下文。
`options` | [`SDKOptions`](https://doc.yunxin.163.com/messaging2/references/android/doxygen/Latest/zh/classcom_1_1netease_1_1nimlib_1_1sdk_1_1_s_d_k_options.html) | SDK 的配置信息，如 App Key、第三方离线推送配置、是否开启会话已读多端同步等。<br>其中 App Key 如果已在 [集成 SDK](https://doc.yunxin.163.com/messaging2/guide/DA5NzIyMjc?platform=client#%E6%AD%A5%E9%AA%A42%EF%BC%9A%E9%85%8D%E7%BD%AE%E6%9D%83%E9%99%90%E4%B8%8E%E9%98%B2%E6%B7%B7%E6%B7%86) 时在 `AndroidManifest.xml` 传入，则此处不需要再传入，**否则必须传入**，如果两处都设置了，取此处的值。<br>更多重要配置参数说明，可单击下方的 **SDKOptions 的部分重要配置参数** 查看。<note type=note>如果您的应用主要服务于海外用户，且数据需要写入海外数据中心，请先在 [网易云信控制台](https://app.yunxin.163.com/global/home) [选择服务区域](https://doc.yunxin.163.com/messaging2/guide/Dk0Nzk3MDE?platform=client#%E5%88%9B%E5%BB%BA%E5%BA%94%E7%94%A8%E5%B9%B6%E9%80%89%E6%8B%A9%E6%9C%8D%E5%8A%A1%E5%8C%BA%E5%9F%9F)为海外，并配置私有化服务器地址中的 LBS 和 link 域名相关信息，具体参考 [配置 LBS 和 link 域名](https://doc.yunxin.163.com/messaging2/guide/Dk0Nzk3MDE?platform=client#%E9%85%8D%E7%BD%AE%E5%AE%A2%E6%88%B7%E7%AB%AF%20SDK%20%E4%B8%AD%E7%9A%84%20link%20%E5%9F%9F%E5%90%8D%E5%92%8C%20LBS%20%E5%9F%9F%E5%90%8D)。</note>

<details><summary>单击展开 SDKOptions 的<b>部分</b>重要配置参数。</summary>

| 参数 | 说明 |
| ---- | ---- |
| **disableV2Login** | 是否在开启 V10 API 后，禁用 V10 的登录 API，仍使用 V9 的登录 API 登录 IM。<li>默认 false，即开启 V10 API 后使用 V10 的登录 API 登录 IM。<li>设置为 true，则开启 V10 API 后仍需要使用 V9 的登录 API 登录 IM。<note type=notice>V9 和 V10 版本的登录 API 无法同时使用，只能选择其一。 |
| **enableV2CloudConversation** | 是否开启云端会话功能，默认为 false，不启用云端会话模式（默认使用本地会话模式）。只有设置为 true 后，才能正常使用 `V2NIMConversationService` 服务。 |
| appKey | 设置网易云信 SDK 的 appKey。appKey 还可以在 `AndroidManifest.xml` 文件中，通过 meta-data 的方式设置。 |
| statusBarNotificationConfig | 网易云信封装的消息提醒配置。 |
| userInfoProvider | 用户信息提供者，目前主要用于通知栏显示用户昵称和头像。 |
| messageNotifierCustomization | 通知栏提醒文案定制。 |
| sdkStorageRootPath | 外置存储根目录,用于存放多媒体消息，日志等文件<br/>如果使用 data/user/0/的子目录，在没有存储权限的情况下会导致部分文件操作失败，可以使用 data/data/或者/sdcard/Android/data/包名/的子目录。 |
| preloadAttach | 是否需要 SDK 自动预加载 [多媒体消息](https://doc.yunxin.163.com/messaging2/guide/DYzMjA0Njc?platform=client#%E5%AE%9E%E7%8E%B0%E5%AF%8C%E5%AA%92%E4%BD%93%E6%B6%88%E6%81%AF%E6%94%B6%E5%8F%91) 的附件。 |
| recentContactContentSource | 最近会话的内容来源，默认为 `MessageTypeTipPreferred`（即优先使用与该联系人的最后一条消息的消息类型 Tip 字段，若为空，则使用与该联系人的最后一条消息的消息内容）<br/>具体类型请参考 `RecentContactContentSource`。
| thumbnailSize | 消息缩略图的尺寸。 |
| sessionReadAck | 是否开启会话已读多端同步。 |
| improveSDKProcessPriority | 是否提高 SDK 进程优先级（默认提高，可以降低 SDK 核心进程被系统回收的概率） |
| serverConfig | 配置私有化的服务器地址。 |
| preLoadServers | 预加载服务，默认 true，不建议设置为 false，预加载连接可以优化登录流程。 |
| teamNotificationMessageMarkUnread | 群通知消息是否计入未读数，默认不计入未读。 |
| useXLog | 是否使用性能更好的 SDK 日志模式。默认 false，即使用普通日志模式。 |
| animatedImageThumbnailEnabled | 开启对动图缩略图的支持，默认为 false，截取第一帧。 |
| asyncInitSDK | 是否异步初始化 SDK，默认为 false。开启可降低 Application#onCreate 中 SDK 初始化函数的同步响应时间。 |
| reducedIM | 是否是弱 IM 场景，默认为 false。如果您的 App 仅在部分场景按需使用 IM 能力(不需要在应用启动时就做自动登录)，并不需要保证消息通知、数据的实时性，那么这里可以填 true。弱 IM 场景下，push 进程采用懒启动策略(延迟到用户登录阶段)，启动后其生命周期将跟随 UI 进程，降低弱 IM 场景的 App 的后台功耗开销。 |
| checkManifestConfig | 是否在 SDK 初始化时检查清单文件配置是否完全，默认为 false，建议开发者在调试阶段打开，上线时关掉。 |
| [mixPushConfig](https://doc.yunxin.163.com/messaging2/references/android/doxygen/Latest/zh/classcom_1_1netease_1_1nimlib_1_1sdk_1_1mixpush_1_1_mix_push_config.html) | 配置第三方离线推送的 appid、appkey、证书。 |
| enableBackOffReconnectStrategy | 是否使用随机退避重连策略，默认 true，强烈建议打开。如需关闭，请咨询网易云信技术支持。 |
| enableLBSOptimize | 是否启用网络连接优化策略，默认开启。 |
| enableTeamMsgAck | 是否启用群消息已读功能，默认关闭。 |
| shouldConsiderRevokedMessageUnreadCount | 撤回消息时未读数减一。 |
| mNosTokenSceneConfig | nos token 场景配置。 |
| loginCustomTag | 登录时的自定义字段，登录成功后会同步给其他端，获取可参考 `AuthServiceObserver#observeOtherClients()`。 |
| disableAwake | 禁止后台进程唤醒 ui 进程。 |
| fetchServerTimeInterval | 获取服务器时间连续请求间隔时间, 最小 1000ms，默认 2000ms。 |
| customPushContentType | 离线推送不显示详情时，要显示的文案对应的类型名称。 |
| notifyStickTopSession | 置顶会话是否同步。 |
| enableForegroundService | 启动 NimService 失败时，是否尝试以前台服务方式启动。 |
| cdnRequestDataInterval | Cdn 信息上报的回调间隔。 |
| rollbackSQLCipher | 是否回滚 SQLCipher 加密的数据库。 |
| coreProcessStartTimeout | core 进程启动的超时时间（单位：毫秒）。 |
| clearTimeTagAtBeginning | 是否重置同步时间戳，启动时重置，只重置一次（不建议开启，如需开启请 [提交工单](https://app.yunxin.163.com/global/service/ticket/create) 联系网易云信技术支持工程师） |
| enableDatabaseBackup | 是否开启数据库备份功能。 |
| captureDeviceInfoConfig | 设备信息获取配置<br />null 代表都可以获取<br />不获取设备信息可能影响功能，使用请 [提交工单](https://app.yunxin.163.com/global/service/ticket/create) 联系网易云信技术支持工程师。 |
| secondTimeoutForSendMessage | 发消息的第二超时时间，非常规功能，开启请 [提交工单](https://app.yunxin.163.com/global/service/ticket/create) 联系网易云信技术支持工程师。 |
| enableRecentContactsTimeIndex | 是否开启最近联系人会话时间索引,默认不开启，开启最近联系人会话时间索引后会明显减少查询最近联系人会话耗时，但是同时也会增加最新联系人会话写入操作耗时。 |
| notificationChannelProvider | 可配置通知要走的通道，如果不配置，根据响铃振动等配置走对应默认通道。 |
| enableFcs | 是否支持 aws s3 存储，默认开启。 |
| fcsDownloadAuthStrategy | aws s3 存储下载鉴权策略，如需开启，请咨询网易云信技术支持。 |
| enabledQChatMessageCache | 是否开启 [圈组消息缓存](https://doc.yunxin.163.com/messaging/docs/jE3NTUxOTM?platform=android) 支持，默认不开启。 |
| syncConfig | 同步配置，可设置是否在初始化时同步群成员以及超大群成员，默认为 true，同步。 |
| qchatAutoSubscribe | 是否开启圈组自动订阅，默认为 false，不开启。 |

</details>

::: note note
以上仅展示初始化重要参数，如需查看其他参数请参考 [`SDKOptions`](https://doc.yunxin.163.com/messaging2/references/android/doxygen/Latest/zh/classcom_1_1netease_1_1nimlib_1_1sdk_1_1_s_d_k_options.html)。
:::

**示例代码**：

```Java
// 激活 V10 API 后，可以根据该字段选项选择是否禁用 V10 API 登录，默认 false，即使用 V10 API 登录
// sdkOptions.disableV2Login = true;
// 开启云端会话功能
// enableV2CloudConversation = true;
...
// 按需设置其它 SDKOptions 设置项

NIMClient.initV2(context, sdkOptions);
```

## 下一步

完成初始化后，可 [登录 IM](https://doc.yunxin.163.com/messaging2/guide/Dk1MTY4MzA?platform=client)。

## 相关参考

### 如何初始化离线推送配置

如果您的应用需要实现离线推送，需在初始化时完成推送证书信息等配置。详情请参考 [实现离线推送](https://doc.yunxin.163.com/messaging2/guide/TkzMjk1ODg?platform=client#%E6%AD%A5%E9%AA%A4%203%EF%BC%9A%E5%88%9D%E5%A7%8B%E5%8C%96%20NIM%20SDK)。

### 如何实现应用的隐私合规

建议您参考 [IM 应用隐私合规](https://doc.yunxin.163.com/messaging2/guide/DY2OTcyMjY?platform=client#分步初始化)中介绍的最佳实践，实现应用隐私合规。

### 如何配置 SDK 数据缓存目录

<details><summary>单击展开查看如何在初始化 SDK 时，配置 <b>SDK 数据缓存目录</b>。</summary>

当用户收到 [富媒体消息](https://doc.yunxin.163.com/messaging2/guide/DYzMjA0Njc?platform=client#实现富媒体消息收发) 后，SDK 默认会下载相关的文件，同时 SDK 还会记录一些关键的日志文件，因此 SDK 需要一个数据缓存目录（下文简称为 **该目录**）。

- 该目录可以在 SDK 初始化时通过 `SDKOptions#sdkStorageRootPath` 进行设置。

    ::: note note
    上述应用扩展存储缓存目录的文件会随着应用卸载而被删除，也可以由用户手动在设置界面里面清除。
    :::

- 如果不设置，则该目录默认为 `/{外卡根目录}/{应用包名}/nim/`，其中外卡根目录可通过 `Environment.getExternalStorageDirectory().getPath()` 获取。

- 如果您的应用需要清除缓存功能，可扫描该目录下的文件，按照规则清理即可。

SDK 初始化完成后，可通过 `NIMClient#getSdkStorageDirPath` 获取该目录。该目录中包含：

子目录 | 内容
--- | ---
`log` | SDK 日志文件：如 `nim_sdk.log`，一般不超过 8 MB。
`image` | 图片消息中的原图
`audio` | 语音消息中的音频
`video` | 视频消息中的原视频
`thumb` | 图片/视频消息中的缩略图
`file` | 文件消息中的文件

</details>

### 如何查询 NIM SDK 版本号

可调用 [`NIMClient#getSDKVersion`](https://doc.yunxin.163.com/messaging2/references/android/doxygen/Latest/zh/classcom_1_1netease_1_1nimlib_1_1sdk_1_1_n_i_m_client.html#ad860b320cdea1d30f0133eb5075c3f07) 方法获取当前使用的 SDK 的版本号。
