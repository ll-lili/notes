## React

### React项目开发环境

#### 组件化

* 拆分页面结构，通过组件拼装页面，组件复用
* 易开发维护，尤其对于多人协作开发的大型项目

#### 数据驱动试图

* UI = f(state)
* 只关注业务数据的修改，不用再操作DOM，增加开发效率
* 尤其对于DOM结构复杂的项目

#### Create React App

```undefined
npm install --legacy-peer-deps
```

```shell
npx create-react-app my-app

# ts项目
npx create-react-app my-app --template typescript
```

#### Vite创建

```shell
# npm 6.x
npm create vite@latest my-vue-app --template react-ts

# npm 7+, extra double-dash is needed:
npm create vite@latest my-vue-app -- --template react-ts

# yarn
yarn create vite my-vue-app --template react-ts

# pnpm
pnpm create vite my-vue-app --template react-ts
```

#### eslint

* 参考：https://github.com/AlloyTeam/eslint-config-alloy
* vscode安装eslint插件

```shell
npm i eslint --save-dev # 安装eslint
npx eslint --init # 初始化eslint配置文件 npm 版本建议6

./node_modules/.bin/eslint --init
✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · Yes
✔ Where does your code run? · browser
✔ What format do you want your config file to be in? · JSON
The config that you've selected requires the following dependencies:
eslint-plugin-react@latest @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
✔ Would you like to install them now with npm? · Yes
```

```json
// package.json
"lint": "eslint \"src/**/**.{js,jsx,ts,tsx}\" --quiet"
```



#### prettier

* vscode安装prettier插件

```shell
npm install prettier eslint-config-prettier eslint-plugin-prettier --save-dev
```

```json
// .eslintrc.json
"extends": [
  ...
  "plugin:prettier/recommended"
]
// 在配置文件中添加react版本
"settings": {
    "react": {
        "version": "detect"
    }
}
```

```js
// .prettierrc.js
module.exports = {
  // 一行最多 120 字符
  printWidth: 120,
  // 使用 2 个空格缩进
  tabWidth: 2,
  // 不使用缩进符，而使用空格
  useTabs: false,
  // 行尾需要有分号
  semi: true,
  // 使用单引号
  singleQuote: true,
  // 对象的 key 仅在必要时用引号
  quoteProps: 'as-needed',
  // jsx 不使用单引号，而使用双引号
  jsxSingleQuote: false,
  // 末尾需要有逗号
  trailingComma: 'all',
  // 大括号内的首尾需要空格
  bracketSpacing: true,
  // jsx 标签的反尖括号需要换行
  bracketSameLine: false,
  // 箭头函数，只有一个参数的时候，也需要括号
  arrowParens: 'always',
  // 每个文件格式化的范围是文件的全部内容
  rangeStart: 0,
  rangeEnd: Infinity,
  // 不需要写文件开头的 @prettier
  requirePragma: false,
  // 不需要自动在文件开头插入 @prettier
  insertPragma: false,
  // 使用默认的折行标准
  proseWrap: 'preserve',
  // 根据显示样式决定 html 要不要折行
  htmlWhitespaceSensitivity: 'css',
  // vue 文件中的 script 和 style 内不用缩进
  vueIndentScriptAndStyle: false,
  // 换行符使用 lf
  endOfLine: 'lf',
  // 格式化嵌入的内容
  embeddedLanguageFormatting: 'auto',
  // html, vue, jsx 中每个属性占一行
  singleAttributePerLine: false
}

```

```json
// package.json
"format": "prettier --write \"src/**/*.{js,jsx,ts,tsx}\""
```

```json
// 保存时自动修复 ESLint 错误
// 如果想要开启「保存时自动修复」的功能，你需要配置 .vscode/settings.json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
}
// 设置保存自动格式化，对文件默认格式化方式改成prettier
// 不生效重启code
```

#### 选择仓库

* 工作使用公司内部git仓库
* 正式的开源项目，需要积累star，使用github
* 个人demo选择国内平台，如coding.net(1132251310@qq.com/china3423542.)

#### husky

* 一个git hook工具

* 再git commit之前执行的自定义的命令
* 如执行代码风格的检查，避免提交非规范代码

