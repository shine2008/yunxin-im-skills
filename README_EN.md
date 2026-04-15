# yunxin-im-skills

English | 中文

🚀 An AI agent skills repo for integrating YunXin IM (including the IMUIKit UI components and the IMSDK core SDK) across Android / iOS / Web / Flutter. Describe what you want, and your AI assistant will follow a strict workflow to fetch up-to-date docs via YunXin MCP tools and verify API signatures from local datasets.

## 🚀 Quick Start

1) Configure the MCP Server (YunXin MCP)

Add the following to your AI IDE / MCP client config:

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

2) Install Skills (recommended: clone repo)

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

3) Configure the Skills path in your AI IDE

Add `~/.skills/yunxin-im-skills` as a skill source in your IDE.

4) Start building

Ask your AI assistant, for example:
- "How to integrate IMUIKit in Android?"
- "Flutter: query message history"
- "What is the Web login API?"
- "iOS unread count APIs"

## 📋 Table of Contents

- Quick Start
- Overview
- Coverage
- Prerequisites
- Installation
- Examples
- How It Works
- Troubleshooting

## 🌟 Overview

This repository helps AI assistants (Claude, Cursor, CodeBuddy, etc.) to:

- 🎯 Route requests to the right layer: IMUIKit (UI) vs IMSDK (SDK)
- 📖 Fetch documentation via MCP: `imuikit_doc` / `imsdk_doc`
- 🔎 Verify API signatures via local datasets: `nim_sdk_search_symbols` / `nim_sdk_list_members`
- 💻 Output minimal runnable guidance and code snippets, grounded in docs + signatures

## 🎯 Coverage

| Module | Description | Related MCP Tools |
| --- | --- | --- |
| IMUIKit (UI) | Conversation / Chat / Contact UI, initialization, integration, config, FAQ | `imuikit_doc` |
| IMSDK (Core SDK) | Initialization, login/logout, conversation, history, send/receive, unread, friends, block list | `imsdk_doc` |
| API Reference | Search symbols, list members, method signatures, fields | `nim_sdk_*` |

## ✅ Prerequisites

- Node.js >= 18
- npm >= 9
- An AI IDE/agent that supports Skills + MCP (Claude Desktop / Cursor / CodeBuddy, etc.)
- YunXin MCP Server can be started successfully (check IDE logs)

## 📦 Installation

### Step 1: Install / Configure YunXin MCP Server

Recommended via npx:

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

Restart your AI IDE after configuration changes.

### Step 2: Install Skills

Recommended:

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

Then add the repo path as a skill source in your IDE.

## 💬 Usage Examples

- Android IMUIKit integration: "How to integrate Android IMUIKit?"
- Web initialization + login: "What is the Web initialization API? What is the login API?"
- Flutter history messages: "How to query history messages in Flutter? What's the signature of getMessageListEx?"

## ⚙️ How It Works

1) Classify: UI (IMUIKit) vs SDK (IMSDK)  
2) Select platform (Android/iOS/Web/Flutter)  
3) Fetch official docs via MCP (`imuikit_doc` / `imsdk_doc`)  
4) When code/signature is required, verify via local datasets (`nim_sdk_*`)  
5) Output minimal runnable steps and code snippets with references

## ❓ Troubleshooting

### MCP Server connection failed

- Check Node version: `node --version` (>= 18 required)
- Validate JSON syntax (no trailing commas)
- Restart the IDE after changes
- Check IDE logs for errors

### Skills not loaded

- Verify skill source path is correct and accessible
- Ensure `SKILL.md` exists in the repo root
- Restart the IDE

