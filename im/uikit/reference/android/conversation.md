# IM UIKit Android 会话列表集成

## 适用范围

用于 Android 会话列表集成（路由跳转 / 新建 Activity + Fragment）。

## 前置条件（必须满足）

为保证运行成功，在集成会话列表页面前必须先完成：

1. 已完成 UIKit 组件导入（参考 `import_components.md`）
2. 已在启动页面之前完成 UIKit 初始化（参考 `initialization.md`）
3. 已完成 IM 登录（参考 `login_logout.md`）

## 开始前询问用户

1. 集成方式：
   - 路由跳转
   - 新建 Activity + Fragment
2. 会话模式：
   - 本地会话
   - 云端会话
3. UI 风格：
   - 普通版本
   - 娱乐风格

## 相关导入

```java
import com.netease.yunxin.kit.corekit.route.XKitRouter;
import com.netease.yunxin.kit.corekit.im2.utils.RouterConstant;
import com.netease.yunxin.kit.conversationkit.local.ui.normal.page.LocalConversationFragment;
import com.netease.yunxin.kit.conversationkit.ui.normal.page.ConversationFragment;
import com.netease.yunxin.kit.conversationkit.local.ui.fun.page.FunLocalConversationFragment;
import com.netease.yunxin.kit.conversationkit.ui.fun.page.FunConversationFragment;
```

## 路由映射

| UI 风格 | 路由 |
|---|---|
| 普通版本 | `RouterConstant.PATH_CONVERSATION_PAGE` |
| 娱乐风格 | `RouterConstant.PATH_FUN_CONVERSATION_PAGE` |

## 方式一：路由跳转（直接输出代码）

### 普通版本（本地/云端会话统一）

```java
XKitRouter.withKey(RouterConstant.PATH_CONVERSATION_PAGE).withContext(context).navigate();
```

### 娱乐风格（本地/云端会话统一）

```java
XKitRouter.withKey(RouterConstant.PATH_FUN_CONVERSATION_PAGE).withContext(context).navigate();
```

## 方式二：新建 Activity + Fragment

### 步骤

1. 新建 `ConversationContainerActivity`
2. 布局放一个 `FrameLayout` 容器
3. 普通版本：
   - 本地会话使用 `LocalConversationFragment`
   - 云端会话使用 `ConversationFragment`
4. 娱乐风格：
   - 本地会话使用 `FunLocalConversationFragment`
   - 云端会话使用 `FunConversationFragment`
5. 将 Fragment 加载到容器

### 布局示例

```xml
<FrameLayout
    android:id="@+id/conversation_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### Activity 示例

```java
Fragment fragment = new LocalConversationFragment();
getSupportFragmentManager()
    .beginTransaction()
    .replace(R.id.conversation_container, fragment)
    .commit();
```

若是云端会话，将 `LocalConversationFragment` 替换为 `ConversationFragment`。

若是娱乐风格：

- 本地会话将 `LocalConversationFragment` 替换为 `FunLocalConversationFragment`
- 云端会话将 `ConversationFragment` 替换为 `FunConversationFragment`

## MCP 输出规则

1. 用户选“路由跳转”时，只输出对应 UI 风格的路由代码（本地/云端会话共用同一路由）
2. 用户选“新建 Activity + Fragment”时，只输出对应会话模式和 UI 风格的 Activity + Fragment 集成代码
3. 未选择的分支不输出
