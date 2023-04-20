# this 的指向

参考：<https://juejin.cn/post/6981251280236707853>

核心一句话：

哪个对象调用函数，函数中的 this 指向哪个对象。

this 调用的 5 种模式：

1. 对象方法调用模式: this 总是指向调用它的对象(默认指向全局对象 window)，与函数声明位置无关。

   ```js
   // 声明位置
   var test = function () {
     console.log(this.x);
   };

   var x = "2";

   var obj = {
     x: "1",
     fn: test,
   };

   // 调用位置
   obj.fn(); // 1

   test(); // 2
   ```

2. 构造函数模式: this 总是指向新创建的对象。

   ```js
   var Person = function (name) {
     this.name = name;
   };

   var p1 = new Person("111");
   var p2 = new Person("222");

   console.log(p1.name); // 111
   console.log(p2.name); // 222
   ```

3. call、apply、bind 模式：this 指向第一个参数。

   ```js
   var obj = {
     name: "111",
     getName: function () {
       console.log(this.name);
     },
   };

   var otherObj = {
     name: "222",
   };

   var name = "333";

   obj.getName(); // 111
   obj.getName.call(); // 333
   obj.getName.call(otherObj); // 222
   obj.getName.apply(); // 333
   obj.getName.apply(otherObj); // 222
   obj.getName.bind(this)(); // 333
   obj.getName.bind(otherObj)(); // 222
   ```

4. 箭头函数: 它的外层没有函数，this 是 window；外层有函数，看外层函数的 this 是谁，它的 this 就是谁

```js
var name = "window";
var A = {
  name: "A",
  sayHello: () => {
    console.log(this.name);
  },
};

A.sayHello(); // 输出的是window,因为外层没有函数，this是window

// 那如何改造成永远绑定A呢：

var name = "window";
var A = {
  name: "A",
  sayHello: function () {
    var s = () => console.log(this.name);
    return s; //返回箭头函数s
  },
};

var sayHello = A.sayHello();
sayHello(); // 输出A
```

5. 严格模式: this 指向 undefined

   ```js
   "use strict";
   var name = "window";
   var A = {
     name: "A",
     sayHello: function () {
       console.log(this.name);
     },
   };

   A.sayHello(); // undefined
   ```
