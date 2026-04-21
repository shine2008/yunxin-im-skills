# IM UIKit Android 集成总控

## 资源标识

- resource_id: `imkit.integration.main`
- category: `mcp-guide`
- scope: `android-im-uikit`

## 适用场景

当用户出现以下任意意图时，优先读取本文件：

- 集成 IM UIKit
- 集成云信 IM / NIM SDK
- 快速开始 IM 聊天能力
- 添加会话列表、通讯录、聊天、音视频通话
- 希望按步骤完成 Android IM 集成

## 检索关键词

`IM UIKit` `云信 IM` `NIM SDK` `Android 集成` `会话列表` `通讯录` `初始化` `登录` `Normal` `Fun` `本地会话` `云端会话`

## 示例检索问句

- 帮我从零开始集成云信 IM UIKit Android
- 我想在 Android 项目里按步骤接入网易云信 IM UIKit
- 给我一个 IM UIKit 的完整集成流程，包含依赖、初始化、登录和界面
- 集成 NIM SDK 和 IM UIKit 时，应该先做哪些步骤
- 我需要一个能控制 IM UIKit 六步集成流程的总控文档
- 帮我整理 Android IM UIKit 的完整接入向导

## 补充检索问句

### 中文口语化

- 我要把云信聊天能力整套接进 Android，先看哪个总文档
- 想完整接入 IM UIKit，你给我一个总流程
- 别分散看文档了，给我一个能管全流程的 IM UIKit 集成说明

### 中英混合

- 给我一个 IM UIKit Android integration guide，总控版
- 想做 NIM SDK + IM UIKit quick start，先读哪个文档
- 我需要一个 Android IM UIKit full flow 文档

### 缩写型

- IM UIKit 总控流程
- NIM SDK 集成总览
- Android IM 接入总流程

## 总体职责

本文件只负责流程控制，不负责展开每一步的全部细节。执行原则如下：

1. 先识别用户是否要进行 IM UIKit Android 集成
2. 进入流程后，按顺序读取对应 guide 文档
3. 每一环只在需要时读取对应 guide 文档，避免一次性加载全部内容
4. 每一步完成后再进入下一步
5. 维护跨步骤变量，后续步骤不得重复无意义询问

## 跨步骤变量

- `FEATURES`：用户在步骤 1 选择的功能模块
- `SESSION_MODE`：`本地会话` 或 `云端会话`
- `UI_STYLE`：`Normal` 或 `Fun`
- `ENABLE_LOCATION`：是否启用位置消息，依据 `FEATURES` 自动推导
- `USER_NIM_APP_KEY`：云信 App Key
- `USER_AMAP_CLIENT_KEY`：高德客户端 Key
- `USER_AMAP_WEB_KEY`：高德 Web Key
- `TARGET_PAGE`：登录逻辑放置页面
- `TARGET_METHOD`：登录触发方法
- `SUCCESS_TARGET_CLASS`：登录成功跳转页面
- `CONVERSATION_ACTIVITY`：会话列表集成目标 Activity
- `CONTACT_ACTIVITY`：通讯录集成目标 Activity

## 启动提示

当用户首次进入流程时，模型应先输出类似欢迎信息，再开始执行步骤 1：

```text
欢迎使用 IM UIKit 集成向导 🚀

集成流程共 6 步：
① 按需导入组件 → ② 添加权限 → ③ 防止代码混淆 → ④ 初始化 → ⑤ 登录 → ⑥ 集成界面风格

准备项：
- 已创建云信应用并拿到 App Key
- 已准备 IM 账号 account_id 和 token
- Android Studio、Java、Gradle 版本满足要求
```

## 步骤顺序

1. 读取 `import_components.md`
2. 读取 `permission.md`
3. 读取 `proguard.md`
4. 读取 `initialization.md`
5. 读取 `login_logout.md`
6. 读取 `conversation_contact.md`

## 步骤映射

- 第一步：`mcp/imkit-integration-guide/import_components.md`
- 第二步：`mcp/imkit-integration-guide/permission.md`
- 第三步：`mcp/imkit-integration-guide/proguard.md`
- 第四步：`mcp/imkit-integration-guide/initialization.md`
- 第五步：`mcp/imkit-integration-guide/login_logout.md`
- 第六步：`mcp/imkit-integration-guide/conversation_contact.md`

## 执行规则

- 先读后做：进入某一步前必须先读取该步文档
- 先分析现有工程，再改文件
- 尽量复用已有文件与已有 Activity / Application
- 不引入与仓库现状不一致的依赖写法
- UI 相关类名必须由 `UI_STYLE` 与 `SESSION_MODE` 决定
- 位置消息相关逻辑必须由 `ENABLE_LOCATION` 控制
- 当步骤文档要求询问用户时，应只问必要问题
- 代码写入后必须构建验证

## 输出要求

每一步结束后，模型应输出：

- 当前步骤已完成的内容
- 修改了哪些关键文件
- 是否继续下一步

最终总结至少包含：

- 会话模式
- 功能模块
- UI 风格
- 已修改文件
- 后续建议

## 生成代码时必须遵守

- Android 项目优先保持 Kotlin / Gradle KTS 现有风格
- 不要重复添加已有依赖和 Manifest 组件
- 不要硬编码无关字符串到代码中，能放资源文件时放资源文件
- 多 Fragment 场景优先使用 `ViewPager2 + TabLayout`
- 构建失败时优先修正包名、类路径、泛型签名与导入问题
