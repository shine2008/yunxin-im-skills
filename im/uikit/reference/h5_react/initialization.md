# 集成源码

> 更新时间：2026/02/02 18:41:12

nim-kit-react-ui-h5（简称 IM H5 React Demo）是基于网易云信 IM UIKit（NEUIKit）打造的一款 Demo，托管在 GitHub 开源代码仓库 `nim-uikit-H5`。该 Demo 提供了会话、聊天、群组等通用功能，您可以基于源码搭建自己的即时通讯业务逻辑。

---

## 前提条件

开始跑通 Demo 之前，请确保您已完成以下操作：

- 在**网易云信控制台**上创建应用，并获取 App Key。
- 注册 IM 账号，获取账号 ID 和 token。
- 在**网易云信控制台**上，为应用开通**消息漫游**开关（开启步骤请参考《控制台文档》配置应用）。
- 调整本地开发环境，满足如下要求：

| 配置项              | 要求                             |
| ------------------- | -------------------------------- |
| React 版本          | v18                              |
| 开发语言            | TypeScript                       |
| Node.js 版本        | v16 及以上                       |
| JavaScript 包管理器 | npm（版本请与 Node.js 版本匹配） |

---

## 第一步：创建项目

创建 React 项目，详细步骤请参考 **React 官网文档**。Demo 演示工程使用 umi。

> 如果您已创建项目，请忽略本步骤。

---

## 第二步：下载组件

运行以下命令将 NEUIKit GitHub 源码项目 `nim-uikit-H5` 下载到您的项目中：

```bash
# 找桌面任意目录，clone 组件项目
git clone https://github.com/netease-kit/nim-uikit-H5.git
```

在 clone 下的组件项目中找到 **NEUIKit** 文件夹，将其复制到您项目的 `components` 目录下。

执行以下命令安装 NEUIKit 所需依赖：

```bash
npm i nim-web-sdk-ng @xkit-yx/im-store-v2 @xkit-yx/utils dayjs mobx mitt --save
```

| 依赖                   | 说明                                                                                                                                                                              |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nim-web-sdk-ng`       | 网易云信 NIM SDK，提供了一整套即时通讯基础能力。                                                                                                                                  |
| `@xkit-yx/im-store-v2` | store 用于全局状态管理，基于 MobX 和 NIM SDK 封装的全局上下文对象，提供数据和 UI 之间的双向绑定能力。包含多个子模块，分别管理会话、消息、通讯录、群组等数据。详见**全局上下文**。 |
| `@xkit-yx/utils`       | 项目中的工具库，提供 `getFileType`、`parseFileSize` 等函数。                                                                                                                      |
| `dayjs`                | 轻量级日期和时间处理库。                                                                                                                                                          |
| `mobx`                 | 主要用于全局数据状态管理。                                                                                                                                                        |
| `mitt`                 | 主要用于在应用程序中实现事件的发布和订阅机制。                                                                                                                                    |

下载成功后，可根据业务需求对源码进行修改，具体目录结构请参考**工程结构**。

---

## 第三步：集成并初始化组件

在 `src/layouts/index.tsx` 文件中初始化 `nim` 和 `store`（其他架构请在页面入口处自行处理）。

```typescript
import { Outlet, useNavigate, useLocation } from '@/utils/router'
import V2NIM from 'nim-web-sdk-ng'
import React, { useEffect, useMemo, useState, useCallback } from 'react'
import { LocalOptions } from '@xkit-yx/im-store-v2/dist/types/types'
import { Provider } from '@/NEUIKit/common/contextManager/Provider'
import { toast } from '@/NEUIKit/common/utils/toast'
import { NIM_APP_KEY, NIM_DEBUG_LEVEL, NIM_LBS_URLS, NIM_LINK_URL } from '@/config'
import zh from '../NEUIKit/common/locales/zh'
import en from '@/NEUIKit/common/locales/en'
import TabBar from './tabBar'
import { V2NIMError } from 'nim-web-sdk-ng/dist/esm/types'
import { defaultLocalOptions } from '@/NEUIKit/common/utils/init'

