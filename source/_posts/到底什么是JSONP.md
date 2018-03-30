---
title: 到底什么是JSONP
date: 2018-03-25 13:52:21
tags: JSONP
---
到底什么是JSONP？它和JSON到底有什么关系？这是我碰到JSONP时脑海里的浮现的第一个问题。先看看维基百科上是怎么解释的：
> JSONP(JSON with Padding)是数据格式JSON的一种“使用模式”，可以让网页从别的网域要数据。

并不知道在说什么，JSON with Padding？也太抽象了。。。既然说“可以让网页从别的网域要数据”，那就是说JSONP的使用场景是需要跨域的，先试着码一码吧。
> 假设我有一个账户，每当我点击付款按钮，就会从账户中扣款1元。

发送请求有两种方案：
1. 通过`<img>`标签造`get`请求
```
button.addEventListener('click', (e)=>{
    let image = document.createElement('img')
    image.src = '/pay'
    image.onload = function(){ // 状态码是 200~299 则表示成功
        alert('成功')
    }
    image.onload = function(){ // 状态码大于等于 400 则表示失败
        alert('失败')
    }
})
```
2. 通过`<script>`标签造`get`请求
```
button.addEventListener('click', (e)=>{
    let script = document.createElement('script')
    script.src = '/pay'
    document.body.appendChild(script)
    script.onload = function(e){ // 状态码是 200~299 则表示成功
        e.currentTarget.remove()
    }
    script.onload = function(e){ // 状态码大于等于 400 则表示失败
        e.currentTarget.remove()
    }
})
```

显然，`<script>`标签更具有可行性，毕竟你得需要有一张真的图片来骗过浏览器，仅仅用来发送请求也太不合算了。
先写一个服务器来模拟一下所需环境，当然我还需要一个数据库，在当前目录下创建。（这个数据库先用来模拟第三方网址）
```
touch db
```
后端代码长这样：
```
...
  if(path === '/pay'){ //如果用户点击付款会向这个路径发送请求
    let amount = fs.readFileSync('/db', 'utf8')
    amount -= 1
    fs.writeFileSync('/db', amount)
    response.setHeader('Content-Type', 'application/javascript')
    response.write('amount.innerText = ' + amount)
    response.end()
  }
...
```
上面代码，当我点击付款按钮，会将我数据库中记录的余额减1，然后再将当前余额显示在页面。
**这种技术叫做 SRJ - Server Rendered JavaScript**
所以，利用`<script>`标签没有跨域限制的“漏洞”，可以与第三方网址进行通信。也就是维基百科所说的“让网页从别的网域要数据”。
等等，通信刚刚实现了，“要数据”怎么实现？接着码。。。
既然`<script>`标签没有跨域限制，那么我只需指定`src`到任何我想要请求数据的路径就可以了。
第三个方案：JSONP
先设定一个场景：
```
请求方：aaa.com 的前端程序员（浏览器）
响应方：bbb.com 的后端程序员（服务器）
  1. 请求创建 script，src 指向响应方，同时传一个查询参数?callbackName=yyy
  2. 响应方根据查询参数callbackName，构造形如
    i. yyy.call(undefied,'你要的数据')
    ii. yyy('你要的数据')
    这样的响应
  3. 浏览器接收到响应，就会执行yyy.call(undefined,'你要的数据')
  4. 那么请求方就知道了他要的数据
```
这样应该就可以拿到我想要的数据了。
那么前端代码长这样：
```
button.addEventListener('click', (e)=>{
    let script = document.createElement('script')
    let functionName = 'yyy'+ parseInt(Math.random()*10000000 ,10)
    window[functionName] = function(){  // 每次请求之前搞出一个随机的函数
        amount.innerText = amount.innerText - 0 - 1
    }
    script.src = '/pay?callback=' + functionName
    document.body.appendChild(script)
    script.onload = function(e){ // 状态码是 200~299 则表示成功
        e.currentTarget.remove()
        delete window[functionName] // 请求完了就干掉这个随机函数
    }
    script.onload = function(e){ // 状态码大于等于 400 则表示失败
        e.currentTarget.remove()
        delete window[functionName] // 请求完了就干掉这个随机函数
    }
})
```
后端代码长这样：
```
...
if (path === '/pay'){
    let amount = fs.readFileSync('/db', 'utf8')
    amount -= 1
    fs.writeFileSync('/db', amount)
    let callbackName = query.callback
    response.setHeader('Content-Type', 'application/javascript')
    response.write(`
        ${callbackName}.call(undefined, 'success')
    `)
    response.end()
}
...
```
程序员们约定这样写：
```
callbackName -> callback
```
（文中为了方便理解用了`callbackName`代替）

这样的话就真正达到双方的通讯目的了，后端根据前端提供的`callback`参数构造一个函数调用，把数据返回给第一个参数，`.call`就拿到数据了。
看来给`<script>`标签的`src`属性加`callback`参数就是JSONP啦。
如果使用jQuery，JSONP的请求方式就更简单啦：
```
$.ajax({
 url: "http://bbb.com:8002/pay",
 dataType: "jsonp",
 success: function( response ) {
     if(response === 'success'){
     amount.innerText = amount.innerText - 1
     }
 }
 })

 $.jsonp()
```
吐槽一下：和ajax又有个鬼的关系。。。

**另外，JSONP是通过动态创建`<script>`标签实现的，动态创建的<script>标签只能用get，不能用post。**

所以说，通过动态创建<script>标签，并利用其src属性提供一个callback参数构造回调函数来接收数据，从而达到与第三方网址建立通信的目的，这就是JSONP啦。
