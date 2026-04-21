# 登录登出

用户业务侧的切换账号登录分为 **登录账号** 和 **登出账号** 两部分。

## 前提条件

根据本文操作前，请确保您已经完成了以下设置：

- 集成 IM UIKit
- 导入 IM UIKit 组件
- 集成 IM V10 SDK
- 注册 IM 账号，获取账号 ID（`account_id`）和 token。

## 模式介绍

- 登录分为使用 IM SDK 的登录接口和初始化 IM UIKit。
- 登出分为使用 IM SDK 的登出接口和重置 IM UIKit 中的 `store`。

    ```mermaid
    graph TD
    切换账号登录 --> 登录账号 --> A["IM SDK 登录接口"]
    登录账号 --> B["初始化 IM UIKit"]
    切换账号登录 --> 登出账号 --> C["IM SDK 登出接口"]
    登出账号 --> D["重置 IM UIKit store"]
    ```

## 登录账号

- **IM SDK 登录接口**：

    通过 IM SDK 提供的 `nim.V2NIMLoginService.login` 接口，登录网易云信 IM 服务端。

    ```JavaScript
    nim.V2NIMLoginService.login(account, token, {
      retryCount: 5,
    })
    ```

- **初始化 IM UIKit**：

    React 框架下，您只需将 `nim` 实例传给 `Provider`，`Provider` 会自动初始化 `store`。

    ```JavaScript
    import React, { useEffect, useState } from 'react'
    import { Provider, ConversationContainer, ChatContainer } from '@xkit-yx/im-kit-ui'
    import V2NIM from 'nim-web-sdk-ng/dist/v2/NIM_BROWSER_SDK'

    import '@xkit-yx/im-kit-ui/es/style/index'

    const appkey = "" // 填入 appkey

    // 初始化 v10 nim
    const nim = V2NIM.getInstance({
      appkey,
      debugLevel: 'debug', // 设置日志 level
      apiVersion: 'v2',
    })

    export default () => {
      const [account, setAccount] = useState('')
      const [token, setToken] = useState('')

      useEffect(() => {
        // 设置账号 1
        setAccount('')
        setToken('')
      }, [])

      // 连接 SDK 服务器
      useEffect(() => {
        if (!account || !token) {
          return
        }
        // 当 App 完成渲染后，登录 IM 账号 1
        nim.V2NIMLoginService.login(account, token, {
          retryCount: 5,
        })
      }, [account, token])

      // 将 SDK 实例传入 Provider 组件，完成 store 初始化
      return (
        <Provider nim={nim}>
          <ConversationContainer />
          <ChatContainer />
        </Provider>
      )
    }
    ```


## 登出账号

- **IM SDK 登录接口**：

    通过 IM SDK 提供的 `nim.V2NIMLoginService.logout` 接口，可登出网易云信 IM 服务端。

    ```JavaScript
    nim.V2NIMLoginService.logout();
    ```

- **重置 IM UIKit `store`**：

    ```JavaScript
    store.resetState()
    ```

    ```JavaScript
    import { useStateContext } from "@xkit-yx/im-kit-ui";
    const { store, nim } = useStateContext();

    const logout = () => {
      nim.V2NIMLoginService.logout()
      store.resetState()
    }
    ```
