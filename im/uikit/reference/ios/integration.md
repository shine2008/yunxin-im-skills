# IM UIKit iOS 集成指南

## 集成方式说明
网易云信即时通讯 iOS 版 V10 系列 UI Kit 采用 CocoaPods 进行构建，您可以采用添加远端仓库依赖或者本地代码依赖的方式（同一个组件库只能选择一种依赖方式），将所需组件快速导入到您的项目中。

- **添加远端仓库依赖**：适用于采用 IM UIKit 默认界面的集成。
- **添加本地代码依赖**：适用于通过 IM UIKit 进行界面自定义的集成。

## 前提条件

已准备如下开发环境/工具：

- iOS 13.0 及以上版本
- Xcode 11.0 及以上版本

<a id="import"></a>

## 导入组件

您可通过添加远端仓库依赖或者添加本地代码依赖，导入组件。

### 添加远端仓库依赖

本节介绍如何通过 CocoaPods 添加远程依赖，将您业务所需的的 UI 组件导入到您的项目，进行项目构建。

1. 创建 Podfile 文件，并在 Podfile 文件中引入 UI 组件。

    例如，需要会话聊天功能，可添加 `pod 'NEChatUIKit'` 和 `pod 'NEChatKit'`。

    :::note note
    IM UIKit 会依赖少量三方库。
    - 若引入 UI 组件时不指定三方库版本，那么将默认引入当前兼容的最新版本（参考 更新日志）。
    - 若引入 UI 组件时需要指定三方库的版本，那么需要在引入时填写正确的版本号，具体请参考以下示例代码。示例代码中的 10.8.6 为版本号，仅用于示例。建议使用最新版本。
    :::

    :::::: div linked-codes
    ::: code 不指定三方库版本
    ```Swift
    # Uncomment the next line to define a global platform for your project
    platform :ios, '13.0'

    # 请使用您的真实项目名称替换 your project name
    target 'your project name' do
    # Comment the next line if you don't want to use dynamic frameworks
    use_frameworks!

    # 基础库
    pod 'NEChatKit', '10.8.6'

    # UI 组件
    pod 'NEChatUIKit', '10.8.6'               # 会话（聊天）组件
    pod 'NEContactUIKit', '10.8.6'            # 通讯录组件
    pod 'NEConversationUIKit', '10.8.6'       # 云端会话列表组件。如果使用云端会话，则不需要依赖下面本地会话列表组件，与本地会话组件二者选其一
    pod 'NELocalConversationUIKit', '10.8.6'  # 本地会话列表组件,。如果使用本地会话，则不需要依赖上面云端会话列表组件，与云端会话组件二者选其一
    pod 'NETeamUIKit', '10.8.6'               # 群相关设置组件

    # 扩展库-地理位置组件
    pod 'NEMapKit', '10.8.6'

    # 扩展库 - AI 划词搜索
    pod 'NEAISearchKit', '10.8.6'

    # 扩展库 - 呼叫组件
    pod 'NERtcSDK/RtcBasic'                   #  RTC 音视频基础组件
    pod 'NERtcSDK/Nenn'                       #  RTC 音视频神经网络组件（使用背景虚化功能需要集成）
    pod 'NERtcSDK/Segment'                    #  RTC 音视频背景分割组件（使用背景虚化功能需要集成）
    pod 'NERtcCallKit', '3.5.0'
    pod 'NERtcCallUIKit', '3.5.0'             # (源码地址：NERtcCallUIKit 源码)

    end
    ```
    :::
    ::: code 指定三方库版本
    ```Swift
    # Uncomment the next line to define a global platform for your project
    platform :ios, '13.0'

    # 请使用您的真实项目名称替换 your project name
    target 'your project name' do
    # Comment the next line if you don't want to use dynamic frameworks
    use_frameworks!

    # 以指定 NIMSDK 版本为例，其他未指定版本的三方库默认拉取最新版
    pod 'NIMSDK_LITE','10.2.6-beta'

    # 基础库
    pod 'NEChatKit/NOS_Special', '10.8.6'

    # UI 组件
    pod 'NEChatUIKit/NOS_Special', '10.8.6'               # 会话（聊天）组件
    pod 'NEContactUIKit/NOS_Special', '10.8.6'            # 通讯录组件
    pod 'NEConversationUIKit/NOS_Special', '10.8.6'       # 云端会话列表组件。如果使用云端会话，则不需要依赖下面本地会话列表组件，与本地会话组件二者选其一
    pod 'NELocalConversationUIKit/NOS_Special', '10.8.6'  # 本地会话列表组件,。如果使用本地会话，则不需要依赖上面云端会话列表组件，与云端会话组件二者选其一
    pod 'NETeamUIKit/NOS_Special', '10.8.6'               # 群相关设置组件

    # 扩展库-地理位置组件
    pod 'NEMapKit/NOS_Special', '10.8.6'

    # 扩展库 - AI 划词搜索
    pod 'NEAISearchKit/NOS_Special', '10.8.6'

    # 扩展库 - 呼叫组件
    pod 'NERtcSDK/RtcBasic'                   #  RTC 音视频基础组件
    pod 'NERtcSDK/Nenn'                       #  RTC 音视频神经网络组件（使用背景虚化功能需要集成）
    pod 'NERtcSDK/Segment'                    #  RTC 音视频背景分割组件（使用背景虚化功能需要集成）
    pod 'NERtcCallKit/NOS_Special', '3.5.0'
    pod 'NERtcCallUIKit/NOS_Special', '3.5.0' # (源码地址：NERtcCallUIKit 源码)

    end
    ```
    :::
    ::: code （海外数据中心）不指定三方库版本
    ```Swift
    # Uncomment the next line to define a global platform for your project
    platform :ios, '13.0'

    # 请使用您的真实项目名称替换 your project name
    target 'your project name' do
    # Comment the next line if you don't want to use dynamic frameworks
    use_frameworks!

    # 基础库
    pod 'NEChatKit/FCS', '10.8.6'

    # UI 组件
    pod 'NEChatUIKit/FCS', '10.8.6'               # 会话（聊天）组件
    pod 'NEContactUIKit/FCS', '10.8.6'            # 通讯录组件
    pod 'NEConversationUIKit/FCS', '10.8.6'       # 云端会话列表组件。如果使用云端会话，则不需要依赖下面本地会话列表组件，与本地会话组件二者选其一
    pod 'NELocalConversationUIKit/FCS', '10.8.6'  # 本地会话列表组件,。如果使用本地会话，则不需要依赖上面云端会话列表组件，与云端会话组件二者选其一
    pod 'NETeamUIKit/FCS', '10.8.6'               # 群相关设置组件

    # 扩展库-地理位置组件
    pod 'NEMapKit/FCS', '10.8.6'

    # 扩展库 - AI 划词搜索
    pod 'NEAISearchKit/FCS', '10.8.6'

    # 扩展库 - 呼叫组件
    pod 'NERtcSDK/RtcBasic'                   #  RTC 音视频基础组件
    pod 'NERtcSDK/Nenn'                       #  RTC 音视频神经网络组件（使用背景虚化功能需要集成）
    pod 'NERtcSDK/Segment'                    #  RTC 音视频背景分割组件（使用背景虚化功能需要集成）
    pod 'NERtcCallKit/FCS', '3.5.0'
    pod 'NERtcCallUIKit/FCS', '3.5.0'         # (源码地址：NERtcCallUIKit 源码)

    end
    ```
    :::
    ::: code （海外数据中心）指定三方库版本
    ```Swift
    # Uncomment the next line to define a global platform for your project
    platform :ios, '13.0'

    # 请使用您的真实项目名称替换 your project name
    target 'your project name' do
    # Comment the next line if you don't want to use dynamic frameworks
    use_frameworks!

    # 以指定  版本为例，其他未指定版本的三方库默认拉取最新版
    pod 'NIMSDK_LITE/FCS','10.2.6-beta'

    # 基础库
    pod 'NEChatKit/FCS_Special', '10.8.6'

    # UI 组件
    pod 'NEChatUIKit/FCS_Special', '10.8.6'               # 会话（聊天）组件
    pod 'NEContactUIKit/FCS_Special', '10.8.6'            # 通讯录组件
    pod 'NEConversationUIKit/FCS_Special', '10.8.6'       # 云端会话列表组件。如果使用云端会话，则不需要依赖下面本地会话列表组件，与本地会话组件二者选其一
    pod 'NELocalConversationUIKit/FCS_Special', '10.8.6'  # 本地会话列表组件,。如果使用本地会话，则不需要依赖上面云端会话列表组件，与云端会话组件二者选其一
    pod 'NETeamUIKit/FCS_Special', '10.8.6'               # 群相关设置组件

    # 扩展库-地理位置组件
    pod 'NEMapKit/FCS_Special', '10.8.6'

    # 扩展库 - AI 划词搜索
    pod 'NEAISearchKit/FCS_Special', '10.8.6'

    # 扩展库 - 呼叫组件
    pod 'NERtcSDK/RtcBasic'                   #  RTC 音视频基础组件
    pod 'NERtcSDK/Nenn'                       #  RTC 音视频神经网络组件（使用背景虚化功能需要集成）
    pod 'NERtcSDK/Segment'                    #  RTC 音视频背景分割组件（使用背景虚化功能需要集成）
    pod 'NERtcCallKit/FCS_Special', '3.5.0'
    pod 'NERtcCallUIKit/FCS_Special', '3.5.0' # (源码地址：NERtcCallUIKit 源码)

    end
    ```
    :::
    ::::::

