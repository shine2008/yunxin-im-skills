# IM UIKit 登录接入指引

## 资源标识

- resource_id: `imkit.integration.login.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于把 IM 登录逻辑接入启动页、登录页或主页面。

在总控流程里，它通常作为第五步使用。

## 适用场景

当 IM UIKit 已初始化，需要在启动页、登录页或主页面中接入 `IMKitClient.login(...)` 登录逻辑时，读取本文件。

## 相关导入

```java
import com.netease.yunxin.kit.corekit.im2.IMKitClient;
import com.netease.yunxin.kit.corekit.im2.extend.FetchCallback;
```

## 输入采集

### 必问信息

1. 登录逻辑放在哪个页面：
   - `SplashActivity`
   - `LoginActivity`
   - `MainActivity`
   - 其他页面
2. 登录逻辑在哪个方法触发：
   - `onCreate()`
   - `onResume()`
   - 按钮点击回调
   - 其他方法
3. 登录成功后是否跳转：
   - 跳转 `MainActivity`
   - 跳转会话列表页
   - 跳转其他页面
   - 不跳转
4. 是否需要登出逻辑：
   - 需要
   - 不需要

### 输出变量

- `TARGET_PAGE`
- `TARGET_METHOD`
- `SUCCESS_TARGET_CLASS`
- `NEED_LOGOUT`

## 执行步骤

### 1. 定位目标页面文件

- 搜索 `TARGET_PAGE.java` 或 `TARGET_PAGE.kt`
- 先了解当前页面是否已有登录相关逻辑

### 2. 在 `TARGET_METHOD` 中加入登录调用

```java
loginIM(accountId, token);
```

### 3. 新增登录方法

```java
private void loginIM(String accountId, String token) {
    V2NIMLoginOption option = new V2NIMLoginOption();
    IMKitClient.login(accountId, token, option, new FetchCallback<Void>() {
        @Override
        public void onError(int errorCode, @NonNull String errorMsg) {
            Log.e("IMLogin", "登录失败: " + errorCode + ", " + errorMsg);
        }

        @Override
        public void onSuccess(@Nullable Void data) {
        }
    });
}
```

### 4. 登录成功后的处理

- 若用户要求跳转，则在 `onSuccess` 中补充页面跳转
- 若不跳转，则保留成功回调供当前页面继续处理

### 5. 询问并按需补充登出逻辑

- 在完成登录接入后，询问用户是否需要登出
- 若用户选择需要，则输出并接入以下登出代码

```java
IMKitClient.logout(
    new FetchCallback<Void>() {
      @Override
      public void onError(int errorCode, @NonNull String errorMsg) {
        
      }

      @Override
      public void onSuccess(@Nullable Void data) {
        
      }
    });
```

## 生成代码时的要求

- 不要把账号和 token 永久硬编码在生产逻辑里
- 若用户仅用于调试，可以保留临时代码，但应明确说明
- Kotlin 文件使用等价 Kotlin 语法
- 若页面已有登录按钮，优先复用现有按钮和输入框
- 写入前优先核对当前 SDK 实际方法签名，尤其是 `V2NIMLoginOption` 包路径、`FetchCallback` 泛型和 `onSuccess/onError` 参数签名
- 若项目已能解析 aar 或源码，优先依据真实类路径生成代码，不要只依赖模板猜测

## 完成后的输出

- 说明登录逻辑写到了哪个页面和方法
- 说明成功后的跳转行为
- 说明是否已补充登出逻辑
- 若处于总控流程中，提示是否继续下一环节“界面集成”
