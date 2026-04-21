# 初始化

在使用网易云信即时通讯 UIKit（简称 IM UIKit）功能前，您需要先完成初始化。本文介绍了如何初始化 V10 系列 IM UIKit，主要适用于 React 框架项目。

## 前提条件

根据本文操作前，请确保您已经完成了以下设置：

- 集成 IM UIKit
- 导入 IM UIKit 组件
- 集成 IM V10 SDK
- 注册 IM 账号，获取账号 ID（`account_id`）和 token。

## 注意事项

- 在应用生命周期内，只需初始化一次。
- Provider 内部不再管理 IM UIKit 与 IM 服务端的连接与断开。

## 初始化参数

 参数 | 类型 | 是否必选 | 说明 |
 ---- | ---- | ---- | ---- |
 `children` | ReactNode | 是 | 子组件 |
 `nim` | V2NIM | 是 | IM V10 SDK 实例 |
 `singleton` | boolean | 否 | 单例模式，多个 Provider 使用同一个 store 实例，并且在 Provider 组件销毁时，不会销毁 store 实例 |
  `locale` | string  | 否 | 语言配置，取值范围为 `zh` 或 `en`。 |
 `localeConfig` | Object | 否 | 自定义文案。详细配置参考 |
 `renderImConnecting` | () => JSX.Element | 否 | 连接中的渲染函数，不传默认为"Loading……" |
 `renderImDisConnected` | () => JSX.Element | 否 | 连接断开时的渲染函数，不传默认为"当前网络不可用，请检查网络设置"。 |
 `localOptions` | Partial\<LocalOptions> | 否 | 本地默认行为参数。 |
   `addFriendNeedVerify` | boolean | 否 | 添加好友是否需要对方确认，默认为 `true`。 |
   `iconfontUrl` | string\[] | 否 | 静态资源地址，10.6.0 及后续版本支持。该参数适用于私有化服务场景。 |
   `teamUpdateTeamMode` | `V2NIMTeamUpdateInfoMode` | 否 | 群组信息更新模式，默认仅群主和管理员可修改。 |
   `teamUpdateExtMode` | `V2NIMTeamUpdateExtensionMode` | 否 | 群组自定义字段更新模式，默认仅群主和管理员可修改。 |
   `needMention` | boolean | 否 | 是否需要@消息提醒，默认为 `true`。 |
   `enableLocalConversation` | boolean | 否 | 是否开启本地会话模式，默认为 `true`。 |
   `leaveOnTransfer` | boolean | 否 | 转让群主身份的同时是否退出群聊，默认为 `false`。 |
   `allowTransferTeamOwner` | boolean | 否 | 是否允许群主转让，默认为 `false`。 |
   `loginStateVisible` | boolean | 否 | 是否显示在线离线状态，默认为 `true`。 |
   `p2pMsgReceiptVisible` | boolean | 否 | 是否需要显示单聊消息的已读和未读标记，默认为 `false`。 |
   `teamMsgReceiptVisible`      | boolean | 否 | 是否需要显示群聊消息的已读和未读标记，默认为 `false`。 |
   `teamManagerVisible` | boolean | 否 | 是否需要显示群管理员功能，默认为 `false`（不显示）<br>该配置项用来控制当身份为管理员时的主动操作是否展示，不影响其他管理员身份的显示。若需要，可设置为 true。 |
   `teamManagerLimit` | number | 否 | 单个群管理员默认数量限制，默认 10 个。 |
   `sendMsgBefore` | function | 否 | 发送消息前的钩子函数，异步函数，可在发送消息前拿到消息体，向消息体中添加自定义参数。原型：`sendMsgBefore: <T = ISendCustomMsgOptions | IBaseSendFileOptions | ISendTextMsgOptions>(options: T) => Promise<T>;` <br>例如给消息加上推送相关参数，具体请参考以下示例。
   `debug` | string | 否 | 是否开启日志打印，取值范围为 `'off' | 'debug'`。
   `aiVisible` | boolean | 否 | 是否开启 AI 数字人功能，默认为 true。
   `aiUserAgentProvider` | `AIUserAgentProvider` | 否 | AI 数字人的模型供应商。
   `conversationLimit` | number | 否 | 会话列表拉取会话数量，默认为 100。
   `aiStream` | boolean | 否 | 是否使用流式输出 AI 消息。默认为 true（流式）。

## 初始化示例

### 给消息加上推送

给消息加上 **推送** 相关参数的示例代码如下，在 `localOptions` 中的 `sendMsgBefore` 中获取到发送前的消息体，在消息体上挂载相关参数：

```TypeScript
// 本地默认行为参数
const localOptions = {
  addFriendNeedVerify,
  sendMsgBefore: (options: any) => {
    const pushInfo = {
      needPushNick: '',
      isPushable: true,
      pushContent: '',
      pushPayload: JSON.stringify({ channel_id: 'xxxxx' }),
      apns: {
        accounts: '',
        content: '',
        forcePush: '',
      },
    }
    options = {
      ...options,
      ...pushInfo,
    }
    return options
  },
}

<Provider
  // ...
  localOptions={localOptions}
>
  <IMApp />
</Provider>
```

### 发送消息并抄送

可以在发送消息时，设置将当前消息进行 **抄送**。在 `sendMsgBefore` 进行配置，将 `env` 字段挂载在发送前的消息体上即可。

```JavaScript
// 本地默认行为参数
const localOptions = {
  addFriendNeedVerify,
  sendMsgBefore: (options, type) => {
    const env = 'xxxx'
    return { ...options, env }
  },
}

<Provider
  // ...
  localOptions={localOptions}
>
  <IMApp />
</Provider>
```

### **完整示例**

```JSX
import React, { useMemo, useEffect } from 'react'
import { Provider } from '@xkit-yx/im-kit-ui'
import V2NIM from 'nim-web-sdk-ng'

const initOptions = {
  appkey: '', // 传入您的 App Key
  account: '', // 传入 IM 账号
  token: '',   // 传入您的 Token
}

const App = () => {
  // 初始化 SDK 实例
  const nim = useMemo(() => {
    return V2NIM.getInstance({
      ...initOptions,
      debugLevel: 'debug', // 设置日志 level
      apiVersion: 'v2',
    })
  }, [])

  // 连接 SDK 服务器
  useEffect(() => {
    // 当 App 完成渲染后，登录 IM
    nim.V2NIMLoginService.login(initOptions.account, initOptions.token, {
      retryCount: 5,
    })

    // 当 App 卸载时，登出 IM
    return () => {
      nim.V2NIMLoginService.logout()
    }
  }, [nim])

  // 将 SDK 实例传入 Provider 组件，完成初始化
  return (
    <Provider nim={nim}>
      <div className="app">……</div>
    </Provider>
  )
}
```
