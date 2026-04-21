# 集成源码

> 更新时间：2026/02/03 13:37:11

网易云信 IM UIKit 是基于网易云信 IM SDK（NIM SDK）开发的即时通讯 UI 组件库，功能涵盖聊天、会话、群管理等核心场景。本文介绍如何在微信小程序中集成 IM UIKit。

---

## 集成流程概览

**步骤一：准备工作 → 步骤二：创建项目 → 步骤三：集成组件 → 步骤四：配置参数 → 步骤五：运行测试**

---

## IM UIKit 主要模块

| 模块           | 说明                                     |
| -------------- | ---------------------------------------- |
| `Conversation` | 会话列表，展示所有聊天会话               |
| `Chat`         | 聊天界面，支持发送文本、图片、语音等消息 |
| `Contact`      | 通讯录，管理好友和群组                   |
| `User`         | 个人信息，查看和编辑用户资料             |

---

## 前提条件

在开始集成之前，请确保您的开发环境满足以下要求：

| 环境要求       | 版本要求                |
| -------------- | ----------------------- |
| Node.js        | ≥ 16.0.0                |
| npm            | 与 Node.js 版本匹配     |
| 微信开发者工具 | 最新稳定版              |
| 开发语言       | JavaScript / TypeScript |

---

## 步骤一：准备工作

### 创建云信应用并获取 AppKey

1. 登录**网易云信控制台**。
2. 单击**创建应用**，填写应用名称等信息。
3. 创建完成后，在应用详情页获取 **AppKey**。

> 请妥善保管 AppKey，后续配置时需要使用。

---

## 步骤二：创建小程序项目

1. 打开微信开发者工具。
2. 单击**新建项目**。
3. 填写项目信息，选择 **TS + Less-基础模板**。
4. 单击**确定**创建项目。

---

## 步骤三：集成 UIKit 组件

### 1. 下载 UIKit 组件

下载 `NEUIKit_MiniApp.zip`，解压后将 `NEUIKit` 文件夹复制到项目的 `miniprogram/components` 目录下。

目录结构如下：

```
miniprogram/
├── components/
│   └── NEUIKit/          # UIKit 组件库
│       ├── Chat/         # 聊天模块
│       ├── Contact/      # 通讯录模块
│       ├── Conversation/ # 会话列表模块
│       ├── Login/        # 登录模块
│       └── User/         # 用户模块
├── pages/
└── app.ts
```

### 2. 安装依赖

在项目根目录执行以下命令安装所需依赖：

```bash
npm i nim-web-sdk-ng@^10.9.60 @xkit-yx/im-store-v2@^0.8.0 @xkit-yx/utils@^0.5.6 dayjs@^1.11.7 mitt@^3.0.1 --save
```

| 依赖包                 | 说明                                                       |
| ---------------------- | ---------------------------------------------------------- |
| `nim-web-sdk-ng`       | 网易云信 IM SDK，提供即时通讯基础能力。                    |
| `@xkit-yx/im-store-v2` | 基于 MobX 的全局状态管理，负责会话、消息、群组等数据管理。 |
| `@xkit-yx/utils`       | 工具库，提供文件类型判断、大小解析等函数。                 |
| `dayjs`                | 轻量级日期处理库。                                         |
| `mitt`                 | 事件总线库。                                               |

---

## 步骤四：配置参数

在 `miniprogram/app.ts` 中配置登录参数。

### 配置参数说明

| 参数                  | 说明                   | 获取方式         |
| --------------------- | ---------------------- | ---------------- |
| `APP_KEY`             | 云信应用的唯一标识     | 从云信控制台获取 |
| `LOGIN_BY_PHONE_CODE` | 是否使用手机验证码登录 | 默认 `true`      |
| `ACCID`               | 登录账号               | 手动登录时需配置 |
| `TOKEN`               | 登录密码 / Token       | 手动登录时需配置 |

