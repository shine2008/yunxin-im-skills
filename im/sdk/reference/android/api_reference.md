<!-- keywords: NIM SDK,Android,API Reference,接口查询,实体结构,方法列表,字段列表,Doc JSON -->

本文介绍如何通过 MCP 查询 Android NIM SDK（latest）的接口/实体结构信息。该能力用于回答“某接口有哪些方法”“某实体有哪些字段”“方法签名与参数是什么”等结构化问题，不替代集成流程类文档（初始化、登录、收发消息等）。

## 使用前说明

- 数据集由您提供（上传到仓库指定目录），MCP 工具仅读取本地 Doc JSON 文件。
- 数据集版本固定为 latest（目录固定），当前不提供多版本切换。

## 数据集放置位置

将 Doc JSON 数据集放到以下目录：

`src/resource/nim-sdk-api/android/latest/`

目录结构约定：

- `index.json`：全局索引（列包/列类/搜索）
- `index.members.ndjson`：成员索引（用于成员级搜索，推荐）
- `com/netease/.../*.json`：按类/接口分片的详情文件（用于列出某个类的 methods/fields 等）

建议始终提供 `index.json`（packages/classes）与 `index.members.ndjson`（members）。当缺少 `index.json` 时，工具可能会进入 `filesystem_scan` 模式从 `com/**.json` 扫描构建索引（可用但性能较差）。

## index.json 最小字段（schemaVersion=1）

```json
{
  "schemaVersion": "1",
  "platform": "android",
  "sdkVersion": "latest",
  "packages": ["com.netease.nimlib.sdk.v2.message"],
  "classes": [
    {
      "kind": "interface",
      "name": "V2NIMMessage",
      "qualifiedName": "com.netease.nimlib.sdk.v2.message.V2NIMMessage",
      "package": "com.netease.nimlib.sdk.v2.message",
      "sourcePath": "com/netease/nimlib/sdk/v2/message/V2NIMMessage.json",
      "documentation": null
    }
  ],
  "generatedAt": "2026-04-13T00:00:00Z"
}
```

字段说明（最小集）：

- `packages`：用于 `nim_sdk_list_packages` 的返回
- `classes[]`：用于 `nim_sdk_list_classes` / `nim_sdk_search_symbols(scope=classes)` 命中类
- `classes[].sourcePath`：类详情 JSON 的相对路径（相对 `src/resource/nim-sdk-api/android/latest/`）

## index.members.ndjson（可选但推荐）

当需要成员级搜索（`scope=members`）时，建议提供该索引文件以提升性能与减少 `index.json` 体积。

该文件为 NDJSON（每行一个 JSON 对象），示例（单行）：

```json
{"kind":"method","name":"getText","qualifiedName":"com.netease.nimlib.sdk.v2.message.V2NIMMessage#getText","ownerQualifiedName":"com.netease.nimlib.sdk.v2.message.V2NIMMessage","signature":"java.lang.String getText()","documentation":null}
```

## 类详情 JSON 最小字段

类详情 JSON 用于 `nim_sdk_list_members` 返回 methods/fields/constructors 等。

```json
{
  "name": "V2NIMMessage",
  "qualifiedName": "com.netease.nimlib.sdk.v2.message.V2NIMMessage",
  "kind": "interface",
  "modifiers": ["public", "abstract"],
  "superClass": null,
  "interfaces": [],
  "documentation": null,
  "fields": [],
  "constructors": [],
  "methods": [
    {
      "name": "getText",
      "signature": "java.lang.String getText()",
      "returnType": "java.lang.String",
      "modifiers": ["public", "abstract"],
      "parameters": [],
      "documentation": null
    }
  ],
  "enumConstants": [],
  "nestedTypes": []
}
```

## MCP 工具列表

### 1) 查询工具概览

- `nim_sdk_help`：返回数据集加载状态、数据集路径、可用工具与示例
- `nim_sdk_list_packages(prefix?, limit?, offset?)`：列出包名（可用 prefix 过滤，支持分页）
- `nim_sdk_list_classes(package, limit?, offset?, with_docs?)`：列出某个包下的类/接口/枚举（支持分页，默认不返回 documentation）
- `nim_sdk_search_symbols(query, scope?, limit?, with_docs?)`：按关键字搜索类/成员（默认不返回 documentation）
- `nim_sdk_list_members(class_name, kind?, with_docs?)`：列出指定类的成员（methods/fields/constructors/enum/nested）

### 2) 常见用法示例

1. 先搜索类全限定名：

   - tool: `nim_sdk_search_symbols`
   - params: `{ "query": "V2NIMMessage", "scope": "classes", "limit": 20 }`

2. 再查看该类的方法列表：

   - tool: `nim_sdk_list_members`
   - params: `{ "class_name": "com.netease.nimlib.sdk.v2.message.V2NIMMessage", "kind": "methods", "with_docs": true }`

3. 查看字段列表：

   - tool: `nim_sdk_list_members`
   - params: `{ "class_name": "com.netease.nimlib.sdk.v2.message.V2NIMMessage", "kind": "fields", "with_docs": false }`
