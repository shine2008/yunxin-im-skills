# 集成通讯录组件

通讯录组件（`ContactListContainer`）主要功能为显示当前用户的好友、黑名单以及群组关系。

## 界面效果

通讯录组件由以下个子组件组成：

- `ContactListContainer`：关系导航组件。
- `ContactInfoContainer`：关系详情组件，内部包含了好友列表、群组列表以及黑名单列表。

![Web 通讯录界面 930.png](https://yx-web-nosdn.netease.im/common/873aaf2964c33fabbe4d5e0d093ff98c/Web通讯录界面930.png){: style="width:80%;border: 1px solid #BFBFBF;"}

## 界面集成

在您的 React 或 Vue 项目中导入关系导航组件和关系详情组件，并配置界面相关设置（宽高、宽高比、位置），即可集成 IM UIKit 默认的通讯录界面。

示例代码如下：

```JavaScript
import React from 'react'
import { ContactListContainer, ContactInfoContainer } from '@xkit-yx/im-kit-ui'

import '@xkit-yx/im-kit-ui/es/style'

const Page1 = () => {
  return (
    <div
      style={{
        display: 'flex',
        width: 1000,
        height: 400,
        alignItems: 'center',
      }}
    >
      <div style={{ width: 300, height: '100%' }}>
        <ContactListContainer />
      </div>
      <div style={{ flex: 1, height: '100%', overflowY: 'auto' }}>
        <ContactInfoContainer />
      </div>
    </div>
  )
}
```
