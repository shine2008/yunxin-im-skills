# IM UIKit 依赖导入指引

## 资源标识

- resource_id: `imkit.integration.import.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于帮助工程导入云信 IM UIKit 依赖。

在总控流程里，它通常作为第一步使用。

## 执行步骤

### 1. 定位 app 模块依赖文件

- 查找 `app/build.gradle` 或 `app/build.gradle.kts`
- 先识别当前 DSL 风格，再沿用同风格写法

### 2. 依赖组装规则

| 条件 | 需要添加的依赖 |
|------|----------------|
| 始终添加 | `teamkit-ui`、`chatkit-ui`、Glide、Retrofit、OkHttp |
| 本地会话 | `localconversationkit-ui` |
| 云端会话 | `conversationkit-ui` |
| 选择通讯录 | `contactkit-ui` |
| 选择位置消息 | `locationkit` |
| 选择音视频通话 | `call-ui:3.0.0`、`avsignalling` |

### 3. 推荐版本

- `contactkit-ui:10.9.10`
- `localconversationkit-ui:10.9.10`
- `conversationkit-ui:10.9.10`
- `teamkit-ui:10.9.10`
- `chatkit-ui:10.9.10`
- `locationkit:10.9.10`
- `call-ui:3.0.0`
- `avsignalling:10.9.52`
- `glide:4.13.1`
- `retrofit:2.9.0`
- `converter-gson:2.9.0`
- `converter-scalars:2.9.0`
- `okhttp:4.12.0`

### 4. 写入规则

- 在 `dependencies {}` 末尾追加
- 相同 `groupId:artifactId` 已存在时不重复添加
- 不修改与本步骤无关的配置

## 代码参考

```kotlin
dependencies {
    implementation("com.netease.yunxin.kit.contact:contactkit-ui:10.9.10")
    implementation("com.netease.yunxin.kit.localconversation:localconversationkit-ui:10.9.10")
    implementation("com.netease.yunxin.kit.team:teamkit-ui:10.9.10")
    implementation("com.netease.yunxin.kit.chat:chatkit-ui:10.9.10")
    implementation("com.netease.yunxin.kit.call:call-ui:3.0.0")
    implementation("com.netease.nimlib:avsignalling:10.9.52")
    implementation("com.github.bumptech.glide:glide:4.13.1")
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.retrofit2:converter-scalars:2.9.0")
    implementation("com.squareup.okhttp3:okhttp:4.12.0")
}
```

## 完成后的输出

- 已确定功能模块、会话模式、UI 风格
- 已写入或检查依赖
- 若处于总控流程中，提示是否继续下一环节“权限与 Manifest”
