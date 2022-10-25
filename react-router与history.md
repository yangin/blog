# ReactRouter 与 History

`react-router`: version 6.x

`history`: version 5.x

[react-router](https://github.com/remix-run/react-router) 的唯一依赖库为 [history](https://github.com/remix-run/history)

## history

### 使用场景

| 类型            | 应用场景                                      | 创建 API             | 实现原理                                                |
| --------------- | --------------------------------------------- | -------------------- | ------------------------------------------------------- |
| Browser history | 正常的现代浏览器应用，每次请求发往服务端      | createBrowserHistory | 基于 `window.history` API ,监听 `popstate`              |
| Hash history    | 现代浏览器应用，请求不发往服务端，如 Electron | createHashHistory    | 基于 `window.location.hash` , 监听`window.onhashchange` |
| Memory history  | 原生应用，如 React Native， test              | createMemoryHistory  | 基于 `Array`                                            |

### Action 执行原理

Action 包括 pop、push、replace

`createBrowserHistory`与``createHashHistory`的实现原理都是基于`window.history`API, 通过`pushState`、`replaceState`会触发`popstate`事件，然后在内部监听该事件进行处理。

`listen` 与 `block` 方法在 `push`、`replace`的生命周期中执行

history 的堆栈数据存储在浏览器的文件里

onPopState 事件监听不到 `history` 的 `pushState` 和 `replaceState`方法

### history.listen(listener: Listener)

用来监听路由变化，并执行 callback, 返回一个取消监听的函数

```js
// 这里的 location 为跳转后的Location对象，包含 pathname, search, hash, state, key
let unlisten = history.listen(({ action, location }) => {
  // The current location changed.
});

// Later, when you are done listening for changes...
unlisten();
```

### history.block(blocker: Blocker)

用来拦截路由的跳转，当执行完内容后通过 `retry` 参数方法来继续执行跳转

```javascript
// location为要跳转后的Location对象
let unblock = history.block(({ action, location, retry }) => {
  // A transition was blocked!
  if (window.confirm(`Are you sure you want to go to ${location.pathname}?`)) {
    // 当满足条件后，调用 retry 方法，可以继续跳转
    retry();
  }
});

// Later, when you want to start allowing transitions again...
unblock();
```
