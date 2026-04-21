---
name: "yunxin-im-skills"
description: "ROOT Skill for YunXin IM + IMUIKit. Invoke when user asks IM integration/API/signature across Android/iOS/Web/Flutter or asks YunXin IMUIKit usage."
---

# YunXin Skills (ROOT)

本仓库是云信 Skills（类似“产品级工作流约束”），配合 YunXin MCP Server 使用。

本仓库同时支持两种运行形态：

- Skill-only：仅依赖本仓库（使用 `im/**/reference/**` 的文档镜像完成大多数能力），不要求 MCP 可用
- Skill + MCP：当检测到 MCP 可用时，仅在“接口签名/参数/返回值精确查询”场景调用 MCP 的 `nim_sdk_*`

## 必须触发（关键词）

当用户提到任意关键词时，应优先加载本 ROOT Skill 做路由与工作流约束：

- 产品/品牌：云信、YunXin、NIM、IM、IMSDK、IMUIKit、UIKit
- SDK/接口：V2NIM、会话、消息、历史消息、未读、已读、好友、黑名单
- 场景：聊天、会话列表、通讯录、消息收发、查询消息、删除会话
- 集成：初始化、登录、集成、组件导入、权限、混淆、路由

英文关键词（与中文同等触发优先级）：

- Product/Brand: YunXin, NIM, IM, IMSDK, IMUIKit, UIKit
- SDK/API: V2NIM, conversation, message, history message, unread, read receipt, friend, block list
- Scenarios: chat, conversation list, contacts, send/receive message, query messages, delete conversation
- Integration: initialization, login, integration, import components, permission, proguard, routing

英文示例问句（命中 ROOT 路由）：

- "How to integrate YunXin IMUIKit on Android?"
- "How do I initialize IMSDK and login on iOS?"
- "How to send and receive messages with NIM SDK on Web?"
- "How can I query message history and unread count?"
- "What is the API signature of V2NIMLoginService.login?"

## 总体路由（domain / layer / platform）

### domain（产品域）

当前仓库首先覆盖 IM；后续可扩展 RTC/Meeting/Call/Push 等域。

### layer（层）

- UI 组件：IMUIKit（页面级能力：会话/聊天/通讯录等）
- 底层 SDK：IMSDK（能力实现：会话/消息/好友等）

### platform（平台）

- UI 组件：android / ios / react / flutter / vue / h5_vue3 / h5_react / miniapp_wx / electron / uniapp
- 底层 SDK：android / ios / web / flutter / node / pc / harmony

## 工具总表（YunXin MCP Server）

- UI 文档：`imuikit_doc`（platform + topic）
- SDK 文档：`imsdk_doc`（framework + topic）
- API 签名：`nim_sdk_search_symbols` / `nim_sdk_list_members`（framework + query/class_name）
- 示例工程：`get_rtc_sample_code` / `get_meeting_sample_code` / `get_group_voice_room_sample_code` / `get_pk_sample_code` / `get_call_kit_sample_code`

参数名提醒：

- `imuikit_doc` 用 `platform`
- `imsdk_doc` 与 `nim_sdk_*` 用 `framework`

## 统一执行规则（强制）

1) 文档优先：默认使用本仓库 `im/**/reference/**` 文档镜像作为依据输出  
2) 按需校验：若 MCP 可用，可用 `imuikit_doc/imsdk_doc` 复核文档或补充细节  
3) 签名精确：仅当用户需要“方法签名/参数/返回值精确查询”时调用 `nim_sdk_*`  
4) 无 MCP 兜底：若 MCP 不可用且用户仍追问接口细节，返回能命中的 SDK Service 相关文档集合，并提示需后续接入 MCP 复核签名  
5) 最小化：先给最小可用方案，扩展功能必须由用户明确要求

## 进入 IM 域

使用子 Skill：

- 默认路径：优先进入 IM Router Skill，由 Router 判定 UI（IMUIKit）或 SDK（IMSDK/API）并下发子 Skill。
- [IM Router Skill](im/SKILL.md)
- [IMUIKit Skill](im/uikit/SKILL.md)
- [IMSDK Skill](im/sdk/SKILL.md)

## 文档镜像

- IMUIKit 文档镜像：`im/uikit/reference/<platform>/<topic>.md`
- IMUIKit 索引：`im/uikit/reference/index.md`
- IMSDK 文档镜像：`im/sdk/reference/<framework>/<topic>.md`
- IMSDK 索引：`im/sdk/reference/index.md`
- 覆盖清单：`im/reference/coverage.md`
