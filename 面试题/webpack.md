# 1. Loader 与 Plugin

### Q1: Loader 与 Plugin 的区别

- Loader

  loader 是一个`转换器`，将 A 文件进行编译形成 B 文件，这里`操作的是文件`，比如将 A.scss 转换为 A.css，单纯的文件转换过程

  运行在打包文件之前

- Plugin

  plugin 是一个`扩展器`，用来`丰富 webpack 本身功能`，解决 loader 无法解决的问题（如：打包优化，资源管理，环境变量注入等）。在 Webpack 整个编译生命周期中会基于 `Tapable` 广播出许多事件，Plugin 可以`监听这些事件`，在合适的时机实现自定义功能

  运行在 webpack 整个编译周期中

### Q2: 常见的 Loader

- babel-loader: 将 ES6 转换成 ES5, 并允许 babel 的插件, 完成一些特殊的转换，如 babel-preset-react
- css-loader: 解析 CSS 文件(如 @import、@global、url() 等语法)为最终能被浏览器识别的 CSS 代码，并生成指定格式的 CSS 文件（如带 hash 值的文件名）
- style-loader: 将模块导出的 CSS 内容作为样式，通过`<style>`标签添加到 DOM 中
- less-loader: 将 less 转换成 css
- sass-loader: 将 sass 转换成 css

### Q3: 常见的 Plugin

- HtmlWebpackPlugin: 生成 HTML 文件
- MiniCssExtractPlugin: 将 CSS 提取为独立的文件
- CleanWebpackPlugin: 清理构建目录
- DefinePlugin: 允许在编译时创建配置的全局对象，是一个 webpack 内置的插件，不需要安装
- copy-webpack-plugin: 将单个文件或整个目录复制到构建目录

### Q4: 手写一个 Plugin

Step1: 初始化一个 plugin

格式：

```bash
npx webpack plugin [output-path] [options]
```

Step2: 编写 plugin 方法

```js
class MyWebpackPlugin {
  //编写一个构造器
  constructor(options) {
    console.log(options);
  }

  apply(compiler) {
    //遇到同步时刻
    compiler.hooks.compile.tap("CopyrightWebpackPlugin", () => {
      console.log("compiler");
    });

    //遇到异步时刻
    //当要把代码放到dist目录之前，要走下面这个函数
    //Compilation存放打包的所有内容，Compilation.assets放置生成的内容
    compiler.hooks.emit.tapAsync("MyWebpackPlugin", (Compilation, callback) => {
      // 往代码中增加一个文件，copyright.txt
      Compilation.assets["copyright.txt"] = {
        source: function () {
          return "copyright by monday";
        },
        size: function () {
          return 19;
        },
      };
      callback();
    });
  }
}

module.exports = MyWebpackPlugin;
```

注意：

- plugin 必须是一个函数或者是一个包含 apply 方法的对象
- apply 参数 compiler 是一个 webpack 实例，包含了 webpack 的所有配置信息，如 options、loaders、plugins、hooks 等
- 在 compiler.hooks 上目标生命周期挂载插件，当事件触发时，执行回调函数 callback

### Q5: 手写一个 Loader

