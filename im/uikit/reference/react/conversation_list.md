# 集成会话列表组件

会话列表组件（`ConversationContainer`）模块用于显示当前用户的会话（包括单聊和群聊）列表，同时提供会话单击和右键菜单功能，方便对会话进行操作。

## 界面效果

![Web 会话列表 930.png](https://yx-web-nosdn.netease.im/common/04da379d4fb3208979db3fd125c887ca/Web会话列表930.png){: style="width:50%;border: 1px solid #BFBFBF;"}

## 界面集成

在您的项目中导入会话列表组件，并设置会话列表界面尺寸，即可集成 IM UIKit 默认的会话列表界面。

示例代码如下：

```JSX/TSX
import React from 'react'
import { ConversationContainer } from '@xkit-yx/im-kit-ui'

import '@xkit-yx/im-kit-ui/es/style'

const Page1 = () => {
  return (
    <div style={{ width: '30vw', height: '100%' }}>
      <ConversationContainer />
    </div>
  )
}
```

## 功能介绍

IM UIKit 默认的会话列表界面，支持如下功能：

- 会话单击事件：
  - 选中会话
  - 重置会话未读数
- 右键菜单事件：
  - 设置会话免打扰
  - 删除会话
  - 置顶会话
