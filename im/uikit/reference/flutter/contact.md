# IM UIKit Flutter 通讯录界面集成指南


本文档详细介绍如何在Flutter应用中集成IM UIKit的通讯录界面，支持个性化定制UI界面。


## 1. 集成通讯录界面

通讯录页面在 `ContactPage` 类中实现,接入之前请确保已经引入 `nim_contactkit_ui` 依赖。

```yaml
dependencies:
  nim_contactkit_ui: ^1.0.0-rc.0
```

### 1.1 初始化 nim_contactkit_ui

使用 `nim_contactkit_ui` 之前，需调用 `ContactKitClient.init` 方法对该模块进行初始化，以保证该模块功能的正常使用。初始化方法全局只需要调用一次，建议您在 main.dart 文件中调用。

::: note notice 
初始化需要在 IM UIKit 界面加载之前进行。
:::

示例代码如下：

```dart
// 初始化 nim_contactkit_ui 模块
void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    ContactKitClient.init();
    // ...
  }
}
```

### 1.2 命名路由直接跳转

您可以使用命名路由直接跳转到 `ContactPage`：

```dart
Navigator.pushNamed(context, RouterConstants.PATH_CONTACT_PAGE);
```

### 1.3 实例化 ContactPage 跳转

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) => ContactPage()));
```

- **ContactPage 实例化参数**

```dart
ContactPage({Key? key, this.config})
```

- **ContactPage 参数说明**  

参数 | 类型 | 是否必填 | 说明 |
---- | ---- | ---- | ---- | 
`config` | ContactUIConfig | 否 | 页面配置。