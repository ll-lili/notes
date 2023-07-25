### React

#### 组件化

* 拆分页面结构，通过组件拼装页面，组件复用
* 易开发维护，尤其对于多人协作开发的大型项目

#### 数据驱动试图

* UI = f(state)
* 只关注业务数据的修改，不用再操作DOM，增加开发效率
* 尤其对于DOM结构复杂的项目

#### Create React App

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
npx eslint --init # 初始化eslint配置文件

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

