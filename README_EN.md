# yunxin-im-skills

English | 中文

This repository provides YunXin IM Skills (covering IMUIKit UI components and the IMSDK core SDK) for Android / iOS / Web / Flutter. Describe what you want, and your AI assistant will follow a strict workflow based on the reference docs shipped with this repo.

## Quick Start

1) Install Skills (recommended: clone the repo)

```bash
git clone <YOUR_GIT_REPO_URL> ~/.skills/yunxin-im-skills
```

2) Configure the Skills path in your AI IDE

Add `~/.skills/yunxin-im-skills` as a skill source in your IDE.

3) Start building

Ask your AI assistant, for example:
- "How to integrate IMUIKit in Android?"
- "Flutter: how to query history messages?"
- "What is the Web login flow?"
- "iOS: how to get unread counts?"

## Table of Contents

- Quick Start
- Overview
- Coverage
- Structure
- Examples
- How It Works
- FAQ

## Overview

This repository helps AI assistants (Claude, Cursor, CodeBuddy, etc.) to:

- Route requests to the right layer: IMUIKit (UI) vs IMSDK (SDK)
- Prefer repo-local reference docs when generating guidance and code
- Output minimal runnable steps first, then expand when needed
- Apply a signature strategy: do not promise exact method signatures by default; when an API lookup capability is available in the runtime, use it to verify signatures and parameters

## Coverage

| Module | Description |
| --- | --- |
| IMUIKit (UI) | Conversation / Chat / Contact UI, initialization, integration, configuration, FAQ |
| IMSDK (Core SDK) | Initialization, login/logout, conversations, history, send/receive, unread, friends, block list, etc. |
| Reference | Platform/topic reference docs and indexes for IMUIKit/IMSDK |

## Structure

- Skill entry points and routing:
  - Root entry: `SKILL.md`
  - IM Router: `im/SKILL.md`
  - IMUIKit: `im/uikit/SKILL.md`
  - IMSDK: `im/sdk/SKILL.md`
- Reference docs (offline-friendly):
  - IMUIKit: `im/uikit/reference/<platform>/<topic>.md`
  - IMSDK: `im/sdk/reference/<framework>/<topic>.md`
  - Indexes:
    - `im/uikit/reference/index.md`
    - `im/sdk/reference/index.md`
    - `im/reference/coverage.md`

## Examples

- Android IMUIKit integration: "How to integrate Android IMUIKit?"
- Web initialization and login: "What is the Web initialization flow? What is the login flow?"
- Flutter history messages: "How to query history messages in Flutter?"

## How It Works

When you describe a requirement, the AI assistant will:

1) Identify whether it is UI (IMUIKit) or SDK (IMSDK)
2) Select the target platform (Android/iOS/Web/Flutter, etc.)
3) Prefer the repo-local reference docs (by platform/framework and topic)
4) If the user asks for deeper API details but the runtime has no API lookup capability, return the most relevant Service docs to ground the answer and suggest verifying signatures later
5) Output minimal runnable steps and examples, with reference doc entry points

## FAQ

### Skills not loaded

- Verify the skill source path is correct and accessible
- Ensure `SKILL.md` exists in the repo root
- Restart the IDE
