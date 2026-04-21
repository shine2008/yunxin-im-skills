# IM UIKit 混淆配置指引

## 资源标识

- resource_id: `imkit.integration.proguard.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于补齐 IM UIKit 的 ProGuard / R8 规则。

在总控流程里，它通常作为第三步使用。

## 适用场景

当项目已经接入 IM UIKit，准备补齐 `proguard-rules.pro` 或 `R8` 保留规则时，读取本文件。

## 输入采集

询问用户是否还使用以下需要附加 keep 规则的功能，可多选：

- 音视频通话 `call-ui`
- 高德地图位置消息 `locationkit`
- 全文检索 `Lucene`
- 数据库加密 `SQLCipher`
- 仅基础配置

## 执行步骤

### 1. 基础规则必须追加

```proguard
-dontwarn com.netease.nim.**
-keep class com.netease.nim.** {*;}
-dontwarn com.netease.nimlib.**
-keep class com.netease.nimlib.** {*;}
-dontwarn com.netease.share.**
-keep class com.netease.share.** {*;}
-dontwarn com.netease.mobsec.**
-keep class com.netease.mobsec.** {*;}
-dontwarn com.netease.yunxin.kit.**
-keep class com.netease.yunxin.kit.** {*;}
-keep public class * extends com.netease.yunxin.kit.corekit.XKitInitOptions
-keep class * implements com.netease.yunxin.kit.corekit.XKitService {*;}
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.module.AppGlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** { **[] $VALUES; public *; }
-dontwarn okhttp3.**
-keep class okhttp3.**{*;}
```

### 2. 可选追加规则

- 音视频通话：

```proguard
-keep class com.netease.lava.** {*;}
-keep class com.netease.yunxin.** {*;}
```

- 高德地图：

```proguard
-keep class com.amap.api.maps.**{*;}
-keep class com.autonavi.**{*;}
-keep class com.amap.api.trace.**{*;}
-keep class com.amap.api.location.**{*;}
-keep class com.amap.api.fence.**{*;}
-keep class com.loc.**{*;}
-keep class com.autonavi.aps.amapapi.model.**{*;}
```

- Lucene：

```proguard
-dontwarn org.apache.lucene.**
-keep class org.apache.lucene.** {*;}
```

- SQLCipher：

```proguard
-keep class net.sqlcipher.** {*;}
```

## 写入规则

- 追加到现有 `proguard-rules.pro`
- 已存在的规则不要重复写
- 基础规则始终保留

## 完成后的输出

- 说明基础规则已补齐
- 说明用户选择的附加规则是否已追加
- 若处于总控流程中，提示是否继续下一环节“初始化”
