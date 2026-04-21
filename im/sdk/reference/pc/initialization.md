本文介绍如何初始化适配桌面系统（Windows、macOS、Linux）应用的 V10 系列网易云信即时通讯 SDK（简称 NIM SDK）。初始化 SDK 时，可同时配置 APNs 离线推送服务、会话已读多端同步、群消息已读和融合存储等重要功能。

## 调用时机

对 NIM SDK 进行初始化的时机，是在调用各项即时通讯功能之前。一般情况下，在应用的生命周期内，仅需进行一次初始化。

## 前提条件

开始 NIM SDK 初始化前，请确保您已 [集成 SDK](https://doc.yunxin.163.com/messaging/guide/zQ3MzQyMzQ?platform=client)。

## 第一步：引入动态库文件

针对桌面系统应用，V10 NIM SDK 提供了不同系统的动态库文件，区别如下：

| <div style="Width:75px;">系统</div> | <div style="Width:75px;">文件后缀</div> | 处理方式 |
| --- | --- | --- |
| Linux | .so | 将动态库文件，放置到应用程序相同目录。 |
| macOS | .dylib | 在打包应用时，将 `.dylib` 文件放置到 `<YourApp>.app/Contents/Frameworks` 目录下。 |
| Windows | .dll | 将动态库文件，放置到应用程序相同目录。<note type="note">Windows SDK 基于 MSVC15（2017） 开发，如果 App 没有对应的运行时库文件，请在安装应用时部署 [微软提供的 MSVC 2017 运行时库组件](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170)。</note> |

## 第二步：配置初始化可选项

:::note note
您可按需对初始化的可选项进行配置。本步骤为可选步骤，如果不配置，将使用默认配置。
:::

初始化可选项配置主要分为：

- 基础配置 [`V2NIMBasicOption`](https://doc.yunxin.163.com/messaging2/references/pc/doxygen/Latest/zh/structv2_1_1_v2_n_i_m_basic_option.html)：设置日志等级、自定义登录端类型等信息。
- 连接相关配置 [`V2NIMLinkOption`](https://doc.yunxin.163.com/messaging2/references/pc/doxygen/Latest/zh/structv2_1_1_v2_n_i_m_link_option.html)：设置 HTTPS 协议、加密算法等能力。
- 数据库配置 [`V2NIMDatabaseOption`](https://doc.yunxin.163.com/messaging2/references/pc/doxygen/Latest/zh/structv2_1_1_v2_n_i_m_database_option.html)：设置数据备份、机密、恢复等能力。
- 融合存储配置 [`V2NIMFCSOption`](https://doc.yunxin.163.com/messaging2/references/pc/doxygen/Latest/zh/structv2_1_1_v2_n_i_m_f_c_s_option.html)：开启和设置融合存储认证类型。
- 私有化配置 [`V2NIMPrivateServerOption`](https://doc.yunxin.163.com/messaging2/references/pc/doxygen/Latest/zh/structv2_1_1_v2_n_i_m_private_server_option.html)：设置私有化 LBS 和 Link 地址等。

## 第三步：调用初始化接口

调用 `init` 方法初始化 SDK，推荐在应用程序启动时初始化。初始化成功后，即可使用 V10 所有的 API。

### 参数说明

参数 | 类型 | 是否必选 | 说明
--- | ---- | ---- | ---- |
`option` | `V2NIMInitOption` | 是 | V10 初始化配置参数。

`V2NIMInitOption` 参数说明：

参数 | 类型 | 是否必选 | 说明
---- | ---- | ---- | ---- |
`appkey` | nstd::string | 是 | 网易云信应用的 AppKey。获取方式请参考 [创建应用并获取 AppKey](https://doc.yunxin.163.com/console/concept/TIzMDE4NTA?platform=console)。 |
`appDataPath` | nstd::string | 否 | 应用的数据目录，为空则使用默认目录。<br>默认数据：<li>Windows：%localappdata%/NIM<li>macOS：~/Library/Application Support/NIM<li>Linux：~/.local/share/NIM
`basicOption` | `V2NIMBasicOption` | 否 | 基础配置。<note type=note>若需要开启云端会话功能，可以配置 `V2NIMBasicOption.enableCloudConversation` 为 true。默认为 false，不启用云端会话模式（默认使用本地会话模式）。只有设置为 true 后，才能正常使用 `V2NIMConversationService` 服务。
`linkOption` | `V2NIMLinkOption` | 否 | 连接相关配置。
`databaseOption` | `V2NIMDatabaseOption` | 否 | 数据库配置。
`fcsOption` | `V2NIMFCSOption` | 否 | 融合存储配置。
`privateServerOption` | `nstd::optional<V2NIMPrivateServerOption>` | 否 | 私有化配置。

### 示例代码

```C++
int main(int argc, char* argv[]) {
    // 创建初始化选项
    v2::V2NIMInitOption option;

    // 设置必要参数
    option.appkey = "your_app_key_here";  // 替换为您的 AppKey

    // 设置可选参数
    option.appDataPath = "custom/data/path";  // 自定义数据路径，如果不设置则使用默认路径

    // 设置基础配置
    v2::V2NIMBasicOption basicOption;
    basicOption.enableCloudConversation = true;  // 启用云端会话功能
    option.basicOption = basicOption;

    // 初始化 SDK
    auto error = v2::V2NIMClient::get().init(option);
    if (error) {
        std::cerr << "初始化失败: " << error->getErrorCode() << " - " << error->getErrorMsg() << std::endl;
        return -1;
    }
    std::cout << "SDK 初始化成功" << std::endl;

    // 这里可以添加您的业务逻辑
    // ...
    // 在应用退出时反初始化 SDK
    error = v2::V2NIMClient::get().uninit();
    if (error) {
        std::cerr << "反初始化失败: " << error->getErrorCode() << " - " << error->getErrorMsg() << std::endl;
        return -1;
    }
    std::cout << "SDK 反初始化成功" << std::endl;
    return 0;
}
```

## 下一步

完成初始化后，您可以尝试 [登录 IM](https://doc.yunxin.163.com/messaging/guide/Dk1MTY4MzA?platform=client)。