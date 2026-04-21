# IM UIKit 权限与 Manifest 指引

## 资源标识

- resource_id: `imkit.integration.permission.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于补齐 IM UIKit 所需权限和 `AndroidManifest.xml` 组件。

在总控流程里，它通常作为第二步使用。

## 适用场景

当依赖已接入，需要补齐 `AndroidManifest.xml` 中的 IM 组件和动态权限说明时，读取本文件。

## 输入要求

- 本指引不再单独询问用户
- 直接复用总控文档 `integration.md` 已收集的功能选择结果
- 根据是否选中“音视频通话”和“位置消息（高德地图）”决定权限提示范围
- 直接定位 `app/src/main/AndroidManifest.xml`

## 执行步骤

### 1. 在 `<application>` 内确保存在以下组件

```xml
<service android:name="com.netease.nimlib.service.NimService" android:process=":core" tools:remove="true" />
<service android:name="com.netease.nimlib.push.net.HeartbeatService" android:process=":core" tools:remove="true" />
<service android:name="com.netease.nimlib.job.NIMJobService" android:exported="false" android:permission="android.permission.BIND_JOB_SERVICE" android:process=":core" tools:remove="true" />

<receiver android:name="com.netease.nimlib.service.NimReceiver" android:exported="false" android:process=":core" tools:remove="true">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>

<receiver android:name="com.netease.nimlib.service.ResponseReceiver" tools:remove="true" />
<service android:name="com.netease.nimlib.service.ResponseService" tools:remove="true" />

<provider android:name="com.netease.nimlib.ipc.cp.provider.PreferenceContentProvider" android:authorities="${applicationId}.ipc.provider.preference" android:exported="false" tools:remove="true" />
<provider android:name="com.netease.nimlib.ipc.NIMContentProvider" android:authorities="${applicationId}.ipc.provider" android:exported="false" android:process=":core" tools:remove="true" />

<service android:name="com.netease.nimlib.service.NimServiceV2" />
<provider android:name="com.netease.nimlib.ipc.NIMContentProviderV2" android:authorities="${applicationId}.ipc.provider.v2" android:exported="false" />
```

### 2. 写入原则

- 已存在同名组件时，不重复添加
- `NimServiceV2` 和 `NIMContentProviderV2` 必须保留
- 若 Manifest 已配置正确，只需确认并说明，无需重复改写

### 3. 需要提醒用户的动态权限

- 基础媒体权限（建议默认提示）：
  - Android 12 及以下：
    - `READ_EXTERNAL_STORAGE`
    - `WRITE_EXTERNAL_STORAGE`
  - Android 13 及以上：
    - `READ_MEDIA_IMAGES`
    - `READ_MEDIA_VIDEO`
    - `READ_MEDIA_AUDIO`
- 当总控步骤已选中“音视频通话”时，再提示：
  - `CAMERA`
  - `RECORD_AUDIO`
- 当总控步骤已选中“位置消息（高德地图）”时，再提示定位相关运行时权限（按项目现状补齐）

## 完成后的输出

- 说明 Manifest 是否已补齐
- 说明哪些组件被新增或复用
- 提醒动态权限需要运行时申请
- 若处于总控流程中，提示是否继续下一环节“混淆配置”