### 方式一：手机验证码登录（推荐）

```typescript
// miniprogram/app.ts
const APP_KEY = "your_app_key"; // 替换为您的 AppKey
const LOGIN_BY_PHONE_CODE = true; // 使用手机验证码登录
```

### 方式二：账号密码登录

```typescript
// miniprogram/app.ts
const APP_KEY = "your_app_key"; // 替换为您的 AppKey
const LOGIN_BY_PHONE_CODE = false; // 使用账号密码登录
const ACCID = "your_account_id"; // 替换为您的账号
const TOKEN = "your_token"; // 替换为您的 Token
```

> **快捷替换**：全局搜索 `replace_your_app_key` 可快速定位需要替换的位置。

> ⚠️ **安全提示**：如果您在调试环境中使用的是网易云信控制台生成的测试用 IM 账号和 Token，请确保在正式生产环境中，将其替换为通过 IM 服务端 API 生成的正式 IM 账号（`accid`）和 Token，以保障通信安全。

---

## 步骤五：运行与测试

### 本地运行

1. 在微信开发者工具中，勾选**本地设置 > 不校验合法域名、web-view（业务域名）、TLS 版本以及 HTTPS 证书**。
2. 单击**清缓存 > 全部清除**，避免缓存导致渲染异常。
3. 单击**编译**运行项目。

### 域名配置（上线必须）

> ⚠️ **重要**：正式上线前，必须在微信公众平台配置服务器域名，否则无法正常使用。
>
> 配置路径：**微信公众平台 > 开发 > 开发管理 > 开发设置 > 服务器域名**

**socket 合法域名**

| 域名                        | 说明           | 是否必须   |
| --------------------------- | -------------- | ---------- |
| `wss://wlnimsc0.netease.im` | IM 业务域名    | ✅ 必须    |
| `wss://wlnimsc1.netease.im` | 聊天室业务域名 | 聊天室必须 |

**request 合法域名**

| 域名                              | 说明                     | 是否必须   |
| --------------------------------- | ------------------------ | ---------- |
| `https://lbs.netease.im`          | LBS 地址服务             | ✅ 必须    |
| `https://wlnimsc0.netease.im`     | IM 业务域名              | ✅ 必须    |
| `https://wlnimsc0.netease.im:443` | IM 业务域名（含端口）    | ✅ 必须    |
| `https://wlnimsc1.netease.im`     | 聊天室业务域名           | 聊天室必须 |
| `https://wlnimsc1.netease.im:443` | 聊天室业务域名（含端口） | 聊天室必须 |
| `https://statistic.live.126.net`  | 数据上报                 | 可选       |
| `https://abt-online.netease.im`   | A/B Test                 | 可选       |

**uploadFile 合法域名**

| 域名                         | 说明     | 是否必须 |
| ---------------------------- | -------- | -------- |
| `https://nos.netease.com`    | 文件上传 | ✅ 必须  |
| `https://oss.chatnos.com`    | 文件上传 | ✅ 必须  |
| `https://fileup.chatnos.com` | 文件上传 | ✅ 必须  |
| `https://upload.chatnos.com` | 文件上传 | ✅ 必须  |

**downloadFile 合法域名**

| 域名                           | 说明     | 是否必须 |
| ------------------------------ | -------- | -------- |
| `https://nim-nosdn.netease.im` | 文件下载 | ✅ 必须  |
| `https://nim.nosdn.127.net`    | 文件下载 | ✅ 必须  |

---

## 常见问题

### Q：编译报错找不到依赖？

请确保：

- 已执行 `npm install` 安装依赖。
- 在微信开发者工具中单击**工具 > 构建 npm**。

### Q：登录失败？

请检查：

- `AppKey` 是否正确配置。
- 账号和 Token 是否有效（账号密码登录模式）。
- 网络是否正常。

### Q：真机调试失败？

开发阶段可勾选**不校验合法域名**，正式上线前请完成域名配置。
