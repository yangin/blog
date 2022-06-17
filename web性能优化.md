# Web性能优化

## 优化步骤

1. 将所有第三方CND引入改为本地加载；
2. 将所有图片等静态资源进行压缩，减小加载体积；
3. 移除不必要的依赖包；
4. 将大体积的依赖包（如echarts等）单独拆出来，进行动态引入；
5. 将首页非必要的静态资源引入（link、script等引入方式）改为动态引入
6. 灵活运用script标签的 async 与 defer属性，让首页的js加载更快；


## 将静态引入改为动态引入
`js脚本执行会消耗较多时间，所以将首页非必要的脚本从首页移除，改为动态加载，来提升性能`

核心加载脚本代码load.ts:
```javascript
/**
 * 动态加载一组脚本
 * 此处对已加载脚本进行了缓存，避免重复加载
 */
const loadedScripts: string[] = [];

const checkLoaded = (urls: string[]) => {
  const unloaded = urls.filter((url: string) => !loadedScripts.includes(url));
  if (unloaded.length > 0) {
    return false;
  }
  return true;
};

export function loadScript(urls: string[], callback: () => void) {
  if (checkLoaded(urls)) {
    callback();
    return;
  }
  const total = urls.length;
  let loaded = 0;
  let error = false;
  urls.forEach(url => {
    const script = document.createElement('script');
    script.src = url;
    script.onload = () => {
      loaded += 1;
      if (loaded === total && !error) {
        callback();
      }
    };
    script.onerror = () => {
      error = true;
      if (loaded === total && !error) {
        callback();
      }
    };
    document.body.appendChild(script);
    loadedScripts.push(url);
  });
}
```

使用方法：
```javascript
  useEffect(() => {
    // 加载初始化所需要的脚本
    loadScript([
      '/neditor/neditor.config.js',
      '/neditor/neditor.all.js',
      '/neditor/neditor.parse.min.js',
    ], initEditor);
  }, []);
```