# React.setState

<https://github.com/sisterAn/blog/issues/26>

## 总结

在 React 中， 如果是由 React 引发的事件处理（包括生命周期函数、组件绑定的事件），调用 setState 不会同步更新 this.state，除此之外（如 React 之外的 addEventListener，setTimeout/setInterval、Promise 产生的异步调用）的 setState 调用会同步执行 this.state 。

## 案例

```js
class Example extends React.Component {
  constructor() {
    super();
    this.state = {
      val: 0,
    };
  }

  componentDidMount() {
    this.setState({ val: this.state.val + 1 });
    console.log(this.state.val); // 第 1 次 log

    this.setState({ val: this.state.val + 1 });
    console.log(this.state.val); // 第 2 次 log

    setTimeout(() => {
      this.setState({ val: this.state.val + 1 });
      console.log(this.state.val); // 第 3 次 log

      this.setState({ val: this.state.val + 1 });
      console.log(this.state.val); // 第 4 次 log
    }, 0);
  }

  render() {
    return null;
  }
}

// 0 0 2 3
```
