---
name: "yunxin-im"
description: "IM router skill. Decide UI (IMUIKit) vs SDK (IMSDK/API) and delegate to sub skills."
---

# YunXin IM Skill（Router）

本文件只做“路由与职责分配”，不承载细节规则。

## 必须触发（关键词）

中文：

- IM、云信、YunXin、NIM、IMSDK、NIM SDK、IMUIKit、UIKit、V2NIM
- 会话、消息、历史消息、未读、已读、多端同步、好友、黑名单、用户资料、在线状态
- 会话列表、聊天页面、通讯录、组件、页面、路由、权限、混淆、集成、初始化、登录

English:

- IM, YunXin, NIM, IMSDK, NIM SDK, IMUIKit, UIKit, V2NIM
- conversation, message, history message, unread, read receipt, multi-device sync, friend, block list, user profile, online status
- conversation list, chat UI, contacts UI, components, pages, routing, permission, proguard, integration, initialization, login

## 拆分原则

- IMUIKit Skill：面向 UI 组件集成；必须包含必要的 SDK 下钻与签名查询能力
- IMSDK Skill：面向底层接口与能力实现；只需要文档与签名查询，不涉及 UI 组件

## 路由规则（先判层）

### A) UI 组件（IMUIKit）

适用：用户问“页面/组件/路由/会话列表 UI/聊天 UI/通讯录 UI/如何集成 UIKit/仅集成聊天”等。

进入子 Skill：

- [IMUIKit Skill](uikit/SKILL.md)

### B) 底层 SDK（IMSDK + API）

适用：用户问“接口/能力如何实现/历史消息/未读/收发消息/会话管理/好友/黑名单/用户资料/在线状态/接口签名”等。

进入子 Skill：

- [IMSDK Skill](sdk/SKILL.md)

## 歧义词分流优先级（强制）

1) 任何出现“方法签名/参数/返回值/API/接口/错误码/具体调用/示例代码/what is the signature”等意图：优先走 IMSDK Skill（并触发签名查询链路）。
2) 出现“页面/组件/路由/会话列表 UI/聊天 UI/通讯录/IMUIKit/仅集成聊天/导入组件/权限/混淆/集成步骤”等意图：优先走 IMUIKit Skill。
3) 仅出现“初始化/登录/会话/未读/历史消息”等泛能力词且未出现 UI 词：默认走 IMSDK Skill。

## 示例问句（Router 命中）

- Android 会话列表页面怎么接？
- Web 查询历史消息和未读怎么做？
- iOS 登录接口参数是什么？
- What is the signature of V2NIMLoginService.login on Web?
