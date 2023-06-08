# 1. var、let、const 的区别

- 变量提升
  > var 声明的变量会提升到作用域的顶部，let 和 const 声明的变量不会提升。
- 重复声明
  > var 可以重复声明，let 和 const 不可以重复声明。
- 作用域
  > let 和 const 声明的变量是块级作用域，var 不存在块级作用域。
- 临时死区
  > let 和 const 声明的变量会绑定在块级作用域，如果在声明之前使用，会报错。var 不存在临时死区。
- 修改变量值
  > var 和 let 声明的变量可以修改，const 声明的变量不可以修改。

# 2. ES6 中 Set、Map、WeakSet、WeakMap 的区别

Set 和 Map 都是 ES6 中新增的数据结构，都是用来存储数据的。

### Set

Set 是一种叫做`集合`的数据结构，它的特点是`成员的值都是唯一的`，没有重复的值，且是无序的。存储结构为[value: value]，没有键名，只有键值。

遍历方法

- keys()
- values()
- entries()
- forEach()

操作方法

- add
- delete
- has
- clear

> 注意：
>
> 遍历的顺序是按照插入顺序排列的，Set 本身没有键名，只有键值，所以 keys()和 values()返回的结果是一样的。

```js
const set = new Set([1, 2, 3, 4, 4, 4, 5, 5]);
console.log(set); // Set(5) {1, 2, 3, 4, 5}

set.add(6);
```

### Map

Map 是一种叫做`字典`的数据结构，它的特点是`键值对的集合`，且`键名和键值都可以是任意类型`。存储结构为[key: value], 为有序列表。

遍历方法

- keys()
- values()
- entries()
- forEach()

操作方法

- set
- get
- delete
- has
- clear
- size 属性

```js
const m = new Map();

m.set("edition", 6); // 键是字符串
m.set(1, "a").set(2, "b").set(3, "c"); // 链式操作
```

### WeakSet

WeakSet 是一种特殊的 Set，它的成员`只能是对象`，而且对象都是`弱引用`。

> 弱引用：即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。简单点说就是：WeakSet 里面的引用只要在外部消失，它在 WeakSet 里面的引用就会自动消失。

#### WeakSet 与 Set 区别

- WeakSet 的成员只能是对象，而不能是其他类型的值。
- WeakSet 没有遍历操作的 API。
- WeakSet 没有 size 属性。

#### WeakSet 的使用场景

- DOM 节点作为键名，比如 document.querySelectorAll()返回的是一个类数组对象，如果直接作为键名，可能会造成内存泄漏。
- 部署私有属性，比如在一个类中，有些属性应该是只读的，但是在 ES6 之前，只能通过约定来命名，比如 \_bar，但是这样的命名是不保险的，外部依然可以访问到。ES6 中可以使用 WeakSet 来存储这些私有属性。

  ```js
  const foos = new WeakSet();
  class Foo {
    constructor() {
      foos.add(this);
    }
    method() {
      if (!foos.has(this)) {
        throw new TypeError("Foo.prototype.method 只能在Foo的实例上调用！");
      }
    }
  }
  ```

  > 上面代码保证了 Foo 的实例方法，只能在 Foo 的实例上调用。这里使用 WeakSet 的好处是，foos 对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑 foos，也不会出现内存泄漏。

- 缓存，比如上面提到的 DOM 节点，如果不进行缓存，每次查询都会进行一次 DOM 查询操作，这是非常耗费性能的，可以将查询到的 DOM 节点进行缓存。

### WeakMap

WeakMap 是一种特殊的 Map，它的键名只能是对象，而且对象都是弱引用。

#### WeakMap 与 Map 区别

- WeakMap 只接受对象作为键名（null 除外），不接受其他类型的值作为键名。
- WeakMap 的键名所指向的对象，不计入垃圾回收机制。
- WeakMap 没有遍历操作（即没有 keys()、values()和 entries()方法），也没有 size 属性。
- WeakMap 没有 clear()方法。

> 注意：
>
> WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用

#### WeakMap 的使用场景

- 数据缓存，比如上面提到的 DOM 节点，如果不进行缓存，每次查询都会进行一次 DOM 查询操作，这是非常耗费性能的，可以将查询到的 DOM 节点进行缓存。

  ```js
  let myWeakmap = new WeakMap();

  myWeakmap.set(document.getElementById("logo"), { timesClicked: 0 });

  document.getElementById("logo").addEventListener(
    "click",
    function () {
      let logoData = myWeakmap.get(document.getElementById("logo"));
      logoData.timesClicked++;
    },
    false
  );
  ```

  > 上面代码中，document.getElementById('logo')是一个 DOM 节点，每当发生 click 事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是这个节点对象。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。

