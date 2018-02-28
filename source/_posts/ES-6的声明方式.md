---
title: ES 6的声明方式
date: 2018-02-28 20:01:00
tags: ES 6
---
[ES 6 新特性列表]([https://frankfang.github.io/es-6-tutorials/](https://frankfang.github.io/es-6-tutorials/))

# let 和 const

声明变量的4种方式
1. a = 1
2. var a = 1
3. let a = 1
4. const a = 1

很多人认为 a = 1 声明了一个全局变量，其实并不是这样，如果有两层函数，比如：

![image](http://upload-images.jianshu.io/upload_images/6882087-52a1d513d0aee2c4..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面代码说明了，如果当前作用域有a， 会直接使用a，只有没有a这个变量的情况下才会声明全局变量a，所以不建议使用。

现在再来说说var a = 1 这种声明方式，看代码：
![image](http://upload-images.jianshu.io/upload_images/6882087-df3c84b3c465db76..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意，a没有被声明，所以代码会报错，在此基础上稍加改动：

![image](http://upload-images.jianshu.io/upload_images/6882087-690f0ef25c8fbc0b..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

没错，看似没有被执行的一行代码却改变了运行结果。这就是传说中的变量提升，实际上代码会变成下面这样：

![image](http://upload-images.jianshu.io/upload_images/6882087-59147225e6ee3aa1..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以var a这种声明方式，为代码带来了很多不符合逻辑的地方，真是奇葩。既然它总是会提升，建议直接将 var 写在第一行，会减少很多始料未及的情况。

不止是变量提升，var 这种声明方式还会暴露全局变量，上代码：
```
{
     var a =1
     window.b = function(){
          console.log(b) 
     }
}
```
看看，明明我只想要一个全局变量b，但是上面这行代码中的 var a，一定会被变量提升的呀，我的var a 一定会跑到外层去。不经意的，我暴露了两个全局变量。要想不暴露我的局部变量a，我得拿一层函数包裹：
```
function x(){    
     var a =1
     window.b = function(){
          console.log(b) 
     }
}
x()
```
修改之后，看似没什么毛病了，我的 a 老老实实的待在了我想要的位置。但是，x()又成为了我新暴露的全局变量，再改：
```
function (){    
     var a =1
     window.b = function(){
          console.log(b) 
     }
}()
```
把函数改成匿名函数，直接调用。不行，会有语法错误，那我再加一层括号：
```
(function (){    
     var a =1
     window.b = function(){
          console.log(b) 
     }
}())
```
这下好了。。。但是我做了这么多复杂的工作，只是为了让函数没有名字？？？这个操作真的有点贱贱的。。。

于是，在ES 6 中，新的声明方式诞生了

## let
直接上代码：
![image](http://upload-images.jianshu.io/upload_images/6882087-31eb5475bf5eed09..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
没有做任何多余的工作，我的 a 没有暴露。

在 let 诞生之前，要想使用一个局部变量，就得用一个立即执行函数这种变态方案。有了 let，就可以很方便的使用局部变量了。

所以说，let 的作用域就在最近的花括号中。

再上代码：
![image](http://upload-images.jianshu.io/upload_images/6882087-b7f19ba2da7cee97..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
是的，不用再考虑我要用的 a 到底是外面的 a 还是里面的 a，不用考虑我的 a 会不会变量提升这些乱七八糟的问题了，代码会严格按顺序执行，先声明再使用。这样看可能不明显，我再加一段对比一下：

![image](http://upload-images.jianshu.io/upload_images/6882087-722f0293c66d5e9d..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在接触了 let 以后，var 瞬间显得很奇葩了。。。

所以说，在一个代码块中，如果在 let 声明之前，就使用这个变量，会直接报错（Temp Dead Zone）。先声明再使用，真是清爽不做作！

另，不能重复let，一样会报错：

![image](http://upload-images.jianshu.io/upload_images/6882087-4fd896abb634a51f..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## const

const 和 let 只有一个区别，上代码：

![image](http://upload-images.jianshu.io/upload_images/6882087-cebbaf9b6677a0c7..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以简单的理解为，const 就是一锤子买卖，只有一次赋值机会，声明过后不能被覆盖。且声明的同时必须赋值（否则报错）。通常可以用来声明常量。

有了 let 和 const 我们就有更精确的作用域了！