```shell
npm install husky -D

# Edit package.json > prepare script and run it once:
npm pkg set scripts.prepare="husky install" # npm v7
npm run prepare
# "scripts": {
#     "prepare": "husky install"
#  },

# Add a hook:
npx husky add .husky/pre-commit "npm run lint"
npx husky add .husky/pre-commit "npm run format"
git add .husky/pre-commit

# husky/pre-commit文件
npm run lint
npm run format

```

#### commitlint

```shell
# Install commitlint cli and conventional config
npm install --save-dev @commitlint/{config-conventional,cli}
# For Windows:
npm install --save-dev @commitlint/config-conventional @commitlint/cli

# Configure commitlint to use conventional config
# 注意文件字符集是否是utf-8
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js

# Add hook
npx husky add .husky/commit-msg  'npx --no -- commitlint --edit ${1}'
```

#### craco.js扩展webpack配置

https://github.com/dilanx/craco

https://craco.js.org

```shell
npm i -D @craco/craco

```

```
  my-app
  ├── node_modules
+ ├── craco.config.js
  └── package.json
```

```
"scripts": {
-  "start": "react-scripts start"
+  "start": "craco start"
-  "build": "react-scripts build"
+  "build": "craco build"
-  "test": "react-scripts test"
+  "test": "craco test"
}
```



### JSX 语法

#### JSX标签和属性

* 区分大小写，小写就是html标签，首字母大写就是自定义组件
* 标签必须闭合
* jsx代码片段必须有一个根节点（或者Fragment，<></>）
* 属性 class改为className
* style要写成js对象（不能是string）而且key用驼峰的写法
* for改为htmlFor
* {}模板语法：写js表达式, 函数， 变量，注释
* 属性渲染变量： 属性={变量}
* 添加事件渲染函数：onClick={handleClick}

```jsx
const html = (
    const handleClick = () => {
    	console.log('click')
	}
	
	<>
    	<a style={{color: 'red', textdecoration: 'none'}}>hello</a>
		{/* 注释 */}
   		<div className="container">
    		<label htmlFor="user">用户名</label>
    		<input id="user" />
        	<button onClick={handleClick}>点击</button>
    	</div>
    </>
)

```

#### 事件

* 使用onXxx的形式，如：onClick
* 必须传入一个函数（是fn,非fn()）

```tsx
import React from 'react'
import type { MouseEvent } from 'react'
import logo from './logo.svg'
import './App.css'

function App() {
  const fn = (event: MouseEvent<HTMLButtonElement>) => {
    event.preventDefault()
    console.log('click')
  }
  const fn2 = (event: MouseEvent<HTMLButtonElement>, name: string) => {
    event.preventDefault()
    console.log('click', name)
  }
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <p>hello</p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
        <div>
          <button onClick={fn}>点击</button>
           <button onClick={(event) => fn2(event, 'll')}>点击2</button>
        </div>
      </header>
    </div>
  )
}

export default App

```

#### 条件判断

* 使用 &&
* 使用三元表达式
* 使用函数封装

```jsx
const flag = true
function Hello () {
    if (flag) {
        return <p>hello</p>
    } else {
        <p>你好</p>
    }
}
const html = (
	<div>
        { flag && <p>hello</p>}
        { flag ? <p>hello</p> : <p>你好</p>}
        <Hello></Hello> {/* 自定义组件 */}
    </div>
)
```

#### 循环

* 使用数组map方法
* 每个item元素需要key属性,同级别不能重复

```jsx
const list = [
    {username: 'zhangsan'},
    {username: 'lisi'}
]
const html = (
	<ul>
        {
            list.map((user, index) => {
                const { username } = user
                return <li key={username}>{ username } { index }</li>
            })
        }
    </ul>
)
```

### 组件

* 组件就是一个ui的片段
* 拥有独立的逻辑和显示
* 组件可大可小可嵌套
* 组件拆分，利于代码维护，和多人协作开发
* 可封装公共组件（或第三方组件）复用代码，提高开发效率

### props

- 要传递 props，请将它们添加到 JSX，就像使用 HTML 属性一样。
- 要读取 props，请使用 `function Avatar({ person, size })` 解构语法。
- 你可以指定一个默认值，如 `size = 100`，用于缺少值或值为 `undefined` 的 props 。
- 你可以使用 `<Avatar {...props} />` JSX 展开语法转发所有 props，但不要过度使用它！
- 像 `<Card><Avatar /></Card>` 这样的嵌套 JSX，将被视为 `Card` 组件的 `BeforeEach` prop。
- Props 是只读的时间快照：每次渲染都会收到新版本的 props。
- 你不能改变 props。当你需要交互性时，你可以设置 state。

