[//]: # (关键词: iOS, IM UIKit, 配置中心, 功能开关, 全局设置, SettingRepo, IMKitConfigCenter, 初始化配置)
# IM UIKit iOS 功能配置指南

<a id="IMKitConfigCenter"></a>

## 1. 配置中心 (IMKitConfigCenter)

IMKitConfigCenter 是 NIM UIKit 的全局功能配置中心，提供丰富的配置开关，让您可以根据业务需求灵活控制各项功能的启用状态。通过这些配置，您可以定制个性化的即时通讯体验，包括群功能、@功能、消息收藏、置顶等多种功能。


**主要配置参数**：

| 参数 | 类型 | 说明 | 默认值 |
| --- | --- | --- | --- |
| `enableTeam` | Bool | 控制是否启用群功能。如果设置为 false，将不会出现任何与群相关的功能。 | true |
| `enableTeamJoinAgreeModelAuth` | Bool | 控制是否开启群聊申请邀请功能。启用后，验证消息展示群聊入群申请、入群邀请，群管理新增入群申请、入群邀请开关。 | false |
| `enableAtMessage` | Bool | 控制是否启用@提及功能。启用后，用户可以在消息中@其他用户。 | true |
| `enableCollectionMessage` | Bool | 控制是否启用消息收藏功能。启用后，用户可以收藏重要消息以便日后查看。 | true |
| `enablePinMessage` | Bool | 控制是否启用消息固定(Pin)功能。启用后，用户可以固定重要消息在会话顶部。 | true |
| `enableTopMessage` | Bool | 控制是否启用消息置顶功能。启用后，用户可以将重要会话置顶。 | true |
| `enableOnlineStatus` | Bool | 控制是否开启订阅在线状态。启用后，可以查看用户是否在线。 | true |
| `enableOnlyFriendCall` | Bool | 控制是否仅允许好友之间进行通话。启用后，只有好友关系的用户才能互相发起通话。 | true |
| `enableDismissTeamDeleteConversation` | Bool | 控制是否在解散群或被踢出群聊后自动删除对应的会话。 | true |
| `enableAIUser` | Bool | 控制是否启用 AI 数字人功能。启用后，应用将支持 AI 数字人相关功能。 | true |
| `enableAIChatHelper` | Bool | 控制是否开启 AI 聊天助手功能。启用后，AI 将辅助用户聊天。 | true |
| `enableRichTextMessage` | Bool | 控制是否支持富文本消息，包括换行等高级文本格式。 | true |
| `enableAIStream` | Bool | 控制是否开启流式消息功能。启用后，支持 AI 流式回复等功能。 | true |

<!--
| `enableTypingStatus` | [Bool] | 是否支持正在输入状态功能，默认 true。 | -->

## 2. 配置时机

为确保配置能够正确生效，您需要在 NIM UIKit 功能或页面加载之前进行配置。以下是一些建议的配置时机：

- 建议在应用启动时统一配置所有全局设置，便于管理和调试。
- 某些设置如语音播放模式可以记住用户的偏好，保存到 UserDefaults 中。
- 某些功能可以根据用户权限、VIP 级别等条件动态调整。

## 3. 配置方法 (IMKitConfigCenter)

推荐在 `AppDelegate` 的 `application(_:didFinishLaunchingWithOptions:)` 方法中，在初始化 IM SDK 之前完成 NIM UIKit 的配置：

:::::: div linked-codes
::: code Swift

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    // 初始化 SDK 前配置 NIM UIKit
    // 配置功能开关示例
    IMKitConfigCenter.shared.enableTeam = true          // 启用群功能
    IMKitConfigCenter.shared.enableAIUser = true        // 启用 AI 数字人
    IMKitConfigCenter.shared.enableAtMessage = true     // 启用@功能
    IMKitConfigCenter.shared.enablePinMessage = true    // 启用 Pin 消息功能
    
    // 设置 IM SDK 的配置项
    let option = NIMSDKOption()
    option.appKey = "your app key"
    option.apnsCername = "网易云信控制台配置的 APNS 推送证书名称"
    option.pkCername = "网易云信控制台配置的 PushKit 推送证书名称"
    
    // 设置 IM SDK V2 的配置项
    let v2Option = V2NIMSDKOption()
    v2Option.enableV2CloudConversation = false
    
    // 初始化 IM UIKit
    IMKitClient.instance.setupIM2(option, v2Option)

    return true
}
```

:::
::: code Objective-C

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // 初始化 SDK 前配置 NIM UIKit
    // 配置功能开关示例
    IMKitConfigCenter.shared.enableTeam = YES;           // 启用群功能
    IMKitConfigCenter.shared.enableAIUser = YES;         // 启用 AI 数字人
    IMKitConfigCenter.shared.enableAtMessage = YES;      // 启用@功能
    IMKitConfigCenter.shared.enablePinMessage = YES;     // 启用 Pin 消息功能
    
    // 设置 IM SDK 的配置项
    NIMSDKOption *option = [NIMSDKOption optionWithAppKey:@"your app key"];
    option.apnsCername = @"网易云信控制台配置的 APNS 推送证书名称";
    option.pkCername = @"网易云信控制台配置的 PushKit 推送证书名称";
    
    // 设置 IM SDK V2 的配置项
    V2NIMSDKOption *v2Option = [[V2NIMSDKOption alloc] init];
    v2Option.enableV2CloudConversation = NO;
    
    // 初始化 IM UIKit
    [IMKitClient.instance setupIM2:option :v2Option];

    return YES;
}
```
:::
::::::

