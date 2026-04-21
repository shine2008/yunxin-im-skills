# IM UIKit 界面集成指引

## 资源标识

- resource_id: `imkit.integration.ui.guide`
- category: `mcp-guide`

## 使用定位

本文件可以独立使用，用于接入会话列表、通讯录以及相关界面风格。

在总控流程里，它通常作为第六步使用。

## 适用场景

当用户要在 Android 中同时接入会话列表和通讯录，并统一采用“新建 Activity + 底部 Tab + Fragment”时，读取本文件。

## 前置变量

- `SESSION_MODE`
- `UI_STYLE`

## 执行默认约定

无需开始前询问，直接按以下约定执行：

1. 固定新建 `IMTabActivity`
2. 代码语言优先复用当前工程；若无法识别，默认使用 Java
3. Tab 文案固定使用默认值：`会话`、`通讯录`

## 统一集成模式

会话列表与通讯录合并为一个页面实现，固定采用：

- 新建 Activity
- `ViewPager2 + TabLayout`
- 底部 Tab 布局
- 第一个 Tab 放会话列表
- 第二个 Tab 放通讯录

## Fragment 类映射

| UI_STYLE | SESSION_MODE | 会话 Fragment | 通讯录 Fragment |
|----------|--------------|---------------|-----------------|
| Normal | 本地会话 | `LocalConversationFragment` | `ContactFragment` |
| Normal | 云端会话 | `ConversationFragment` | `ContactFragment` |
| Fun | 本地会话 | `FunLocalConversationFragment` | `FunContactFragment` |
| Fun | 云端会话 | `FunConversationFragment` | `FunContactFragment` |

## Fragment 导入路径

```java
import com.netease.yunxin.kit.contactkit.ui.fun.contact.FunContactFragment;
import com.netease.yunxin.kit.contactkit.ui.normal.contact.ContactFragment;
import com.netease.yunxin.kit.conversationkit.local.ui.fun.page.FunLocalConversationFragment;
import com.netease.yunxin.kit.conversationkit.local.ui.normal.page.LocalConversationFragment;
import com.netease.yunxin.kit.conversationkit.ui.fun.page.FunConversationFragment;
import com.netease.yunxin.kit.conversationkit.ui.normal.page.ConversationFragment;
```

## 执行步骤

### 1. 新建底部 Tab 布局

```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="0dp"
        android:layout_height="0dp" />

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/bottomTab"
        android:layout_width="0dp"
        android:layout_height="wrap_content" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 2. 新建 Activity 并绑定两个 Fragment

```kotlin
val fragments = listOf(
    CONVERSATION_FRAGMENT_CLASS(),
    CONTACT_FRAGMENT_CLASS()
)
val titles = listOf("会话", "通讯录")

viewPager.adapter = object : FragmentStateAdapter(this) {
    override fun getItemCount(): Int = fragments.size
    override fun createFragment(position: Int): Fragment = fragments[position]
}

TabLayoutMediator(bottomTab, viewPager) { tab, position ->
    tab.text = titles[position]
}.attach()
```

若工程最终判定为 Java，使用等价 Java 写法实现相同逻辑。

### 3. Tab 顺序固定

1. 第一个 Tab：会话列表
2. 第二个 Tab：通讯录

## 生成代码时的规则

- 统一采用新建 Activity，不改造旧页面
- Activity 类名固定为 `IMTabActivity`
- 固定采用底部 `TabLayout + ViewPager2 + Fragment`
- Tab 顺序固定为“会话”在前，“通讯录”在后
- 文案固定使用默认值“会话”“通讯录”
- 代码语言优先复用当前工程；若无法识别则默认 Java
- `UI_STYLE` 决定 Normal / Fun 类路径
- `SESSION_MODE` 决定本地会话或云端会话类路径
- 优先读取真实 aar 中的类路径，不要凭经验硬写包名

## 完成后的输出

- 说明新建的 Activity、布局、Adapter 文件
- 说明会话 Fragment 与通讯录 Fragment 的最终类名
- 说明底部 Tab 顺序
- 输出最终界面结构总结
