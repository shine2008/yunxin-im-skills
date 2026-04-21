---
name: "yunxin-im-uikit"
description: "IMUIKit (UI) skill with SDK fallback. Invoke when user asks UI pages/components/routing; may call IMSDK docs and API signatures when needed."
---

# YunXin IMUIKit Skill（UI 组件 + 必要的 SDK 补充）

适用：用户问“页面/组件/路由/会话列表 UI/聊天 UI/通讯录 UI/如何集成 UIKit”。

本 Skill 以 **IMUIKit（UI 层）为主**，并在需要“接口签名/参数/返回值/具体 SDK 调用”时，自动下钻到 IMSDK 与 API Reference。

默认优先使用本仓库的文档镜像（可在无 MCP 场景下工作）：

- `im/uikit/reference/<platform>/<topic>.md`

## 必须触发（关键词）

中文：

- IMUIKit、UIKit、UI 组件、页面、组件、路由、会话列表、聊天页面、通讯录、搜索、仅集成聊天、功能开关、权限、混淆

English:

- IMUIKit, UIKit, UI components, pages, routing, conversation list, chat UI, contacts UI, search, standalone chat, feature flags, permission, proguard

## 主路径：UI 文档（优先）

执行顺序：

1) 读取文档镜像：`im/uikit/reference/<platform>/<topic>.md`
2) 若检测到 MCP 可用且需要复核，可再调用 `imuikit_doc(platform=..., topic=...)`

参数名防呆：

- `imuikit_doc` 用 `platform`
- 不要把 SDK 的 `framework` 传给 `imuikit_doc`

参数：

- `platform`：android / ios / react / flutter / vue / h5_vue3 / h5_react / miniapp_wx / electron / uniapp
- `topic`：integration / initialization / login_logout / conversation / chat / contact / kit_config / faq（以及平台特有 topic）

平台映射（用户口语 → platform / framework）：

- 用户说 Web/H5/Vue3：UI 用 `platform=vue` 或 `platform=h5_vue3`；SDK 用 `framework=web`
- 用户说 uni-app：UI 用 `platform=uniapp`；SDK 用 `framework=web`
- 用户说 小程序/微信小程序：UI 用 `platform=miniapp_wx`；SDK 用 `framework=web`
- 用户说 Electron：UI 用 `platform=electron`；SDK 默认 `framework=web`（除非用户明确在问 Node 端能力/接口）

英文示例问句：

- "How to integrate IMUIKit on Android?"
- "How to add conversation list UI and chat UI?"
- "How to integrate contacts UI in IMUIKit?"
- "How to integrate standalone chat page on H5 Vue3 / UniApp?"

## 下钻路径：SDK 文档（当用户问能力实现/流程细节时）

触发条件（任一满足即下钻）：

- 用户问“能力怎么做/接口怎么调用/历史消息/未读/会话/消息收发/好友/黑名单”等实现细节
- UI 文档里出现需要进一步解释的 SDK 能力点（例如云端会话/多端已读/历史消息删除等）

执行顺序：

1) 读取文档镜像：`im/sdk/reference/<framework>/<topic>.md`
2) 若检测到 MCP 可用且需要复核，可再调用 `imsdk_doc(framework=..., topic=...)`

参数名防呆：

- `imsdk_doc` 用 `framework`
- 不要把 UI 的 `platform` 传给 `imsdk_doc`

参数：

- `framework`：android / ios / web / flutter / node / pc / harmony
- `topic`：initialization / login_logout / local_conversation / history_message / send_receive_message / cloud_conversation / user_status_subscription / user_profile / friend_relationship / block_list / api_reference

## 必须链路：签名查询（当需要写代码或用户问签名时）

当需要写代码或用户问“方法签名/参数/返回值”时，必须按顺序调用：

1) `nim_sdk_search_symbols`（先找到类/成员）
2) `nim_sdk_list_members`（列出类的 methods/fields，拿到准确签名）

参数：

- `framework`：android / flutter / web / ios
- `with_docs`：默认 false

歧义兜底（强制）：

- 如果检测到 MCP 可用：用户在 UI 问题中出现“接口/方法/参数/返回值/签名/错误码/代码怎么写”，必须先走 `nim_sdk_search_symbols` → `nim_sdk_list_members`，再输出代码。
- 如果没有 MCP：返回能命中的 IMSDK Service 相关文档集合（同 framework 优先），并明确提示“当前无法做精确签名确认”。

Service 文档集合召回策略（无 MCP 时使用）：

- 优先返回同 `framework` 下的：`send_receive_message`、`history_message`、`local_conversation`、`cloud_conversation`、`login_logout`、`initialization`

英文示例问句：

- "What is the signature of V2NIMLoginService.login on Web?"
- "Show me the initialize method signature in Flutter NimCore."

## 输出规范

- 先给 UI 集成路径（平台 + topic）
- 需要实现细节时再给 IMSDK topic
- 需要代码时先查签名，再给最小可运行代码片段
