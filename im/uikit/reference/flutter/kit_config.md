# IM UIKit Flutter 功能配置指南

本文档详细介绍如何在Flutter应用中配置IM UIKit的全局功能设置，包括已读/未读状态、语音消息播放模式以及消息标记功能。这些设置在IM UIKit中全局生效。

## 1. 已读未读状态设置

- 调用 `ConfigRepo.updateShowReadStatus` 方法设置是否全局展示会话消息（包含单聊消息和群聊消息）的已读和未读状态。入参配置为 true 则展示下图中表示已读状态和未读状态的图标，为 false 则不展示。 

    示例代码如下：
    
    ```dart
// 配置消息标记功能开关
// 设置语音消息播放模式
// 设置已读未读状态显示
// 获取当前已读未读状态配置dart
    //入参为true则代表展示，为false不展示
    ConfigRepo.updateShowReadStatus(true);
    ```

- 调用 `ConfigRepo.getShowReadStatus` 方法可查询当前是否全局展示会话消息的已读未读状态。

    示例代码如下：

    ```dart
    // 获取当前已读未读状态配置
    ConfigRepo.getShowReadStatus();
    ```

## 2. 语音消息播放模式

调用 `ConfigRepo.updateAudioPlayMode` 方法可设置语音消息的播放模式（听筒模式或外放模式）。

```dart
// 入参value值，0代表听筒，1代表外放，ConfigRepo.audioPlayEarpiece 和 ConfigRepo.audioPlayOutside
ConfigRepo.updateAudioPlayMode(value);
```

## 3. 消息标记功能开关

调用以下代码全局设置消息标记功能开关，设置后在 IM UIKit 全局生效。

```dart
// 配置消息标记功能开关（默认开启）
IMKitClient.enablePin = true;
```