- 私有属性，比如上面提到的 WeakSet 的使用场景。
- 部署私有方法。
- 部署观察者模式，比如 DOM 事件的监听。

# 3. Promise

Promise 的异步操作替代方案 async/await

### Q1: Promise 的状态有哪些？

- pending：初始状态，既不是成功，也不是失败状态。
- fulfilled：意味着操作成功完成。
- rejected：意味着操作失败。

### Q2: Promise 的优缺点？

- 优点：

  - 支持链式调用，可以解决回调地狱的问题。
  - 解决了同步异步的问题。
  - Promise.all 解决了多个异步请求并发的问题。

- 缺点：
  - 无法取消 Promise，错误需要通过回调函数捕获。
  - 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。
  - 当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### Q3: all、race、allSettled、any 的区别？

- Promise.all(): 接收一个 Promise 实例的数组作为参数，当这个数组里的所有 Promise 对象全部变为 resolve 状态时，才会 resolve `一组值`。如果数组里的 Promise 对象有一个变为 reject 状态，那么 Promise.all()返回的 Promise 对象就会变为 reject 状态。

  ```js
  const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("p1");
    }, 1000);
  });

  const p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("p2");
    }, 2000);
  });

  const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("p3");
    }, 3000);
  });

  Promise.all([p1, p2, p3]).then((res) => {
    console.log(res); // ["p1", "p2", "p3"]
  });
  ```

- Promise.allSettled(): 接收一个 Promise 实例的数组作为参数, 并等待所有 Promise 执行完毕后返回每一个 Promise 执行结果，不管是 resolve 还是 reject。

> all 与 allSettled 的区别
>
> - all 与 allSettled 都是接收一个 Promise 实例的数组作为参数
> - all 与 allSettled 的正常返回值都是一个数组
> - all 的参数中所有 Promise 是依赖关系，只要有一个 Promise 被 reject，就会立即 reject
> - allSettled 的参数中所有 Promise 非依赖关系，会等所有的 Promise 都执行完毕后再返回每一个的执行结果。

```js
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("p1");
  }, 1000);
});

const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("p2");
  }, 2000);
});

const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("p3");
  }, 3000);
});

Promise.allSettled([p1, p2, p3]).then((res) => {
  console.log(res); // [{status: "fulfilled", value: "p1"}, {status: "rejected", reason: "p2"}, {status: "fulfilled", value: "p3"}]
});
```

- Promise.any(): 接收一个 Promise 实例的数组作为参数, 返回`第一个 Promise 的 resolve 值`，并结束后续操作。否则等所有的 Promise 都被 reject 时，返回 reject。
- Promise.race(): 接收一个 Promise 实例的数组作为参数,一旦其中的某个 Promise resolve 或 reject,就会结束后续的操作，并返回一个 resolve 或 reject 的 Promise。

> any 与 race 的区别
>
> - any、race 都返回一个 promise 对象，而不是数组
> - any 是一旦有一个 promise 对象变成 `fulfilled` 状态的话，就会终止其他 promise 对象的执行，并返回第一个 promise 对象的结果
> - race 是一旦有一个 promise 对象变成 `fulfilled 或者 rejected` 状态的话，就会终止其他 promise 对象的执行，并返回第一个 promise 对象的结果

# 4. Generator

### Q1: Generator 是什么？

Generator 是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

### Q2: Generator 的特点？

- function 关键字与函数名之间有一个星号。
- 函数体内部使用 yield 表达式，定义不同的内部状态。

### Q3: Generator 的使用场景？

- 异步操作同步化表达

  ```js
  function* gen() {
    const res1 = yield fetch("https://api.github.com/users/github");
    const json1 = yield res1.json();
    const res2 = yield fetch("https://api.github.com/users/github/followers");
    const json2 = yield res2.json();
    const res3 = yield fetch("https://api.github.com/users/github/repos");
    const json3 = yield res3.json();
    console.log([json1.bio, json2[0].login, json3[0].full_name].join("\n"));
  }

  function run(gen) {
    const g = gen();

    function next(data) {
      const res = g.next(data);
      if (res.done) return res.value;
      res.value.then((data) => {
        next(data);
      });
    }

    next();
  }

  run(gen);
  ```

  > yield 需要配合 next()使用，next()的参数会作为上一个 yield 的返回值。
  >
  > next()的返回值是一个对象，对象的 value 属性是 yield 后面表达式的值，done 属性是一个布尔值，表示是否遍历结束。`{value: xxx, done: true}`

