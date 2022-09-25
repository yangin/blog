# Javascript 的 Event Loop

## EventLoop 理解

可以理解为浏览器内部为每个站点配置了一个基于 `setInterval` 的 EventLoop。每隔一段时间就会去检查一下事件队列，如果有任务就执行，如果没有就继续等待下一次的检查。

EventLoop 的执行过程如下：

```js
type taskFn = () => void;
type taskQueue = taskFn[];

class EventLoop {
  constructor() {
    this.taskQueue: taskQueue = [];  // 任务队列
    this.microTaskQueue: taskQueue = [];  // 微任务队列
    this.scheduleQueue: taskQueue[] = [];  // 执行的任务队列
    this.running = false;  // 是否正在执行

    // 每隔50ms检查一次任务队列，如果有任务，就将其放入执行队列，并清空任务队列
    this.interval = setInterval(() => {
      const currentTaskQueue = [...this.taskQueue];
      this.taskQueue = [];
      scheduleQueue.push(currentTaskQueue);
      this.run();
    }, 50);
  }

  addTask(fn) {
    this.taskQueue.push(fn);
  }

  addMiscTask(fn) {
    this.microTaskQueue.push(fn);
  }

  run() {
    if (this.running) return;
    // 依次执行本轮事件队列中的所有任务，直到完成
    while (this.scheduleQueue.length) {
      this.running = true;
      // 取出本次迭代中的所有任务， 并依次执行完
      const currentTaskQueue = this.scheduleQueue.shift();
      while (currentTaskQueue.length) {
        const task = currentTaskQueue.shift();
        task();
      }
      // 当执行完本轮任务队列中的所有任务后，再执行微任务队列中的所有任务，直到微任务队列中的所有任务都执行完，再执行下一轮任务队列
      while (this.microTaskQueue.length) {
        const task = this.microTaskQueue.shift();
        task();
      }
    }
    this.running = false;
  }
}
```

说明：

1. EventLoop 允许添加 2 种任务类型，任务（task）与 微任务（microTask）

2. 在每一次迭代中（即每隔一段时间触发的一次检查中），会`将当前 taskQueue 中的所有任务都取出来执行`，taskQueue 同时重置为空，供后续 EventCallback 的继续添加，然后在下一轮的迭代中取出执行。

3. 每一轮迭代的最后，即执行完 taskQueue 中的所有任务后，会执行一次`microTaskQueue`中的所有任务，直到`microTaskQueue`中的所有任务都执行完，再执行下一轮任务队列。

4. 每一个线程（包括主线程、worker 线程），管理一个 Event Loop，同源的站点（这里同源指基于某个站点 window.open 了一个新的窗口）共享一个 Event Loop。

5. 在主线程中，Event Loop 除了执行 Javascript 代码外，还负责执行渲染、绘制等操作

## Event Loop 模型

一个事件运行时包括堆、栈、消息队列（即任务队列）
![](https://raw.githubusercontent.com/yangin/code-assets/fd96ea09b8d8045543cbf49e80b0e3dd27f03bb0/blog/event-loop/1-1.svg)

## 任务 与 微任务

任务的添加方式：

1. `<script>`标签中的运行代码
2. 事件触发的回调函数，例如 DOM Events、I/O、requestAnimationFrame
3. setTimeout、setInterval 的回调函数
4. fetch，ajax 等异步请求的回调函数

微任务的添加方式：

1. Promises：Promise.then、Promise.catch、Promise.finally
2. MutationObserver API
3. queueMicrotask API， 属于 windows 对象，可以直接调用
4. process.nextTick：Node 独有

注意：

1. promise 里的内容是同步执行的，只是将回调函数放入了微任务队列中
2. async/await 也是同步执行的，只是将 await 后面的内容放入了微任务队列中
3. 执行上下文为当前 task 队列中的任务内容, 优先于微任务执行

## 面试题

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");

function getUrl() {
  console.log("fetch action");
  return "https://api.github.com/users/github";
}
fetch(getUrl()).then((res) => {
  console.log("fetch res");
});
setTimeout(function () {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2");
});
console.log("script end");
```

结果为

```js
/*
script start
fetch action
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
fetch res
*/
```