2. 执行以下命令导入组件。

    ```CocoaPods
    pod install
    ```

    ::: note notice
    - 各组件相互独立，添加或删除均不影响项目编译。
    - 暂不支持 bitcode。
    - 如果出现类似 **版本不存在** 的报错，可执行 `pod update` 命令，然后双击 `.xcworkspace` 文件，启动项目即可。
    - 如果需要在 Objective-C 项目中导入组件，头文件引用请参考以下引用方式。

        ```Objective-C
        #import <NEConversationUIKit/NEConversationUIKit-Swift.h>
        #import <NEContactUIKit/NEContactUIKit-Swift.h>
        #import <NEChatUIKit/NEChatUIKit-Swift.h>
        #import <NETeamUIKit/NETeamUIKit-Swift.h>
        #import <NERtcCallUIKit/NERtcCallUIKit.h>
        ...
        ```
    :::

### 添加本地代码依赖

本节介绍如何通过 CocoaPods 添加本地依赖，将所需的 IM UIKit 源码导入到您的项目。

1. 前往 网易云信开源代码仓库，下载开源的 IM UIKit 到本地，然后将源码文件夹拷贝到项目目录。
2. 将上述远端仓库依赖的 Podfile 文件中的对应库改为本地依赖，例如将 UI 组件和地图组件改为本地依赖只需更改以下内容（其他组件）：

    ```CocoaPods
    # 源码依赖时如果需要指定 NIM SDK 版本（Special），建议同样在 podspec 中指定基础库版本
    pod 'NEContactUIKit', :path => 'NEContactUIKit/NEContactUIKit.podspec'
    pod 'NELocalConversationUIKit', :path => 'NELocalConversationUIKit/NELocalConversationUIKit.podspec'
    /* 
       云端会话组件（`NEConversationUIKit`）和本地会话组件（`NELocalConversationUIKit`），二者请选择其一
       pod 'NEConversationUIKit', :path => 'NEConversationUIKit/NEConversationUIKit.podspec'
    */    
    pod 'NETeamUIKit', :path => 'NETeamUIKit/NETeamUIKit.podspec'
    pod 'NEChatUIKit', :path => 'NEChatUIKit/NEChatUIKit.podspec'
    pod 'NEMapKit', :path => 'NEMapKit/NEMapKit.podspec'
    ```

    :::note notice
    - 上述内容中的 `path` 为相对路径，即当对应组件源码文件与 Podfile 文件处于同级目录时，才能通过上述相对路径正确引入组件。
    - 自 V10.5.3 起，UIKit 不再包含呼叫组件源码，若需要依赖呼叫组件，请单独下载 呼叫组件源码。
    - 上述云端会话组件（`NEConversationUIKit`）和本地会话组件（`NELocalConversationUIKit`），二者请选择其一。
    :::

