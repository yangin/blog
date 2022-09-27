# react-redux

[react-redux](https://github.com/reduxjs/react-redux) 为 react 的 redux 绑定库, 使 react 组件可以使用 redux， 且当 redux store 发生变化时，可以自动更新 react 组件

其主要输出 API 为

- Provider 核心，用来传递 createStore 后的 store
- connect 为 React 组件绑定 state,并订阅更新
- useSelector 从 store 获取 state 值
- useDispatch 从 store 获取 dispatch

## Provider

Provider 提供了 3 个作用

1. 提供接收订阅者的方法 subscription（订阅器），接收订阅者并将其都存储到 listeners 的链表中
2. 监听 store 的变化，当 store 发生变化时，通知订阅者，即遍历 listeners，执行每个订阅者的回调函数
3. 传递 store，subscription（订阅器） 到子组件

订阅者的回调函数一般是用来重新渲染组件

内部使用了 React 的 Context API，将 store 传递给所有的子组件

```js
const contextValue = useMemo(() => {
  const subscription = createSubscription(store);
  return {
    store,
    subscription,
    getServerState: serverState ? () => serverState : undefined,
  };
}, [store, serverState]);

return <Context.Provider value={contextValue}>{children}</Context.Provider>;
```

## connect

connect 是 react-redux 的高阶组件，用来连接组件和 store。

```js
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)
```

connect 用来订阅 store, 即 store 的每次更新都会触发 mapStateToProps 的执行， 如果不想订阅，则 mapStateToProps 为 null 或 undefined

默认情况下，connect 会将 dispatch 作为 props 传递给组件

### connect 如何实现当 store 变化时，触发组件的重新渲染

connect 内部为被包装组件重新赋予了新的 props, 这个新的 props 为 mapStateToProps、mapDispatchToProps、ownProps 的合并。

```js
// WrappedComponent 为被包装的组件
// actualChildProps 为新的props
<WrappedComponent {...actualChildProps} ref={reactReduxForwardedRef} />
```

当 store 变化时， 实际上是根据订阅，重新计算 actualChildProps， 将新的 props 传递给 WrappedComponent，这样就触发了组件的重新渲染

```js
// 在这里为actualChildPropsSelector绑定订阅
actualChildProps = useSyncExternalStore(
  // TODO We're passing through a big wrapper that does a bunch of extra side effects besides subscribing
  subscribeForReact,
  // TODO This is incredibly hacky. We've already processed the store update and calculated new child props,
  // TODO and we're just passing that through so it triggers a re-render for us rather than relying on `uSES`.
  actualChildPropsSelector,
  getServerState
    ? () => childPropsSelector(getServerState(), wrapperProps)
    : actualChildPropsSelector
);

// 这里创建订阅
const subscribeForReact = useMemo(() => {
  const subscribe = (reactListener: () => void) => {
    if (!subscription) {
      return () => {};
    }

    return subscribeUpdates(
      shouldHandleStateChanges,
      store,
      subscription,
      // @ts-ignore
      childPropsSelector,
      lastWrapperProps,
      lastChildProps,
      renderIsScheduled,
      isMounted,
      childPropsFromStoreUpdate,
      notifyNestedSubs,
      reactListener
    );
  };

  return subscribe;
}, [subscription]);
```

每个 connect 为 store 添加一个订阅， 当 store 变化时，会依次执行所有的订阅，重新计算 actualChildProps，从而触发所有相关联组件的重新渲染

## useSelector

用来从 store 获取指定的 state， 内部依赖 useContext 实现， 从 Provider 处订阅更新。当 store 变化时， useContent 会触发订阅组件的更新。

Q: useSelector 是如何实现当 store 变化时，更新 useSelector 的返回值 ?

A: useSelector 会订阅 store， store 通过 useReduxContext 获取，useReduxContext 基于 useContext 实现， 所有当 store 变化时，会通过 useContext 来通知，从而重新计算 useSelector 的返回值

```js
  const { store, subscription, getServerState } = useReduxContext()!
```

每一次 dispatch ，都会触发 useSelector 重新计算 selector 的值 ？，如果值发生变化，会触发组件重新渲染, 否则不触发渲染

组件中可以使用多个 useSelector 来获取 store 中的数据， 每个 useSelector 都会增加一个对 store 的订阅，所以`尽量减少 useSelector 的使用次数，可以放在一个里面， 否则当一个 dispatch 导致多个 useSelector 中的值发生变化时，会导致多次渲染`

useSelector 使用的是 ===， 而 connect 使用的是浅比较，所以如果是对象，需要使用 useMemo 来缓存

useSelector 与 connect 在返回对象的比较上有差异，这里需要注意

当父组件渲染导致的子组件重新渲染，这时子组件中的 useSelector 会重新执行，不会组织重新渲染，好的解决办法是使用 react.memo 包装子组件
