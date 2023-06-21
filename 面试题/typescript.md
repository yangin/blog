[参考 1](https://juejin.cn/post/7162011064819777567)
[参考 2](https://juejin.cn/post/7236319311099297853)

# 1. 为什么要用 Typescript

### Q1: 什么是 Typescript

Typescript 是 JavaScript 的语法超集，使其支持更多的语法，例如类型系统、接口、泛型等等。最终通过编译器编译成 JavaScript。

### Q2: 为什么要用 Typescript

- 增加了代码的可读性和可维护性，尤其是对大型多人项目的维护。
- 通过类型检查，在编译阶段就能发现大部分错误，提升代码质量。
- 能让编译器更好，更准确的提示代码，提升编码效率。

### Q3: Typescript 的缺点

- 学习成本高
- 开发成本高，既要写代码，还要写类型。

### Q4: Typescript 的适用场景

- 多人协作的大型项目
- 库和框架开发
- 对于一些重要的逻辑或者代码，可以使用 TS 来增强其可靠性

# 2. Typescript 内置数据类型有哪些

- 基本类型：boolean、number、string、null、undefined
- 引用类型：object、array、`tuple`、`enum`、function、class、`interface`
- 其他类型：`any`、`unknown`、`never`、`void`、`type`

# 3. 什么是泛型，有什么作用

泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

简单来说就是`类型参数`，在定义某些函数、接口和类时，不写死类型，而是改用类型参数的形式，让`类型更加灵活`。

最常见的泛型是数组类型，例如：`Array<Type>`。

```ts
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

通过增加类型参数 Type, 我们可以捕获用户传入的类型，然后使用这个类型，输出这个类型。

```ts
// s is of type 'string'
const s = firstElement(["a", "b", "c"]);
// n is of type 'number'
const n = firstElement([1, 2, 3]);
// u is of type undefined
const u = firstElement([]);
```

# 4.类型别名 Type 与 接口 Interface 的区别

- Type 用来声明类型别名，包括基本类型、联合类型、元组、函数、类等等。Interface 只能用来声明对象类型。
- Type 一旦声明不可以改变，Interface 可以扩展合并。

# 5. any 与 unknown 的区别， never 与 void 的区别

### Q1: any 与 unknown 的区别

unknown 指的是 不可预知的类型。在很多场景下，它可以替代 any 的功能，同时保留静态检查能力。

不同是：在静态编译的时候，unknown 不能调用任何方法，而 any 可以。

### Q2: never 与 void 的区别

void 表示没有任何类型，一般用于`函数没有返回值`的情况。

never 表示永远不存在的值的类型。一般用于`函数抛出异常`的情况。

不同是：拥有 void 返回值类型的函数能`正常运行`。拥有 never 返回值类型的函数`无法正常返回`，无法终止，或会抛出异常。

# 6. 常用的工具类型

### Q1: 什么是工具类型

工具类型是一些内置的、预先定义好的泛型类型，它们可以帮助我们在编码过程中更方便地使用泛型。

工具类型可以认为是 TS 类型的工具函数，把原有类型当参数来处理。

```ts
// 已有类型User
interface User {
  name: string;
  age: number;
}

// 新类型PartialUser，使用Partial将属性都变成可选
type PartialUser = Partial<User>; // { name?: string; age?: number; }
```

### Q2: 常用的工具类型

- Partial<T>：将传入的属性变为可选
- Required<T>：将传入的属性变为必选
- Readonly<T>：将传入的属性变为只读
- Record<K, T>：创建一个类型，其属性名的类型为 K，属性值的类型为 T
- Pick<T, K>：从类型 T 中选择属性名为类型 K 中的属性，创建一个新类型
- Omit<T, K>：从类型 T 中剔除属性名为类型 K 中的属性，创建一个新类型
- Exclude<T, U>：从类型 T 中排除类型 U 中的所有属性。
- Extract<T, U>：从类型 T 中提取类型 U 中的所有属性。
- NonNullable<T>：从类型 T 中剔除 null 和 undefined。
- ReturnType<T>：获取函数类型 T 的返回类型。

# 7. 说说 TypeScript 中命名空间与模块的理解和区别

### Q1: 什么是命名空间

命名空间是`位于全局命名空间下`的一个普通的带有名字的 JavaScript 对象。它们提供了一种`避免全局命名冲突`的方法。

```ts
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}
```

### Q2: 什么是模块

模块是`外部模块`，它是一个独立的文件，且每个文件都是一个模块。

```ts
// Validation.ts
export interface StringValidator {
  isAcceptable(s: string): boolean;
}

// Test.ts
import { StringValidator } from "./Validation";
```

### Q3: 命名空间与模块的区别

- 命名空间是位于`全局命名空间`下的一个普通的带有名字的 JavaScript 对象。它们提供了一种`避免全局命名冲突`的方法
- 像命名空间一样，模块可以包含代码和声明。 不同的是模块可以`声明它的依赖`
- 在正常的 TS 项目开发过程中并`不建议用命名空间`，但通常在通过 d.ts 文件标记 js 库类型的时候使用命名空间，主要作用是给编译器编写代码的时候`参考`使用

# 8. TypeScript 中的 Declare 关键字有什么作用？

Declare 关键字用于声明一个`全局变量`的类型，但是并不会真的定义这个全局变量。

> declare 关键字除了可以声明全局变量之外，它还可以用来声明全局函数、全局类或全局枚举类型等
>
> ```ts
> declare var jQuery: any;
> declare function isNaN(number: number): boolean;
> ```

我们知道所有的 JavaScript 库/框架都没有 TypeScript 声明文件，但是我们希望在 TypeScript 文件中使用它们时不会出现编译错误。为此，我们使用`declare`关键字，在全局命名空间中声明一个变量，以便 TypeScript 编译器知道它的存在。

例如，引入微信公众平台的 JS-SDK。初始化之后，你就会在某个 TypeScript 文件中调用该 JS-SDK 提供的接口。

```ts
// 比如，调用拍照或从手机相册中选图接口来实现选图的功能
wx.chooseImage({
  // Error：找不到名称“wx”。ts(2304)
  count: 1, // 默认9
  sizeType: ["original", "compressed"],
  sourceType: ["album", "camera"],
  success: function (res) {
    var localIds = res.localIds;
  },
});
```

虽然你是按照微信开发文档来使用 JS-SDK 提供的接口，但对于以上的代码，TypeScript 编译器仍会提示相应的错误信息。这是因为 TypeScript 编译器不认识 wx 这个全局变量。

时候可以使用 declare 关键字来声明 wx 全局变量，从而让 TypeScript 编译器能够识别该全局变量。

```ts
declare var wx: any;
```

# 9. .d.ts 文件的作用是什么？

[参考](https://juejin.cn/post/6987735091925483551)

### Q1: 什么是 .d.ts 文件

`.d.ts`文件（即 TypeScript `Declaration` File）是 TypeScript 的`声明文件`，通过 `Declare` 关键字声明`全局变量类型`。

通常分为`自定义`的声明文件和`第三方库`的声明文件。

```ts
// 自定义声明文件
// global.d.ts
declare const i18n: {
  t: (key: string, replaceKey?: {}) => string;
};
```

### Q2: 如何引入第三方库的声明文件

在社区中有很多第三方库的声明文件，我们可以直接使用。只需要在项目中安装对应的声明文件即可。

```bash
npm install @types/react --save-dev
```

这样第三方库的声明文件会被安装到`node_modules/@types`目录下

根据 TypeScript 的编译机制，在`import`第三方库时，会先查找`node_modules/@types`目录下是否存在对应的声明文件，如果存在则直接使用，从而不会报错。

# 10. React 中如何使用 typescript

- 使用`create-react-app`脚手架创建项目时，可以选择`typescript`模板
- 在 webpack 中的 babel-loader 中添加`@babel/preset-typescript`插件
- 项目中类型检查
  - 定义 props 和 state 类型
  - 事件处理函数中事件对象的类型，尽量不要用 any
  - 对第三方库的封装要考虑类型声明
  - 对 Hooks 的参数和返回值要考虑类型声明
  - 数据接口需要定义类型

参考:

[react 中使用 typescript 最佳实践](https://github.com/typescript-cheatsheets/react)