### Context

- 可跨层级传递，而不像props层层传递
- 类似于Vue的provide/inject

```tsx
// Demo.tsx
import React, { FC, createContext, useState } from 'react'
import Toolbar from './Toolbar'
const temes = {
    light: {
        fore: '#000',
        bg: '#eee'
    },
    dark: {
        fore:  '#fff',
        bg: '#222'
    }
    
}
// 定义Context
export conet TemeContext = createContext(themes.light)

const Deom: FC = () => {
    const [theme, setTeme] = useState(themes.light)
    const toDark = () => {
        setTeme(themes.dark)
    }
    return (
    	<TemeContext.Provider value={theme}>
            <div>
                <span> Conttext theme</span>
                <button onClick={toDark}>dark</button>
            </div>
            <Toolbar/>
        </TemeContext.Provider>
    )
}

// Toolbar.tsx
import React, { FC } from 'react'
import ThemeButton from './ThemeButton'

const Toolbar: FC = ()=> {
	return <>
    	<div>Toolbar</div>
    	<ThemeButton/>
    </>
}

// ThemeButton.tsx
import React, { FC, useContext } from 'react'
import {TemeContext} from './Demo'
const ThemeButton: FC = ()=> {
    const theme = useContext(TemeContext)
    const style = {
        color: theme.fore,
        background: theme.bg
    }
    return <button style={style}>ThemeButton</button>
}
```



### React Hooks

* 只能在组件内调用hook,或者在其他hook中调用
* 必须保证在组件顶层调用，不能再if和for中调用

#### useState

* props 父组件传递过来的信息
* state 组件内部的信息，不对外
* state 变化，触发组件更新，重新渲染rerender页面
* `setState`异步更新
* state不用于JSX中显示，那就不要用setState管理，用useRef
* `setState`可能会被合并, 通过函数更新不会被合并
* **不可变数据**，不能直接修改state,通过`setState`传入新的值

```jsx
import React, { useState } from 'react'
function App () {
    const [count, setCount] = useState(0)
    const add = () => {
        setCount(count + 1) // 异步更新，无法直接拿到最新值
        // setCount(count => count + 1) // 函数形式
        console.log(count) // 更新前的值
    }
    
    const [userInfo, setUserInfo] = useState({
        username: 'll',
        age: 22
    })
    const handleChange = () => {
        // 不可变数据
        // 错误的写法：userInfo.age = 23
        setUserInfo({
            ...userInfo,
            age: 23
        })
    }
    return (
    	<>
        	<button onClick={ add }>add: { count }</button>
        </>
    )
}
```

##### immer

* 改变state不可变数据

```jsx
/**
	安装immer: npm install immer --save
*/
import produce from 'immer'

// ...
const [userInfo, setUserInfo] = useState({
        username: 'll',
        age: 22
})
function handleChange () {
    setUserInfo(produce( draft => {
        draft.age = 20
    }))
}
//...
```

#### useEffect

##### 副作用

* 当组件渲染完成时（时机），触发一个行为，如：加载一个网络请求
* 当某个state更新时（时机），触发一个行为，如：加载一个网络请求
* 使用useEffect实现

```jsx
import { useEffect } from 'react'
// ...
const App = () => {
    useEffect(() => {
        console.log('当组件渲染完成时')
    }, []) // []是依赖的state, 有无依赖初次渲染都会触发
    
    useEffect(() => {
        console.log('当组件渲染完成时')
        return () => {
            console.log('当组件销毁时')
        }
    }, []) 
    // 生命周期：创建 更新（state变化） 销毁
    return(
        <>
            <div>app</div>
        </>
    )
}
// ...
```

##### useEffect执行两次

* React18开始，useEffect在**开发环境下**会执行两次
* 模拟组件创建、销毁、再创建的完整流程，及早暴露问题
* 生产环境下会执行一次

#### useRef

* 一般用于操作DOM
* 也可以传入普通js变量，但更新不会触发rerender

```tsx
import React, { FC, useRef } from 'react'

const Demo:FC = () => {
    const inputRef = useRef<HTMLInputElement>(null) // 传入泛型
    const selectInput = () => {
        const inputElem = inputRef.current // dom节点
        if (inputElem) {
            inputElem.select()
        }
    }
    return (
    	<>
        	<input onClick={ selectInput } defaultValue="默认值" />
        </>
    )
}
```