const Layout: React.FC = () => {
  const location = useLocation()
  const navigate = useNavigate()
  const [language, setLanguage] = useState<'zh' | 'en'>('zh')

  // 使用 useRef 确保即使在 StrictMode 下也只执行一次
  const isInitializedRef = React.useRef(false)
  const isLoggingInRef = React.useRef(false)

  const canShowBottomNav = ['/', '/conversation', '/contacts', '/my'].includes(location.pathname)

  // 使用 useRef 确保 getInstance 只执行一次（即使在 StrictMode 下组件重新挂载）
  const nimInstanceRef = React.useRef<ReturnType<typeof V2NIM.getInstance> | null>(null)
  const nimInstance = nimInstanceRef.current

  // 避免每次渲染都创建新对象，配置对象只创建一次
  const languageMap = useMemo(() => ({ zh, en }), [])

  // 初始化 UIKit 并登录 NIM
  const initIMUiKit = useCallback(async (opts: string) => {
    let account = ''
    let token = ''
    // ...忽略前置检查

    try {
      await nimInstance.V2NIMLoginService.login(account, token, {
        // 强制模式默认设置为 false：自动登录时若多端登录遇到冲突，不会挤掉其他端，本次登录会以 code: 417 失败
        forceMode: false,
        // 演示为静态登录模式 - 固定账号密码
        authType: 0,
      })
      // IM 登录成功后跳转到会话页面
      navigate('/conversation')
    } catch (err) {
      const error = err as V2NIMError
      console.error('NIM Login failed!', error.toString())
      // 302 账号密码不可用，需要重新输入；417 多端登录冲突，需要重新强制登录
      if (error.code === 302 || error.code === 417) {
        toast.error(`NIM Login failed: ${error.message}`)
        navigate('/login')
        return
      }
      toast.error(`NIM Login failed: ${error.code}, ${error.message}`)
    }
  }, [nimInstance, navigate])

  // 初始化检查 - 只执行一次（即使在 StrictMode 下）
  useEffect(() => {
    // ...忽略前置检查

    isLoggingInRef.current = true

    try {
      initIMUiKit(loginInfo).finally(() => {
        isLoggingInRef.current = false
      })
    } catch (err) {
      console.log('LoginInfo JSON parse error', err)
      // 登录信息无效，清除并跳转到登录页
      localStorage.removeItem('__yx_im_options__h5')
      navigate('/login')
      isLoggingInRef.current = false
    }
  }, [initIMUiKit])

  // 本地默认行为参数
  const localOptions: Partial<LocalOptions> = useMemo(() => {
    return { ...defaultLocalOptions }
  }, [])

  return (
    <div className="layout-container">
      <Provider
        localeConfig={languageMap[language]}
        localOptions={localOptions}
        locale={language}
        // @ts-expect-error
        nim={nimInstance}
        singleton={true}
      >
        <div className={'main-content'}>
          <Outlet />
        </div>
        {canShowBottomNav && <TabBar />}
      </Provider>
    </div>
  )
}

export default Layout
```

示例代码中提供了一个 `Provider` 组件，内部用 `Context.Provider` 包裹，便于 React 组件获取全局对象（如 `nim`、`store` 等）：

```typescript
export const Provider: FC<ProviderProps> = memo(function Main({
  children,
  localOptions,
  nim,
  locale = 'zh',
  localeConfig = zh,
  singleton = false,
}) {
  const localeMap = useMemo(() => ({ zh }), [])

  const t = useCallback(
    (str: keyof typeof zh) => {
      return { ...(localeMap[locale] || zh), ...localeConfig }[str]
    },
    [locale, localeConfig, localeMap]
  )

  const finalLocalOptions = useMemo(() => ({ ...localOptions }), [localOptions])

  const rootStore = useMemo(() => {
    if (singleton) {
      // @ts-ignore
      return RootStore.getInstance(nim, finalLocalOptions, 'H5')
    }
    // @ts-ignore
    return new RootStore(nim, finalLocalOptions, 'H5')
  }, [nim, singleton, finalLocalOptions])

  // @ts-ignore
  window.__xkit_store__ = {
    nim,
    store: rootStore,
    localOptions: finalLocalOptions,
    sdkVersion: sdkPkg.version,
  }

  useEffect(() => {
    return () => {
      if (!singleton) {
        rootStore.destroy()
      }
    }
  }, [rootStore, singleton])

  return (
    <Context.Provider
      value={{
        store: rootStore,
        nim,
        localOptions: finalLocalOptions,
        locale,
        t,
      }}
    >
      <App>{children}</App>
    </Context.Provider>
  )
})
```

---

## 第四步：配置路由

1. 在 `src/routes.tsx` 中配置路由，例如：

```typescript
const ContactsPage = lazy(() => import('./pages/contacts'))
// ...
{ path: 'contacts', element: <ContactsPage /> }
```

2. 在 `src/pages/contacts/index.tsx` 中引入对应的 NEUIKit 组件：

```typescript
import React from 'react'
import ContactContainer from '@/NEUIKit/contact'

const ContactsPage = () => {
  return <ContactContainer />
}

export default ContactsPage
```

完整路由可参考 Demo 中的 `src/routes.tsx`。

---

## 第五步：运行项目

通过 `npm run start` 或您项目的自定义运行命令，在合适的时机展示聊天页面。

---

## 自定义开发

### API 参考

IM H5 Demo 基于 `UIKitStore` 开发，相关 API 详情请参考 **UIKitStore**。

---

## 常见问题

### 关于 React 降低版本运行的说明

自 `@xkit-yx/im-kit-react-ui-h5@10.1.0` 版本起，需要注意以下几点：

- umi 配置已经被移除，打包请使用 **webpack**。
- 由于 React 16 与 React 18 引入 DOM render 的方式不同，云信仓库内部已提供 `src/utils/reactDomCompat.ts` 用于 React DOM 兼容性适配层。开发者只需安装 React 16 版本，再打开 `src/utils/reactDomCompat.ts` 中关于 React 16 的兼容代码并注释 React 18 的兼容代码，即可在 React 16 中正常运行。
- 更多 React 版本兼容方式，可自行查阅 React 官网文档。
