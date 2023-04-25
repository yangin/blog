# 转换 CommonJS 项目为 ESM

- [转换 CommonJS 项目为 ESM](#转换-commonjs-项目为-esm)
  - [背景](#背景)
  - [怎么转换 CommonJS 项目为 ESM](#怎么转换-commonjs-项目为-esm)
  - [参考](#参考)

## 背景

NodeJs 自 v12.17.0 起，支持了 ESM 模块，但是默认情况下，NodeJs 仍然使用 CommonJS 模块。

当前许多流行的库（如 [chalk](https://github.com/chalk/chalk)、[inquirer](https://github.com/SBoudrias/Inquirer.js)）已改用 ESM 模块, 这导致在 CommonJS 项目中使用这些库时，会报错：require() is not support。

为了更好的支持 ESM 模块，并向后兼容，我们需要将 CommonJS 项目转换为 ESM 项目。

## 怎么转换 CommonJS 项目为 ESM

- 在你的 package.json 中添加 "type": "module" 字段。
- 在你的 package.json 中将"main":"index.js" 改为 "exports":"./index.js"。
- 更新 package.json 中的 "engines" 字段，指定 NodeJs 版本为 "node": ">=14.16"。
- 从所有 Javascript 文件中移除 `'use strict';` 。
- 用 import/export 语法替换 require()/module.export 语法。
- 仅使用完整的相对文件路径进行导入:import x from '.'; -> import x from './index.js';。
- 如果你有一个 Typescript 的类型定义文件（例如，index.d.ts），那么你需要使用 ESM imports/exports 更新它。
- 可选但推荐使 `node: protocol` 显式引入 node 模块 :import fs from 'fs'; -> import fs from 'node:fs';。

## 参考

[Pure ESM package](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c)