#### useMemo

* 函数组件，每次state更新都会重新执行函数
* useMemo可以缓存数据，不用每次执行函数都重新生成
* 可以用计算量较大的场景，缓存提高性能

```tsx
import React, { FC, useState, useMemo } from 'react'

const Demo:FC = () => {
    const [num1, setNum1] = useState(1)
    const [num2, setNum2] = useState(1)
    // num1, num1不放生改变，total不会重新计算
    const total = useMemo(() => num1 + num2, [num1, num1])
    return (
    	<>
        	<p>total</p>
        </>
    )
}
```

#### useCallback

* 和useMemo作用一样  
* 专门用于缓存函数

```tsx
import React, { FC, useCallback } from 'react'

const Demo:FC = () => {
    const fn1 = () => {
        console.log('fn1')
    }
    // fn2函数被缓存， 重新执行函数组件不会重新生成（根据[]依赖）
    const fn2 = useCallback(() => {
        console.log('fn2')
    }, [])
    return (
    	<>
        	<p>total</p>
        </>
    )
}
```

#### useReducer

- useState的替代方案
- 数据结构简单时用useState,复杂是用useReducer
- 简化版的redux

```tsx
// CountReducer.tsx
import React, { FC, useReducer } from 'react'

type StateType = {
    count: number
}
type AcionType = {
    type: string
}
const initialState: StateType = { count: 100 }

// 根据传入的action 返回新的 state(不可变数据）
function reducer (state: StateType, action: ActionType) {
    switch(action.type) {
        case 'increment':
            retutrn {count: state.count + 1}
        case 'decrement':
            retutrn {count: state.count - 1}
        default:
            thorw new Error()
    }
}

const CountReducer: FC = () => {
    const [state, dispatch] = useReducer(reducer, initialState)
    return (
    	<>
        	<div>Count: {state.count}</div>
        	<button onClick={() => dispatch({type: 'increment'})}>+<button>
            <button onClick={() => dispatch({type: 'decrement'})}>-button>
        </>
    )
}
```



#### 自定义Hook

* React组件公共逻辑的抽离和复用

```ts
// hooks/useTitle.ts
// 设置标题
import { useEffect } from 'react'

function useTitle (title: string) {
    useEffect(() => {
        document.title = title
    }, [])
}
export default useTitle

// 获取鼠标位置
import { useEffect, useState } from 'react'

function useMouseMove() {
  const [x, setX] = useState(0)
  const [y, setY] = useState(0)
  useEffect(() => {
    document.addEventListener('mousemove', handleMouseMove)
    return () => {
      document.removeEventListener('mousemove', handleMouseMove)
      console.log('移除mousemove')
    }
  }, [])
  const handleMouseMove = (e: MouseEvent) => {
    setX(e.pageX)
    setY(e.pageY)
  }
  return { x, y }
}
export default useMouseMove

// hook异步加载数据
// 其他组件中直接引入使用
```

#### 第三方Hooks

* `ahooks`国内
* react-use国外

### 闭包陷阱（`closureTrap`）

* 当异步函数获取state时，可能不是当前最新的state
* 使用useRef解决

```tsx
import React, { FC, useState, useRef, useEffect } from 'react'

const Demo:FC = () => {
    const [count, setCount] = useState(0)
    const countRef = useRef(0)
    useEffect(() => {
        countRef.current = count
    }, [count])
    const add = () => {
        setCount(count + 1)
    }
    const alertFn = () => {
        setTimeOut(() => {
            // alert(count) // 值类型
            alert(countRef.current) // 引用类型
        }, 3000)
    }
    return (
    	<>
        	<p>{count}</p>
        	<button onClick={add}>add</button>
       		<button onClick={alertFn}>alert</button>
        </>
    )
}
```

### React 中使用 `CSS `样式

#### 动态条件判断className

* 第三方库`classnames`、`clsx`

```tsx
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```



#### `CSS Module`

* 每个`css`文件都当做单独的模块。命名`xxx.module.css`
* 为每个className增加后缀名，不让他们重复
* `create-react-app`原生支持`css module`

```tsx
import styles from './xx.module.css'

const html =(
	<>
		<div className={styles.title}>title</div>
   		<p className={styles['list-item']}>item</p>
    </>
)
```

