# Babel

[Babel](https://babeljs.io/docs/) 是一个 Javascript 编译器，可以将 ES6 代码转换为 ES5 代码，从而在现有环境中运行。

- [Babel](#babel)
  - [使用](#使用)
    - [命令行](#命令行)
    - [@babel/core](#babelcore)
    - [babel-loader](#babel-loader)
  - [@babel/core](#babelcore-1)
  - [@babel/preset 工作原理](#babelpreset-工作原理)
  - [@babel/preset-env](#babelpreset-env)
  - [@babel/preset-react](#babelpreset-react)
  - [@babel/preset-typescript](#babelpreset-typescript)

## 使用

### 命令行

通过引入 @babel/cli，可以在命令行中使用 babel。

```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

配置 babel.config.json

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
        "useBuiltIns": "usage",
        "corejs": "3.6.5"
      }
    ]
  ]
}
```

```bash
npx babel src --out-dir lib
```

### @babel/core

通过引入 @babel/core，可以在代码中使用 babel。

```bash
npm install --save-dev @babel/core
```

```js
const babel = require("@babel/core");

babel.transformSync("code", optionsObject);
```

### babel-loader

通过引入 babel-loader，可以在 webpack 中使用 babel。

依赖 @babel/core，并配合 @babel/preset 来完成目标语法的转换。

```bash
npm  install -D babel-loader @babel/core @babel/preset-env
```

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            configFile: false, // 不使用 babel.config.js
            babelrc: false, // 不使用 .babelrc
            presets: ["@babel/preset-env"],
          },
        },
      },
    ],
  },
};
```

## @babel/core

@babel/core 是 babel 的核心库，提供了转换语法的所有 API。

如果要完成语法转换，这个库是必须的。再配合 plugin、preset，就可以完成语法转换。

preset 可以看成是 plugin 的集合。

## @babel/preset 工作原理

@babel/preset-env 获取指定的目标环境，并根据其映射检查它们，得到编译插件列表并将其传递给 Babel。本身不具备转换语法的能力，需要配合 @babel/plugin-transform-runtime 或 @babel/core 使用。

## @babel/preset-env

@babel/preset-env 用来识别 ES6+ 语法，根据目标环境，自动确定需要的插件和 polyfill。然后配合 Babel 的转换能力将其转换为 ES5 语法。

需要配合 [browserslist](https://github.com/browserslist/browserslist#queries) 使用。比如在 package.json 中配置：

```json
{
  "browserslist": ["last 2 versions", "safari >= 7"]
}
```

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "useBuiltIns": "usage" // 按需引入 polyfill
        }
      }
    ]
  ]
}
```

> 注意
>
> targets.useBuiltIns 配置用来告诉 @babel/preset-env 怎么处理 polyfill， 用来替代@babel/polyfill（在 babel v7+后弃用）。
> 其有 3 个值：
>
> - entry: 在入口文件中引入所有的 polyfill,即执行了 `import "core-js"` ,只执行一次。如果另外引用了@babel/polyfill，会导致引用 2 次 core-js,会报错。
> - usage: 根据代码中的使用情况，引入相关的 polyfill，而不是所有的 polyfill。
> - false(默认): 不引入任何 polyfill。

## @babel/preset-react

@babel/preset-react 用来识别 React 语法。

```json
{
  "presets": ["@babel/preset-react"]
}
```

## @babel/preset-typescript

@babel/preset-typescript 用来识别 TypeScript 语法，将其转换为 Javascript 语法。

```json
{
  "presets": ["@babel/preset-typescript"]
}
```
