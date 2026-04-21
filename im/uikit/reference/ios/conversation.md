# IM UIKit iOS 会话列表界面集成指南

网易云信 IM UIKit 支持本地会话组件（`NELocalConversationUIKit`）和云端会话组件（`NEConversationUIKit`），在其他组件中需要根据您当前使用到的会话列表组件来进行对应的功能。

IM UIKit 提供两种方式使用会话列表界面：

1. 直接使用 `ConversationController`/`LocalConversationController` 构建默认的 UI 界面。
2. 继承 `ConversationController`/`LocalConversationController` 实现自定义 UI 界面。

## 开始前先询问用户

1. 会话模式：
   - 本地会话
   - 云端会话
2. 当前工程语言环境：
   - Swift
   - Objective-C / Objective-C++

::: note note 
- **`ConversationController`** 为包含导航栏的云端会话类；**`LocalConversationController`** 为包含导航栏的本地会话类。
- 会话列表界面 UI 个性化定制的相关说明，请参考 自定义会话列表界面 UI。
:::


## 前提条件

根据本文操作前，请确保您已经完成以下操作：

1. 实现 登录。

## 执行流程（MCP）

1. 先根据用户输入确定本地会话或云端会话。
2. 若用户选择云端会话，先检查工程中是否已有初始化代码，并定位以下语句：

```Swift
let v2Option = V2NIMSDKOption()
```

```Objective-C
V2NIMSDKOption *v2Option = [[V2NIMSDKOption alloc] init];
```

3. 在上述初始化语句后，确保存在：

```Swift
v2Option.enableV2CloudConversation = true
```

```Objective-C
v2Option.enableV2CloudConversation = YES;
```

## 代码示例（继承实现自定义会话列表界面）

:::::: div custom-tabs
::: tab 本地会话
**Swift：**

```Swift
open class CustomLocalConversationController: LocalConversationController, NEBaseLocalConversationControllerDelegate {
    override public init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: Bundle?) {
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
        delegate = self
        ...
    }

    public required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
}
```
**Objective-C：**

```Objective-C
@interface CustomConversationController : ConversationController <NEBaseConversationControllerDelegate>
@end

@implementation CustomConversationController

- (instancetype)init {
    self = [super init];
    if (self) {
        self.delegate = self;
    }
    return self;
}

@end
```
:::
::: tab 云端会话
**Swift：**

```Swift
open class CustomConversationController: ConversationController, NEBaseConversationControllerDelegate {
    override public init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: Bundle?) {
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
        delegate = self
        ...
    }

    public required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
}
```