#### 使用sass

* npm install sass --save

* `CRA`原生支持Sass Module,后缀名.scss即可

#### `CSS-in-JS`

* 一种解决方案（而非工具名称），有好几个工具
* 再`JS`中写`CSS`，带来极大的灵活性
* 它和内联style完全不一样，也不会有内联style的问题

##### styled-components

```shell
npm install --save styled-components
npm install @types/styled-components --save
```

```tsx
import styled, { css } from 'styled-components'

type ButtonPropsType = {
    primary?: boolean
}

const BUTTON = styled.button`
	color: red;
	font-size: 24
	${ (props: ButtonPropsType) => props.primary && css`
		background: red;
	`}
`
const Container = styled.div`
	text-align: center;
`
// styled.div`` 是函数调用，实参返回一个数组
const html =(
	<>
		<div>title</div>
   		<p>item</p>
    	<Container>
            <Button>按钮</Button>
        	<Button primary={true}>按钮2</Button>
        </Container>
    </>
)
```

##### styled-jsx

* 不适合ts(需要配置)

##### Emotion

* 不适合ts(需要配置)

### React-router

#### react-router-dom

```shell
npm install react-router-dom -save
```

#### router配置

```tsx
// router/index.tsx 
// router配置
import React from 'react'
import { createBrowserRouter, createHashRouter} from 'react-router-dom'

import MainLayout from '../layouts/MainLayout'
import ManageLayout from '../layouts/ManageLayout'

import Home from '../pages/Home'
const routes = [
    {
        path: '/'
        element: <MainLayout />
        children: [
        	{
                path: '/',
                element: <Home/>
            },
    		{
                path: '/login',
                element: <Login/>
            },
    		{
                path: '/register',
                element: <Legister/>
            },
    		{
                path: '*',
                element: <NotFound/>
            }
        ]
    }
]
const router = createBrowserRouter(routes)

export default router

// 路由表组件写法
import { createRoutesFromElements, Route} from 'react-router-dom'
const routes = createRoutesFromElements(
	<Route> path="/" element={<MainLayout/>}>
        <Route path="/" element={<Home />}></Route>
        <Route path="/login" element={<Login />}></Route>
    </Route>
)
```

#### router应用

```tsx
// App.tsx
// router应用
import React from 'react'
import { RouterProvider } from 'react-router-dom'
import routerConfig'./router'
const App = () => {
    return <RouterProvider router={routerConfig}></RouterProvider>
}
export default App
```

#### 二级路由

```tsx
// MainLayout
// 二级路由
import React, { FC } from 'react'
import { Outlet } from 'react-router-dom'
// Outlet相当于vue中<router-view>

const MainLayout: FC = () => {
    return (
        <>
            <div>main layout header</div>
            <div>
        		<Outlet/>	
        	</div>
            <div>main layout footer</div>
    	</>
    )
}
export default MainLaypit
```

#### 路由跳转，参数处理



```tsx
// 路由跳转，参数处理
import React, { FC } from 'react'
import { useNavigate, Link, NavLink, useParams, useSearchParams, useLocation } from 'react-router-dom'

const Home: FC = () => {
    // 编程式路由
    const nav = useNavigate() 
    // 获取动态路由参数， { path: "/edit/:id"}
    const { id = '' } = useParams() 
    console.log(id)
    // 获取路由参数
    const [searchParams， setSearchParams] = useSearchParams()
 	console.log(searchParams.get('b'))
    // setSearchParams({})
    // 获取pathname
    const { pathname } = useLocation()
    const handleLogincLick = () => {
        // nav('/login?b=20')
        nav({
          pathname: '/login',
          search: 'b=20'
        })
        // nav(-1)
        
    }
    return (
        <div>
            <h1>home</h1>
            <div>
                <button onClick={handleLogincLick}>登录</button>
                <Link to="/register?a=10">注册</Link>
                {/* 
                	带样式的声明式路由<NavLink>
                	默认样式.active{}
                	自定义样式.active2{}
                */}
                <NavLink className={(isActive) => isActive? "active2" : ''} to="/register?a=10">注册</NavLink>
            </div>
        </div>
    )
}
```

####  默认路由，重定向，404

* 默认路由

* 组件重定向

