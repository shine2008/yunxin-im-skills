# 集成会话消息（聊天）组件

聊天（会话消息）组件（`ChatContainer`）模块主要功能为传递用户间消息、展示消息、控制聊天，同时提供了聊天输入框、聊天内容展示和控制、聊天设置等功能。

## 界面效果

![会话消息界面 930.png](https://yx-web-nosdn.netease.im/common/fd013d869d7baf58949d8c7559f46ebd/会话消息界面930.png)

## 界面集成

在您的 React 项目中导入会话消息组件，并设置会话消息界面尺寸，即可集成 IM UIKit 默认的会话消息界面。

示例代码如下：

```JSX/TSX
import React from 'react'
import { ChatContainer } from '@xkit-yx/im-kit-ui'

import '@xkit-yx/im-kit-ui/es/style'

const Page1 = () => {
  return (
    <div style={{ width: '100vw', height: '100vh' }}>
      <ChatContainer />
    </div>
  )
}
```

## 功能介绍

IM UIKit 默认的会话消息界面，支持如下功能：

- 聊天输入框
  - 发送消息
  - 发送表情
  - 发送图片
  - 发送文件
- 聊天内容展示和控制
  - 聊天消息、好友资料展示
  - 聊天消息撤回、删除、重新编辑
  - 聊天好友拉黑/解除拉黑、删除/添加
- 聊天设置
  - 单聊设置，创建群聊
  - 群聊设置，群解散/群退出、群资料展示与修改、群成员添加/移除、群权限设置等
