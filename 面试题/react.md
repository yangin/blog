# React

React + Redux + Next.js 20 题

[参考](https://juejin.cn/post/7194760495956492344)

# 1. React 的特性有哪些

- 组件化
  > 将`视图与逻辑`存放在一个松耦合单元内（即存放在一个文件，或一个文件夹中）。提高了组件的复用性。
- 数据驱动视图
  > 通过 setState() 方法更新组件的 state，从而触发组件的重新渲染。
- JSX 语法
  > 用于声明组件结构，更加直观的表达组件的事件处理及状态变化逻辑。
- 单向数据绑定
  > 只支持将数据通过 setState 传递给 UI, 不支持将 UI 传递给 state 并进行保存。这样更安全，更快速。
- 虚拟 DOM
  > 使用虚拟 DOM, 通过 Diff 算法来有效的操作真实 DOM，从而提高性能。
- 声明式编程
  > 不需要关注元素的创建过程，只需要关注元素的结果。

# 2. 对 JSX 的理解

### Q1：什么是 JSX

JSX 是 Javascript 的一个扩展语法，允许`在 JS 中使用 XML` 语法。由 Facebook 团队创造。浏览器引擎无法直接识别其语法，需要 Babel 之类插件转换成 JS 代码，然后被浏览器识别。

```js
// 此行为jsx语法
const heading = <h1>Welcome to Scotch</h1>;
```

### JSX 语法是必须的吗

JSX 语法`非必须`，可以使用 React.createElement() 方法来创建元素，但是 JSX 语法更加简洁。

### Q3：Babel 插件是如何实现 JSX 到 JS 的编译

JSX 是 JS 的扩展语法，主要用于`声明元素`，可以理解为 React.createElement()的语法糖。最后会被 Babel 编译成 `React.createElement()` 方法。

```js
// JSX
const heading = <h1>Welcome to Scotch</h1>;
// Babel 编译后
const heading = React.createElement("h1", null, "Welcome to Scotch");
```

声明式设计只注重结果，不注重元素的创建过程，而命令式则注重元素的创建过程。

### Q4：为什么提出 JSX

更直观的表达组件的结构与功能，更方便的去理解组件的事件处理及状态变化逻辑。从而提高组件的复用性。

React 核心是`组件`，为了更直观的表达组件的事件处理及状态变化逻辑。将`视图与逻辑`耦合在一起（即存放在一个文件，或一个文件夹中），提高了组件的复用性。

### Q5: .jsx 的后缀名有什么作用？与 .js 有什么区别？.tsx 与 .ts 也是可以替换的吗？

.jsx 类似于.ts,是一种 JS 扩展语法文件名后缀，表示该`文件中包含 JSX 语法`。方便编译器选择对应的`语言模式`（如 VSCode 的 Javascript JSX 语言模式）进行识别，并默认高亮显示，否则针对 JSX 语法会高亮报错。

.jsx 与.js 后缀文件在`内容书写`与`编译`时是没有区别的，可以互相替换。

.tsx 与.ts 文件后缀`不可以互换`，这是由 Typescript 的编译规则决定的。

# 3. 对于 React 虚拟 DOM 的理解

虚拟 DOM，即 Fiber 对象

### Q1: 什么是虚拟 DOM

- 一个 JS 对象, 保存在内存中
- 是对真实 DOM 结构的映射

### Q2: 虚拟 DOM 的工作流程

Mount 阶段：

React 将结合 JSX 的描述，构建出虚拟 DOM 树，然后通过 ReactDOM.render 实现虚拟 DOM 到真实 DOM 的映射；

Update 阶段：

当组件的 state 或 props 发生变化时，React 会重新构建虚拟 DOM 树，然后通过 Diff 算法，找出需要更新的节点，然后通过 ReactDOM.render 实现虚拟 DOM 到真实 DOM 的映射；

### Q3: 虚拟 DOM 解决的关键问题

- 提高渲染性能：通过 diff 算法得到最小 DOM 操作量，然后批量执行，减少了与 DOM 的交互次数，从而提高性能。
- 提高研发效率：开发者只需要关注数据的变化，无需手动操作 DOM，提高开发效率。
- 跨平台：其本质上是 JS 对象，因此可以实现跨平台，如 React Native 就是通过虚拟 DOM 实现跨平台。

> - react 将虚拟 DOM 这一环节单独打包成了一个独立的库 react。这样做的好处是，屏蔽了底层平台差异，可以让 react 适用于更多的平台，比如 react-native，react-canvas 等等。

# 4. 虚拟 DOM 与 DOM 的比较

| 比较 |                                    DOM                                    |                                                                                   虚拟 DOM                                                                                    |
| :--: | :-----------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| 位置 |                                浏览器内存                                 |                                                                                  浏览器内存                                                                                   |
| 语法 |                                   HTML                                    |                                                                                    JS 对象                                                                                    |
| 优点 |                                直观，易用                                 | 1. 无需手动操作 DOM（通过 state 与 props 更新），提高了开发效率 <br/> 2. 将频繁修改比较后一次性绘制到 DOM 中，减少过多 DOM 节点排版与重绘损耗，提高了渲染性能 <br/> 3. 跨平台 |
| 缺点 | 1. 解析速度慢，内存占用量高<br/>2. 多次操作引起频繁排版与重绘，性能消耗大 |                         在一些性能要求极高的应用中虚拟 DOM 无法进行针对性的极致优化，首次渲染大量 DOM 时，由于多了一层虚拟 DOM 的计算，速度比正常稍慢                         |

# 5. React 的生命周期

[React 生命周期图解](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

Mounting 时：

- constructor()
- [static getDerivedStateFromProps()](https://react.dev/reference/react/Component#static-getderivedstatefromprops)
- render()
- componentDidMount()

Updating 时：

- static getDerivedStateFromProps()
- shouldComponentUpdate()
- render()
- getSnapshotBeforeUpdate()
- componentDidUpdate()

Unmounting 时：

- componentWillUnmount()

Error Handling 时：

- static getDerivedStateFromError()
- componentDidCatch()

> 在 Mounting 与 Updating 时，整理处理过程分为 3 个阶段：
>
> - Render 阶段：根据组件的 props 与 state，生成虚拟 DOM 树。
> - Pre-commit 阶段：在生成真实 DOM 树前，此时可以读取 DOM，对应的生命周期为 getSnapshotBeforeUpdate()。
> - Commit 阶段：将虚拟 DOM 树转换成真实 DOM 树，并将其插入到 DOM 容器中。此时可以使用 DOM，运行 Side Effects，安排更新

# 6. React 父子组件的生命周期调用顺序

```jsx
//parent组件
import React from "react";
import Son from './son'

class Parent extends React.Component {
    constructor(props) {
        super(props)
        this.state={}
        console.log('parent constructor')
    }
    static getDerivedStateFromProps(){
        console.log('parent getDerivedStateFromProps')
        return {}
    }
    componentDidMount() {
        console.log('parent didMount')
    }
    componentWillUnmount() {
        console.log('parent willUnmount')
    }
    shouldComponentUpdate(){
        console.log('parent scu')
        return true
    }
    render() {
        console.log('parent render')
        return <div>
            <h3>parent</h3>
            <Son></Son>
        </div>
    }
}

export default Parent

//son 组件
import React from "react";

class Son extends React.Component {
    constructor(props) {
        super(props)
        this.state={}
        console.log('son constructor')
    }
    static getDerivedStateFromProps(){
        console.log('son getDerivedStateFromProps')
        return {}
    }
    componentWillUnmount() {
        console.log('son willUnmount')
    }

    componentDidMount() {
        console.log('son didMount')
    }
    shouldComponentUpdate(){
        console.log('son scu')
        return true
    }
    render() {
        console.log('son render')
        return <h3>son</h3>
    }

}

export default Son
```

结果：

```js
parent constructor
parent getDerivedStateFromProps
parent render
son constructor
son getDerivedStateFromProps
son render
son didMount
parent didMount
// 下面是当页面卸载时的输出
// parent willUnmount
// son willUnmount

```

### 7. React 事件和原生事件执行顺序

```jsx
// React 事件和原生事件的执行顺序
import React from "react";

class EventRunOrder extends React.Component {
  constructor(props) {
    super(props);
    this.parent = null;
    this.child = null;
  }

  componentDidMount() {
    this.parent.addEventListener("click", (e) => {
      console.log("dom parent");
    });

    this.child.addEventListener("click", (e) => {
      console.log("dom child");
    });

    document.addEventListener("click", (e) => {
      console.log("document");
    });
  }

  childClick = (e) => {
    console.log("react child");
  };

  parentClick = (e) => {
    console.log("react parent");
  };

  render() {
    return (
      <div onClick={this.parentClick} ref={(ref) => (this.parent = ref)}>
        <div onClick={this.childClick} ref={(ref) => (this.child = ref)}>
          test
        </div>
      </div>
    );
  }
}

export default EventRunOrder;
```

结果：

```js
// 这里输出结果有疑问，需要验证
react child
dom child
react parent
dom parent
document
```

# 8. React 的事件机制

[参考](https://juejin.cn/post/7068649069610024974)

### Q1: React 事件机制

React 构建了一套`合成事件`机制，将 React 注册的事件通过`事件委托`的方式`绑定到 document` 上（原 DOM 元素上的事件会被置为空），然后进行统一的事件监听。合成事件的处理是基于冒泡阶段的顺序。

浏览器对`原生事件`的处理流程分为自外向内的捕获阶段与自内向外的冒泡阶段，浏览器默认`在冒泡阶段执行事件处理`函数, 在捕获阶段不处理。

### Q2: React 事件与原生事件执行顺序

- 原生事件`先于`合成事件
  > 因为合成事件是通过事件委托的方式绑定到 document 上的，原生事件是直接绑定到元素上的，浏览器是在冒泡阶段处理事件的，所以是先处理元素上的事件，当冒泡到 document 时，再来处理 document 上的合成事件。
- 合成事件`先于`document 上的原生事件
  > 这里要看源码？？？

### Q3: 阻止事件冒泡

- 原生事件：e.stopPropagation()，同时阻止所有合成事件的触发，因为阻止了到 document 的冒泡
- 合成事件：e.preventDefault()，不能阻止原生事件的触发，因为其先执行的

所以不建议原生事件与合成事件一起混用。

### Q4: React 为什么使用合成事件

- 进行浏览器兼容，实现更好的跨平台
- 减少内存消耗，提升性能
- 方便事件统一管理和事务机制

# 9. 函数组件闭包陷阱问题

以下函数组件代码，先 alert 再 add，页面显示的值和 alert 的值分别是什么

```jsx
import { useState } from "react";

const FunctionComponentClosure = () => {
  const [value, setValue] = useState(1);
  const log = () => {
    setTimeout(() => {
      alert(value);
    }, 3000);
  };
  return (
    <div>
      <p>{value}</p>
      <button onClick={log}>alert</button>
      <button onClick={() => setValue(value + 1)}>add</button>
    </div>
  );
};

export default FunctionComponentClosure;
```

结果：

alert: 1

页面显示: 2

> 原因：
>
> log 方法内的 value 和`点击动作触发时`的 value 相同，后续 value 的变化不会对 log 内部的 value 产生任何的影响。这种现象被称为 `闭包陷阱`，即函数式组件每次 render 都产生一个新的 log 函数，这个 log 函数会产生一个当前阶段 value 值的闭包。

解决方案：

- 使用 useRef、useCallback、useReducer 可以保留上一次的值的特性来处理

```jsx
const Test = () => {
  const [value, setValue] = useState(1);
  const countRef = useRef(value);

  const log = function () {
    setTimeout(() => {
      alert(countRef.current);
    }, 3000);
  };
  useEffect(() => {
    countRef.current = value;
  }, [value]);

  return (
    <div>
      <p>{value}</p>
      <button onClick={log}>alert</button>
      <button onClick={() => setValue(value + 1)}>add</button>
    </div>
  );
};
```

- 使用 class 组件,class 组件不存在闭包陷阱问题

```jsx
class Test extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 1,
    };
  }
  log = () => {
    setTimeout(() => {
      alert(this.state.value);
    }, 3000);
  };
  render() {
    return (
      <div>
        <p>{this.state.value}</p>
        <button onClick={this.log}>alert</button>
        <button
          onClick={() =>
            this.setState({
              value: this.state.value + 1,
            })
          }
        >
          add
        </button>
      </div>
    );
  }
}
export default Test;
```

结果：alert 和页面显示的值相同

# 10. 受控组件和非受控组件

受控组件：表单元素的值`由 React 组件控制`，通过 props 传递 value 值，通过 onChange 事件处理函数`更新` value 值。

非受控组件：表单元素的值`由 DOM 元素控制`，通过 ref 获取 DOM 元素，通过 defaultValue 设置初始值，通过 onChange 事件处理函数`获取值`。

例如，下面的代码使用非受控组件接受一个表单的值:

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }

  handleSubmit(event) {
    alert("A name was submitted: " + this.input.current.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

# 11. React 如何实现状态保存

### Q1: 为什么需要状态保存

在 React 中通常使用路由去管理不同的页面，在切换页面时，路由将会卸载掉未匹配的页面组件，所以比如从列表进入详情页面，等到退回列表页面时会回到列表页的顶部。

### Q2: 需要状态保存的场景

- 列表进入详情
- 已填写但是未提交的表单
- 管理系统中可切换和关闭的标签

### Q3: React 为什么不支持状态保存

状态保存在 vue 中可以使用 keep-alive 进行实现，但是 react 认为这个功能容易造成`内存泄漏`，所以暂时不支持。

### Q4: React 如何实现状态保存

- 使用 redux 手动保存: 在状态更新或 componentWillUnmount 时，将状态通过 redux 保存，然后在 componentDidMount 时进行数据恢复。

# 12. 常用 hooks 的使用

[了解常用 hooks 的使用场景及使用方法](https://react.dev/reference/react)

hooks：

- useState
- useEffect
- useLayoutEffect
- useContext
- useReducer
- useMemo --> memo
- useCallback
- useRef --> ref
- useImperativeHandle

# 13. React 中的 setState 执行机制

[参考](https://vue3js.cn/interview/React/setState.html#%E4%BA%8C%E3%80%81%E6%9B%B4%E6%96%B0%E7%B1%BB%E5%9E%8B)

### Q1: 同步异步执行时机

- 在组件生命周期或 React 合成事件中，setState 是异步。要想获取更新后的值，可以通过 setState 的第二个参数传入一个函数（函数组件通过 useEffect）。
- 在 setTimeout、Promise 或者原生 dom 事件中，setState 是同步。

example

```jsx
import React, { Component } from "react";

export default class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      message: "Hello World",
    };
  }

  render() {
    return (
      <div>
        <h2>{this.state.message}</h2>
        <button onClick={(e) => this.changeText()}>面试官系列</button>
      </div>
    );
  }

  changeText() {
    this.setState({
      message: "JS每日一题",
    });
  }
}
```

异步更新

```jsx
changeText() {
  this.setState({
    message: "你好啊"
  })
  console.log(this.state.message); // Hello World
}
```

同步更新

在 setTimeout 中

```jsx
changeText() {
  setTimeout(() => {
    this.setState({
      message: "你好啊"
    });
    console.log(this.state.message); // 你好啊
  }, 0);
}
```

在原生 DOM 事件中

```jsx
componentDidMount() {
  const btnEl = document.getElementById("btn");
  btnEl.addEventListener('click', () => {
    this.setState({
      message: "你好啊,李银河"
    });
    console.log(this.state.message); // 你好啊,李银河
  })
}
```

### Q2: 批量更新

- 合成事件或者生命周期中 setState 传入对象会被合并。要想避免合并可以将第一个参数写成函数。
- 而在 setTimeout、Promise 或者原生 dom 事件中，由于是同步的操作，所以并不会进行覆盖现象。

example

```jsx
handleClick = () => {
  this.setState({
    count: this.state.count + 1,
  });
  console.log(this.state.count); // 1

  this.setState({
    count: this.state.count + 1,
  });
  console.log(this.state.count); // 1

  this.setState({
    count: this.state.count + 1,
  });
  console.log(this.state.count); // 1
};
```

点击按钮触发事件，打印的都是 1，页面显示 count 的值为 2

对同一个值进行多次 setState， setState 的批量更新策略会对其进行覆盖，取最后一次的执行结果

上述的例子，实际等价于如下：

```jsx
Object.assign(
  previousState,
  {index: state.count+ 1},
  {index: state.count+ 1},
  ...
)
```

由于后面的数据会覆盖前面的更改，所以最终只加了一次

如果是下一个 state 依赖前一个 state 的话，推荐给 setState 一个参数传入一个 function，如下：

```jsx
onClick = () => {
  this.setState((prevState, props) => {
    return { count: prevState.count + 1 };
  });
  this.setState((prevState, props) => {
    return { count: prevState.count + 1 };
  });
};
```

而在 `setTimeout` 或者`原生 dom 事件`中，由于是同步的操作，所以并不会进行覆盖现象

# 14. super 和 super(props)的区别

在 ES6 的 class 中：

```js
class sup {
  constructor(name) {
    this.name = name;
  }

  printName() {
    console.log(this.name);
  }
}

class sub extends sup {
  constructor(name, age) {
    super(name); // super代表的是父类的构造函数
    this.age = age;
  }

  printAge() {
    console.log(this.age);
  }
}

let jack = new sub("jack", 20);
jack.printName(); //输出 : jack
jack.printAge(); //输出 : 20
```

在上面的例子中，可以看到通过 super 关键字实现调用父类，super 代替的是父类的构建函数，使用 super(name) 相当于调用 `sup.prototype.constructor.call(this,name)`

如果在子类中不使用 super 关键字，则会引发报错，报错的原因是`子类是没有自己的 this 对象的，它只能继承父类的 this 对象，然后对其进行加工`。

而 `super() 就是将父类中的 this 对象继承给子类`的，没有 super() 子类就得不到 this 对象。

如果先调用 this，再初始化 super()，同样是禁止的行为。所以在子类 constructor 中，必须先代用 super 才能引用 this。

在 React 中，类组件是基于 ES6 的规范实现的，继承 React.Component，因此如果用到 constructor 就必须写 super() 才初始化 this。

这时候，在调用 super() 的时候，我们一般都需要传入 props 作为参数，如果不传进去，React 内部也会将其定义在组件实例中。

所以无论有没有 constructor，在 render 中 this.props 都是可以使用的，这是 React 自动附带的，是可以不写的。

综上所述：

- ES6 中类必须有一个 constructor，如果没有显式定义，一个空的 constructor 会被默认添加。
- ES6 要求，子类的 constructor 必须执行一次 super 函数，否则会报错。
- 在 React 中，类组件基于 ES6，所以在 constructor 中必须使用 super
- 在调用 super 过程，无论是否传入 props，React 内部都会将 props 赋值给组件实例 props 属性中
- 如果只调用了 super()，那么 this.props 在 super() 和构造函数结束之间仍是 undefined

# 15. react 引入 css 的方式有哪些

### Q1: react 引入 css 时，要注意哪些

- 可以编写局部 css，不会随意污染其他组件内的原生；
- 可以编写动态的 css，可以获取当前组件的一些状态，根据状态的变化生成不同的 css 样式；
- 支持所有的 css 特性；
- 编写起来简洁方便、最好符合一贯的 css 风格特点；

### Q2：最佳解决方案

- 基于 webpack 的 `css-loader` 引入 less, sass, css 等
  > css-loader 通过 options.modules.localIdentName 来生成带 hash 值的类名，防止类名污染
  > css-loader 通过:global()来声明全局样式
  > 通过编写不同状态下的 css 类名，来实现动态的 css
- css in js，如 `styled-components`
  > styled-components 通过 props 来传递组件的状态参数，可以实现动态的 css
  > styled-components 支持 hash 值，防止类名污染

### Q3: 传统的 css 引入方式问题

- 组件中直接引入.css 文件，其作用域是全局的，会造成类名污染；
- 通过.module.css 文件引入，解决嘞局部作用域问题，但不方便基于状态变化动态修改样式，需要内联的方式编写，且文件名后缀过长。

# 16. react 事件绑定方式有哪些

#### 1. 在 render 中直接通过 bind 绑定

```jsx
<div onClick={this.handleClick.bind(this)}>test</div>
```

问题：

- 每次 render 都会重新进行 bind 操作，影响性能

#### 2. render 方法中使用箭头函数

```jsx
<div onClick={(e) => this.handleClick(e)}>test</div>
```

问题：

- 每次 render 都会生成一个新的函数，造成不必要的性能损耗
- 且如果将该函数作为 props 传递给子组件时，会造成子组件的不必要的重新渲染。

#### 3. 在 constructor 中进行 bind 操作

```jsx
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this);
}
```

问题：

- 编写过于冗杂

##### 4. 组件定义阶段使用箭头函数绑定

```jsx
handleClick = useCallback((e) => {
  console.log(e);
}, []);

<div onClick={handleClick}>test</div>;
```

此种绑定方式最简单，性能也做好，`推荐`。

# 17. React 中组件之间如何通信

- 父组件向子组件通信：通过 props 传递数据
- 子组件向父组件通信：父组件向子组件传一个函数，然后通过这个函数的回调，拿到子组件传过来的值
- 兄弟组件之间的通信：状态提升，在公共的父组件中进行状态定义
- 父组件向后代组件传递：React.createContext 创建一个 context 进行组件传递
- 非关系组件传递：redux

# 18. React Diff 算法

[参考 1](https://react.iamkasong.com/diff/prepare.html)

[参考 2](https://github.com/yangin/blog/blob/main/react/source-code.md)

### Q1: Diff 算法是怎么提高性能的？

- 通过对比前后两次更新的虚拟 DOM 树，找出需要更新的节点，然后`批量执行`，减少了与 DOM 的交互次数，从而提高性能。
- Diff 算法，对未变化的 DOM `节点进行复用`，`减少了创建新 DOM 节点的时间`。
- 通过设置限制条件，只对同级元素进行 Diff，`降低了算法复杂度`。

### Q2: Diff 算法的限制

为了降低 react diff 算法的复杂度，增加了一些限制条件：

- 只对同级元素进行 Diff。如果一个 DOM 节点在前后两次更新中跨越了层级，那么 React 不会尝试复用他。
- 两个不同类型的元素会产生出不同的树。如果元素由 div 变为 p，React 会销毁 div 及其子孙节点，并新建 p 及其子孙节点。
- 开发者可以通过 key prop 来暗示哪些子元素在不同的渲染下能保持稳定。

### Q3: Diff 算法是何时执行的

- 在 React 的 Reconciler（包括 render、commit 两个阶段） 的 render 阶段。

- render 阶段的主要工作是通过`深度优先遍历`创建出一棵新的 Fiber 树（即更新后的 Fiber 树）

- render 的`深度优先遍历`过程，是基于 beginWork、completeWork 来开展工作的。

- diff 算法是在`beginWork`阶段执行的（且只有在 update 阶段有，mount 阶段没有）

### Q4: Diff 算法的执行过程

### _单节点 Diff_

React 通过先判断 key 是否相同，如果 key 相同则判断 type 是否相同，只有都相同时一个 DOM 节点才能复用。

> 注意：
>
> - 当 child !== null 且 key 相同且 type 不同时执行 deleteRemainingChildren 将 child 及其兄弟 fiber 都标记删除。
> - 当 child !== null 且 key 不同时仅将 child 标记删除。

为什么只需要比较 key 与 type 两个属性呢？

因为 props 及 children 的改变（children 实际也为 props）,会被作为参数传入到 createFiber 中去。即不需要生成一个新的对象，只需要在原来的旧的对象上进行重新赋值即可，节省的是创建 object 的性能开销。

#### _多节点 Diff_

[参考](https://react.iamkasong.com/diff/multi.html#%E6%A6%82%E8%A7%88)

    - 新增节点
    - 删除节点
    - 更新节点（如 type 或 props）

### Q5: React 中的 key 有什么作用

- key 是 React 中用于追踪哪些元素被修改、被添加或者被移除的辅助标识。
- 在 Diff 算法中，key 用于对比新旧节点，用于追踪哪些元素被修改、被添加或者被移除，从而判断该节点是否可以复用。
- 在开发中，key 值应该是唯一且稳定的，不要使用 index 作为 key 值。

# 19. 说说 Component, PureComponent, memo 的区别

### Component

- Component 是 React 中的基类，用来创建类组件。
- 内部的 shouldComponentUpdate 总是返回 true。
- 所以每次调用 setState 都会触发 render，无论 props 或 state 是否发生了变化。
- 父组件更新会导致子组件也跟着 render，即使子组件的 props 没有发生变化。

### PureComponent

- PureComponent 继承自 Component
- 实现了 shouldComponentUpdate 方法，对 props 和 state 进行浅比较。
- 所以 PureComponent 只有在 `props 或 state` 发生变化时才会触发 render。
- PureComponent 在性能上更优于 Component。

### Memo

- memo 是一个高阶组件，一般只用来包装函数组件。
- memo 是一个高阶组件，其接收一个组件作为参数，返回一个新的组件，
- 新组件中实现了 shouldComponentUpdate 方法，只对 props 进行浅比较，
- 所以 memo 只有在 `props` 发生变化时才会触发 render。

# 20. react 高阶组件以及应用场景

在 React 中，高阶组件是参数为组件，返回值为新组件的函数。本质也就是一个函数，并不是一个组件。高阶组件的这种实现方式，本质上是一个`装饰者设计模式`。

### Q1: 怎么写高阶组件

_`无传入参数`的高阶组件_

```jsx
import React, { Component } from "react";

export default (WrappedComponent) => {
  return class EnhancedComponent extends Component {
    // do something
    render() {
      return <WrappedComponent />;
    }
  };
};
```

_`有传入参数`的高阶组件_

```jsx
export default function highOrderRequest(options) => {
  return function (WrappedComponent) {
    return class EnhancedComponent extends Component {
      constructor(props) {
        super(props);
        this.state = {
          data: null,
          loading: false,
          hasData: false,
          hasError: false,
        };
      }
      async componentDidMount() {
        this.setState({ loading: true });
        /** 请求相关代码，不写了 */
        await request();
        this.setState({
          data: {
            /** 请求回来的数据 */
          },
          loading: false,
          hasData: true,
          hasError: false,
        });
      }

      render() {
        const combinedProps = { ...this.props, ...this.state };
        return <Component {...combinedProps} />;
      }
    };
  }
};
```

使用带参数的高阶组件

```jsx
function UseHighOrderExample(props) {
  console.log(props);
  const { data, loading, hasData, hasError } = props;
  return <div>high render</div>;
}

export default highOrderRequest({
  url: "",
  path: "/",
  method: "post",
  body: { test: 1 },
})(UseHighOrderExample);
```

通过对传入的原始组件 WrappedComponent 做一些你想要的操作（比如操作 props，提取 state，给原始组件包裹其他元素等），从而加工出想要的组件 EnhancedComponent。

常见的高阶组件有：

- React.memo : 内部实现了对 props 的浅比较，只有在 props 发生变化时才会触发 render。
- Redux 的 connect : 用于连接 React 组件与 Redux store, 将 redux 中的状态添加给被传入的组件。

### Q2: 高阶组件的应用场景

高阶组件能够提高代码的复用性和灵活性。常常用于`与核心业务无关`但又在多个模块使用的功能，如权限控制、日志记录、数据校验、异常处理、统计上报等

# 21. 对 Immutable 的理解,如何应用在 React 项目中

### Q1: 什么是 Immutable

- Immutable 是一种数据的不可变性，即数据一旦创建，就不能再被更改。
- 对 Immutable 对象的任何修改或添加删除操作都会返回一个新的 Immutable 对象。

### Q2: Immutable 的实现原理

Immutable 的实现原理是`持久化数据结构`

- 用一种数据结构来持久化保存数据
- 当数据被修改时，会返回一个新的对象，新对象与旧对象共享未变更的部分结构，只变更了修改的部分。这样避免了 deepCopy 把所有节点都复制一遍带来的性能损耗。即`通过共享数据来降低复制数据的成本`。

### Q3: Immutable 的使用场景

- 用来替代 deepCopy，提高性能。
  > 父组件传递给子组件的 props，若是一个`复杂的对象`，且希望每次变更属性，子组件都渲染，则需要每次都进行 deepCopy，此时可以使用 Immutable 来提高性能。
- 需要对数据进行大量的增删改查时，可以使用 Immutable 来提高性能。
  > 如 Redux 中的 reducer，每次都会返回一个新的 state，此时可以使用 Immutable 来提高性能。
- 需要对数据进行前后比较时，可以使用 Immutable 来提高性能。
  > 如 React 中的 shouldComponentUpdate，每次都会对 props 和 state 进行比较，此时可以使用 Immutable 来提高性能。

### Q4: Immutable 的写法

在 redux 的 reducer 中使用

```jsx
import * as constants from "./constants";
import { fromJS } from "immutable";
const defaultState = fromJS({
  //将数据转化成immutable数据
  home: true,
  focused: false,
  mouseIn: false,
  list: [],
  page: 1,
  totalPage: 1,
});
export default (state = defaultState, action) => {
  switch (action.type) {
    case constants.SEARCH_FOCUS:
      return state.set("focused", true); //更改immutable数据
    case constants.CHANGE_HOME_ACTIVE:
      return state.set("home", action.value);
    case constants.SEARCH_BLUR:
      return state.set("focused", false);
    case constants.CHANGE_LIST:
      // return state.set('list',action.data).set('totalPage',action.totalPage)
      //merge效率更高，执行一次改变多个数据
      return state.merge({
        list: action.data,
        totalPage: action.totalPage,
      });
    case constants.MOUSE_ENTER:
      return state.set("mouseIn", true);
    case constants.MOUSE_LEAVE:
      return state.set("mouseIn", false);
    case constants.CHANGE_PAGE:
      return state.set("page", action.page);
    default:
      return state;
  }
};
```

### Q5: 相关库

- [immutable.js](https://github.com/immutable-js/immutable-js/)
- [immutable-helper](https://github.com/kolodny/immutability-helper)
- [immer](https://github.com/immerjs/immer)

# 22. React 内部执行过程（即源码解读）

[参考 1](https://github.com/yangin/blog/blob/main/react/render-progress.md)

[参考 2](https://github.com/yangin/blog/blob/main/react/source-code.md)

# 23. react 组件间的过度动画如何实现

- 为组件手写 原生的 css 动画
- 使用第三方库
  - [react-transition-group](https://github.com/reactjs/react-transition-group)
  - [react-motion](https://github.com/chenglou/react-motion)
  - [animated](https://github.com/animatedjs/animated)

# 24. React 中常用的性能优化手段

- 使用`shouldComponentUpdate`, `PureComponent`或`React.memo`来避免不必要的渲染。
- 使用 Immutable 来提高性能，如在 shouldComponentUpdate 中，在 redux 的 reducer 中使用。
- 事件绑定方式上，使用箭头函数绑定，或者在 constructor 中进行 bind 操作。避免每次 render 都重新进行 bind 操作。
- 使用`React.Fragments`来避免额外的父标签。
- 使用`React.lazy`和`Suspense`来实现组件的懒加载。
  > 使用如下：
  >
  > ```jsx
  > const OtherComponent = React.lazy(() => import("./OtherComponent"));
  >
  > function MyComponent() {
  >   return (
  >     <div>
  >       <Suspense fallback={<div>Loading...</div>}>
  >         <OtherComponent />
  >       </Suspense>
  >     </div>
  >   );
  > }
  > ```
- 采用服务端渲染，提高首屏渲染速度。

# 25. React 项目中如何捕获错误

- 使用 componentDidCatch 方法捕获错误
- 使用 ErrorBoundary 组件捕获错误
- 使用 window.onerror 捕获错误
- 使用 try catch 捕获错误
- 第三方监控平台，如 sentry 等

# 26. React Router

### Q1: 说说对 React Router 的理解

React Router 是一个基于 React 之上的强大路由库，它可以让你向应用中快速地添加视图和数据流，同时保持页面与 URL 间的同步。

### Q2: React Router 有几种模式及实现原理

React Router 有三种模式：

- BrowserRouter：使用 HTML5 提供的 history API 实现，不兼容 IE9 及以下版本。
- HashRouter：使用 URL 中的 hash（#）部分去创建路由，兼容性好。
- MemoryRouter：不使用 URL，而是将 URL 存储在内存中的 router 中，适用于非浏览器环境，如 React Native。

### Q3: 常用的路由组件

- Route：路由匹配时渲染的组件
- Switch：只渲染第一个匹配到的路由组件
- Redirect：重定向
- Prompt：路由跳转前进行提示
- withRouter：将路由信息注入到组件的 props 中
- useLocation：获取 location 对象
- useParams：获取路由参数
- Router：路由容器，可自定义路由实现

参考

- [ReactRouter 与 History](https://github.com/yangin/blog/blob/main/react-router%E4%B8%8Ehistory.md)

# 27. Redux

### Q1: 说说对 Redux 的理解

Redux 是一个状态管理库，它可以用来管理 React 应用中的所有状态。

### Q2: Redux 的工作原理

Redux 的工作原理是单向数据流，即数据的流动方向是单向的，从而避免了数据的混乱。

Redux 的工作流程如下：

- 用户通过组件触发一个 action
- action 通过 store.dispatch(action) 发送到 store
- store 通过 reducer 处理 action，返回一个新的 state
- store 通过 store.subscribe(listener) 通知组件 state 发生了变化
- 组件通过 store.getState() 获取最新的 state
- 组件根据最新的 state 进行渲染

### Q3: 对 Redux 中间件的理解

Redux 中间件是对 store.dispatch 方法的扩展，它提供了一种机制，可以在 action 发出之后，到达 reducer 之前的这个过程中，进行一些额外的操作。

### Q4: 常用的 Redux 中间件

- redux-thunk：用于处理异步操作
- redux-logger：用于打印日志
- redux-promise：用于处理异步操作，基于 promise

### Q5: Redux 中间件的实现原理

Redux 中间件的实现原理是`函数柯里化`，即将一个接受多个参数的函数，转化为接受一个参数的函数，然后返回一个新的函数的技术。

参考：

[Redux](https://github.com/yangin/blog/blob/main/Redux.md)

[React-Redux](https://github.com/yangin/blog/blob/main/React-Redux.md)

[Redux 面试题](https://vue3js.cn/interview/React/redux.html#%E4%BA%8C%E3%80%81%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86)

# 28. 服务端渲染-SSR

常用的实现方式：Next.js

### Q1: 什么是服务端渲染

服务端渲染是指在服务端将 React 组件渲染成 HTML 字符串，然后将其发送到浏览器端，最后将其激活为可交互的应用程序。

### Q2: 服务端渲染解决了什么问题？

- SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面
- 加速首屏加载，解决首屏白屏问题

[参考](https://vue3js.cn/interview/React/server%20side%20rendering.html#%E4%B8%80%E3%80%81%E6%98%AF%E4%BB%80%E4%B9%88)