3. 执行以下命令导入 IM UIKit 源码。

    ```CocoaPods
    pod install
    ```

4. （可选）处理冗余文件，减少资源引入。

    网易云信 IM UIKit 提供两套风格的 UI 组件库，可任意选择一种使用。确定后，可移除另一套 UI 组件相关文件，以减小包体积。

   **若确定只集成通用版 UI 组件，则可以删除各 Kit 下的基础版 UI 相关文件。此处以 `NEChatUIKit` 为例，其他 Kit 组件同理。**

    即可删除 `NEChatUIKit` 下的 `NormalChatUIKit.xcassets` 和 `NormalUI` 文件。

    - `NEChatUIKit` 引入的资源结构如下：

        ```
        //每个 Kit 下包含三分资源文件
        NEChatUIKit
        └── NEBaseChatUIKit.xcassets // 公共资源文件
        └── NormalChatUIKit.xcassets //基础版 UI 资源文件
        └── FunChatUIKit.xcassets // 通用版 UI 资源文件
        ```

    - 引入的代码文件中，NormalUI 和 FunUI 文件夹分别用于存放所有与基础版 UI 相关的文件和所有与通用版 UI 相关的文件。

        源码结构图示

## 下一步

请按以下顺序继续执行：

1. 第二步：执行 `initialization.md`，完成 UIKit 初始化
2. 第三步：执行 `login_logout.md`，完成登录/登出接入
3. 第四步：执行 `conversation.md`，完成会话列表界面集成
