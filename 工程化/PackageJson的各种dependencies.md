# PackageJson 的各种 dependencies

## dependencies

`dependencies` 始终安装依赖项。通常，如果缺少依赖项，某些东西就会中断。例如：像 Bootstrap 这样的 UI 库或像 React 这样的框架。 NPM 建议不要将测试工具或转译器放在依赖项对象中。

> 项目中始终应用到的包，如 react 等。

## devDependencies

`devDependencies` 通常是在开发中使用的包，但对于项目的实际功能来说并不是必需的。例如：像 ESLint 这样的 linters 和像 Jest 这样的测试框架。

> 项目中开发时使用的包，在实际应用中不需要的包。如 jest、eslint 等。

## peerDependencies

`peerDependencies` 是通常不会自动安装的包。在某些情况下，您想表达您的包与另一个包的兼容性，而不是自己包含它。当您缺少 peerDependencies 时，NPM 会警告您。示例：react-router 包依赖于正在安装的 React。

> 这一般在写插件或者自定义工具包时使用。表示依赖于某个包，但是不会自动安装，需要用户自己安装。

## bundledDependencies

`bundledDependencies` 是在发布包时将被捆绑的包。

## optionalDependencies

`optionalDependencies` 是不一定需要安装的依赖项。如果可以使用依赖项，但如果找不到或安装失败，您希望 npm 继续，那么您可以将它放在 optionalDependencies 对象中。

> 通常我们将一些 CI 中非必要的包放入`optionalDependencies`， 然后配合`npm ci --no-optional`,在 CI 中不加载 optionalDependencies 的包，从而来加速 CI 的构建速度。例如：Cypress 等。