[参考](https://webpack.docschina.org/api/loaders/)

Step1: 初始化一个 loader

格式：

```bash
npx webpack loader [output-path] [options]
```

Step2: 编写 loader 方法

```js
/**
 * @param {string|Buffer} content 源文件的内容
 * @param {object} [map] 可以被 https://github.com/mozilla/source-map 使用的 SourceMap 数据
 * @param {any} [meta] meta 数据，可以是任何内容
 */
function webpackLoader(content, map, meta) {
  // 你的 webpack loader 代码
  const result = content.replace("monday", "mondaylab");
  this.callback(null, result);
}
```

注意：

- loader 函数的第一个参数为`源文件的内容`，第二、三个参数为可选参数
- `同步 loader` 通过 `return` 或 `this.callback` 返回结果，返回一个字符串或者 Buffer
- `异步 loader` 通过 `this.async` 返回一个 callback 函数，callback 函数的第一个参数为 error，第二个参数为返回结果，可返回多个结果

# 2. Webpack 的构建流程

### Q1: Webpack 的构建流程

- 初始化阶段
  - 启动构建，读取与合并配置参数，并设置默认值
  - 加载 Plugin，实例化 Compiler，并在 Compiler 各个阶段的 hooks 上挂载插件
- 编译阶段（编译 + seal 2 个过程）
  - 从 Entry 出发，基于文件生成 Module
  - 对 Module 进行 build，根据配置中的 Loader 对 Module 进行转译
  - 基于 runLoader 后的结果，通过 Parser 解析为 AST
  - 遍历 AST 进行依赖收集（查找 require/import/...关键字）
  - 再对依赖的 Module 递归执行 build，直到所有依赖的 Module 都经过了本次的 build
  - 进行 optimization 阶段各种优化，包括：Tree Shaking、Code Splitting 等
  - 将编译后的 Module 根据 Entry 组合成 Chunk，将 Chunk 转换成文件(指定格式的文件名)，准备输出到文件系统中
- 输出阶段
  - 在确定好输出内容后，根据配置确定输出的路径和文件名，将文件内容写入到文件系统

<img src="https://raw.githubusercontent.com/yangin/code-assets/main/webpack-cli/images/webpack-process.png">

### Q2: Webpack 中几个重要的概念

- Compiler

  Webpack 编译器，用于启动编译，监听文件变化，执行编译等。在 Webpack 中，Compiler 对象是全局唯一的，它是 Webpack 的核心对象，它包含了 Webpack 所有的核心功能。

- Compilation

  Compilation 对象代表了一次资源版本构建，它包含了当前的模块资源、编译生成资源、变化的文件等。当 Webpack 以开发模式运行时，每当检测到一个文件变化，一次新的 Compilation 就会被创建。Compilation 对象也提供了很多事件回调供插件做扩展。

- Entry

  入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入

- Module

  Module 是一个模块，一个模块对应一个文件，一个文件可能对应多个模块。

- NormalModuleFactory

  NormalModuleFactory 是一个工厂类，用于创建 NormalModule 对象，NormalModule 对象是一个模块对象，包含了模块的依赖，源码，资源等信息。在 webpack 整个构建流程中，文件都是以 NormalModuleFactory 来进行处理的。

- Chunk

  Chunk 是一个代码块，一般一个入口产生一个 chunk, 由多个模块组合而成。

- AsyncQueue

  AsyncQueue 是一个异步队列，它的作用是串行执行任务。在 Webpack 中，AsyncQueue 主要用于文件（即 module）的解析工作。因为是异步处理，在深度遍历时，既保证每个文件的解析顺序，也保证了文件的并行解析。

# 3. Webpack 实现 HMR 原理

- 通过`webpack serve`命令调用 webpack-dev-server 库的 Server.start() 方法
- 通过 webpack-dev-server 创建两个服务器：提供静态资源的 `http 服务`（基于 express）和 `socket 服务`

  > - http server 负责直接提供静态资源的服务（打包后的资源直接被浏览器请求和解析）
  > - socket server 是一个 websocket 的长连接，双方可以通信

- 在 webpack-dev-middleware 中间件中启用 webpack 的 `watch 模式`, 用来监听文件的变化
- 当文件发生变化时，webpack 会重新编译，打包完成后，webpack-dev-middleware 会将打包后的文件`输出到内存中`
- 当 socket server 监听到对应的模块发生变化时，会讲新的 `hash 值`发送给客户端（浏览器）
- 客户端中的 HMR runtime 对比新旧 hash 值，如果发现不一致，就会请求获取到最新的 `manifest 文件`，即 oldHash.`hot-update.json`
- 根据 manifest 文件，知道待更新模块（即 hot-update.json 文件中返回的 c 值）
- 为每个待更新模块发起一个 jsonp 请求，以获取到变化的内容，即 file.oldHash.hot-upload.js
- HRM runtime 根据请求到的 hot-upload.js 文件，将变化的`内容替换`到当前模块中，完成模块的热更新

# 4. Webpack 的优化

[参考](https://juejin.cn/post/7129690544468393992)

### Q1: 优化构建速度

我们在开发环境引入了 speed-measure-webpack-plugin 用来测速

1. 持久化缓存

   开启持久化缓存可以大幅降低我们项目二次构建的时间，非常建议开启，建议使用 `filesystem`，使用 memory 对于中大型项目会吃不消

   ```js
   module.exports = {
     //...
     cache: {
       type: "filesystem",
       // 可选配置
       buildDependencies: {
         // 将你的配置添加依赖，更改配置时，使得缓存失效
         config: [__filename],
         // 如果有其他的依赖，可以添加在这里
         // 注意，dependency 会触发缓存失效
         // dependency: ['package.json']
       },
     },
   };
   ```

2. source-map 优化

- 开发环境：使用 eval-cheap-module-source-map, 内联 sourcemap, 减少构建时间

  ```js
  devtool: "eval-cheap-module-source-map";
  ```

- 生产环境：使用 source-map，生成专门的.map.js 文件，然后根据具体的需求删除或移动 sourcemap 文件，不要暴露给用户。如果不需要调试，可以关闭 sourcemap

  ```js
  devtool: "source-map";
  ```

3. watch 优化

   因为 node_modules 的变换频率都是极低的，所以我们在使用 watch 功能的时候可以通过配置 `ignored` 来`忽略 node_modules` 从而减少性能压力

   ```js
   watchOptions: {
     ignored: /node_modules/,
   }
   ```

   当需要更新 node_modules 时，重新编译即可

4. 在开发环境中使用 style-loader

   style-loader 会将 css 代码以 style 标签的形式插入到 html 中，这样可以减少 css 文件的请求，提高开发环境的构建速度

   且 style-loader 对 `HMR` 的支持更好，可以实现样式的热更新

   `MiniCssExtractPlugin` 对 `HMR` 支持不是很好，且在开发环境中配置 `hash` 会进一步降低构建速度

   ```js
   module.exports = {
     //...
     module: {
       rules: [
         {
           test: /\.css$/,
           use: ["style-loader", "css-loader"],
         },
       ],
     },
   };
   ```

5. TerserPlugin 插件缓存

   使用 TerserPlugin 插件压缩 js 代码时，可以开启缓存，这样可以减少二次构建的时间。且最好 exclude 掉 node_modules

   ```js
   module.exports = {
     //...
     optimization: {
       minimize: true,
       minimizer: [
         new TerserPlugin({
           cache: "./cache",
           exclude: /node_modules/,
           parallel: true,
           sourceMap: true,
           terserOptions: {},
         }),
       ],
     },
   };
   ```

6. noParse 优化

   React 已经为我们打包好了生产环境需要使用的文件，可以跳过编译环节

   ```js
   module.exports = {
     resolve: {
       alias: {
         react: path.resolve(
           __dirname,
           "./node_modules/react/umd/react.production.min.js"
         ),
         "react-dom": path.resolve(
           __dirname,
           "./node_modules/react-dom/umd/react-dom.production.min.js"
         ),
       },
     },
     module: {
       noParse: /(react|react-dom).production.min.js$/,
     },
   };
   ```

7. JS 加载优化

- 使用 thread-loader 加快构建速度
- 为 babel-loader 开启缓存
- 通过 exclude 排除不需要解析的模块
- 使用`react-refresh/babel`为 React 项目添加热更新能力

  ```js
  module.exports = {
    //...
    module: {
      rules: [
        {
          test: /\.js$/,
          use: [
            {
              loader: "thread-loader",
              options: {
                workers: 3,
              },
            },
            {
              loader: "babel-loader",
              options: {
                cacheDirectory: true,
                plugins: ["react-refresh/babel"],
              },
            },
          ],
          exclude: [/node_modules/, /dist/],
        },
      ],
    },
  };
  ```

### Q2: 打包体积优化

通过 `webpack-bundle-analyzer` 来分析打包体积

1. lodash 优化

   由于 lodash 是一个 UMD 规范的包，所以默认做的全量引入

   我们可以通过 `lodash-webpack-plugin` 来移除你未用到的 lodash 特性

   ```js
   const LodashModuleReplacementPlugin = require("lodash-webpack-plugin");

   module.exports = {
     //...
     plugins: [
       new LodashModuleReplacementPlugin({
         // 只引入你需要的方法
         collections: true,
         paths: true,
       }),
     ],
   };
   ```

2. moment 优化

   webpack 打包 momentjs 时会把所有语言包都打包，这样会使打包文件很大。基于 webpack 自带的 ContextReplacementPlugin 插件可以帮助我们只打包需要的语言包，大大减小打包文件大小。

   ```js
   module.exports = {
     //...
     plugins: [
       new webpack.ContextReplacementPlugin(/moment[/\\]locale$/, /zh-cn/),
     ],
   };
   ```

   限定查找 moment/locale 上下文里符合 /zh-cn/ 表达式的文件，只打包这几种本地化内容

3. CSS 优化

   - 使用 MiniCssExtractPlugin 插件将 css 代码提取到单独的文件中，减少 js 文件的体积
   - 使用 css-loader 的 `importLoaders` 选项，减少 css 文件的解析次数
   - 使用 postcss-loader 为 css 代码自动添加浏览器前缀
   - 使用 cssnano 压缩 css 代码
   - 使用 PurgeCSSPlugin 插件清除无用的 css 代码

     > 注意：
     >
     > - PurgeCSSPlugin 插件需要在 MiniCssExtractPlugin 插件之后使用
     > - PurgeCSSPlugin 的 paths 配置项需要指定要扫描的文件路径，否则不会生效，注意样式丢失

   ```js
   module.exports = {
     //...
     module: {
       rules: [
         {
           test: /\.css$/,
           use: [
             {
               loader: MiniCssExtractPlugin.loader,
               options: {
                 publicPath: "../",
               },
             },
             {
               loader: "css-loader",
               options: {
                 importLoaders: 2,
               },
             },
             "postcss-loader",
           ],
         },
       ],
     },
     plugins: [
       new MiniCssExtractPlugin({
         filename: "css/[name].[contenthash:8].css",
       }),
       new PurgeCSSPlugin({
         paths: glob.sync(`${PATHS.src}/**/*`, { nodir: true }),
       }),
     ],
   };
   ```

4. splitChunks 提取公共代码

   SplitChunks 插件是 webpack 中用来提取或分离代码的插件，主要作用是提取公共代码，减少代码被重复打包，拆分过大的 js 文件，合并零散的 js 文件

   ```js
   module.exports = {
     //...
     optimization: {
       splitChunks: {
         chunks: "all",
         minSize: 30000,
         minChunks: 1,
         maxAsyncRequests: 5,
         maxInitialRequests: 3,
         automaticNameDelimiter: "~",
         name: true,
         cacheGroups: {
           vendors: {
             test: /[\\/]node_modules[\\/]/,
             priority: -10,
           },
           default: {
             minChunks: 2,
             priority: -20,
             reuseExistingChunk: true,
           },
         },
       },
     },
   };
   ```

   - minChunks: 代码被引用的次数，大于等于 minChunks 才会被提取
   - maxInitialRequests/maxAsyncRequests: 入口文件/异步加载的最大并行请求数，本质上是控制拆分后的文件数量
   - minSize: 超过这个大小的 Chunk 会被拆包
   - maxSize: 超过这个大小的 Chunk 会尝试继续拆包
   - cacheGroups: 缓存组规则，为不同类型的资源设置更有针对性的拆包策略
   - priority: 缓存组优先级，当一个模块同时符合多个缓存组规则时，优先级高的生效

5. gzip 压缩

   - 在 nginx 中开启 `gzip` 压缩，可以大幅减小文件体积，提高加载速度

     ```bash
     gzip on;
     gzip_static on;
     gzip_vary on;
     ```

   - 在无法开启 gzip 压缩的情况下，可以使用 `compression-webpack-plugin` 插件来压缩文件

     ```js
     const CompressionWebpackPlugin = require("compression-webpack-plugin");

     module.exports = {
       //...
       plugins: [
         new CompressionWebpackPlugin({
           algorithm: "gzip",
           test: /\.(js|css|html|svg)$/,
           threshold: 10240,
           minRatio: 0.8,
         }),
       ],
     };
     ```

# 5. Webpack 4.x 升级到 5.x

[参考](https://juejin.cn/post/7129690544468393992)

### Q1: Webpack 5.x 有哪些新特性

- 开箱即用的`持久化缓存`, 默认开启，可以大幅降低二次构建的时间
- 优雅的`资源处理模块`, 可以通过 `asset/resource`、`asset/inline`、`asset/source`、`asset` 四种模式来处理资源
- 打包体积优化，通过 `splitChunks` 插件来提取公共代码

### Q2: 升级的变化

1. `命令行 env` 的传参格式变化

   Webpack5 不在需要使用 --env.key=value 的语法， 现在使用 --env key=value 的语法

   ```bash
   webpack --env test=false
   ```

2. `webpack-dev-server 命令`调用方式变化

   ```bash
   # Webpack4
   webpack-dev-server --config webpack.config.js

   # Webpack5
   webpack serve --config webpack.config.js
   ```

   且需要把 webpack-dev-server 升级到最新版本

3. 资源加载配置方式的变化

   webpack4, 通过 url-loader 与 file-loader 等 loader 来处理资源

   webpack5，只需要指定 type:

   - asset/resource: 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现。
   - asset/inline: 导出一个资源的 data URI。之前通过使用 url-loader 实现。
   - asset/source: 导出资源的源代码。之前通过使用 raw-loader 实现。
   - asset: 可以在这里通过限制 limit 来决定使用 inline 还是 resource

     ```js
     module.exports = {
       module: {
         rules: [
           {
             test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
             type: "asset/resource",
             generator: {
               filename: "images/[name][ext][query]",
             },
           },
         ],
       },
     };
     ```

4. 核心包依赖版本升级

   执行命令：

   ```bash
   npm i webpack@latest webpack-cli@latest webpack-dev-server@latest -D
   ```

   ```json
   webpack: ^5.0.0
   webpack-cli: ^4.0.0
   webpack-dev-server: ^4.0.0
   ```

5. 需要手动注入 Node Polyfill

   webpack5 不再自动注入 Node Polyfill，需要手动通过 resolve.fallback 来注入

   ```js
   module.exports = {
     resolve: {
       fallback: {
         // path: require.resolve("path-browserify"),
         // util: require.resolve("util/"),
         // assert: require.resolve("assert/"),
         // fs: false,
         // crypto: require.resolve("crypto-browserify"),
         // stream: require.resolve("stream-browserify"),
         // os: require.resolve("os-browserify/browser"),
         // buffer: require.resolve("buffer/"),
         // constants: require.resolve("constants-browserify"),
         // http: require.resolve("stream-http"),
         // https: require.resolve("https-browserify"),
         // vm: require.resolve("vm-browserify"),
         // process: require.resolve("process/browser"),
         // querystring: require.resolve("querystring-es3"),
         // events: require.resolve("events/"),
         // url: require.resolve("url/"),
         // punycode: require.resolve("punycode/"),
         // dns: require.resolve("dns/"),
         // net: require.resolve("net-browserify"),
         // tls: require.resolve("tls-browserify"),
         // string_decoder: require.resolve("string_decoder/"),
         // zlib: require.resolve("browserify-zlib"),
       },
     },
   };
   ```

   并且在文件中手动引入

   ```js
   import process from "process";
   ```

   这些在 webpack4 中是不需要的

6. 替换或者升级不兼容的 webpack5 插件

# 6. publicPath 的作用

[参考](https://juejin.cn/post/7245291960729632829)

# 6. 几种 sourceMap 的区别及对应的使用场景

### map 文件中的字段

- version: map 文件的版本号
- sources: 被映射的源文件路径
- names: 被映射的源文件中的变量名
- mappings: 映射关系，用来定位源文件中的代码位置
- file: 生成的 map 文件名
- sourceRoot: 被映射的源文件的根路径
- sourcesContent: 被映射的源文件内容

### sourceMap 原理

基于浏览器对 sourceMap 功能的支持（在 settings 中设置），可以通过 js 文件的`#sourceMappingURL`注释，指向对应的 map 文件，从而实现 js 文件与源文件的映射关系。

### map 文件的生成方式

#### 格式

```bash
^(inline-|hidden-|eval-)?(nosources-)?(cheap-(module-)?)?source-map$
```

- inline: 内联，表示将 map 文件以 base64 编码，添加到 js 文件的末尾
- hidden: 隐藏，表示源文件中没有 map 文件的引用注释, 但会生成 map 文件, 浏览器控制台无法查看到 map 文件
- eval: 源文件通过 eval 函数来 map 源码， map 的内容以 base64 编码。无法映射 CSS 文件。
- nosources: map 文件中不包含源代码，即没有 sourcesContent 这个字段，只包含行列信息，可以用来定位错误位置，但无法打开源文件查看源码
- cheap: 只包含`行`信息，无法定位到错误的`列`
- module: 可以定位到` loader``编译前 `的源码，即可以定位到源文件中的错误位置，否则只能定位到`编译后`的文件中的错误位置

#### 重点记忆类型

- source-map

  `外部`生成 map 文件，可以定位到错误的`行和列`，且可以打开源文件查看源码。

  映射方式：基于编译后 js 文件末尾的`#sourceMappingURL`注释指向 map 文件。

  适用于生产环境

- inline-source-map

  `内联` map 文件，可以定位到错误的`行和列`，且可以打开源文件查看源码。

  映射方式：将 map 文件内容 base64 编码后，以`#sourceMappingURL=data:application/json;base64,`的形式，添加到编译后文件的末尾。

  内联的方式会使源文件体积变大，生产环境不建议使用。

- eval-source-map

  `内联` map 文件，只能映射 `JS 文件`，无法映射 CSS 文件，可以定位到错误的`行和列`，且可以打开源文件查看源码。

  映射方式：将 map 文件内容 base64 编码后，以`#sourceMappingURL=data:application/json;base64,`的形式，以模块为单位，添加到编译后 js 文件中。且包括源码在内，都通过 eval 函数执行。

  不支持 CSS 文件的映射，无论开发或生产环境，都不建议使用。

- hidden-source-map

  `外部`生成 map 文件，源码文件中无映射关系，即`没有 #sourceMappingURL`注释，无法定位到错误的位置，无法打开源文件查看源码。

  映射方式：无

  适用于生产环境，配合 Sentry 等错误监控平台，可以定位到错误的位置，且不会暴露源码。即将 map 文件上传到 Sentry，Sentry 会根据 map 文件，将错误定位到源文件中，但不会暴露源码。

- nosources-source-map

  `外部`生成 map 文件， 生成的 sourceMap 文件中不包含源代码，即`没有 sourcesContent` 这个字段，只包含行列信息，可以用来定位错误位置，但无法打开源文件查看源码

  映射方式：基于编译后 js 文件末尾的`#sourceMappingURL`注释指向 map 文件。

- cheap-source-map

  `外部`生成 map 文件，错误信息定位到 loader `编译后`文件的行，只包含`行`信息，无法定位到错误的`列`，但可以打开源文件查看源码。

  映射方式：基于编译后 js 文件末尾的`#sourceMappingURL`注释指向 map 文件。

  可用于开发环境，但无法直接定位并查看到编译前的源文件，不建议使用。

- cheap-module-source-map

  `外部`生成 map 文件，错误信息定位到 loader `编译前`文件的行，只包含`行`信息，无法定位到错误的列，但可以打开源文件查看源码。

  映射方式：基于编译后 js 文件末尾的`#sourceMappingURL`注释指向 map 文件。

  适用于开发环境，可直接定位到编译前的源文件，推荐使用。

### 生产环境可用的 sourceMap

根据[官方文档](https://webpack.js.org/configuration/devtool/#devtool)说明，以下 5 种 sourceMap 可以用于生产环境：

- `(none)`
  > 当不需要查看源码且没有错误监控平台时，可以不使用 sourceMap，此时性能最好。
- `source-map`
  > 需要在浏览器定位和查看源码时使用，但会暴露源码，性能最差。
- nosources-source-map
- hidden-nosources-source-map
- `hidden-source-map`
  > 在浏览器上无法查看源码，但可以配合 Sentry 等错误监控平台，定位到错误的位置，不会暴露源码。
  >
  > 即将 map 文件上传到 Sentry，Sentry 会根据 map 文件，将错误定位到源文件中，但不会暴露源码。

### 总结

- 开发环境：推荐使用`cheap-module-source-map`，可以直接定位到编译前的源文件，且性能较好。
- 生产环境：推荐使用`hidden-source-map`，可以配合 Sentry 等错误监控平台，定位到错误的位置，不会暴露源码。实际根据需求还可以选择`(none)`和`source-map`

# 7. contentHash、chunkHash、hash 的区别

### 为什么使用 hash

[参考](https://blog.51cto.com/u_14115828/3733990)

为了让浏览器更好的使用缓存，加快页面加载速度。我们可以通过 hash 变化来表现文件内容的变化。

### 区别

- hash

  基于整个项目的构建，只要项目中有文件更改，整个项目构建的 hash 值都会更改，即使更改的文件与某个 chunk 无关，该 chunk 的 hash 值也会更改。

- chunkHash

  基于 chunk 的构建，每个 chunk 都有一个 hash 值，当某个 chunk 引用的文件（包括引用文件引用的文件）发生变化时，该 chunk 的 hash 值才会更改，其他 chunk 的 hash 值不会更改。

- contentHash

  基于文件内容的 hash 值，只有文件内容发生变化时，该文件的 hash 值才会更改，其他文件的 hash 值不会更改。

### 使用场景

hash 编译效率：`hash > chunkHash > contentHash`

一般只在`生成环境`中使用 hash，开发环境中不使用 hash，因为会降低编译效率。

- hash

  适用于只有一个 chunk 文件的场景。几乎不存在，所以一般不推荐使用。

- chunkHash

  适用于多个入口文件或`多个 chunk` 文件的场景，一般用于 `js 文件`名生成。

- contentHash

  适用于 CSS 从 js 中抽离出来的场景(同属于一个 chunk, 避免 js 的更新影响了 CSS 的更新)，一般用于 `CSS 文件`名生成。

# 8. Webpack5 中的资源如何管理

[参考](https://github.com/yangin/study-webpack/blob/main/WEBPACK-ASSETS.md)