- 控制流管理
- 为 object 部署 Iterator 接口(实现 Object.entries()方法)

  ```js
  function* iterEntries(obj) {
    let keys = Object.keys(obj);
    for (let i = 0; i < keys.length; i++) {
      let key = keys[i];
      yield [key, obj[key]];
    }
  }

  let myObj = { foo: 3, bar: 7 };

  for (let [key, value] of iterEntries(myObj)) {
    console.log(key, value);
  }
  ```

### Q4: Generator 与 async/await、Promise 的区别？

- promise 和 async/await 是专门用于处理异步操作的
- Generator 并不是为异步而设计出来的，它还有其他功能（对象迭代、控制输出、部署 Interator 接口...）
- promise 编写代码相比 Generator、async 更为复杂化，且可读性也稍差
- Generator、async 需要与 promise 对象搭配处理异步情况
- async 实质是 Generator 的语法糖，相当于会自动执行 Generator 函数
- async 使用上更为简洁，将异步代码以同步的形式进行编写，是处理异步编程的最终方案

# 5. Reflect

### Q1: Reflect 是什么？

Reflect 是 ES6 为了简化`对象操作`而提供的一个新的 API。其提供了 13 个静态方法, 用函数式替代了原来的命令式。

```js
// 老写法
"assign" in Object; // true
// 新写法
Reflect.has(Object, "assign"); // true

// 老写法
delete obj[name];
// 新写法
Reflect.deleteProperty(obj, name);

// 老写法
Function.prototype.apply.call(Math.floor, undefined, [1.75]); // 1
// 新写法
Reflect.apply(Math.floor, undefined, [1.75]); // 1
```

### Q2: 13 个静态方法

Reflect 的 13 个静态方法与 Proxy 的 13 个拦截方法一一对应。

- Reflect.apply(target, thisArg, args)
  > 等同于 Function.prototype.apply.call(target, thisArg, args)，用于绑定 this 对象后执行给定函数。
- Reflect.construct(target, args)

  > 等同于 new target(...args)，这提供了一种不使用 new，来调用构造函数的方法。

  ```js
  function Greeting(name) {
    this.name = name;
  }

  // new 的写法
  const instance1 = new Greeting("张三");

  // Reflect.construct 的写法
  const instance2 = Reflect.construct(Greeting, ["张三"]);
  ```

- Reflect.get(target, name, receiver)

  > 读取对象属性，如果读取失败，返回 undefined。
  >
  > 如果 name 属性部署了读取函数（getter），则读取函数的 this 绑定 receiver。

  ```js
  var myObject = {
    foo: 1,
    bar: 2,
    get baz() {
      return this.foo + this.bar;
    },
  };

  var myReceiverObject = {
    foo: 4,
    bar: 4,
  };

  Reflect.get(myObject, "baz", myReceiverObject); // 8
  ```

- Reflect.set(target, name, value, receiver)
  > 设置对象属性，如果设置失败，返回 false。
  >
  > 如果 Proxy 对象和 Reflect 对象联合使用，前者拦截赋值操作，后者完成赋值的默认行为，而且传入了 receiver，那么 Reflect.set 会触发 Proxy.defineProperty 拦截。
- Reflect.defineProperty(target, name, desc)
  > 等同于 Object.defineProperty(target, name, desc)，用来为对象定义属性。
- Reflect.deleteProperty(target, name)
  > 等同于 delete obj[name]，用于删除对象的属性。
- Reflect.has(target, name)
  > 等同于 name in obj，用于检查对象是否拥有某个属性。
- Reflect.ownKeys(target)
  > 等同于 Object.getOwnPropertyNames(obj).concat(Object.getOwnPropertySymbols(obj))，用于返回对象的所有属性。
- Reflect.isExtensible(target)
  > 等同于 Object.isExtensible(obj)，用于判断对象是否可扩展。
- Reflect.preventExtensions(target)
  > 等同于 Object.preventExtensions(obj)，用于让一个对象变为不可扩展。它返回一个布尔值，表示是否操作成功。