* 全局404：errorElement

  ```tsx
  const routes = [
      {
          path: '/'
          element: <MainLayout />,
          errorElement: <div>404</div>, // 全局
          children: [
          	{
                  index: true,
                  element: <div>默认内容</div>
              },
      		{
                  path: '',
                  element: <Navigate to="/" />
              }
          ]
      }
  ]
  ```

#### 路由loader函数与redirect方法

* loader函数进行路由前触发，配合redirect做权限拦截
* useLoaderData()获取loader函数返回的数据

```tsx
import { redirect } from 'react-router-dom'
const routes = [
    {
        path: 'list',
        element: <List />,
        loader () { // 同步
            console.log('list')
        }
     }，
    {
    
        path: 'list',
        element: <List />,
        loader: async () => { // 异步
             const res = await new Promise((resolve, reject) => {
                 setTimeOut(()=> { resolve(1)})
             })
            if (res) {
                return redirect('/login') // 重定向
            } else {
                return res // 返回数据
            }
         }
    }
]

// List.tsx 页面
import { useLoaderData } from 'react-router-dom'
 const List = () => {
     const data = useLoaderData()
     console.log(data) // list123
     return <div>list</div>
 }
```

#### 自定义全局守卫与自定义元信息

- 自定义全局组件：包裹根组件
- 自定义元信息：meta
- 组件中获取meta信息：matchRoutes 与useLocation

```tsx
import React, { FC } from 'react'
import { useLocation, matchRoutes } from 'react-router-dom'
import { routes } from '../router'

type PropsType = {
  children: React.ReactNode
}
const BeforeEach: FC<PropsType> = (props) => {
  const location = useLocation()
  const matchs = matchRoutes(routes, location)
  console.log('beforeEach')
  console.log(matchs)
  return <>{props.children}</>
}

export default BeforeEach

```

### 表单组件

- 受控组件：值同步到state，使用value属性
- 非受控组件：值不同步到state，使用defaultValue属性

```tsx
import React , {useState, ChangeEvent} from 'react'

const Demo: React.FC = () => {
    const [value, setValue] = useState<string>('hello')
    const handleChange = (e: ChangeEvent<HTMLInputElemnt>) => {
        setValue(e.target.value)
    }
	return <>
    	<input value={value} onChange={handleChange} />
    </>
}
```

### 状态管理Redux

#### 传统方式（弃用或过时）

- redux的使用

```jsx
/**
* npm install redux --save
*/
// store/index.js
import { createStore } from 'redux'
function counterReducer(satte = {count: 0}, action) {
    switch (action.type) {
        case 'increment':
            return {count: state.count + action.payload]} // state是不可变数据
        case 'decrement':
            return {count: state.count - 1}
    }
}
const store = createStore(counterReducer)
export default store

// src.demo.jsx
import store from '../store'
export default function Demo () {
    const [count, setCount] = useState(store.getState().count)
    // 修改count
    const handleClick = () => {
        store.dispatch({type: 'increment', payload: 5})
    }
    // 监听state变化
    store.subscribe(() => {
        setCount(store.getState().count)
    })
    return (<div>{count}</div>)
}

```

- react-redux简化对redux的使用

```JSX
/**
* react-redux简化对redux的使用
* npm install react-redux
*/
// index.js
import {Provider} from 'react-redux'
import store from '../store'
const root = ReactDOM.createRoot(document.getElementById('root')
root.render(
  <React.StrictMode>
    <Provider store={store}>
   	 <App />
    </Provider>
  </React.StrictMode>
)
// src.demo.jsx
import {useSelector, useDispatch} from 'react-redux'
export default function Demo () {
    const count = useSelector(state => state.count)
    const dispatch = useDispatch()
    // 修改count
    const handleClick = () => {
        dispatch({type: 'increment', payload: 5})
    }
    return (<div>{count}</div>)
}
```

- 多个reducer函数及redux模块化

```JS
// store/modules/counterReducer.js
export default function counterReducer(satte = {count: 0}, action) {
    switch (action.type) {
        case 'counter/increment':
            return {count: state.count + action.payload]} // state是不可变数据
        case 'counter/decrement':
            return {count: state.count - 1}
    }
}
// store/index.js
import { createStore, combineReducers } from 'redux'
import counterReducer from './modules/counterReducer.js'
const store = createStore(combineReducers({
    counter: counterReducer
}))
export default store

```

#### redux-thunk 中间件处理异步操作