## 4. 常用全局设置 (SettingRepo)

除了功能开关配置外，NIM UIKit 还提供了一些常用的全局设置。建议您使用 `SettingRepo.shared` 进行设置，以下是两个典型示例：

### 4.1 设置消息已读未读状态

调用 `SettingRepo.shared.getShowReadStatus()` 方法您可以控制是否在全局范围内显示消息的已读和未读状态指示。

:::::: div linked-codes
::: code Swift

```swift
// 设置会话消息是否展示已读未读状态
// 参数为 true 表示显示已读/未读状态，false 表示隐藏
SettingRepo.shared.setShowReadStatus(true)

// 查询当前的已读状态显示设置
let isShowingReadStatus = SettingRepo.shared.getShowReadStatus()
```

:::
::: code Objective-C

```objectivec
// 设置会话消息是否展示已读未读状态
// 参数为 YES 表示显示已读/未读状态，NO 表示隐藏
[SettingRepo.shared setShowReadStatus:YES];

// 查询当前的已读状态显示设置
BOOL isShowingReadStatus = [SettingRepo.shared getShowReadStatus];
```
:::
::::::

启用后，消息会显示如下状态指示图标：

已读未读状态效果图

### 4.2 设置语音消息播放模式

调用 `SettingRepo.shared.setHandsetMode()` 方法您可以控制语音消息的播放方式，选择通过听筒播放（适合私密场合）或外放播放（适合共享场合）。

:::::: div linked-codes
::: code Swift

```swift
// 设置语音消息的播放模式
// true - 听筒模式，适合私密内容
// false - 扬声器模式，适合公开场合
SettingRepo.shared.setHandsetMode(true)

// 获取当前的语音播放模式
let isHandsetMode = SettingRepo.shared.getHandsetMode()
```

:::
::: code Objective-C

```objectivec
// 设置语音消息的播放模式
// YES - 听筒模式，适合私密内容
// NO - 扬声器模式，适合公开场合
[SettingRepo.shared setHandsetMode:YES];

// 获取当前的语音播放模式
BOOL isHandsetMode = [SettingRepo.shared getHandsetMode];
```
:::
::::::

### 4.3 其他可配置项

除了上述设置外，SettingRepo 还提供以下配置功能：

- 通知栏展示推送详情设置
- PC/Web 同步开关设置
- 消息提示音开关设置
- 新消息通知设置
- 节点配置等

有关这些配置的详细使用方法，请参考 `SettingRepo` 的相关文档。