# IM UIKit Android 功能配置指南（MCP 输出给大模型）

## 目标

让大模型根据用户的业务描述生成配置代码，不要求用户输入任何开关接口名。

## 执行规则

1. 先询问业务诉求，不询问接口名
2. 把用户诉求映射到对应开关
3. 仅输出需要修改的开关代码
4. 配置代码必须在 IM UIKit 页面加载前执行
5. 全局配置优先放在 `Application.onCreate`

## 开关说明（保留）

| 参数 | 类型 | 说明 | 默认值 |
| --- | --- | --- | --- |
| `enableAIUser` | Boolean | 是否启用 AI 数字人能力。 | true |
| `enableAtMessage` | Boolean | 是否启用 @ 提及能力。 | true |
| `enableCollectionMessage` | Boolean | 是否启用消息收藏。 | true |
| `enableDismissTeamDeleteConversation` | Boolean | 解散群或被移出群后是否自动删除会话。 | true |
| `enableOnlyFriendCall` | Boolean | 是否仅允许好友之间发起通话。 | true |
| `enablePinMessage` | Boolean | 是否启用消息 Pin。 | true |
| `enableTeam` | Boolean | 是否启用群聊能力。 | true |
| `enableTopMessage` | Boolean | 是否启用会话置顶。 | true |
| `enableTypingStatus` | Boolean | 是否显示正在输入状态。 | true |
| `enableOnlineStatus` | Boolean | 是否显示在线状态。 | true |
| `enableAIChatHelper` | Boolean | 是否启用 AI 助聊。 | true |
| `enableRichTextMessage` | Boolean | 是否支持富文本消息。 | true |

## 额外全局设置说明（保留）

| 参数 | 说明 | 示例调用 |
| --- | --- | --- |
| 消息已读状态显示 | 是否显示已读/未读状态 | `SettingRepo.setShowReadStatus(true)` |
| 语音播放模式 | 听筒或外放 | `SettingRepo.setHandsetMode(ConfigRepo.AUDIO_PLAY_EARPIECE)` |

## 询问方式（面向业务）

优先按业务语言询问，不出现接口名：

1. 是否需要 AI 数字人和 AI 助聊能力
2. 是否需要 @、收藏、置顶、Pin
3. 是否展示在线状态和正在输入状态
4. 是否启用群聊能力
5. 解散群后是否自动清理对应会话
6. 是否限制为仅好友可通话
7. 是否需要富文本消息
8. 是否显示消息已读状态
9. 语音播放使用听筒还是外放
10. 配置写在全局（Application）还是页面（Activity）

## 业务描述到开关映射

| 用户描述 | 对应代码 |
| --- | --- |
| 开启 AI 数字人 | `IMKitConfigCenter.setEnableAIUser(true)` |
| 关闭 AI 数字人 | `IMKitConfigCenter.setEnableAIUser(false)` |
| 开启 @ 功能 | `IMKitConfigCenter.setEnableAtMessage(true)` |
| 关闭 @ 功能 | `IMKitConfigCenter.setEnableAtMessage(false)` |
| 开启消息收藏 | `IMKitConfigCenter.setEnableCollectionMessage(true)` |
| 关闭消息收藏 | `IMKitConfigCenter.setEnableCollectionMessage(false)` |
| 解散群后自动删会话 | `IMKitConfigCenter.setEnableDismissTeamDeleteConversation(true)` |
| 解散群后保留会话 | `IMKitConfigCenter.setEnableDismissTeamDeleteConversation(false)` |
| 仅好友可通话 | `IMKitConfigCenter.setEnableOnlyFriendCall(true)` |
| 非好友也可通话 | `IMKitConfigCenter.setEnableOnlyFriendCall(false)` |
| 开启 Pin 消息 | `IMKitConfigCenter.setEnablePinMessage(true)` |
| 关闭 Pin 消息 | `IMKitConfigCenter.setEnablePinMessage(false)` |
| 开启群聊 | `IMKitConfigCenter.setEnableTeam(true)` |
| 关闭群聊 | `IMKitConfigCenter.setEnableTeam(false)` |
| 开启会话置顶 | `IMKitConfigCenter.setEnableTopMessage(true)` |
| 关闭会话置顶 | `IMKitConfigCenter.setEnableTopMessage(false)` |
| 显示正在输入 | `IMKitConfigCenter.setEnableTypingStatus(true)` |
| 隐藏正在输入 | `IMKitConfigCenter.setEnableTypingStatus(false)` |
| 显示在线状态 | `IMKitConfigCenter.setEnableOnlineStatus(true)` |
| 隐藏在线状态 | `IMKitConfigCenter.setEnableOnlineStatus(false)` |
| 开启 AI 助聊 | `IMKitConfigCenter.setEnableAIChatHelper(true)` |
| 关闭 AI 助聊 | `IMKitConfigCenter.setEnableAIChatHelper(false)` |
| 开启富文本消息 | `IMKitConfigCenter.setEnableRichTextMessage(true)` |
| 关闭富文本消息 | `IMKitConfigCenter.setEnableRichTextMessage(false)` |
| 显示已读状态 | `SettingRepo.setShowReadStatus(true)` |
| 隐藏已读状态 | `SettingRepo.setShowReadStatus(false)` |
| 语音听筒播放 | `SettingRepo.setHandsetMode(ConfigRepo.AUDIO_PLAY_EARPIECE)` |
| 语音外放播放 | `SettingRepo.setHandsetMode(ConfigRepo.AUDIO_PLAY_OUTSIDE)` |

## 代码产出规则

1. 根据用户明确提到的功能产出开关代码
2. 未提到的开关不改动
3. 输出最小变更代码，不生成整段全量默认代码
4. 当用户同时提到“全局配置”时，输出 `Application.onCreate` 片段
5. 当用户提到“某页面单独配置”时，输出对应 `Activity.onCreate` 片段

## 代码模板

### 模板 A：全局配置

```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        IMKitConfigCenter.setEnableOnlineStatus(false);
        IMKitConfigCenter.setEnableTypingStatus(false);
        SettingRepo.setShowReadStatus(true);
    }
}
```

### 模板 B：页面配置

```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        IMKitConfigCenter.setEnablePinMessage(true);
        IMKitConfigCenter.setEnableTopMessage(true);
    }
}
```

## MCP 返回给大模型的推荐结构

1. `BUSINESS_QUESTIONS`：面向业务的提问列表
2. `SELECTION_SUMMARY`：用户选择汇总（业务语义）
3. `CODE_PATCH_PLAN`：文件位置 + 对应开关修改代码
