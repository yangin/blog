# React 源码阅读笔记

_记录了阅读 react 源码时的一些总结，帮助掌握理解_

## 目录

1. [关键词](#关键词)
1. [全局变量](#全局变量)
1. [目录结构](#目录结构)
1. [架构流程](#架构流程)

## 关键词

- [1.1]() Fiber

  一个通过自定义数据结构来模拟一个 DOM Tree 结构的对象。

  它通过 child、sibling、return 属性来存放该节点的子集、兄弟节点、父级。通过 memoizedProps，memoizedState 存储着这个节点的 props 与 state 等。

  其具体结构如下（相比于源码中的 Type 有补充）：

```javascript
type Fiber = {
  // 标记不同的组件类型，如FunctionComponent 0、ClassComponent 1、HostComponent 5 等
  // 用来在beginWork等环节进行区分处理
  tag: WorkTag,

  // ReactElement里面的key
  // 在diff环节来区分是新建还是复用，以节省性能开销
  key: null | string,

  // TODO:
  elementType: any,

  // TODO:
  type: any,

  // 当前节点对应的DOM节点，为HTMLElement
  stateNode: any,

  // 当前节点的父节点, 为Fiber结构
  // 在completeWork阶段，当处理完当前节点后，向上查找
  return: Fiber | null,

  // 指向自己的第一个子节点 ???
  // 当前节点所有子节点的集合？还是第一个子节点 ？？？
  // TODO:
  child: Fiber | null,

  // 当前节点的兄弟节点，指向右侧的兄弟
  // TODO: 右侧兄弟？
  sibling: Fiber | null,

  // 表示的是当前节点在父节点中的位置，属于第几个子节点。当在做多节点的Diff算法时会用来进行比较
  index: number,

  // ref属性
  // 在completeWork阶段使用 ？？？
  ref:
    | null
    | (((handle: mixed) => void) & { _stringRef: ?string, ... })
    | RefObject,

  // ================== 更新相关 ===================
  // 作为动态的工作单元的属性, 保存了本次更新相关的信息

  // 新的变动带来的新的props
  pendingProps: any,
  // 上一次渲染完成之后的props, 即preProps
  memoizedProps: any,
  // 该Fiber对应的Update会存放在这里
  // 当执行setState时，就会产生一个Update
  updateQueue: mixed,
  // 上一次渲染时的state,即preState
  memoizedState: any,
  // TODO:
  dependencies: Dependencies | null,

  // 表示通过哪种方式来渲染，同步or异步
  // 值为NoMode(传统模式)、ConcurrentMode（异步更新）、ProfileMode
  // TODO: 发生在什么时间？
  mode: TypeOfMode,

  // 标识当前Fiber在commit阶段要执行的操作类型，是Update, Placement, Deletion等
  flags: Flags,
  // TODO:
  subtreeFlags: Flags,
  // 待删除的Fiber数组，在？？？中存入，在？？？中执行
  deletions: Array<Fiber> | null,

  // ================ useEffect Hook ==============
  // 下一个useEffect，为单链表结构，帮助快速查找
  // TODO: 整体需要理解一下
  // TODO: 理解
  nextEffect: Fiber | null,
  // 子树中的第一个 useEffect
  firstEffect: Fiber | null,
  // 子树中的最后一个 useEffect
  lastEffect: Fiber | null,

  // =============== Lanes ===================
  // TODO: 通道？？？
  lanes: Lanes,
  childLanes: Lanes,

  // 在Fiber树更新的过程中，每一个Fiber都会有一个跟其对应的Fiber
  // 我们称它`current <=====> workInProgress`
  // current中的fiber即为alternate， 而当前的fiber即为workInProgress中的fiber
  // 在渲染完成之后，他们的位置交换,在commitRoot阶段切换整个Fiber树
  alternate: Fiber | null,
};
```

-[1.2]() WorkInProgress

当前进程中正在处理的 Fiber， 与 current 相对应。current 表示当前页面展示的内容所对应的 Fiber, 对于当前变更来说，即上一次的最终渲染的 Fiber 树。 workInProgress 为本地变更中的 Fiber 树，当执行完变更逻辑后，将其赋值给 current, 渲染到页面上去。是双缓存机制中的一环。

-[1.3]() Effects

useEffect ??

-[1.4]() Lane

通道？？？

-[1.5]() Priority

优先级

-[1.6]() Update

像 Fiber 一样，update queue 也总是成对出现，一个是 current queue，表示屏幕可见的状态，一个是 work-in-progress queue，可以在提交前修改并处理异步的工作，这是一种双缓冲的方式。

两个 queue 共享一个持久的单链表结构。要调度一个 update，我们将它添加到两个 queue 的末尾。每一个 queue 都会维护一个指向没有被处理的持久列表中的第一个 update 的指针。work-in-progress queue 指针总是大于或等于当前 queue，因为我们总是在工作中。current queue 的指针只在提交阶段更新，在我们交换 work-in-progress 时

update 不是按照 priority 排序的，而是按照插入顺序。新的 update 总是被添加到列表的末尾。priority 任然是重要的，当在 render 阶段处理 update queue 时，只有具有足够优先级（priority）的 update 才会被包含在结果中。如果我们因为没有足够的优先级 priority 而跳过一个 update，它将保留在队列中，以便在下一个低优先级 render 中处理。

通过 `createUpdate` 来初始化一个无内容的 Update

```javascript
createUpdate(eventTime: number, lane: Lane): Update<*>
```

```javascript
type Update = {
  //
  eventTime: number,

  lane: Lane,

  // 指定更新的类型，值为UpdateState 0, ReplaceState 1, ForceUpdate 2, CaptureUpdate 3
  tag: 0 | 1 | 2 | 3,
  // 更新内容，setState 接收的第一个参数
  payload: any,
  // 更新后执行的回调，setState 接收的第二个参数，会传入 preProps,preState
  callback: (() => mixed) | null,
  // 指向下一个更新
  next: Update<State> | null,
  // TODO: ??
  nextEffect: Update<State> | null,
};
```

-[1.6]() UpdateQueue

用来存放 Update 的队列，通过调用 enqueueUpdate 来往 UpdateQueue 中放入 Update, 通过调用 processUpdateQueue 来处理 UpdateQueue 中的 Update。

```javascript
enqueueUpdate(fiber: Fiber, update: Update<State>, lane: Lane)

processUpdateQueue(workInProgress: Fiber, props: any, instance: any, renderLanes: Lanes)
```

```javascript
type UpdateQueue = {
  // 每次操作完更新之后的`state` ?? 不是之前 ？？
  baseState: State,
  // 队列中的第一个`Update`
  firstBaseUpdate: Update<State> | null,
  //
  lastBaseUpdate: Update<State> | null,
  //
  shared: SharedQueue<State>,
  //
  effects: Array<Update<State>> | null,
};
```

-[1.7]() Context

**[⬆ 回到顶部](#目录)**

## 全局变量

- [1.1]() isWorking

  commitRoot 和 renderRoot 开始都会设置为 true，然后在他们各自阶段结束的时候都重置为 false

  **用来标志是否当前有更新正在进行，不区分阶段**

- [1.2]() nextUnitOfWork

  用于记录 render 阶段 Fiber 树遍历过程中下一个需要执行的节点。为一个 Fiber

  在 resetStack 中分别被重置

  他只会指向 workInProgress

- [1.3]() nextEffect

  用于 commit 阶段记录 firstEffect -> lastEffect 链遍历过程中的每一个 Fiber

- [1.4]() isRendering

  performWorkOnRoot 开始设置为 true，结束的时候设置为 false，表示进入渲染阶段，这是包含 render 和 commit 阶段的。

- [1.5]() nextFlushedRoot & nextFlushedExpirationTime

  用来标志下一个需要渲染的 root 和对应的 expirtaionTime，注意：

  - 通过 findHighestPriorityRoot 找到最高优先级的
  - 通过 flushRoot 会直接设置指定的，不进行筛选

**[⬆ 回到顶部](#目录)**

## 目录结构

- [3.1]() [整体目录](https://react.iamkasong.com/preparation/file.html#%E9%A1%B6%E5%B1%82%E7%9B%AE%E5%BD%95)

- [3.2]() react-reconciler 文件夹

用来管理 react 的处理逻辑，包括 diff 算法、update 操作等

```javascript
├── ReactChildFiber.js              //
├── ReactCurrentFiber.js            //
├── ReactEventPriorities.js
├── ReactFiber.js                   //
├── ReactFiberBeginWork.js          // beginWork相关的方法，包括reconcileChildren
├── ReactCurrentFiber.js
├── ReactFiberCacheComponent.js
├── ReactFiberClassComponent.js
├── ReactFiberCommitWork.js
├── ReactFiberCompleteWork.js
├── ReactFiberComponentStack.js
├── ReactFiberContext.js
├── ReactFiberErrorDialog.js
├── ReactFiberErrorLogger.js
├── ReactFiberFlags.js
├── ReactFiberHooks.js
├── ReactFiberHostConfig.js
├── ReactFiberHostContext.old.js
├── ReactFiberHotReloading.js
├── ReactFiberHydrationContext.js
├── ReactFiberInterleavedUpdates.js
├── ReactFiberLane.js
├── ReactFiberLazyComponent.js
├── ReactFiberNewContext.js
├── ReactFiberOffscreenComponent.js
├── ReactFiberReconciler.js
├── ReactFiberRoot.js
├── ReactFiberScope.js
├── ReactFiberStack.js
├── ReactFiberSuspenseComponent.js
├── ReactFiberSuspenseContext.js
├── ReactFiberSyncTaskQueue.js
├── ReactFiberThrow.js
├── ReactFiberTransition.js
├── ReactFiberTreeReflection.js
├── ReactFiberUnwindWork.js
├── ReactFiberWorkLoop.js
├── ReactHookEffectTags.js
├── ReactInternalTypes.js
├── ReactMutableSource.js
├── ReactPortal.js
├── ReactProfilerTimer.js
├── ReactReconcilerConstants.js
├── ReactRootTags.js
├── ReactStrictModeWarnings.js
├── ReactTestSelectors.js
├── ReactTypeOfMode.js
├── ReactUpdateQueue.js
├── ReactWorkTags.js
├── Scheduler.js
└── SchedulingProfiler.js
```

render 阶段相关文件夹

```javascript

```

commit 阶段相关文件夹

```javascript

```

**[⬆ 回到顶部](#目录)**

## 项目架构

- [4.1]() 架构

  React16 架构可以分为三层：

  - Scheduler（调度器）—— 调度任务的优先级，高优任务优先进入 Reconciler

  - Reconciler（协调器）—— 负责找出变化的组件

  - Renderer（渲染器）—— 负责将变化的组件渲染到页面上

- [4.2]() Scheduler（调度器）

  既然我们以浏览器是否有剩余时间作为任务中断的标准，那么我们需要一种机制，当浏览器有剩余时间时通知我们。Scheduler 是 React 实现的替代 浏览器原生 API requestIdleCallback 的一个调度器。除了在空闲时触发回调的功能外，Scheduler 还提供了多种调度优先级供任务设置。

  Scheduler (opens new window)是独立于 React 的库

- [4.3]() **Reconciler（协调器**）

  可中断循环递归处理虚拟 DOM 的（即 Fiber），每次循环都会调用 shouldYield 判断当前是否有剩余时间。

  其通过接收 props、state 的更改需求，通过 diff 算法，计算出需要变更的 DOM 节点，为其对应的 Fiber 打上相应的 flags(如 Update、Placement, Deletion 等)

- [4.4]() Renderer（渲染器）

  Renderer 根据 Reconciler 为虚拟 DOM 打的标记，同步执行对应的 DOM 操作。Renderer 中会根据应用的不同调用 react-dom、react-native、react-art 中封装方法，将 instance 渲染到页面上。其方法如 updateProperties、appendChild 等

- [4.5]() 流程

  整个项目流程的入口是 ReactDOM.render 方法，然后进入 reconciler 流程。reconciler 分为 render 阶段 与 commit 阶段。

- [4.6]() before mutation 阶段（执行 DOM 操作前）

  1. 处理 DOM 节点渲染/删除后的 autoFocus、blur 逻辑。

  1. `调用getSnapshotBeforeUpdate、componentWillUnmount生命周期钩子`。

  1. `调度useEffect`。 // TODO:

- [4.7]() mutation 阶段（执行 DOM 操作）

  mutation 阶段会遍历 effectList，依次执行 commitMutationEffects。该方法的主要工作为“根据 flags 调用不同的处理函数处理 Fiber, 包括 Placement | Update | Deletion。

  在此阶段会调用 react-dom 的 appendChild 等方法，将 Fiber 的内容渲染到 DOM 上。

- [4.7]() layout 阶段（执行 DOM 操作后）

  该阶段的代码都是在 DOM 渲染完成（mutation 阶段完成）后执行的。

  `在该阶段调用了声明周期钩子，如componentDidMount、 componentUpdateMount`

- [4.8]() render 阶段

  render 阶段开始于 performSyncWorkOnRoot 或 performConcurrentWorkOnRoot 方法的调用。这取决于本次更新是同步更新还是异步更新。

  render 阶段主要工作是 基于 DOM 节点（HTMlElement 类型）创建了对应的 Fiber（即虚拟 DOM）， 然后通过操作 Fiber 来实现 props、state 的更新等操作。

  render 阶段主要实现的是 通过 深度优先遍历 创建出一颗 Fiber 树，并在该 Fiber 树的每个节点打上 操作的标记， 如执行 Update、Deletion 等操作。并将要执行的 变更以 Update 的数据结构存入 UpdateQueue 中。

  深度优先遍历走的是一个 beginWork 阶段 与 completeWork 阶段

- [4.9]() commit 阶段

  commit 阶段，识别 Fiber 上的标记，根据对应的 DOM 方法执行对应节点的更新操作，将其渲染到页面（真实 DOM）上。该阶段又可拆分为 3 个小阶段，分别是

  - before mutation 阶段（执行 DOM 操作前）

  - mutation 阶段（执行 DOM 操作）

  - layout 阶段（执行 DOM 操作后）

  在该阶段执行 react 在 Component 中暴露的钩子，如 componentDidMount、componentUpdateMount、componentWillUnMount 等。

  涉及到比如 useEffect 的触发、优先级相关的重置、ref 的绑定/解绑。

- [4.10]() beginWork 阶段

  1. next 为 beginWork 传入的当前节点 fiber 后创建的新的子节点 fiber，该子节点会被赋值给 workInProgress.child
  1. 创建子节点的过程分为 直接复用子节点与创建新的子节点。
  1. 创建新的子节点分为 mount 阶段与 update 阶段创建，通过 reconcileChildren 函数来创建的。
  1. 其中 update 阶段执行 reconcileChildren，会在其中进行 diff 算法来进行子节点的创意，以适当复用，减小性能开销
  1. beginWork 创建的子节点，fiber.effectsTag 会被赋值为 Placement、Update、PlacementAndUpdate、Deletion，表示在最后的 commitRender 阶段义哪种方式将 fiber.stateNode(DOM 节点格式)插入 DOM 树中
  1. update 阶段会给 fiber.effectsTag 赋值，mount 阶段则不会，而是统一在 rootFiber 上赋值 Placement effectTag

- [4.11]() completeWork 阶段

  1. completeWork 是一个递归函数，会把当前 Fiber 的子 Fiber 全部执行完毕，然后再执行当前 Fiber
  2. 通过 prepareUpdate 来更新 DOM 节点上的属性，如 onClick、onChange 等回调函数的注册、处理 style prop、处理 DANGEROUSLY_SET_INNER_HTML prop， 处理 children prop
  3. 被处理完的 props 会被赋值给 workInProgress.updateQueue，并最终会在 commit 阶段被渲染在页面上
  4. 每个执行完 completeWork 且存在 effectTag 的 Fiber 节点会被保存在一条被称为 effectList 的单向链表中 ???

- [4.12]() diff 算法
  从 reconcileChildFibers 处开始

  - 单节点 Diff

    React 通过先判断 key 是否相同，如果 key 相同则判断 type 是否相同，只有都相同时一个 DOM 节点才能复用。

    注意点：

    当 child !== null 且 key 相同且 type 不同时执行 deleteRemainingChildren 将 child 及其兄弟 fiber 都标记删除。

    当 child !== null 且 key 不同时仅将 child 标记删除。

  为什么只需要比较 key 与 type 2 个属性呢？

  **因为 props 及 children 的改变（children 实际也为 props）,会被作为参数传入到 createFiber 中去。即不需要生成一个新的对象，只需要在原来的旧的对象上进行重新赋值即可，节省的是创建 object 的性能开销。**

  - 多节点 Diff

**[⬆ 回到顶部](#目录)**

## Update 的触发流程

触发 update 的方法主要有

- ReactDOM.render
- this.setState
- this.forceUpdate
- useState
- useReducer

触发步骤

ReactDOM.render

updateContainer 中 存在

```javascript
export function updateContainer(
  element: ReactNodeList,
  container: OpaqueRoot,
  parentComponent: ?React$Component<any, any>,
  callback: ?Function
): Lane {
  // ...省略与逻辑不相关代码

  // 创建update
  const update = createUpdate(eventTime, lane, suspenseConfig);

  // update.payload为需要挂载在根节点的组件
  update.payload = { element };

  // callback为ReactDOM.render的第三个参数 —— 回调函数
  callback = callback === undefined ? null : callback;
  if (callback !== null) {
    update.callback = callback;
  }

  // 将生成的update加入updateQueue
  enqueueUpdate(current, update);
  // 调度更新
  scheduleUpdateOnFiber(current, lane, eventTime);

  // ...省略与逻辑不相关代码
}
```

this.setState 与 this.forceUpdate

```javascript
Component.prototype.setState = function (partialState, callback) {
  // ...

  // 通过enqueueSetState来触发update流程
  this.updater.enqueueSetState(this, partialState, callback, "setState");
};

Component.prototype.forceUpdate = function (callback) {
  // 将callback放入updater队列， 标记为forceUpdate
  // 通过enqueueForceUpdate来触发update流程
  this.updater.enqueueForceUpdate(this, callback, "forceUpdate");
};
```

```javascript
const classComponentUpdater = {
  isMounted,
  enqueueSetState(inst, payload, callback) {
    // ...

    // 创建一个update
    const update = createUpdate(eventTime, lane);
    update.payload = payload;
    if (callback !== undefined && callback !== null) {
      update.callback = callback;
    }

    // 将update放入updateQueue中
    enqueueUpdate(fiber, update, lane);
    // schedule updateQueue, 执行updateQueue内容
    const root = scheduleUpdateOnFiber(fiber, lane, eventTime);
    if (root !== null) {
      entangleTransitions(root, fiber, lane);
    }

    // ...
  },
  enqueueReplaceState(inst, payload, callback) {
    // ...
  },
  enqueueForceUpdate(inst, callback) {
    // ...

    const update = createUpdate(eventTime, lane);
    update.tag = ForceUpdate;

    if (callback !== undefined && callback !== null) {
      update.callback = callback;
    }

    enqueueUpdate(fiber, update, lane);
    const root = scheduleUpdateOnFiber(fiber, lane, eventTime);

    // ...
  },
};
```

scheduleUpdateOnFiber 触发 渲染流程（即 render 与 commit）

```javascript
export function scheduleUpdateOnFiber(
  fiber: Fiber,
  lane: Lane,
  eventTime: number
): FiberRoot | null {
  // ...

  ensureRootIsScheduled(root, eventTime);

  // ...
}
```

render 阶段开始于 performSyncWorkOnRoot 或 performConcurrentWorkOnRoot 方法的调用。这取决于本次更新是同步更新还是异步更新。

commit 阶段开始于 commitRoot 方法的调用。其中 rootFiber 会作为传参。

从 fiber 到 root

我们知道，render 阶段是从 rootFiber 开始向下遍历。那么如何从触发状态更新的 fiber 得到 rootFiber 呢？

答案是：调用 markUpdateLaneFromFiberToRoot 方法。

在 scheduleUpdateOnFiber 中调用 ensureRootIsScheduled

在 ensureRootIsScheduled 中触发 performSyncWorkOnRoot 与 performConcurrentWorkOnRoot

```javascript
unction ensureRootIsScheduled(root: FiberRoot, currentTime: number) {
  // ...

  let newCallbackNode;
  if (newCallbackPriority === SyncLane) {
    if (root.tag === LegacyRoot) {
      // ...

      // 往SyncQueue中添加一个callback(performSyncWorkOnRoot)
      scheduleLegacySyncCallback(performSyncWorkOnRoot.bind(null, root));
    } else {
      scheduleSyncCallback(performSyncWorkOnRoot.bind(null, root));
    }
  } else {

    // ...
    newCallbackNode = scheduleCallback(
      schedulerPriorityLevel,
      performConcurrentWorkOnRoot.bind(null, root),
    );
  }

  root.callbackPriority = newCallbackPriority;
  root.callbackNode = newCallbackNode;
}
```
