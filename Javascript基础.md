# 迭代中对象的变化

在迭代的过程中，object、array 如果被修改了，在判断条件中，也是实时变化的

## 案例

```js
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
for (let i = 0; i < arr1.length; i++) {
  console.log(i, [...arr1]);
  if (i < arr2.length) {
    arr1.push(arr2[i]);
  }
}
// 0 (3) [1, 2, 3]
// 1 (4) [1, 2, 3, 4]
// 2 (5) [1, 2, 3, 4, 5]
// 3 (6) [1, 2, 3, 4, 5, 6]
// 4 (6) [1, 2, 3, 4, 5, 6]
// 5 (6) [1, 2, 3, 4, 5, 6]
```

# Array.map(parseInt)格式的使用

Array.map、Array.forEach、Array.filter 方法的参数为一个函数名时，实际是根据该函数可接收的参数数量，至多依次传入 3 个参数，分别为当前元素、当前元素的索引、当前数组。因为它们至多传入 3 个参数，而后则为 undefined。

```js
["1", "2", "3"].map(parseInt);
// [1, NaN, NaN]
```

## map、filter、forEach

这里的方法参数实际是 3 个， 第一个为当前 element,第二个为 index, 第三个为遍历的数组

```js
Array.filter(element[, index[, array]])
```

## 案例

```js
/* 案例1 */
const arr = [1, 2, 0, 3, 0];
const result = arr.filter(Boolean);
console.log(result);
// [1, 2, 3]

/* 案例2 */
const arr = ["a", "b", "c", "d", "e"];
const customFun = (arg1, arg2) => {
  return `${arg1}${arg2}`;
};
const result = arr.map(customFun);
console.log(result);
// ['a0', 'b1', 'c2', 'd3', 'e4']

/* 案例3 */
const arr = ["a", "b", "c"];
const customFun = (arg1, arg2, arg3, arg4) => {
  return `${arg1}-${arg2}-${arg3.join("_")}-${arg4}`;
};
const result = arr.map(customFun);
console.log(result);
// ['a-0-a_b_c-undefined', 'b-1-a_b_c-undefined', 'c-2-a_b_c-undefined']
```

# 执行上下文（context）

每一个执行上下文包含 3 个主要属性：变量对象、作用域链、this

## 生成执行上下文

3 种生成方法：Global code（全局上下文）、Function Code（function 上下文）、Eval code（eval 上下文）

## 调用规则

1. 每个执行上下文会生成一个属于自己的 this, 该 this 包含当前执行上下文中的所有对象
2. 上下文中的变量遵循`变量提升`规则，即从里往外层赋值，直到全局变量
3. 当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级执行上下文的变量对象中查找，一直找到全局上下文的变量对象（即作用域链）

## 案例

```js
var scope = "global scope";

function checkscope(s) {
  var scope = "local scope";

  function f() {
    return scope;
  }
  return f();
}
checkscope("scope");
// local scope
```

# 防抖函数 & 节流函数

## 原理

1. 利用执行上下文`变量提升`的特性，将 return function(){} 中 timer 的变更赋值给 debounce 的 timer 变量
2. 利用执行上下文`作用域链`的特性，在 return function(){} 中读取 timer 时，从最近的执行上下文中回去，即读取了 debounce 中的 timer 变量
3. 基于上面 2 个特性，实现了在多次调用 debounceFn 时，实际内部共享了一个 timer 变量

## 实现

### 防抖函数

#### 防抖一：每次触发事件时都取消之前的延时调用方法

```js
// 延时调用
const debounce = (fn, delay) => {
  let timer = null;
  return function () {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, delay);
  };
};

const sayHi = (text) => {
  console.log("hi:", text);
};

const debounceHi = debounce(sayHi, 1000);

setTimeout(() => debounceHi("500s"), 500);
setTimeout(() => debounceHi("800s"), 800);
setTimeout(() => debounceHi("1200s"), 1200);
// hi:1200s
```

如上，debounceFn 来源于共同的父级上下文（debounce 函数），所以 timer 变量是共享的，当第一次调用 debounceFn 后，timer 为存在值，所以当第二次调用 debounceFn 时，会执行 clearTimeout。

注意：在调用 debounce 时，需要单独声明一个函数（如 debounceFn）用 debounce 来包裹，这样可以基于 debounceFn 来共享 timer。

```js
const debounceHi = debounce(sayHi, 1000);
```

#### 防抖二: 立即调用，但再接下来的 delay 时间内不会被调用

```js
// 立即调用，但在接下来的 delay 时间内不会被调用
const debounce = (fn, delay) => {
  let running = false;
  return function () {
    if (!running) {
      running = true;
      fn.apply(this, arguments);
      setTimeout(() => {
        running = false;
      }, delay);
    }
  };
};

const sayHi = (text) => {
  console.log(text);
};

const debounceFn = debounce(sayHi, 1000);

setTimeout(() => debounceFn("500s"), 500);
setTimeout(() => debounceFn("800s"), 800);
setTimeout(() => debounceFn("1700s"), 1700);
// 500s 1700s
```

### 节流函数

每隔一段时间执行一次

```js
const throttle = (fn, delay) => {
  let timer = null;
  return function () {
    if (timer) {
      return;
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
      timer = null;
    }, delay);
  };
};
```

## Q&A

Q1: 为什么 fn.apply(this, arguments);而不可以是 fn(...arguments)

A1: 加上 apply 确保 在 sayHi 函数里的 this 指向的是原来的调用对象（如 input 对象），否则会指向 window 对象，这不是我们想要的。

Q2: 当多个函数利用 debounce 函数时，timer 变量是共享的，那么当多个函数同时调用时，会不会出现问题？如下：

```js
const sayHi = (text) => {
  console.log("hi:", text);
};

const sayHello = (text) => {
  console.log("hello:", text);
};

const debounceHi = debounce(sayHi, 1000);
const debounceHello = debounce(sayHello, 1000);

setTimeout(() => {
  debounceHi("500s");
  debounceHello("500s");
}, 500);
setTimeout(() => {
  debounceHi("800s");
  debounceHello("800s");
}, 800);
setTimeout(() => {
  debounceHi("1200s");
  debounceHello("1200s");
}, 1200);
// hi:1200s
// hello:1200s
```

A2：不会，因为每个函数都有自己的执行上下文，所以 timer 变量基于 debounceHi、debounceHello 各自独立。

```js
const debounce = (fn, delay) => {
  let timer = null;
  return function () {
    console.log("timer:", timer, arguments[0]);
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, delay);
  };
};

const sayHi = (text) => {
  console.log("hi:", text);
};

const sayHello = (text) => {
  console.log("hello:", text);
};

setTimeout(() => {
  debounce(sayHi, 1000)("500s");
  debounce(sayHello, 1000)("500s");
}, 500);
setTimeout(() => {
  debounce(sayHi, 1000)("800s");
  debounce(sayHello, 1000)("800s");
}, 800);
setTimeout(() => {
  debounce(sayHi, 1000)("1200s");
  debounce(sayHello, 1000)("1200s");
}, 1200);
// timer: null 500s
// timer: null 500s
// timer: null 800s
// timer: null 800s
// timer: null 1200s
// timer: null 1200s
// hi: 500s
// hello: 500s
// hi: 800s
// hello: 800s
// hi: 1200s
// hello: 1200s
```

如上案例，因为 setTimeout 中直接调用 debounce(sayHi("1200s"), 1000)，所以每个 debounce 函数内的 timer 变量基于 debounce 各自独立，没有达到共享 timer 的效果。
