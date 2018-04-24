---
title: 到底什么是BFC？
date: 2018-04-24 21:23:07
tags: BFC
---
MDN 对 BFC 的描述：
> 块格式化上下文（Block Formatting Context，BFC） 是Web页面的可视化CSS渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

![image](http://upload-images.jianshu.io/upload_images/6882087-2eb1e3ef67f168cd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
往下接着看看：
> 下列方式会创建块格式化上下文：
> - 根元素或包含根元素的元素
> - 浮动元素（元素的 float 不是 none）
> - 绝对定位元素（元素的 position 为 absolute 或 fixed）
> - 行内块元素 （元素的 display 为 inline-block）
> - 表格单元格（元素的 display 为 table-caption）
> - 具有overflow 且值不是 visible 的块元素，
> - display: flow-root
> - column-span: all 应当总是会创建一个新的格式化上下文，即便具有 column-span: all 的元素并不被包裹在一个多列容器中。

好像有那么点意思，就是说如果符合以上情况，就会创建 BFC 咯？
先不管那么多，写个demo试试看：

![image](http://upload-images.jianshu.io/upload_images/6882087-2a25cfa0f9509f4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面代码，当我们给子元素一个高度时，父元素会将子元素包裹住。
![image](http://upload-images.jianshu.io/upload_images/6882087-58a37b815f833e66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
但是，当我们给子元素一个左浮动后，子元素脱离文档流就跑出去了。
![image](http://upload-images.jianshu.io/upload_images/6882087-8ce08c8048fb7d66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面代码，当我们也给父元素加上一个左浮动后（满足 float 不为 none），触发了 BFC，成功创建了块格式化上下文，父元素重新包裹住了子元素。
![image](http://upload-images.jianshu.io/upload_images/6882087-e2611e7eae9b2fc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面代码，当我们让父元素绝对定位后也触发了 BFC。

![image](http://upload-images.jianshu.io/upload_images/6882087-87a93ab12903ee4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面代码满足元素的 display 为 inline-block的条件，所以触发了 BFC。

再结合 MDN 的描述：
> 创建了块格式化上下文的元素中的所有内容都会被包含到该BFC中。


![image](http://upload-images.jianshu.io/upload_images/6882087-d56f2c9ad7386d9f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这下就清楚多了，BFC 可以用来包住浮动元素（不是清除浮动哟）。就算子元素再怎么折腾，也不会影响外部元素了。

话说回来，虽然我可以通过给父元素加 float 等条件触发 BFC，但是**给父元素加 float 这个行为本身**会不会影响别的元素呢？我可不想拆东墙补西墙。。。


于是，一个新的属性`display: flow-root`诞生了！！！
这个属性的功能就一个：**触发 BFC**
![image](http://upload-images.jianshu.io/upload_images/6882087-90832c902ebf90b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
只需给元素加上`display: flow-root`就能触发 BFC。

既然说 BFC 能够包住浮动元素，那么我是不是可以利用这一点进行布局呢？
![image](http://upload-images.jianshu.io/upload_images/6882087-9df5e0010fa42b44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面代码，当`slide-bar`左浮时，会脱离文档流，和`main`发生重叠。你也许会说，给`main`也加个左浮吧，看代码：
![image](http://upload-images.jianshu.io/upload_images/6882087-7b80e9c5e8659f90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
没错，由于没有宽度，我的`main`被挤扁了。。。那为什么不加个宽度呢？
这样我的`main`就不能自适应了呀（敲黑板！）
BFC 完美的解决了这个问题：
![image](http://upload-images.jianshu.io/upload_images/6882087-81918b3fd3801f83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面代码，利用 BFC 使两个兄弟元素互不干涉，实现了左右自适应布局。

总的来说，你可以这样理解 BFC：
功能一：爸爸管儿子
功能二：兄弟之间划清界限

参考资料:
[块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
[CSS深入理解流体特性和BFC特性下多栏自适应布局](http://www.zhangxinxu.com/wordpress/2015/02/css-deep-understand-flow-bfc-column-two-auto-layout/)



