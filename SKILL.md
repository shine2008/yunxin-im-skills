---
name: "yunxin-im-skills"
description: "ROOT Skill for YunXin IM + IMUIKit. Invoke when user asks IM integration/API/signature across Android/iOS/Web/Flutter or asks YunXin IMUIKit usage."
---

# YunXin Skills (ROOT)

本仓库是云信 Skills（类似“产品级工作流约束”），配合 YunXin MCP Server 使用。

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

1) 先文档：集成/流程/参数含义必须先调用文档工具获取官方说明  
2) 再签名：涉及具体方法调用时，必须用 `nim_sdk_*` 确认签名（禁止凭经验编造）  
3) 再代码：代码示例必须基于“文档 + 签名查询结果”输出  
4) 最小化：先给最小可用方案，扩展功能必须由用户明确要求

## 进入 IM 域

使用子 Skill：

- [IM Skill](im/SKILL.md)
