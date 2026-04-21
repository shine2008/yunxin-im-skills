

# IM UIKit iOS 通讯录界面集成指南

本文主要介绍如何构建通讯录界面。IM UIKit 提供基于 UITableview 实现的通讯录界面，其类名为 `ContactViewController`，并提供多个分组。通讯录界面使用简单，无需外部传参，使用时直接实例化即可。

同时 IM UIKit 还支持从其他界面跳转至通讯录界面以及跳转到对应的信息名片页。

## 方法原型（界面跳转）
:::::: div custom-tabs
::: tab Swift
```swift
// 跳转通讯录页面
let contactVC = ContactViewController()
self.navigationController?.pushViewController(userInfoVC, animated: true)

// 跳转信息名片页
let user = NEUserWithFriend(user: nim_user)
let userInfoVC = ContactUserViewController(user: user)
self.navigationController?.pushViewController(userInfoVC, animated: true)
```
:::
::: tab Objective-C
```objc
// 跳转通讯录页面
ContactViewController* contactVC = [[ContactViewController alloc] init];
[self.navigationController pushViewController:userInfoVC animated:YES];

// 跳转信息名片页
NEUserWithFriend *user = [[NEUserWithFriend alloc] initWithUser: nim_user];
ContactUserViewController *userInfoVC = [[ContactUserViewController alloc] initWithUser:user];
[self.navigationController pushViewController:userInfoVC animated:YES];
```
:::
::::::

## 参数说明

| 参数 | 类型 | 说明 |
| ---- | ---- | ---- |
| nim_user | `V2NIMUser` | 网易云信用户，包含用户 ID 等信息。 |

## 代码示例（通讯录界面集成）

:::::: div custom-tabs
::: tab Swift
```swift
let contactVC = ContactViewController()
let contactsNav = NENavigationController(rootViewController: contactVC)
```
:::
::: tab Objective-C
```objc
ContactViewController *contactVC = [[ContactViewController alloc] init];
UINavigationController *contactsNav = [[UINavigationController alloc] initWithRootViewController:contactVC];
```
:::
::::::