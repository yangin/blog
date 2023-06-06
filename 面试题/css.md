[参考](https://vue3js.cn/interview/css/box.html)

# 1. 对盒子模型的理解

### Q1: 盒模型的组成

一个盒子模型由四个部分组成：content、padding、border、margin。

### Q2: 两种盒模型及其区别

- W3C 盒模型（标准，默认）
  > 元素长度 = content
  >
  > 盒子总长度 = 元素长度 + padding + border + margin
- IE 盒模型
  > 元素长度 = content + padding + border
  >
  > 盒子总长度 = 元素长度 + margin

> 说明：
>
> - 元素长度：通过设置元素的 width 和 height 来设置的值。
>
> - 盒子总长度：在 layout 布局中占据的空间。

可以通过 `box-sizing` 属性来指定使用哪种盒子模型。

```css
box-sizing: content-box; /* W3C 盒子模型 */
box-sizing: border-box; /* IE 盒子模型 */
```

> 注意：
>
> `box-shadow` 不会影响盒子模型的大小，即不会影响盒子总长度。

# 2. CSS 选择器

### Q1: CSS 有哪些选择器

- 基础选择器
  - id 选择器 (#id)
  - class 选择器 (.class)
  - tag 选择器 (div)
- 组合选择器
  - 后代选择器 (div p)
  - 子选择器 (div > p)
  - 相邻兄弟选择器 (div + p)
  - 层级选择器 (div ~ p)：选择前面有 div 的所有 p 元素
  - 属性选择器 (input[type="text"])
  - 群组选择器 (div, p)
- 伪类选择器

  - 伪类选择器 (:hover)
  - 伪元素(::before)

> 伪类和伪元素的区别：伪类是一个元素的特殊状态，而伪元素是一个元素的特殊部分。

### Q2: 选择器的优先级

```bash
!important > 内联样式 > ID 选择器 > 类选择器、属性选择器、伪类选择器 > 标签选择器、伪元素选择器 > 通配符选择器 > 继承 > 浏览器默认样式 > 选择器的组合 > 选择器的嵌套
```

选择器的优先级是由四个级别的权重决定的，权重越高的选择器优先级越高。

- !important：权重为 10000
- 内联样式：权重为 1000
- ID 选择器：权重为 100
- 类选择器、属性选择器、伪类选择器：权重为 10
- 标签选择器、伪元素选择器：权重为 1
- 通配符选择器：权重为 0
- 继承：权重为 0
- 浏览器默认样式：权重为 0
- 选择器的`组合`：权重为各个选择器权重之和
- 选择器的`嵌套`：权重为各个选择器权重之和
- 选择器的`顺序`：权重相同的情况下，后面的选择器优先级高

### Q3: 有哪些继承属性

在 CSS 中，继承是指的是给父元素设置一些属性，后代元素会自动拥有这些属性。

- 可继承属性

  - 字体系列属性

    ```css
    font:组合字体
    font-family:规定元素的字体系列
    font-weight:设置字体的粗细
    font-size:设置字体的尺寸
    font-style:定义字体的风格
    font-variant:偏大或偏小的字体
    ```

  - 文本系列属性

    ```css
    text-indent：文本缩进
    text-align：文本水平对刘
    line-height：行高
    word-spacing：增加或减少单词间的空白
    letter-spacing：增加或减少字符间的空白
    text-transform：控制文本大小写
    direction：规定文本的书写方向
    color：文本颜色
    ```

  - 元素可见性

    ```css
    visibility：是否可见
    ```

  - 表格布局属性

    ```css
    caption-side：表格标题的位置
    border-collapse：是否合并表格边框
    border-spacing：表格边框的间隔
    empty-cells：是否显示表格中的空单元格
    ```

  - 列表属性

    ```css
    list-style-type：列表项标记的类型
    list-style-image：列表项标记的图像
    list-style-position：列表项标记的位置
    ```

  - 引用

    ```css
    quotes：设置引用标签的引用符号
    ```

  - 光标属性

    ```css
    cursor：光标的形状
    ```

  > 注意
  >
  > a 标签的字体颜色不能被继承
  >
  > input 标签的字体颜色不能被继承
  >
  > h1-h6 标签字体的大下也是不能被继承的

- 不可继承属性
  - display
  - 文本属性：vertical-align、text-decoration
  - 盒子模型属性：width、height、margin、padding、border
  - 背景属性：background、background-color、background-image
  - 定位属性：position、top、right、bottom、left、z-index
  - 浮动属性：float、clear
  - 生成内容属性：content、quotes
  - 轮廓样式属性：outline-style、outline-width、outline-color、outline
  - 页面样式属性：size、page-break-before、page-break-after

# 3. px/em/rem/vh/vw/% 区别

- px：绝对单位，页面按精确像素展示。
- em：相对单位，`基准点为父元素字体`的大小，如果自身定义了。font-size，那么就以自身为基准，整个页面内的 1em 不是一个固定的值。
- rem：相对单位，可理解为”root em”，`相对根节点 html 的字体`大小来计算。(在整体布局上，表现更优于 em)
- vh、vw：相对单位，1vh 等于 1%的`视口高度`, 100vh 就表示满高。
  > 在桌面端，指的是浏览器的可视区域；
  >
  > 在移动端，指的是布局视口，不包括状态栏。
- %：`相对于父元素`的百分比。
  > 对于普通定位元素就是我们理解的父元素
  >
  > 对于 position: absolute;的元素是相对于已定位的父元素
  >
  > 对于 position: fixed;的元素是相对于 ViewPort（可视窗口）

# 4. 设备像素、CSS 像素、设备独立像素、dpr、ppi、dpi 之间的区别

- 设备像素：也称`物理像素`，是显示屏幕上最小的`物理单元`（如液晶屏的液晶单元，二极管等）。出厂时即固定，不可修改。
- CSS 像素：CSS 像素是 Web 开发中使用的长度`抽象单位`，以 px 为后缀，适用于 `Web 编程`，当 dpr 为 1 时，1px = 1 个设备像素。
- 设备独立像素：也称`密度无关像素`（dp 或 dip），是一个`抽象单位`。是为了使得不同分辨率的显示屏，能够显示相同的效果，常用于`移动端编程`。如 ReactNative 中的样式单位，实际是一个独立像素。设备独立像素的大小是固定的，是一个相对单位，是一个相对于`设备像素`而言的单位。
  > CSS 像素也是一种设备独立像素，只是其是用于 Web 开发中的抽象单位。
- dpr：设备像素比（device pixel ratio），设备像素比 = 设备像素 / 设备独立像素，可通过 `window.devicePixelRatio` 获取。
  > 例如，iPhone 6 的 dpr 为 2，意味着 1 个 CSS 像素对应 2 个设备像素。
- ppi：每英寸像素数(Pixels Per Inch)，又被称为`像素密度`，是一个`显示屏幕`的物理属性（出厂时即固定，不可修改），表示每英寸上有多少个设备像素。ppi 越高，屏幕显示的图像越清晰。
- dpi：每英寸点数(Dots Per Inch)，又被称为`打印分辨率`，是`打印机`或`扫描仪`的物理属性（出厂时即固定，不可修改）。表示每英寸上有多少个打印点或扫描点。dpi 越高，打印或扫描的图像越清晰。

### Q1: 调整显示器的分辨率，调整的是什么

显示器的分辨率通常指的是显示器的`设备像素`，出厂即固定，无法直接调整。调整分辨率并不会改变显示器的物理像素点数量，而是通过调整显示器的像素插值算法来模拟出不同分辨率的效果。这种插值算法通常会导致显示效果的损失，因此不建议经常调整分辨率。

### Q2：浏览器放大缩小网页的原理

浏览器放大或缩小网页时，是通过改变 `dpr` 来进行的，如网页放大 2 倍后，1 个 css 像素对应了 2 个设备像素，从而在视觉效果上发生了变化，而元素的 CSS 像素值并没有发生变化，布局也没有发生变化。

### Q3：移动端的 1px 问题

移动端的 1px 问题，是指在移动端中，1px 的 CSS 像素在不同的设备上显示的物理像素不同，导致显示效果不同。

解决方案：

- 使用 transform: scale()
- 使用 viewport

[参考](https://juejin.cn/post/6844903506185289735)

# 5. 回流重绘

### Q1: 什么是回流和重绘

- 回流：布局引擎会根据各种样式计算每个盒子在页面上的大小与位置，这个过程称为回流。
- 重绘：绘制引擎会根据布局引擎的计算结果，将像素渲染到页面上，这个过程称为重绘。

具体的浏览器解析渲染机制如下

- 解析 HTML，生成 DOM 树，解析 CSS，生成 CSSOM 树
- 将 DOM 树和 CSSOM 树结合，生成渲染树(Render Tree)
- Layout(回流):根据生成的渲染树，进行回流(Layout)，得到节点的几何信息（位置，大小）
- Painting(重绘):根据渲染树以及回流得到的几何信息，得到节点的绝对像素
- Display:将像素发送给 GPU，展示在页面上

当初始渲染时，回流不可避免的触发，可以理解成页面一开始是空白的元素，后面添加了新的元素使页面布局发生改变。

### Q2: 什么时候回触发回流

当我们对 DOM 的修改引发了 DOM `几何尺寸及位置`的变化（比如修改元素的宽、高或隐藏元素等）时，浏览器需要重新计算元素的几何属性。

具体场景有：

- 添加或者删除可见的 DOM 元素
- 元素的位置、尺寸、内容发生改变
- 元素的字体大小发生改变
- 页面初次渲染
- 浏览器窗口大小发生改变
- 激活 CSS 伪类（例如：:hover）
- 查询某些属性或调用某些方法

  - clientWidth、clientHeight、clientTop、clientLeft
  - offsetWidth、offsetHeight、offsetTop、offsetLeft
  - scrollWidth、scrollHeight、scrollTop、scrollLeft
  - getComputedStyle()
  - getBoundingClientRect()
  - scrollTo()

  > 这些属性和方法有一个共性，就是需要通过即时计算得到。因此浏览器为了获取这些值，也会进行回流。

### Q3: 什么时候触发重绘

当我们对 DOM 的修改导致了`样式的变化`（color 或 background-color），却并`未影响其几何属性`时，浏览器不需重新计算元素的几何属性、直接为该元素绘制新的样式，这里就仅仅触发了重绘

具体场景有：

- 元素的背景色、文字颜色发生改变
- 元素的背景图片发生改变
- 元素的 outline、visibility、outline-color、outline-style、outline-width、outline-offset、border、border-color、border-width、border-style、border-radius、box-shadow、opacity、transform、filter、background、color、border-radius、visibility、outline、outline-color、outline-style、outline-width、outline-offset、border、border-color、border-width、border-style、border-radius、box-shadow、opacity、transform、filter 等属性发生改变

> 这些属性有一个共性，就是不会影响到元素的尺寸与位置。

### Q4: 浏览器的优化机制

由于每次重排都会造成额外的计算消耗，因此大多数浏览器都会通过队列化修改并批量执行来优化重排过程。浏览器会将修改操作放入到队列里，直到过了一段时间或者操作达到了一个阈值，才清空队列

当你获取布局信息的操作的时候，会强制队列刷新，包括前面讲到的 offsetTop 等方法都会返回最新的数据

因此浏览器不得不清空队列，触发回流重绘来返回正确的值

### Q5: 如何减少回流和重绘

- `避免使用 table 布局`，table 中每个元素的大小以及内容的改动，都会导致整个 table 的重新计算
- 如果想调整元素的样式，尽可能`在 DOM 树的最里层改变 class`
- `避免设置多层内联样式`
- 将需要`动画效果的元素，将其 position 属性设置为 absolute 或 fixed`，使它脱离文档流，从而减少对其他元素的影响
- `避免使用 CSS 表达式`（例如：calc()），因为它会强制浏览器重新计算样式
- `避免频繁操作样式`，最好一次性重写 style 属性，或者将样式列表定义为 class 并一次性更改 class 属性
- `避免频繁操作 DOM`
  - 创建一个 documentFragment，在它上面应用所有 DOM 操作，最后再把它添加到文档中
  - 也可以先为元素设置 display: none，操作结束后再把它显示出来。因为在 display 属性为 none 的元素上进行的 DOM 操作不会引发回流和重绘
- `避免频繁读取会引发回流/重绘的属性`，如果确实需要多次使用，就用一个变量缓存起来
- `开启 GPU 硬件加速`, 可以让 transform、opacity、filters 这些动画不会引起回流重绘
- 对于`频繁操作的元素，可以设置其 position 属性`，使其脱离文档流，从而避免频繁的回流

# 6. CSS 中隐藏页面元素的方法，原理及其区别

|        隐藏方法        | display: none | visibility: hidden |                  opacity: 0                   |
| :--------------------: | :-----------: | :----------------: | :-------------------------------------------: |
|         页面中         |    不存在     |        存在        |                     存在                      |
|          重排          |      会       |        不会        |                     不会                      |
|          重绘          |      会       |         会         | 一般会（在 animal 触发 GPU 加速情况下不重绘） |
|      自身绑定事件      |    不触发     |       可触发       |
|      子元素可复原      |     不能      |         能         |                     不能                      |
| 被遮挡的元素可触发时间 |      能       |         能         |                     不能                      |

另外将 `position: absolute; left: -9999px;` 将元素移除可视区域，隐藏后的元素占据页面空间，隐藏后的元素会被渲染。

# 7. 对 BFC 的理解

[参考 1](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)

[参考 2](https://vue3js.cn/interview/css/BFC.html#%E4%B8%89%E3%80%81%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)

# 8. 元素水平居中与垂直居中的方法

### Q1: 水平居中

- 行内元素：`text-align: center;`
- 块级元素：`margin: 0 auto;`
- 绝对定位：`left: 50%; margin-left: -50px;`

> ```html
> <style>
>   .father {
>     position: relative;
>     width: 200px;
>     height: 200px;
>     background: skyblue;
>   }
>   .son {
>     position: absolute;
>     left: 50%;
>     margin-left: -50px;
>     width: 100px;
>     height: 100px;
>     background: red;
>   }
> </style>
> <div class="father">
>   <div class="son"></div>
> </div>
> ```
>
> 注意：
>
> - 该方法需要基于`父子元素`来实现，父元素需要设置 `position: relative;`，子元素需要设置 `position: absolute;`。
> - 该方法中的 left 需要与 margin-left 向对应（在垂直方向上则是 top 与 margin-top 对应）。
> - left 的值必须为 50%，margin-left 的值必须为子元素宽度的一半，且必须`以 px 为单位的负值`。

- 绝对定位：`left: 50%; transform: translateX(-50%);`

> ```html
> <style>
>   .father {
>     position: relative;
>     width: 200px;
>     height: 200px;
>     background: skyblue;
>   }
>   .son {
>     position: absolute;
>     left: 50%;
>     transform: translateX(-50%);
>     width: 60px;
>     height: 60px;
>     background: red;
>   }
> </style>
> <div class="father">
>   <div class="son"></div>
> </div>
> ```
>
> - 这里 `left: 50%` 使元素先向右位移 50%父元素宽度的距离。此时元素的最左侧位于父元素的正中间线位置。
> - 再通过 `translateX(-50%)` 将元素向左位移自己宽度的-50%。这样元素的中间线正好位于父元素的正中间线位置，最终达到水平居中的效果。
>
> 注意：
>
> - 该方法需要基于`父子元素`来实现，父元素需要设置 `position: relative;`，子元素需要设置 `position: absolute;`。
> - 该方法中的 left 需要与 transform 中的 translateX 向对应（在垂直方向上则是 top 与 translateY 对应）。
> - left 与 translateX 的`偏移量都必须是 50%`，用来做居中定位，且为相反数。
> - 该方法适用于`子元素宽度固定`的情况(即 width 存在具体值，为 px 或%)，如果子元素宽度不固定，可以使用 flex 布局。

- flex 布局：`display: flex; justify-content: center;`
- grid 布局：`display: grid; justify-content: center;`

### Q2: 垂直居中

- 行内元素：`line-height: 父元素高度;`
- 块级元素：`margin: auto 0;`
- 绝对定位：`top: 50%; transform: translateY(-50%);`
- flex 布局： `display: flex; align-items: center;`
- grid 布局： `display: grid; align-items: center;`

# 9. 介绍一下 flex 与 grid 网格布局，及 sticky

[参考](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Introduction)

### Q1: 如何实现瀑布流布局

- column-count 方法
- flex 布局
- js + absolute 布局 (推荐)

[参考](https://juejin.cn/post/7014650146000470053)

# 10. 如何实现单行/多行文本溢出的省略样式

必须属性：

- `overflow: hidden;` 超出部分隐藏
- `text-overflow: ellipsis;` 超出部分显示省略号
- `width` 或 `max-width` 为具体值或百分比

### Q1：单行文本溢出省略

```css
white-space: nowrap; // 不换行
overflow: hidden;
text-overflow: ellipsis;
```

注意： 这里需要设置 `width` 或 `max-width`，否则无法生效。

### Q2：多行文本溢出省略

- 基于行数截断

  ```css
  display: -webkit-box; // 将对象作为弹性伸缩盒子模型显示
  -webkit-box-orient: vertical; // 设置或检索伸缩盒对象的子元素的排列方式
  -webkit-line-clamp: 3; // 显示的行数
  overflow: hidden;
  text-overflow: ellipsis;
  ```

注意： 这里需要设置 `width` 或 `max-width`，否则无法生效。

# 11. 用 CSS 画一个三角形及其原理

[参考](https://vue3js.cn/interview/css/triangle.html)

#### 第一步：画一个有着 4 条 border 的普通盒子

```html
<!-- 一个有着4条border的普通盒子 -->
<style>
  .border {
    width: 50px;
    height: 50px;
    border: 2px solid;
    border-color: #96ceb4 #ffeead #d9534f #ffad60;
  }
</style>
<div class="border"></div>
```

<style>
  .border1 {
    width: 50px;
    height: 50px;
    background: #fff;
    border: 2px solid;
    border-color: #96ceb4 #ffeead #d9534f #ffad60;
  }
</style>
<div class="border1"></div>

#### 第二步： 将 border 的宽度放大很多时，如 50px

<style>
  .border2 {
    width: 50px;
    height: 50px;
    background: #fff;
    border: 50px solid;
    border-color: #96ceb4 #ffeead #d9534f #ffad60;
  }
</style>
<div class="border2"></div>

中间白色部分的 width = 50px, height = 50px;

#### 第三步：让中间的白色部分 width,height 为 0

<style>
  .border3 {
    width: 0;
    height: 0;
    border: 50px solid;
    border-color: #96ceb4 #ffeead #d9534f #ffad60;
  }
</style>
<div class="border3"></div>

中间白色部分的 width = 0, height = 0;得到 4 个不同方向的三角形

#### 第四步：隐藏不需要的三角形，如要得到箭头向上的三角形，将顶部的 border-width 设置为 0

<style>
  .border4 {
    width: 0;
    height: 0;
    border-style: solid;
    border-width: 0 50px 50px 50px;
    border-color: #96ceb4 #ffeead #d9534f #ffad60;
  }
</style>
<div class="border4"></div>

#### 第五步：将 左右 两侧的 border-color 设置为透明

<style>
  .border5 {
    width: 0;
    height: 0;
    border-style: solid;
    border-width: 0 50px 50px;
    border-color: transparent transparent #d9534f;
  }
</style>
<div class="border5"></div>

同理可得到其他方向的三角形，和梯形。

# 12. 如何让 Chrome 支持小于 12px 的文字？

### 背景

Chrome 中文版浏览器会默认设定页面的最小字号是 12px，英文版没有限制。

### 解决方案

- 使用图片代替文字
- 使用 transform: scale()，缩放比例为 0.5

  ```html
  <style type="text/css">
    .span1 {
      font-size: 12px;
      display: inline-block;
      -webkit-transform: scale(0.8);
    }
    .span2 {
      display: inline-block;
      font-size: 12px;
    }
  </style>
  <body>
    <span class="span1">测试10px</span>
    <span class="span2">测试12px</span>
  </body>
  ```

  > 注意：
  >
  > 使用 scale 属性只对可以定义宽高的元素生效，所以，上面代码中将 span 元素转为行内块元素

# 13. CSS3 动画有哪些

- transform：实现位移、旋转、缩放、倾斜等动画
  > 四个常用属性：
  >
  > - translate：位移
  > - rotate：旋转
  > - scale：缩放
  > - skew：倾斜
  >
  >   ```css
  >   transform: scale(0.8, 1.5) rotate(35deg) skew(5deg) translate(15px, 25px);
  >   ```
  >
  > 一般配合 transition 过度使用
  >
  > 注意：transform 不支持 inline 元素，使用前把它变成 block
- transition：实现渐变动画

  > 四个常用属性：
  >
  > - property：指定需要变化的 CSS 属性
  > - duration：指定过渡的时间
  > - timing-function：指定过渡的速度曲线
  > - delay：指定过渡的延迟时间
  >
  >   ```css
  >   transition: width 2s ease-in-out 1s;
  >   ```
  >
  > 一般配合 transform 过度使用
  >
  > 注意：并不是所有的属性都能使用过渡的，如 display:none<->display:block
  >
  > 其中 time-function 有以下几种值：
  >
  > - ease：默认值，慢速开始，然后变快，然后慢速结束(cubic-bezier(0.25, 0.1, 0.25, 1.0))
  > - linear：匀速(cubic-bezier(0.0, 0.0, 1.0, 1.0)
  > - ease-in：慢速开始(cubic-bezier(0.42, 0, 1.0, 1.0))
  > - ease-out：慢速结束(cubic-bezier(0, 0, 0.58, 1.0)
  > - ease-in-out：慢速开始和结束(cubic-bezier(0.42, 0, 0.58, 1.0)
  > - cubic-bezier(n,n,n,n)：自定义速度曲线, n 的值在 0~1 之间

- animation：实现复杂自定义动画

|            属性            |                     说明                      |                               属性值                                |
| :------------------------: | :-------------------------------------------: | :-----------------------------------------------------------------: |
|       animation-name       |     规定需要绑定到选择器的 keyframe 名称      |                                                                     |
|     animation-duration     | 规定完成动画所花费的时间，以秒(s)或毫秒(ms)计 |                                                                     |
| animation-timing-function  |              规定动画的速度曲线               | linear、ease、ease-in、ease-out、ease-in-out、cubic-bezier(n,n,n,n) |
|      animation-delay       |           规定在动画开始之前的延迟            |                                                                     |
| animation-iteration0-count |          规定动画应该播放的次数(次)           |                               number                                |
|    animation-direction:    |         规定是否应该轮流反向播放动画          |            normal、alternate、reverse、alternate-reverse            |
|    animation-fill-mode     |        规定对象动画时间之外的状态样式         |          none、forwards、backwards、both、initial、inherit          |
|    animation-play-state    |             规定动画是否正在运行              |                           paused、running                           |

> 注意：
>
> 需要配合 @keyframes 使用， @keyframes 规则用于创建动画。
>
> ```css
> @keyframes myfirst {
>   0% {
>     background: red;
>   }
>   25% {
>     background: yellow;
>   }
>   50% {
>     background: blue;
>   }
>   100% {
>     background: green;
>   }
> }
> ```
>
> @keyframes 规则中的百分比（%）表示在动画的每个“点”所发生的操作，0% 表示动画的开始，100% 表示动画的完成。

# 14. 响应式设计的基本原理是什么？如何做？

### Q1：响应式设计的常见特点

- 同时适配 PC + 平板 + 手机等
- 标签导航在接近手持终端设备时改变为经典的抽屉式导航
- 网站的布局会根据视口来调整模块的大小和位置

### Q2: 响应式设计的基本原理

**原理：通过媒体查询检测不同的设备屏幕尺寸做处理。**

移动端适配方案：

网页头部必填视口标签

```html
<meta
  name="viewport"
  content="width=device-width, initial-scale=1.0, maximum-scale=1, user-scalable=no"
/>
```

### Q3:响应式设计方案

- CSS 媒体查询：通过 CSS 媒体查询，可以针对不同的媒体类型定义不同的样式，从而实现不同的布局。
- 百分比布局：通过百分比布局，可以使元素的宽度相对于其父元素的宽度进行计算，从而实现响应式布局。
- rem 布局：通过 rem 布局，可以使元素的宽度相对于根元素的宽度进行计算，从而实现响应式布局。
- vw/vh 布局：通过 vw/vh 布局，可以使元素的宽度/高度相对于视口的宽度/高度进行计算，从而实现响应式布局。
- flex/grid 布局：通过 flex/grid 布局，可以使元素的宽度相对于其父元素的宽度进行计算，从而实现响应式布局。
- js 媒体查询：通过 js 媒体查询，可以根据不同的媒体类型，动态的为元素添加不同的样式，从而实现响应式布局。

# 15. 说说对 CSS 预编语言的理解？

CSS 预处理语言,如 Sass,Less,Stylus 等,扩充了 CSS 语言，增加了诸如`变量、混合（mixin）、函数`等功能，让 CSS `更易维护、方便`.

本质上，预处理是 CSS 的超集，就像 TypeScript 是 JavaScript 的超集。浏览器本身不支持，需要经过编译器编译（如 less-loader 等）成 CSS 后才可以使用。

常用功能 (以 `Less` 示例)：

- 变量

  > Less 用 `@` 来声明变量, Sass 用 `$` 来声明变量，Stylus 用 要求变量名与值之间用 =

  ```less
  @red: #c00;

  strong {
    color: @red;
  }
  ```

- 作用域

  > Less、Stylus 中变量向上查找，存在全局变量，Sass 中不存在全局变量。

  ```less
  @color: black;

  .scoped {
    @bg: blue;
    @color: white;
    color: @color;
    background-color: @bg;
  }
  .unscoped {
    color: @color;
  }
  ```

- 代码混合

  > Less 中将定义好的 ClassA 混合到 ClassB 中，也能够使用 @ 声明来传递参数。
  >
  > Sass 使用@mixin 来声明 ClassA,用 $ 来声明传递参数。
  >
  > Stylus 直接声明 Mixin 名称，用 = 来声明参数默认值。

  ```less
  .alert {
    font-weight: 700;
  }

  .highlight(@color: red) {
    font-size: 1.2em;
    color: @color;
  }

  .heads-up {
    .alert;
    .highlight(red);
  }
  ```

- 嵌套

  > Less、Sass、Stylus 都支持嵌套, 都采用 & 来引用父级选择器。

  ```less
  .a {
    &.b {
      color: red;
    }
  }
  ```

- 代码模块化

  > Less、Sass、Stylus 引入模块方式相同，都是 @import。

  ```less
  @import "./common";
  @import "./github-markdown";
  @import "./mixin";
  @import "./variables";
  ```

# 16. 如何优化图片

- 图片上传前压缩，如 tinypng 等
- 对于很多装饰类图片，尽量不用图片，因为这类修饰图片完全可以用 CSS 去代替，如三角形等。
- 对于移动端来说，屏幕宽度就那么点，完全没有必要去加载原图浪费带宽。一般图片都用 CDN 加载，可以计算出适配屏幕的宽度，然后去请求相应裁剪好的图片。
- 小图使用 base64 格式。
- 将多个图标文件整合到一张图片中（雪碧图）
- 选择正确的图片格式：
  - JPEG：有损压缩，不支持透明，适合照片
  - PNG：无损压缩，支持透明，适合图标、小图
  - SVG：矢量图，代码内嵌，适合图标
  - GIF：无损压缩，支持动画，适合动画图标
  - WebP：谷歌开发的图片格式，有损压缩，支持透明、动画、颜色更丰富，但是兼容性不好


# 17. 渐进增强和优雅降级之间的不同

渐进增强和优雅降级是前端设计中的两个策略，目的都是为了提高网站的兼容性。

- `渐进增强`：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。
- `优雅降级`：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容。
  
### 区别

优雅降级更注重现代浏览器的`性能和功能`，并在必要时提供降级方案。渐进增强更注重网站的`基础功能`，并为现代浏览器提供额外的优化和功能。


# 18. 如何提高 CSS 性能

- 减少css文件的体积
  - 利用webpack等压缩工具压缩CSS文件。
  - 引用tailwindcss等工具库，减少代码量。
  - 查找并删除未使用的CSS;
- 减少http请求时间
  - 利用雪碧图，结合background-position,宽高来定位icon图，减少http请求。
  - 利用link代替@import来引入外部样式表。因为@import是在页面加载完毕后才会加载，而link则是在页面加载的同时加载。
- 优化CSS解析过程
  - 尽量使用简写属性，如：margin: 0 0 10px;。
  - 合理使用选择器
    - 减少嵌套层级（尽量三级以内）
    - 使用id选择器就没必要进行嵌套
    - 尽量少使用通配符和属性选择器，他们的匹配效率是最低的
  - 避免可以继承的属性重复设置，如：color: #333;。
- 优化渲染过程
  - 减少重排操作，及不必要的重绘。
  - 避免一些需要性能的属性，如：box-shadow、border-radius、transform等。

[还可以参考](https://juejin.cn/post/7223938976158318648)