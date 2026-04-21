# IM UIKit Android 聊天界面集成

## 适用范围

用于 Android 聊天集成（路由跳转 / 新建 Activity + Fragment）。

## 前置条件（必须满足）

为保证运行成功，在集成聊天页面前必须先完成：

1. 已完成 UIKit 组件导入（参考 `import_components.md`）
2. 已在启动页面之前完成 UIKit 初始化（参考 `initialization.md`）
3. 已完成 IM 登录（参考 `login_logout.md`）

## 开始前询问用户

1. 集成方式：
   - 路由跳转
   - 新建 Activity + Fragment
2. UI 风格：
   - 普通版本
   - 娱乐风格
3. 聊天类型：
   - 单聊
   - 群聊

## 相关导入

```java
import com.netease.yunxin.kit.corekit.route.XKitRouter;
import com.netease.yunxin.kit.corekit.im2.utils.RouterConstant;
import com.netease.yunxin.kit.chatkit.ui.fun.page.fragment.FunChatTeamFragment;
import com.netease.yunxin.kit.chatkit.ui.fun.page.fragment.ChatP2PFragment;
import com.netease.yunxin.kit.chatkit.ui.normal.page.fragment.ChatP2PFragment;

import com.netease.yunxin.kit.chatkit.ui.normal.page.fragment.ChatTeamFragment;
```

## 路由映射

| UI 风格 | 单聊路由 | 群聊路由 |
|---|---|---|
| 普通版本 | `RouterConstant.PATH_CHAT_P2P_PAGE` | `RouterConstant.PATH_CHAT_TEAM_PAGE` |
| 娱乐风格 | `RouterConstant.PATH_FUN_CHAT_P2P_PAGE` | `RouterConstant.PATH_FUN_CHAT_TEAM_PAGE` |

## 方式一：路由跳转（直接输出代码）

### 单聊

```java
String accountId = "123456";
XKitRouter.withKey(CHAT_P2P_ROUTE)
    .withParam(RouterConstant.CHAT_ID_KRY, accountId)
    .withContext(context)
    .navigate();
```

### 群聊

```java
String teamId = "123456";
XKitRouter.withKey(CHAT_TEAM_ROUTE)
    .withParam(RouterConstant.CHAT_ID_KRY, teamId)
    .withContext(context)
    .navigate();
```

其中：

- 普通版本：`CHAT_P2P_ROUTE = RouterConstant.PATH_CHAT_P2P_PAGE`，`CHAT_TEAM_ROUTE = RouterConstant.PATH_CHAT_TEAM_PAGE`
- 娱乐风格：`CHAT_P2P_ROUTE = RouterConstant.PATH_FUN_CHAT_P2P_PAGE`，`CHAT_TEAM_ROUTE = RouterConstant.PATH_FUN_CHAT_TEAM_PAGE`

## 方式二：新建 Activity + Fragment

### 步骤

1. 新建 `ChatContainerActivity`
2. 布局放一个 `FrameLayout` 容器
3. 根据聊天类型加载 `ChatP2PFragment` 或 `ChatTeamFragment`
4. 从 `Intent` 读取 `RouterConstant.CHAT_KRY` 后传给 Fragment

### 布局示例

```xml
<FrameLayout
    android:id="@+id/chat_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### 单聊 Fragment 示例

```java
String accountId = (String) getIntent().getSerializableExtra(RouterConstant.CHAT_ID_KRY);
ChatP2PFragment fragment = new ChatP2PFragment();
Bundle bundle = new Bundle();
bundle.putSerializable(RouterConstant.CHAT_ID_KRY, accountId);
fragment.setArguments(bundle);
getSupportFragmentManager().beginTransaction().replace(R.id.chat_container, fragment).commit();
```

### 群聊 Fragment 示例

```java
String teamId = (String) getIntent().getSerializableExtra(RouterConstant.CHAT_ID_KRY);
ChatTeamFragment fragment = new ChatTeamFragment();
Bundle bundle = new Bundle();
bundle.putSerializable(RouterConstant.CHAT_ID_KRY, teamId);
fragment.setArguments(bundle);
getSupportFragmentManager().beginTransaction().replace(R.id.chat_container, fragment).commit();
```

## MCP 输出规则

1. 用户选“路由跳转”时，只输出路由代码
2. 用户选“新建 Activity + Fragment”时，只输出 Activity + Fragment 集成代码
3. 未选择的分支不输出
