English | 中文

🚀 AI 智能体技能包，用于快速集成云信 IM（包含 UI 组件 IMUIKit 与底层 IMSDK），覆盖 Android / iOS / Web / Flutter 等多平台。你只需要描述需求，AI 助手会按固定工作流调用 YunXin MCP Server 的工具获取实时文档与离线 API 签名，并输出可执行的集成步骤与示例代码。

## 🚀 快速开始

1) 配置 MCP Server（YunXin MCP）

在你的 AI IDE/MCP 客户端配置文件中增加：

```json
{
  "mcpServers": {
    "yunxin-sdk-mcp": {
      "command": "npx",
      "args": ["-y", "yunxin-sdk-mcp@latest"]
    }
  }
}
```

2) 安装 Skills（推荐：克隆仓库）

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

3) 在 AI IDE 中配置 Skills 路径

把 `~/.skills/yunxin-im-skills` 添加到 Skills sources（不同 IDE 的配置入口不同）。

4) 开始使用

直接对 AI 提问，例如：
- “Android IMUIKit 怎么集成？”
- “Flutter 查询历史消息”
- “Web 登录接口是什么？”
- “iOS 会话未读数接口怎么查？”

## 📋 目录

- 快速开始
- 概述
- 覆盖能力
- 前置条件
- 安装步骤
- 使用示例
- 工作原理
- 常见问题

## 🌟 概述

本仓库提供的 AI Skills 可以让 AI 助手（如 Claude、Cursor、CodeBuddy 等）实现：

- 🎯 路由与推荐：根据问题在 UI 组件（IMUIKit）与底层 SDK（IMSDK）之间选择正确路径
- 📖 获取文档：通过 MCP 工具获取最新集成文档（`imuikit_doc` / `imsdk_doc`）
- 🔎 API Reference 查询：通过本地数据集查询接口签名/参数/返回（`nim_sdk_search_symbols` / `nim_sdk_list_members`）
- 💻 输出最小可用代码：严格基于“文档 + 签名”生成可执行的最小示例

## 🎯 覆盖能力

| 模块 | 说明 | 相关 MCP 工具 |
| --- | --- | --- |
| IMUIKit（UI 组件） | 会话列表 / 聊天 / 通讯录 / 初始化 / 集成 / 配置 / FAQ | `imuikit_doc` |
| IMSDK（底层 SDK） | 初始化 / 登录登出 / 会话 / 历史消息 / 收发消息 / 未读 / 好友 / 黑名单等 | `imsdk_doc` |
| API Reference | 类/成员检索、方法签名、字段结构 | `nim_sdk_*` |

## ✅ 前置条件

- Node.js >= 18
- npm >= 9
- 支持 Skills + MCP 的 AI IDE 或智能体（如 Claude Desktop / Cursor / CodeBuddy 等）
- 已可正常连接 YunXin MCP Server（可在 IDE 日志中看到 MCP 启动信息）

## 📦 安装步骤

### 第一步：安装/配置 YunXin MCP Server

推荐使用 npx（避免全局安装污染）：

```json
{
  "mcpServers": {
    "yunxin-sdk-mcp": {
      "command": "npx",
      "args": ["-y", "yunxin-sdk-mcp@latest"]
    }
  }
}
```

修改配置后请重启 AI IDE。

### 第二步：安装 Skills

推荐 clone：

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

然后在 IDE 的 Skills 设置中把 `~/.skills/yunxin-im-skills` 作为 skill source 添加进去。

## 💬 使用示例

- Android 集成 IMUIKit：  
  “Android IMUIKit 怎么集成？”
- Web 初始化与登录：  
  “Web 初始化接口是什么？登录接口是什么？”
- Flutter 历史消息：  
  “Flutter 查询历史消息怎么做？getMessageListEx 的签名是什么？”

## ⚙️ 工作原理

当你描述需求时，AI 助手会：

1) 识别问题属于 UI 组件（IMUIKit）还是底层 SDK（IMSDK）  
2) 选择平台（Android/iOS/Web/Flutter 等）  
3) 通过 MCP 获取官方文档（`imuikit_doc` / `imsdk_doc`）  
4) 当需要写代码或确认参数时，通过本地数据集查询 API 签名（`nim_sdk_*`）  
5) 输出最小可用的步骤与示例代码，并给出相关文档入口/接口引用

## ❓ 常见问题

### MCP Server 连接失败

- 检查 Node 版本：`node --version`（需要 >= 18）
- 确认配置 JSON 合法（无尾随逗号、引号正确）
- 重启 IDE（配置修改后必须重启）
- 查看 IDE 日志定位报错

### Skills 无法加载

- 确认 skills 路径配置正确且可访问
- 确认仓库根目录存在 `SKILL.md`
- 重启 IDE