```jsx
/**
* npm install redux-thunk --save
*/
// store/modules/counterReducer.js
export default function counterReducer(satte = {count: 0}, action) {
    switch (action.type) {
        case 'counter/increment':
            return {count: state.count + action.payload]} // state是不可变数据
        case 'counter/decrement':
            return {count: state.count - 1}
    }
}
// 需要thunk支持
export function incrementAction() {
    return (dispatch) => {
			setTimeOut(() => {
                dispatch({type: 'increment', payload: 5})
            }, 200)
        }
}
// store/index.js
import { createStore, combineReducers, applyMiddleWare } from 'redux'
import thunk from 'redux-thunk'
import counterReducer from './modules/counterReducer.js'


const store = createStore(combineReducers({
    counter: counterReducer
}), applyMiddleWare(thunk))
export default store

// src.demo.jsx
import {useSelector, useDispatch} from 'react-redux'
import {incrementAction} from '../store/modules/counterReducer.js'
export default function Demo () {
    const count = useSelector(state => state.count)
    const dispatch = useDispatch()
    // dispatch 可以支持回调函数
    const handleClick = () => {
        dispatch((dispatch) => {
			setTimeOut(() => {
                dispatch({type: 'increment', payload: 5})
            }, 200)
        })
        // 将上面方式封装一个函数
        dispatch(incrementAction())
    }
    return (<div>{count}</div>)
}
```



#### 最新解决方案**RTK**：`@reduxjs/toolkit`

- 可以自定和redux devtools结合，不需要安装模块进行生效
- 数据不需要通过返回值进行修改（自动处理state不可变性）
- 内置redux-thunk这个异步插件
- 代码风格更好，采用选项式编写程序

```tsx
/**
* src/store/index.ts
* npm install @reduxjs/toolkit react-redux
*/
import { configureStore } from '@reduxjs/toolkit'
import countReducer from './count'
export type StateType = {
    count: number
}
export default configureStore({
    reducer: {
        count: countReducer
    }
})


/**
* src/store/count.ts
*/
import { createSlice } from '@reduxjs/toolokit'

const INIT_STATE: number = 100
export const countSlice = createSlice({
    // dispatch('count/increase')
    name: 'count', // 模块名字（命名空间）
    initialState: INIT_STATE,
    reducers: {
        increase (state: number) {
			return state + 1
        },
        decrease (state: number) {
            return state -1 
        }
    }
})

export const {increase, decrease} = countSilce.messageSlice
export default countSilce.reducer
/**
* src/store/todoList.ts
*/
import { createSlice, PayloadAction} from '@reduxjs/tookit'
export const TodoItemType = {
    id: string
    title: string
}
const INIT_STATE: TodoItemType[] = []

const rodoListSlice = createSilce({
    name: 'todoList',
    initialState: INIT_STATE,
    reducers: {
    	addTodo (state: TodoItemType[], action: PayloadAction<TodoItemType>) {
            return [
                action.payload,
                ...state
            ]
        }
    
	}
})

/**
* src/index.tsx
*/
import React from 'react'
import ReactDOM from 'react-dom/client'

import App from './App'
import { Provider } from 'react-redux'
import store from './store/index.ts'

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement)
root.render(
  <React.StrictMode>
    <Provider store={store}>
   	 <App />
    </Provider>
  </React.StrictMode>
)

/**
* src/Count.tsx
*/
import React, { FC } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { increase, decrease } from './store/count'
import type {StateType} from '/store/index'

const Count: FC = () => {
    const count = useSelector(state<StateType> => state.count)
    const dispatch = useDispatch()
    return (
        <div> { count }</div>
        <button onClick={() => dispatch(increase())}>+</button>
        <button onClick={() => dispatch(decrease())}>-</button>
    )
}
```

#### redux-toolkit处理异步任务

- createAsyncThunk方法创建异步任务
- extraReducers配置额外的reducer

