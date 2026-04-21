[//]: # (关键词: iOS, IM UIKit, 常见问题, FAQ, 未读数, 自定义消息, 消息拦截, 模拟器运行)
# IM UIKit iOS 常见问题（FAQ）

## 未读数角标：如何实现底部栏未读数角标

目前 iOS UIKit 会话列表左下角有未读数时只有红点提示，并没有显示具体未读数量，可以通过以下代码修改成未读数量展示。

以展示通讯录未读数为例，在 NETabBarController 的 setUpContactBadgeValue 方法中补充以下示例代码：

```swift
func setUpContactBadgeValue() {
    ContactRepo.shared.getUnreadApplicationCount { [self] unreadCount, error in
        contactUnreadCount = unreadCount
        
//            // 显示红点
//            if unreadCount > 0 {
//                tabBar.showBadgOn(index: 1, tabbarItemNums: 3)
//            } else {
//                tabBar.hideBadg(on: 1)
//            }
        
        // 显示未读数
        if unreadCount > 0 {
            if unreadCount > 99 {
                tabBar.setServerBadge(count: "99+")
            } else {
                tabBar.setServerBadge(count: "\(unreadCount)")
            }
        } else {
            tabBar.setServerBadge(count: nil)
        }
    }
}
```

## 自定义消息：如何调整自定义消息高度

- 方案一：在 tableView(_ tableView: heightForRowAt:) 渲染之前修改。
    协同版（NormalChatViewController）和通用版（FunChatViewController）在 heightForRowAt 之前均会在 getMessageModel 方法中对消息模型的高度进行再次计算，如示例代码所示：

    ```swift
    open func getMessageModel(model: MessageModel) {
        if model.type == .reply {
            let normalMoreHeight = chat_reply_height + chat_content_margin
            model.contentSize = CGSize(
                width: model.contentSize.width,
                height: model.contentSize.height + normalMoreHeight
            )
            model.height += normalMoreHeight
        }
    }
    ```
    以上代码为协同版对【回复消息】的高度进行了再次计算，同理，自定义消息的高度也可以在此处进行修改。

- 方案二：在发消息时，直接对 cell 的 height 进行设置。

    v9 设置 cellHeight
    ```swift
    let data = ["type": customMessageType]
    let attachment = CustomAttachment(customType: customMessageType, cellHeight: 50, data: data)
    let message = NIMMessage()
    let object = NIMCustomObject()
    object.attachment = attachment
    message.messageObject = object
    ...
    // 调用发送消息接口
    ```
    v9 设置 cellHeight 后，还需要在自定义消息解析器中对消息进行解析，参考如下代码：
    ```swift
override public func decodeCustomMessage(info: [String: Any]) -> CustomAttachment {
        // 自定义反序列化方法之前必须调用父类的反序列化方法
        let neCustomAttachment = super.decodeCustomMessage(info: info)
        let customAttachment = CustomAttachment(customType: neCustomAttachment.customType,
                                                cellHeight: neCustomAttachment.cellHeight,
                                                data: neCustomAttachment.data)
        // 解析自定义消息高度
        if customAttachment.customType == 20 {
            customAttachment.cellHeight = 50
        }
        ...

        return customAttachment
    }
```

    v10 直接设置 customHeight
    ```swift
    // type 自定义消息类型，该字段必须指定，且不可为 101、102（UIKit 内部已使用），否则解析为【未知消息体】
    // customHeight 自定义消息的高度
    let dataDic: [String: Any] = ["type": customMessageType, "customHeight": 100]
    let dataJson = NECommonUtil.getJSONStringFromDictionary(dataDic)
    let customMessage = MessageUtils.customMessage(text: "this is a custom message, create time:\(Date.timeIntervalSinceReferenceDate)",
                                                   rawAttachment: dataJson)
    ...
    // 调用发送消息接口
    ```

## 消息拦截：如何在消息发送前后拦截消息

请参考以下方法通过配置项实现消息拦截。

### 注意事项
- 自定义代码需放在消息发送之前，建议放在 **application(_:didFinishLaunchingWithOptions:)** 中。
- 重写 **beforeSend** 与 **beforeSendCompletion** 方法不会生效。
- 完整的实例代码参考：`Custom/CustomConfig.swift`。

- 消息发送前拦截

    可在回调中更改消息内容，或者拦截特定消息。

    ```swift
    /// 消息列表发送消息前的回调
    /// 回调参数：消息参数（包含消息和发送参数）和消息列表的视图控制器
    /// 返回值/回调值：（修改后的）消息参数，若消息参数为 nil，则表示拦截该消息不发送
    /// beforeSend 与 beforeSendCompletion 只能二设一，同时设置时优先使用 beforeSend
    ChatKitClient.shared.beforeSend = { viewController, param in
        param.message.text = (param.message.text ?? "") + "[拦截1]"
        return param
    }

    ChatKitClient.shared.beforeSendCompletion = { viewController, param, completion in
        DispatchQueue.main.asyncAfter(deadline: .now() + 1, execute: DispatchWorkItem(block: {
            param.message.text = (param.message.text ?? "") + "[拦截2]"
            completion(param)
        }))
    }
```

- 消息发送后拦截

可在回调中获取消息反垃圾结果。

```swift
    /// 本端发送消息后的回调，为 sendMessage 接口callback，可在回调中获取消息反垃圾结果
    /// - Parameter completion: sendMessage 接口调用回调
    ChatKitClient.shared.sendMessageCallback = { viewController, result, error, progress in
        if let antispamResult = result?.antispamResult {
            viewController.showToast("反垃圾结果：\(antispamResult)")
        }
    }
```

## 环境配置：如何在模拟器（Apple 芯片）上运行

IM iOS UIKit 在 Intel 芯片中可直接运行。因此这里主要介绍如何在 Apple 芯片机器上使用模拟器运行云信 IM demo。

### 注意事项
由于部分三方库未适配 arm64 架构，因此模拟器运行时需移除 arm64 架构。

1. 在 Podfile 文件末尾添加以下代码，然后重新执行 `pod install`。

    ```ruby
    # Apple 芯片 系列模拟器运行打开以下代码
    post_install do |installer|
        installer.pods_project.targets.each do |target|
            target.build_configurations.each do |config|
                config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"
            end
        end
    end
```



2. 在工程配置中 **移除 arm64 架构** 或者 **使用 Rosetta 模拟器**。

    :::::: div linked-codes
    ::: code 移除 arm64 架构

    :::
    ::: code 使用 Rosetta 模拟器
    指定 Rosetta 模拟器运行云信 IM Demo。
