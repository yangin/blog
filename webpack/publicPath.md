# Webpack5 中的 PublicPath

webpack5 自带的属性中有 3 处 publicPath，分别是：

- output.publicPath
- module.rules[].generator.publicPath
- devServer.static.publicPath

publicPath 表示路径`前缀`，用于修改资源的`引用路径`，不会影响文件的输出位置，可以为`相对路径`或`绝对路径`，通常以`/`结尾，如 `/static/`

## 前置知识

### 相对路径与绝对路径

相对路径

- 相对于当前文件的路径，如 `./static/`，`../static/`。

绝对路径

- 相对于根目录的路径（即 index.html 所在的目录），如 `/static/`。
- 带协议的路径，如 `https://cdn.example.com/static/`。

> `example/` 这种写法是错误的，既不属于相对路径，也不属于绝对路径。

# module.rules[].generator.publicPath

用来修改`匹配资源`的引用路径，如图片、字体等。

```css
/* index.css */
body {
  background: url(./public/xxx.png);
}
```

```js
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/i,
        type: "asset/resource",
        generator: {
          filename: "[hash][ext][query]",
          publicPath: "/static/", // background: url(./xxx.png) 会被编译为 background: url(/static/xxx.png)
          // publicPath: "https://cdn.example.com/static/", // background: url(./xxx.png) 会被编译为 background: url(https://cdn.example.com/static/xxx.png)
        },
      },
    ],
  },
};
```

在编译后的代码中，原来的资源引用路径 `./public/xxx.png` 会被编译为 `/static/xxx.png`，即`[publicPath][filename]`

常用来搭配 CDN 使用，如 `publicPath: "https://cdn.example.com/static/"`，最终静态资源的引用路径会变成 `https://cdn.example.com/static/xxx.png`。

### 应用场景

在开发或生产环境中，当调整了静态资源（如图片、字体等）的存放位置时（即调整了引用路径），可以通过该属性来全局修改引用路径。

### 注意

- 当 output.publicPath 与 module.rules[].generator.publicPath 同时存在时，module.rules[].generator.publicPath 会`覆盖` output.publicPath 对`匹配资源的引用路径`进行修改。

# devServer.static.publicPath

## 前置知识

### devServer.static.directory

devServer.static.directory 是 `webpack-dev-server` 的配置项，用于配置`非 webpack 打包文件`的静态资源的访问路径。即

```bash
Content not from webpack is served from '***' directory
```

这里的 `Content not from webpack` 即为`非 webpack 打包文件`，即项目文件中的静态资源文件，如 package.json 等。

`Content from webpack` 即为 `webpack 打包的文件`，即通过编译后打包的文件，存在`内存中`的。这一部分文件，也正是页面所展示的内容。

所以，directory 仅仅是用来指定 devServer 启动的`根目录`，这对在浏览器通过 url 路径获取项目中的文件有影响，而不影响 webpack-dev-server server 启动时从内存中读取文件信息。不影响页面展示的内容。

默认为 `public` 目录

```js
module.exports = {
  devServer: {
    static: {
      directory: path.join(__dirname, "/"),
    },
  },
};
```

如上，则可以通过 `http://localhost:8080/package.json` 访问到项目根目录下的 `package.json` 文件。因为 directory 指定了`path.join(__dirname, "/")`作为项目根目录。

如果将 directory 设置为 `path.join(__dirname, "src")`，则可以通过 `http://localhost:8080/index.html` 访问到项目的 `src/index.html` 文件。

### devServer.static.publicPath

如果 `output.path` 的输出路径与 `devServer.static.directory` 的路径一致，都为`path.join(__dirname, "dist")`，那么通过 `http://localhost:8080/index.html` 访问到的始终都是从`内存中`读取的 index.html 文件，而无法访问到打包出来的,实际存储在`dist`目录下的 index.html 文件。

> 因为相同的路径对应了 2 个文件， webpack-dev-server 会优先读取内存中的文件，而不是磁盘中的文件。

为了访问到磁盘中的文件，我们就需要引入一个新的配置项 `devServer.static.publicPath`，用于指定`非 webpack 打包文件`的访问路径`前缀`。

```js
module.exports = {
  devServer: {
    static: {
      directory: path.join(__dirname, "dist"),
      publicPath: "/static/",
    },
  },
};
```

这样，我们就可以通过 `http://localhost:8080/static/index.html` 访问到打包出来的 index.html 文件了。而避开了内存中的读取。

### 应用场景

- 在开发阶段，访问打包出来的文件，而不是内存中的文件。
- 在开发阶段，访问非 webpack 打包的文件，如 package.json 等。

# output.publicPath

output.publicPath 用于修改`webpack 打包文件`的引用路径。

```js
module.exports = {
  // ...
  output: {
    publicPath: "/static/", // background: url(./xxx.png) 会被编译为 background: url(/static/xxx.png)
  },
};
```

- 在 webpack-dev-server 中，会影响`webpack 打包文件`的访问路径，如原来访问`http://localhost:8080/`会变成 `http://localhost:8080/static/`。
- 在基于 html-webpack-plugin 进行 webpack build 时，会修改生成的 index.html 文件中的 script、link 等的引用路径，如原来的`<script src="main.js"></script>` 会被修改为 `<script src="static/main.js"></script>`

  > 这会导致在部署生产环境时，需要将打包文件存放在服务器项目的根目录的 static 子目录下，并通过子路径访问，如 `https://www.example.com/static/`。

- 在没有配置 Module.Rule.generator.publicPath 时，会修改了代码中资源的引用路径，如 `background: url(./xxx.png)` 会被编译为 `background: url(/static/xxx.png)`。

  > 如果配置了 Module.Rule.generator.publicPath，在引用静态资源时，会优先使用 Module.Rule.generator.publicPath，而不是 output.publicPath。

### 应用场景

当需要通过`子路径来访问`打包文件（或网站）时，可以通过该属性来进行修改。

### 注意

- publicPath 不会影响 webpack 打包文件的输出位置，只会影响`引用路径`。
- 在 html-webpack-plugin 中，当在 html template 中手动引入一个链接（绝对路径或相对路径）时，并不会被添加前缀
- html-webpack-plugin 中存在一个 publicPath 属性，当配置后，用来覆盖 output.publicPath。
- 这里的 publicPath 一般不使用带协议的绝对路径，因为会影响到 html-webpack-plugin 中的引用路径。

# 总结

- output.publicPath 用于修改`webpack 打包文件`的引用路径。
- devServer.static.publicPath 用于修改`非 webpack 打包文件`的访问路径。
- module.rules[].generator.publicPath 用于修改`匹配资源`的引用路径。
- devServer.static.publicPath 基于 webpack-dev-server，只在开发环境中有效，output.publicPath 与 module.rules[].generator.publicPath 在开发与生成环境中都有效。
- output.publicPath 与 module.rules[].generator.publicPath 都可以修代码中静态资源的引用路径，当同时存在时，只会采用 module.rules[].generator.publicPath 对`匹配资源的引用路径`进行修改。
- publicPath 只会影响`引用路径`，为原来的引用路径添加`前缀`，不会影响`输出位置`。
- publicPath 通常以`/`结尾，可以为绝对路径或相对路径，但在 output.publicPath 中，不建议使用带协议的绝对路径，因为会影响到 html-webpack-plugin 中的引用路径。
