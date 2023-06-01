# React 渲染流程

## React 理念

CPU 的瓶颈
渲染卡帧问题
IO 的瓶颈

JS 脚本执行 ----- 样式布局 ----- 样式绘制

时间切片
这种将长任务分拆到每一帧中，像蚂蚁搬家一样一次执行一小段任务的操作，被称为时间切片（time slice）

解决 CPU 瓶颈的关键是实现时间切片，而时间切片的关键是：将同步的更新变为可中断的异步更新。

在浏览器每一帧的时间中，预留一些时间给 JS 线程，React 利用这部分时间更新组件中，预留的初始时间是 5ms）

## React16 架构可以分为三层

Scheduler（调度器）—— 调度任务的优先级，高优任务优先进入 Reconciler
Reconciler（协调器）—— 负责找出变化的组件
Renderer（渲染器）—— 负责将变化的组件渲染到页面上（ReactDom、ReactNative、ReactTest、ReactArt ）

### Scheduler（调度器）

既然我们以浏览器是否有剩余时间作为任务中断的标准，那么我们需要一种机制，当浏览器有剩余时间时通知我们。

部分浏览器已经实现了这个 API，这就是 requestIdleCallback

React 实现了功能更完备的 requestIdleCallbackpolyfill，这就是 Scheduler。除了在空闲时触发回调的功能外，Scheduler 还提供了多种调度优先级供任务设置。

### Reconciler（协调器）

React15 中递归处理虚拟 Dom， React16 中可中断循环处理处理虚拟 DOM

每次循环都会调用 shouldYield 判断当前是否有剩余时间。

工作逻辑

Scheduler 将任务交给 Reconciler 后，Reconciler 会为变化的虚拟 DOM 打上代表增/删/更新的标记，类似这样：

```
export const Placement = /*             */ 0b0000000000010;
export const Update = /*                */ 0b0000000000100;
export const PlacementAndUpdate = /*    */ 0b0000000000110;
export const Deletion = /*              */ 0b0000000001000;
```

只有当所有组件都完成 Reconciler 的工作，才会统一交给 Renderer。

Reconciler 内部采用了 Fiber 的架构。

React Fiber 可以理解为：

React 内部实现的一套状态更新机制。支持任务不同优先级，可中断与恢复，并且恢复后可以复用之前的中间状态。

其中每个任务更新单元为 React Element 对应的 Fiber 节点。

### Renderer（渲染器）

Renderer 根据 Reconciler 为虚拟 DOM 打的标记，同步执行对应的 DOM 操作。

# Fiber

Fiber 包含三层含义：

- 作为架构来说，之前 React15 的 Reconciler 采用递归的方式执行，数据保存在递归调用栈中，所以被称为 stack Reconciler。React16 的 Reconciler 基于 Fiber 节点实现，被称为 Fiber Reconciler。

- 作为静态的数据结构来说，每个 Fiber 节点对应一个 React element，保存了该组件的类型（函数组件/类组件/原生组件...）、对应的 DOM 节点等信息。

- 作为动态的工作单元来说，每个 Fiber 节点保存了本次更新中该组件改变的状态、要执行的工作（需要被删除/被插入页面中/被更新...）。

### Fiber 节点如何生产 DOM 树

Fiber 节点生成 Fiber 树，Fiber 树对应 DOM 树， 最后通过双缓存机制来更新 DOM