```ts
/**
* messageReducer
*/
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit'

type MessageType = {
    msg: string,
    UpperMsg: string
}

const INIT_STATE: MessageType= {
	msg: '',
    UpperMsg: this.msg.toUpperCase()
}
// 异步action
export const messageTestAction = createAsyncThunk('message/testAction', async () => {
    return await new Promise((resolve) => {
        setTimeOut(() => {
            resolve('response data')
        }, 2000)
    })
})
const messageSlice = createSlice({
    name: 'message',
    inirialState: INIT_STATE,
    reducers: {
		change (state: MessageType, action: PayloadAction<string>) {
			state.msg = action.payload
            state.UpperMsg = state.msg.toUpperCase()
        }
    },
    extraReducers： {
    	[messageTestAction.fulfilled] (state: MessageType, action: PayloadAction<string>) {
    		state.msg = action.payload
            state.UpperMsg = state.msg.toUpperCase()
		}		
	}
})
export const { change } = messageSlice.messageSlice

```



#### Redux单项数据流

### MobX



### React与TS

#### props限制

- 组件参数限制类型，或者FC泛型限制类型

```tsx
// 组件参数限制类型
type DemoType = {
    message: string
}
function Demo (props: DemoType) {
    return <div>hello</div>
}

// FC泛型限制类型
import type { FC } from 'react'
type DemoType = {
    message: string
}
const Demo: FC<DemoType> = (props) => {
    return <div>hello</div>
}

export default Demo
```

#### children与event限制

- children: React.ReactNode
- event: React.MouseEvent<HTMLButtonElement>(或者ChangeEvent)

```tsx
import React from 'react'
import type { FC, ReactNode } from 'react'
type DemoType = {
    message: string,
    children: ReactNode
}
const Demo: FC<DemoType> = (props) => {
    const handleBtnClick = (e: React.MouseEvent<HTMLButtonElement>) => {
	 //...
    }
    return (
        <div>
            <p>hello</p>
            <button onClick={handleBtnClick}>按钮</button>
        </div>
    )
}

export default Demo
```

#### style与component限制

- style: React.CSSProperties
- component: React.ComponentType

```tsx
import React from 'react'
import type { FC, ReactNode } from 'react'
type DemoType = {
    message: string,
    children: ReactNode,
    style: React.CSSProperties,
    component: React.ComponentType // (可反泛型传参数类型<>)
}
const Demo: FC<DemoType> = (props) => {
    return (
        <div>
            <p>hello</p>
        </div>
    )
}

export default Demo
```

#### use函数限制

- useState(): 联合类型、对象字面类类型
- useEffect(): 自动类型推断
- useRef(): 泛型标签类型

```tsx
import React from 'react'
import type { FC } from 'react'
type DemoType = {
    message: string
}
const Demo: FC<DemoType> = (props) => {
    const [count, setCount] = useState<string | number >(0)
    const [list, setList] = useState<string[]>([])
    const [info, setInfo] = useState<{username: string} | null>(null)
    // const [info, setInfo] = useState<{username: string}>({} as {username: string})
    
    const myRef = useRef<HTMLButtonElemnt>(null)
    useEffect(() => {
        console.log(myRef?.current.innerHTML) // 可选链（类型保护）
        console.log(myRef.current!.innerHTML) // 非空断言（慎用）
        return () => {}
    }, [])
    return (
        <div>
            <p>hello</p>
            <div>{info?.username}</div>
            <button ref={myRef}>按钮</button>
        </div>
    )
}

export default Demo
```

#### 类组件类型限制

- React.Component<Props, state>

```tsx
import React, { Component } from 'react'
type DemoState = {
    username: string
}
type DemoProps = {
    count: number
}
class Demo extends Component<DemoProps, DemoState> {
    state = {
        username: 'll'
    }
    render () {
		return <div>hello {this.state.username}</div>
    }
}
```

#### 路由TS

- RouteObject内置类型，限制路由表
- React.createElement()进行组件编写
- 扩展meta元信息

```tsx
// router/index.tsx
import { createBrowserRouter } from 'react-router-dom'
import type { RouteObject } from 'react-router-dom'

declare module 'react-router' {
  interface IndexRouteObject {
    meta?: { title: string }
  }
  interface NonIndexRouteObject {
    meta?: { title: string }
  }
}
const routes: RouteObject[] = [
  {
    path: '/',
    element:
      <BeforeEach>
        <MainLayout />
      </BeforeEach>
    ),
    errorElement: <NotFound />,
    meta: {
      title: '/'
    },
    children: [
      {
        path: '/',
        element: <Home />,
        meta: {
          title: '首页'
        }
      },
      {
        path: '/login',
        element: <Login />,
        meta: {
          title: '登录'
        }
      }
    ]
  }
]
const router = createBrowserRouter(routes)
export default router
```

