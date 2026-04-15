---
name: "yunxin-im"
description: "IM Skill across IMUIKit (UI) + IMSDK (SDK) with multi-platform routing and signature verification. Invoke when user asks IM features/integration/API signatures."
---

# YunXin IM Skill (UI + SDK)

本 Skill 覆盖 IM 的两部分：

- UI 组件（IMUIKit）：会话列表/聊天/通讯录等页面级能力
- 底层 SDK（IMSDK + API）：会话/消息/历史消息/未读/好友/黑名单等能力与签名查询

## 选择路径（先判层）

### A) UI 组件（IMUIKit）

适用：用户问“怎么集成 UI/页面/组件/路由/会话列表 UI/聊天 UI/通讯录 UI”。

英文示例问句：

- "How to integrate IMUIKit on Android/iOS?"
- "How to add conversation list UI and chat UI?"
- "How to integrate contacts UI in IMUIKit?"
- "How to setup IMUIKit routing for chat page?"

调用工具：

- `imuikit_doc`

参数：

- `platform`：必填
- `topic`：建议必填（integration/initialization/login_logout/...）

常用 topic：

- `integration` / `initialization` / `login_logout`
- `conversation` / `chat` / `contact`
- `kit_config` / `faq`

### B) 底层 SDK（IMSDK）

适用：用户问“接口/能力如何实现/历史消息/未读/收发消息/会话管理”等。

英文示例问句：

- "How to initialize IMSDK and login?"
- "How to implement local conversation management?"
- "How to query and clear history messages?"
- "How to send/receive text, image, and custom messages?"
- "How to manage friend relationship and block list?"

调用工具：

- `imsdk_doc`

参数：

- `framework`：必填（android/ios/web/flutter/node/pc/harmony）
- `topic`：必填（initialization/login_logout/...）

常用 topic：

- `initialization` / `login_logout`
- `local_conversation` / `cloud_conversation`
- `history_message` / `send_receive_message`
- `friend_relationship` / `block_list`
- `user_profile` / `user_status_subscription`

## 签名查询（必须）

当需要写代码或用户问“方法签名/参数/返回值”时，必须按顺序调用：

1) `nim_sdk_search_symbols`（先找到类/成员）
2) `nim_sdk_list_members`（列出类的 methods/fields，拿到准确签名）

参数：

- `framework`：android/flutter/web/ios（与本地 API 数据集一致；注意这里是 framework，不是 IMUIKit 的 platform）
- `with_docs`：默认 false

英文示例问句（必须触发签名查询链路）：

- "What is the signature of V2NIMLoginService.login on Web?"
- "Show me the initialize method signature in Flutter NimCore."
- "What are methods in iOS V2NIMMessageService?"
- "Find sendMessage API signature on Android."

## 输出规范

- 结论：列出应使用的接口/步骤
- 引用：指出所用的 doc topic 或所查询的类/成员
- 示例：给最小可运行的代码片段
