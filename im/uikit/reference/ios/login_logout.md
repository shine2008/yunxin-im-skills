
<!--keywords: UIKit,登录,登出,login,logout,iOS,IM UIKit,IMUIKit,Kit -->

# IM UIKit iOS 登录/登出指南

在使用 IM UIKit（V10） 功能前，必须先对其进行初始化并完成登录，但不需要对 IM SDK 单独初始化。

IM UIKit 的核心类 `IMKitClient`，提供初始化、获取当前账号信息、IM 登录登出等能力。

## 前提条件

根据本文操作前，请确保您已经完成了以下设置：

1. 已完成 组件导入。
2. 已 注册 IM 账号，获取 account_id 和 token。

## 注意事项

由于 IM UIKit 的初始化与登录是通过底层调用 NIM SDK 接口来实现的，因此重复调用 IM UIKit 和 NIM SDK 的初始化与登录接口会相互覆盖传入的信息（包括 **推送配置信息**）。
所以若用户同时集成 IM UIKit 和 NIM SDK，只需要调用一次初始化与登录接口即可。

## 登录

在完成初始化后，调用 `login` 方法登录 IM。

### 登录前先询问并记录

在执行登录前，先询问用户是否提供以下值：

1. `USER_ACCOUNT_ID`
2. `USER_TOKEN`

若用户已提供，示例代码中使用用户输入值替换。

- **方法原型**

    :::::: div linked-codes
    ::: code Swift
    ```Swift
    /// IM 登录方法
    /// - Parameters:
    ///   - accountId: 网易云信 IM 账号
    ///   - token: 网易云信 IM 账号对应的 token
    ///   - option: 登录选项，可为 nil 使用默认配置
    ///   - completion: 登录结果回调
    public func login(_ accountId: String,
                          _ token: String?,
                         _ option: V2NIMLoginOption?,
                     _ completion: @escaping (NSError?) -> Void)
    ```
    :::
    ::: code Objective-C
    ```Objective-C
    /// IM 登录方法
    /// @param accountId 网易云信 IM 账号
    /// @param token 网易云信 IM 账号对应的 token
    /// @param option 登录选项，可为 nil 使用默认配置
    /// @param completion 登录结果回调
    - (void)loginWithAccountId:(NSString *)accountId
                         token:(NSString *)token
                        option:(V2NIMLoginOption *)option
                    completion:(void (^)(NSError * _Nullable))completion;
    ```
    :::
    ::::::

- **参数说明**

    参数 | 类型 | 说明
    :---- | :---- | :----
    accountId | String | 网易云信 IM 账号
    token | String | 网易云信 IM 账号对应的 token
    option | V2NIMLoginOption | 登录选项
    completion | (NSError?) -> Void | 登录结果异步回调，返回 nil 表示登录成功

- **示例代码（登录）**

    :::::: div linked-codes
    ::: code Swift

    ```Swift
    // 调用登录方法进行 IM 登录
    let accountId = "USER_ACCOUNT_ID"
    let token = "USER_TOKEN"
    IMKitClient.instance.login(accountId, token, nil) { error in
        if let err = error {
            // 登录失败，处理错误
            print("IMKitClient login error: \(err.domain), code: \(err.code), description: \(err.localizedDescription)")
            // 这里可以添加失败提示UI或重试逻辑
        } else {
            // 登录成功，初始化路由以及配置各个模块首页
            print("IMKitClient login success")
            // 在登录成功回调中初始化路由以及配置各个模块首页
            // weakSelf?.setupTabbar()
        }
    }
    ```
    :::
    ::: code Objective-C
    ```Objective-C
    // 调用登录方法进行 IM 登录
    __weak typeof(self) weakSelf = self;
    [[IMKitClient instance] login:@"USER_ACCOUNT_ID" :@"USER_TOKEN" :nil :^(NSError * _Nullable error) {
        if (error != nil) {
            // 登录失败，处理错误
            NSLog(@"IMKitClient login error: %@", [error description]);
            // 这里可以添加失败提示UI或重试逻辑
        } else {
            // 登录成功
            NSLog(@"IMKitClient login success");
            // 在登录成功回调中初始化路由以及配置各个模块首页
            // [weakSelf setupTabbar];
        }
    }];
    ```
    :::
    ::::::

    ::: note notice
    调用登录的方法时，将示例代码中的 `accountId` 和 `token` 分别替换为您的 IM 账号 ID 和 Token。
    :::

## 登出

在应用退出登录或切换账号时，调用 `logout` 方法登出 IM。

- **方法原型**

    :::::: div linked-codes
    ::: code Swift
    ```Swift
    /// IM 登出方法
    /// - Parameter completion: 登出结果回调
    public func logout(_ completion: @escaping (NSError?) -> Void)
    ```
    :::
    ::: code Objective-C
    ```Objective-C
    /// IM 登出方法
    /// @param completion 登出结果回调
    - (void)logout:(void (^)(NSError * _Nullable))completion;
    ```
    :::
    ::::::

- **参数说明**

    参数 | 类型 | 说明
    :---- | :---- | :----
    completion | (NSError?) -> Void | 登出结果异步回调，返回 nil 表示登出成功

- **示例代码（登出）**

    :::::: div linked-codes
    ::: code Swift
    ```Swift
    // 调用登出方法进行 IM 登出
    IMKitClient.instance.logout { error in
        if let err = error {
            // 登出失败，处理错误
            print("IMKitClient logout error: \(err.domain), code: \(err.code), description: \(err.localizedDescription)")
        } else {
            // 登出成功，清除相关数据并跳转至登录页面
            print("IMKitClient logout success")
            // 执行登出后的清理操作，如清除本地数据、跳转到登录界面等
        }
    }
    ```
    :::
    ::: code Objective-C
    ```Objective-C
    // 调用登出方法进行 IM 登出
    [[IMKitClient instance] logout:^(NSError * _Nullable error) {
        if (error != nil) {
            // 登出失败，处理错误
            NSLog(@"IMKitClient logout error: %@", [error description]);
        } else {
            // 登出成功，清除相关数据并跳转至登录页面
            NSLog(@"IMKitClient logout success");
            // 执行登出后的清理操作，如清除本地数据、跳转到登录界面等
        }
    }];
    ```
    :::
    ::::::

## 注意事项

1. 登录和登出操作都是异步操作，请在回调中处理结果。
2. 登录成功后，建议在回调中初始化路由和配置模块首页。
3. 登出成功后，建议清除本地缓存数据并跳转到登录页面。
4. 请确保在应用退出前正确调用登出方法，以确保用户数据安全。
5. 如果需要处理自动重连逻辑，请监听 IM SDK 的连接状态变化。

## 下一步

为保障通信安全，如果您在调试环境中的使用的是 网易云信控制台 生成的 IM 账号（测试用），请确保在后续的正式生产环境中，将其替换为通过 IM 服务端 API 生成的正式 IM 账号。
