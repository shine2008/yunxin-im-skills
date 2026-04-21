
# IM UIKit iOS 初始化指南

在使用 IM UIKit（V10） 功能前，必须先对其进行初始化并完成登录，但不需要对 IM SDK 单独初始化。

IM UIKit 的核心类 `IMKitClient`，提供初始化、获取当前账号信息、IM 登录登出等能力。

## 前提条件


根据本文操作前，请确保您已经完成了以下设置：

1. 已完成 组件导入。
2. 已 注册 IM 账号，获取 account_id 和 token。

## 注意事项

由于 IM UIKit 的初始化与登录是通过底层调用 NIM SDK 接口来实现的，因此重复调用 IM UIKit 和 NIM SDK 的初始化与登录接口会相互覆盖传入的信息（包括 **推送配置信息**）。

所以若用户同时集成 IM UIKit 和 NIM SDK，只需要调用一次初始化与登录接口即可。

## 初始化前先询问并记录

在开始初始化之前，先询问用户并记录以下输入值：

1. `USER_APP_KEY`
2. `USER_APNS_CERNAME`
3. `USER_PUSHKIT_CERNAME`

若用户已提供这些值，后续示例代码中使用用户提供的真实值进行填充。

## 初始化

1. 在项目中引入需要的组件。

    **示例代码：**

    :::::: div linked-codes
    ::: code Swift

    ```Swift
    import NECoreKit
    import NECoreIM2Kit
    import NEChatKit
    import NEChatUIKit
    import NERtcCallUIKit
    ...
    ```
    :::
    ::: code Objective-C
    ```Objective-C
    #import <NECoreKit/NECoreKit-Swift.h>
    #import <NECoreIM2Kit/NECoreIM2Kit-Swift.h>
    #import <NEChatKit/NEChatKit-Swift.h>
    #import <NEChatUIKit/NEChatUIKit-Swift.h>
    #import <NERtcCallUIKit/NERtcCallUIKit.h>
    ...
    ```
    :::
    ::::::

2. 在应用启动时，通过调用 `setupIM2` 方法实现初始化。

    ::: note notice
    初始化必须在应用的生命周期内进行，且只可进行一次。
    :::

    - **方法原型**
        :::::: div linked-codes
        ::: code Swift
        ```Swift
        // IM 初始化
        open func setupIM2(_ option: NIMSDKOption, _ v2Option: V2NIMSDKOption)
        ```
        :::
        ::: code Objective-C
        ```Objective-C
        - (void) setupIM2:(NIMSDKOption *) option
                    :(V2NIMSDKOption *) v2Option;
        ```
        :::
        ::::::

    - **参数说明**

        `NIMSDKOption` 参数说明如下：

        `NIMSDKOption` 参数 | 是否必传 | 说明
        --- | ---- | ----
        `appKey` | 是 | 从 网易云信控制台 获取到的 App Key
        `apnsCername` | 否 | APNs 推送证书名，如不需要实现离线推送可不配置
        `pkCername` | 否 | PushKit 推送证书名，如不需要实现离线推送可不配置
        `avoidNosAccelerationBuckets` | 否 | 不走 NOS 域名加速的桶名集合
        `v2` | 否 | 是否启用 v2 API <note type=notice>该字段自 10.6.1 版本起废弃。若仍需要使用 V1 的登录接口，请设置 `V2NIMSDKOption.useV1Login` 字段

        `V2NIMSDKOption` 参数说明如下：

        `V2NIMSDKOption` 参数 | 是否必传 | 说明
        --- | ---- | ----
        `useV1Login` | 否 | 是否使用旧的登录接口，默认 NO，不使用
        `enableV2CloudConversation` | 否 | 是否使用 V2 云端会话，默认 NO，不使用

        ::: note notice
        SDK 默认使用本地会话，若需要使用云端会话功能，除了在组件导入时，引入云端会话外，还需要在初始化时，将 `enableV2CloudConversation` 设置为 YES。只有设置为 YES 后，才能正常使用云端会话服务。
        :::

    - **示例代码**：

        :::::: div linked-codes
        ::: code Swift
        ```Swift
        // init
        // 设置 IM SDK 的配置项，包括 AppKey，推送配置和一些全局配置等
        let option = NIMSDKOption()
        option.appKey = "USER_APP_KEY"
        option.apnsCername = "USER_APNS_CERNAME"
        option.pkCername = "USER_PUSHKIT_CERNAME"

        // 设置 IM SDK V2 的配置项，包括是否使用旧的登录接口和是否使用云端会话
        let v2Option = V2NIMSDKOption()
        v2Option.enableV2CloudConversation = false

        // 初始化 IM UIKit，初始化 Kit 层和 IM SDK，将配置信息透传给 IM SDK。无需再次初始化 IM SDK
        IMKitClient.instance.setupIM2(option, v2Option)
        ```
        :::
        ::: code Objective-C
        ```Objective-C
        // 设置 IM SDK 的配置项，包括 AppKey，推送配置和一些全局配置等
        NIMSDKOption *option = [NIMSDKOption optionWithAppKey:@"USER_APP_KEY"];
        option.apnsCername = @"USER_APNS_CERNAME";
        option.pkCername = @"USER_PUSHKIT_CERNAME";

        // 设置 IM SDK V2 的配置项，包括是否使用旧的登录接口和是否使用云端会话
        V2NIMSDKOption *v2Option = [[V2NIMSDKOption alloc] init];
        v2Option.enableV2CloudConversation = NO;

        // 初始化 IM UIKit，初始化 Kit 层和 IM SDK，将配置信息透传给 IM SDK。无需再次初始化 IM SDK
        [IMKitClient.instance setupIM2:option :v2Option];
        ```
        :::
        ::::::


## 下一步

为保障通信安全，如果您在调试环境中的使用的是 网易云信控制台 生成的 IM 账号（测试用），请确保在后续的正式生产环境中，将其替换为通过 IM 服务端 API 生成的正式 IM 账号。
