# 导入 IM UIKit 组件

本文介绍如何安装依赖和导入 IM UIKit（V10）的组件。

::: note notice
IM UIKit 当前不支持 H5 界面搭建，仅支持 Web 浏览器界面搭建。
:::

## 环境要求

- React 建议使用版本： 16.8.0 <= 建议版本
- React DOM 建议使用版本： React DOM 16.8.0 <= 建议版本

## 安装依赖

目前仅支持 npm 方式集成，暂不支持 CDN 方式集成。

```bash
// 目前是 beta 版本，后续会发正式版本，敬请期待
$ npm install @xkit-yx/im-kit-ui@10.x --save
```

## 导入组件

在 React 项目中按需导入组件：

```tsx
import {
  Provider, // 全局上下文
  ConversationContainer, // 会话列表组件
  ChatContainer, // 聊天（会话消息）组件
  AddContainer, // 搜索——添加按钮组件
  SearchContainer, // 搜索——搜索组件
  ContactListContainer, // 通讯录——通讯录导航组件
  ContactInfoContainer, // 通讯录——通讯录详情组件，包含好友列表、群组列表以及黑名单列表
  MyAvatarContainer, // 用户资料组件
} from '@xkit-yx/im-kit-ui';

// 引入 IM V10 SDK
import V2NIM from 'nim-web-sdk-ng';

//如果您希望引入组件 css：
import "@xkit-yx/im-kit-ui/es/style/css";
//如果您希望引入组件 less：
import "@xkit-yx/im-kit-ui/es/style";
```

:::note note
建议引入组件的 css 或 less（两者选其一）。
:::
