---
name: "yunxin-im-sdk"
description: "IMSDK (SDK + API reference) skill. Invoke when user asks capabilities/APIs/signatures without UI components."
---

# YunXin IMSDK Skill（底层能力 + API 签名）

适用：用户问“接口/能力如何实现/历史消息/未读/收发消息/会话管理/好友/黑名单/用户资料/在线状态”等，不涉及 UI 组件集成。

默认优先使用本仓库的文档镜像（可在无 MCP 场景下工作）：

- `im/sdk/reference/<framework>/<topic>.md`

## 必须触发（关键词）

中文：

- IMSDK、NIM SDK、V2NIM、会话、消息、历史消息、未读、已读、收发消息、云端会话、本地会话、好友、黑名单、用户资料、在线状态、接口签名、参数、返回值

English:

- IMSDK, NIM SDK, V2NIM, conversation, message, history message, unread, read receipt, send/receive, cloud conversation, local conversation, friend, block list, user profile, online status, API signature

## SDK 文档（能力实现）

执行顺序：

1) 读取文档镜像：`im/sdk/reference/<framework>/<topic>.md`
2) 若检测到 MCP 可用且需要复核，可再调用 `imsdk_doc(framework=..., topic=...)`

参数名防呆：

- `imsdk_doc` 用 `framework`
- 不要把 UI 的 `platform` 传给 `imsdk_doc`

参数：

- `framework`：android / ios / web / flutter / node / pc / harmony
- `topic`：initialization / login_logout / local_conversation / history_message / send_receive_message / cloud_conversation / user_status_subscription / user_profile / friend_relationship / block_list / api_reference

平台映射（用户口语 → framework）：

- 用户说 Web/H5/Vue/uni-app/小程序：统一用 `framework=web`
- 用户说 Electron：能力实现通常仍用 `framework=web` 或 `framework=node`（以用户明确诉求为准）
- 用户说 Windows/macOS 桌面端：用 `framework=pc`

英文示例问句：

- "How to initialize IMSDK and login on iOS?"
- "How to query and clear history messages in Flutter?"
- "How to send custom messages on Android?"
- "How to manage friend relationship and block list on Node?"

## 必须链路：签名查询（写代码或问签名时）

当需要写代码或用户问“方法签名/参数/返回值”时，必须按顺序调用：

1) `nim_sdk_search_symbols`（先找到类/成员）
2) `nim_sdk_list_members`（列出类的 methods/fields，拿到准确签名）

参数：

- `framework`：android / flutter / web / ios
- `with_docs`：默认 false

歧义兜底（强制）：

- 若用户提到“页面/组件/路由/IMUIKit”，先提示这是 UI 组件问题并引导到 IMUIKit Skill；本 Skill 仅处理底层接口与签名。
- 若检测到 MCP 可用：签名必须走 `nim_sdk_search_symbols` → `nim_sdk_list_members`。
- 若没有 MCP：返回“同 framework 下能命中的 Service 文档集合”（例如 `login_logout / send_receive_message / history_message / local_conversation / cloud_conversation` 等 topic 文档），并提示需接入 MCP 才能精确确认签名。

Service 文档集合召回策略（无 MCP 时使用）：

1) 主召回（按能力域）：`send_receive_message`、`history_message`、`local_conversation`、`cloud_conversation`
2) 流程召回（按生命周期）：`initialization`、`login_logout`
3) 关系类（按用户问题命中）：`friend_relationship`、`block_list`、`user_profile`、`user_status_subscription`
4) 只在用户问“怎么查签名/接口列表”时才返回：`api_reference`

英文示例问句：

- "What is the signature of V2NIMLoginService.login on Web?"
- "Find sendMessage API signature on Android."
- "List methods of iOS V2NIMMessageService."

## 输出规范

- 结论：给出应使用的 IMSDK topic 与关键接口名
- 需要具体调用时：
  - MCP 可用：先查签名，再给最小可运行代码片段
  - 无 MCP：仅基于 Service 文档给出“可能的接口名/调用步骤”，并提示需要 MCP 复核签名
