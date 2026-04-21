# IM UIKit Flutter 会话列表界面集成指南


本文档详细介绍如何在Flutter应用中集成IM UIKit的会话列表界面及其交互逻辑。


## 1. 初始化 nim_conversationkit_ui

使用 `nim_conversationkit_ui` 之前，需调用 `ConversationKitClient.init` 方法对该模块进行初始化，以保证该模块功能的正常使用。初始化方法全局只需要调用一次，建议您在main.dart 文件中调用。

::: note notice 
初始化需要在 IM UIKit 界面加载之前进行。
:::

示例代码如下：

```dart
// 实例化 ConversationPage 跳转示例
// 初始化 nim_conversationkit_ui 模块
void main() {

  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);


  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    ConversationKitClient.init();
    // ...
  }
}
```

## 2. 集成会话列表界面

会话页面在 `ConversationPage` 类中实现，接入之前请确保已经引入 `nim_conversationkit_ui` 依赖。

```yaml
# 添加 nim_conversationkit_ui 依赖
dependencies:
    nim_conversationkit_ui: ^10.0.0
```

### 2.1 命名路由直接跳转

您可以使用命名路由直接跳转到 `ConversationPage`：

```dart
Navigator.pushNamed(context, RouterConstants.PATH_CONVERSATION_PAGE);
```

### 2.2 实例化 ConversationPage 跳转

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) => ConversationPage()));
```

- **ConverationPage 实例化参数**

```dart
ConversationPage({Key? key, this.config, this.onUnreadCountChanged})
```

- **ConversationPage 参数说明**  

| 参数 | 类型 | 说明 |
| ---- | ---- | ---- |
| `context` | BuildContext | 上下文 |
| `config` | ConversationUIConfig | 页面配置（可选） |
| `onUnreadCountChanged` | ValueChanged\<int> | 未读数变化更新通知（可选） |