# 集成搜索组件

IM UIKit 提供搜索组件（`SearchContainer`），搜索组件由搜索功能组件（`SearchContainer`）和添加功能组件（`AddContainer`）组成。

## 界面效果

![Web 搜索组件 930.png](https://yx-web-nosdn.netease.im/common/1f42a6101cc38c2024645dca84c97ea8/Web搜索组件930.png)

## 界面集成

在您的 React 项目中导入搜索功能组件和添加功能组件，并设置搜索界面的位置和尺寸，即可集成 IM UIKit 默认的搜索界面。

示例代码如下：

```JSX
import React from 'react'
import { AddContainer, SearchContainer } from '@xkit-yx/im-kit-ui'

import '@xkit-yx/im-kit-ui/es/style'

const Page = () => {
  const goChat = () => {
    // 点击去聊天，可以在此做路由跳转等操作
  }

  return (
    <div
      style={{
        display: 'flex',
        width: 800,
        alignItems: 'center',
      }}
    >
      <SearchContainer onClickChat={goChat} />
      <div style={{ marginLeft: 20 }}>
        <AddContainer onClickChat={goChat} />
      </div>
    </div>
  )
}
```

## 定制界面

IM UIKit 默认的搜索组件，支持如下功能：

- 搜索好友或群组
- 添加好友
- 创建群组
- 加入群组

如果默认搜索界面无法满足您的业务需求，请根据自定义参数进行自定义，或者基于 NIM SDK 自行实现搜索，搜素组件（`SearchContainer`）提供的自定义参数。