- Reflect.getOwnPropertyDescriptor(target, name)
  > 等同于 Object.getOwnPropertyDescriptor(obj, name)，用于得到指定属性的描述对象。
- Reflect.getPrototypeOf(target)
  > 等同于 Object.getPrototypeOf(obj)，用于读取对象的原型对象。
- Reflect.setPrototypeOf(target, prototype)
  > 等同于 Object.setPrototypeOf(obj, newProto)，用于设置对象的原型（即设置`[[Prototype]]`对象）。

# 6. Proxy

### Q1: Proxy 是什么？

ES6 提供的一个代理机制，用于`拦截并修改某些操作`的默认行为。

语法为：`new Proxy(target, handler)`

```js
const obj = new Proxy(
  {},
  {
    get: function (target, propKey) {
      return 35;
    },
  }
);

obj.time; // 35
obj.name; // 35
obj.title; // 35

// 上面obj对象的get方法被Proxy拦截了，并始终返回35。
```

如果 handler 没有设置任何拦截，那就等同于直接通向原对象。

### Q2: Proxy 的拦截操作

一共可以拦截 13 个操作，与 Reflect 的 13 个静态方法一一对应。‘

### Q3: Proxy 的使用场景？

Proxy 其功能非常类似于设计模式中的代理模式，常用功能如下：

- 验证数据的合法性

  ```js
  let numericDataStore = { count: 0, amount: 1234, total: 14 };
  numericDataStore = new Proxy(numericDataStore, {
    set(target, key, value, proxy) {
      if (typeof value !== "number") {
        throw Error("属性只能是number类型");
      }
      return Reflect.set(target, key, value, proxy);
    },
  });

  numericDataStore.count = "foo";
  // Error: 属性只能是number类型

  numericDataStore.count = 333;
  // 赋值成功
  ```

- 拦截和监视外部对对象的访问
- 降低函数或类的复杂度

# 7. ESM、CommonJS、AMD、CMD

[参考](https://juejin.cn/post/7176934558595481656)

### Q1: ESM、CommonJS、AMD、CMD 是什么？

ESM、CommonJS、AMD、CMD 都是模块化规范，用于解决模块化问题。

- ESM：import + export(ES6)
- CommonJS：require + module.exports + exports (Node.js)
- AMD：define + require(Require.js)
- CMD：define + require(Sea.js)

> Webpack 内部仿 CommonJS 实现了一套自己的模块化规范: `__webpack_require__`+ module.exports + exports

### Q2: ESM、CommonJS、AMD、CMD 的区别？

- ESM
  - 它是静态的，在编译时就能确定模块的依赖关系，
  - 是同步的，模块之间存在依赖关系，只有加载完成，才能执行后面的操作
- CommonJS
  - 主要用于`服务端`编程
  - 是动态的，只有在代码运行时才能确定模块的依赖关系
  - 是同步的，模块之间存在依赖关系，只有加载完成，才能执行后面的操作
- AMD
  - 用于`浏览器`的异步加载
  - AMD 的核心是`预加载`，先对依赖的全部文件进行加载，加载完了再进行处理
  - 适合在浏览器环境中异步加载模块。可以并行加载多个模块。 并可以按需加载。
- CMD
  - 用于`浏览器`的异步加载
  - 模块的加载是异步的，模块使用时才会加载执行
  - 一个模块就是一个文件，所以经常就用文件名作为模块 id，define 是一个全局函数，用来定义模块。
  - CMD 推崇`依赖就近`，所以一般不在 define 的参数中写依赖，在 factory 中写

> AMD 与 CMD 的区别
>
> - AMD 推崇依赖前置，提前执行，CMD 推崇依赖就近，延迟执行

### Q3: 说说 ESM 的特点

- 是`静态`的，在编译时就能确定模块的依赖关系（使静态分析称为可能，可以实现 Tree Shaking 和 Code Splitting）
- 是`同步`的，模块之间存在依赖关系，只有加载完成，才能执行后面的操作
- 每个 ES Module 都是运行在单独的`私有作用域`中，外部无法访问内部变量。
- 可以`导出多个属性和方法`，可以单个导入导出，混合导入导出
- 同一个模块如果`加载多次，只会执行一次`
- `自动采用严格模式`（所以模块顶层的 this 关键字返回 undefined）
