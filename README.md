English | 中文

本仓库是云信 IM 的 Skills 仓库（包含 UI 组件 IMUIKit 与底层 IMSDK 的文档与工作流），覆盖 Android / iOS / Web / Flutter 等多平台。你只需要描述需求，AI 助手会按固定工作流基于本仓库的参考文档输出集成步骤与示例。

## 快速开始

1) 安装 Skills（推荐：克隆仓库）

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

2) 在 AI IDE 中配置 Skills 路径

把 `~/.skills/yunxin-im-skills` 添加到 Skills sources（不同 IDE 的配置入口不同）。

3) 开始使用

直接对 AI 提问，例如：
- “Android IMUIKit 怎么集成？”
- “Flutter 查询历史消息”
- “Web 登录接口是什么？”
- “iOS 会话未读数接口怎么查？”

## 目录

- 快速开始
- 概述
- 覆盖能力
- 使用示例
- 工作原理
- 常见问题

## 概述

本仓库提供的 AI Skills 可以让 AI 助手（如 Claude、Cursor、CodeBuddy 等）实现：

- 路由与推荐：根据问题在 UI 组件（IMUIKit）与底层 SDK（IMSDK）之间选择正确路径
- 文档引用：优先基于本仓库 reference 文档输出流程与能力说明
- 输出最小可用方案：先给最小可用步骤；需要扩展再逐步展开
- 签名策略：默认不承诺精确方法签名；当运行环境具备接口检索能力时，可用于复核签名与参数

## 覆盖能力

| 模块 | 说明 |
| --- | --- |
| IMUIKit（UI 组件） | 会话列表 / 聊天 / 通讯录 / 初始化 / 集成 / 配置 / FAQ |
| IMSDK（底层 SDK） | 初始化 / 登录登出 / 会话 / 历史消息 / 收发消息 / 未读 / 好友 / 黑名单等 |
| Reference | IMUIKit/IMSDK 的平台×主题参考文档与索引 |

## 目录结构

- Skill 入口与路由：
  - 根入口：`SKILL.md`
  - IM Router：`im/SKILL.md`
  - IMUIKit：`im/uikit/SKILL.md`
  - IMSDK：`im/sdk/SKILL.md`
- Reference 文档（可离线使用）：
  - IMUIKit：`im/uikit/reference/<platform>/<topic>.md`
  - IMSDK：`im/sdk/reference/<framework>/<topic>.md`
  - 索引：
    - `im/uikit/reference/index.md`
    - `im/sdk/reference/index.md`
    - `im/reference/coverage.md`

## 使用示例

- Android 集成 IMUIKit：  
  “Android IMUIKit 怎么集成？”
- Web 初始化与登录：  
  “Web 初始化接口是什么？登录接口是什么？”
- Flutter 历史消息：  
  “Flutter 查询历史消息怎么做？”

## 工作原理

当你描述需求时，AI 助手会：

1) 识别问题属于 UI 组件（IMUIKit）还是底层 SDK（IMSDK）  
2) 选择平台（Android/iOS/Web/Flutter 等）  
3) 优先引用本仓库 reference 文档（按 platform/framework × topic）  
4) 当用户追问接口细节且运行环境不具备接口检索能力时：返回可命中的 Service 相关参考文档集合，并提示需要后续复核签名  
5) 输出最小可用的步骤与示例，并给出 reference 文档入口

## 常见问题

### Skills 无法加载

- 确认 skills 路径配置正确且可访问
- 确认仓库根目录存在 `SKILL.md`
- 重启 IDE
