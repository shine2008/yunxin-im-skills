# IM UIKit Android 通讯录集成

## 适用范围

用于 Android 通讯录集成（路由跳转 / 新建 Activity + Fragment）。

## 前置条件（必须满足）

为保证运行成功，在集成通讯录页面前必须先完成：

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

## 相关导入

```java
import com.netease.yunxin.kit.corekit.route.XKitRouter;
import com.netease.yunxin.kit.corekit.im2.utils.RouterConstant;
import com.netease.yunxin.kit.contactkit.ui.normal.contact.ContactFragment;
import com.netease.yunxin.kit.contactkit.ui.fun.contact.FunContactFragment;
```

## Fragment 类映射

| UI 风格 | Fragment 类 |
|---|---|
| 普通版本 | `ContactFragment` |
| 娱乐风格 | `FunContactFragment` |

## 方式一：路由跳转（直接输出代码）

| UI 风格 | 路由 |
|---|---|
| 普通版本 | `RouterConstant.PATH_CONTACT_PAGE` |
| 娱乐风格 | `RouterConstant.PATH_FUN_CONTACT_PAGE` |

### 普通版本

```java
XKitRouter.withKey(RouterConstant.PATH_CONTACT_PAGE).withContext(context).navigate();
```

### 娱乐风格

```java
XKitRouter.withKey(RouterConstant.PATH_FUN_CONTACT_PAGE).withContext(context).navigate();
```

## 方式二：新建 Activity + Fragment

### 步骤

1. 新建 `ContactContainerActivity`
2. 布局放一个 `FrameLayout` 容器
3. 普通版本加载 `ContactFragment`，娱乐风格加载 `FunContactFragment`

### 布局示例

```xml
<FrameLayout
    android:id="@+id/contact_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### 普通版本 Fragment 示例

```java
ContactFragment fragment = new ContactFragment();
getSupportFragmentManager()
    .beginTransaction()
    .replace(R.id.contact_container, fragment)
    .commit();
```

### 娱乐风格 Fragment 示例

```java
FunContactFragment fragment = new FunContactFragment();
getSupportFragmentManager()
    .beginTransaction()
    .replace(R.id.contact_container, fragment)
    .commit();
```

## MCP 输出规则

1. 用户选“路由跳转”时，只输出路由代码
2. 用户选“新建 Activity + Fragment”时，只输出当前 UI 风格对应的 Activity + Fragment 集成代码
3. 未选择的分支不输出
