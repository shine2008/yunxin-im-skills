# IM UIKit 初始化指引

## 资源标识

- resource_id: `imkit.integration.init.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于处理 App Key、Application 和 IM UIKit 初始化逻辑。

在总控流程里，它通常作为第四步使用。

## 适用场景

当依赖、Manifest、混淆已准备好，需要写入 App Key 和 `Application` 初始化逻辑时，读取本文件。

## 前置变量

- `FEATURES`
- `SESSION_MODE`

## 相关导入

```java
import com.netease.yunxin.kit.corekit.im2.IMKitClient;
import com.netease.yunxin.kit.corekit.im2.extend.FetchCallback;
```

## 自动推导规则

### 是否启用位置消息

不再额外询问。根据 `FEATURES` 自动判断：

- 如果包含 `位置消息`，则设置 `ENABLE_LOCATION = true`
- 否则设置 `ENABLE_LOCATION = false`

## 输入采集

### 必填

- `USER_NIM_APP_KEY`

### 条件采集

仅当 `ENABLE_LOCATION = true` 时，再收集：

- `USER_AMAP_CLIENT_KEY`
- `USER_AMAP_WEB_KEY`

## 执行步骤

### 1. 定位并更新 AndroidManifest.xml

在 `<application>` 中确保存在：

```xml
<meta-data android:name="com.netease.nim.appKey" android:value="USER_NIM_APP_KEY" />
```

仅当 `ENABLE_LOCATION = true` 时追加：

```xml
<meta-data android:name="com.amap.api.v2.apikey" android:value="USER_AMAP_CLIENT_KEY" />
<meta-data android:name="com.amap.api.v2.web.apikey" android:value="USER_AMAP_WEB_KEY" />
<service android:name="com.amap.api.location.APSService" />
```

如果相同 `android:name` 已存在，则只替换 value，不重复添加。

### 2. 查找 Application 类

- 优先复用现有 `Application`
- 若不存在，再创建 `MyApplication`
- 若创建了新类，记得在 Manifest 中补 `android:name`

### 3. 初始化模板

#### 本地会话

```java
private void initUiKit() {
    SDKOptions options = new SDKOptions();
    options.appKey = "USER_NIM_APP_KEY";
    IMKitClient.init(this, options, null);
}
```

#### 云端会话

```java
private void initUiKit() {
    SDKOptions options = new SDKOptions();
    options.appKey = "USER_NIM_APP_KEY";
    options.enableV2CloudConversation = true;
    IMKitClient.init(this, options, null);
}
```

#### 位置消息附加初始化

仅当 `ENABLE_LOCATION = true` 时追加：

```java
LocationConfig locationConfig = new LocationConfig();
locationConfig.aMapWebServerKey = "USER_AMAP_WEB_KEY";
LocationKitClient.init(this, locationConfig);
```

## 执行规则

- 若项目同时已集成 NIM SDK，初始化只保留一次，不要重复初始化
- 优先在 `onCreate()` 中调用 `initUiKit()`
- 不要覆盖项目已有的无关初始化逻辑

## 完成后的输出

- 说明 App Key 是否已写入
- 说明是否启用了位置消息初始化
- 说明 Application 是复用还是新建
- 若处于总控流程中，提示是否继续下一环节“登录”
