---
title: 封装一些API
date: 2018-03-12 21:43:46
tags: API
---

用原生JS封装一些常用API

1. 获取所有兄弟元素
```
function getSiblings(node){
    var allChildren = node.parentNode.children

    //这只是一个哈希，伪数组
    var array = {
        length: 0
    }
    for(let i =0;i < allChildren.length;i++){
        if(allChildren[i] !== node){
            array[array.length] = allChildren[i]
            array.length +=1
        }
    }
    return array
}
```

2. 给一个节点添加多个Class
```
function addClass(node,classes){
    for(let key in classes){
        var value = classes[key]
        if(value){
            node.classList.add(key)
        }else{
            node.classList.remove(key)
        }
    }
}
```
<!-- more -->
如何使用封装的API?
命名空间：
```
window.newDom = {}
newDom.getSiblings(node)
newDom.addClass(node,{a:true,b:false,c:true})
```
为了方便使用，将方法写到原型上（修改Node的公有方法）:
```
Node.prototype.getSiblings = function(){
    ...
}
Node.prototype.addClass = function(){
    ...
}
```
就可以直接使用：
node.getSiblings()
node.addClass('a','b','c')

**但是方法并不能获取节点，再做一点改进,将所有node改成this，建立连接。(省略node参数)**
1. node.getSiblings()
```
Node.prototype.getSiblings = function(){
    var allChildren = this.parentNode.children

    var array = {
        length: 0
    }
    for(let i =0;i < allChildren.length;i++){
        if(allChildren[i] !== this){
            array[array.length] = allChildren[i]
            array.length +=1
        }
    }
    return array
}
```
2. node.addClass()
```
Node.prototype.addClass = function(){
    classes.forEach((value)=> this.classList.add(value))
}
```

仔细想想：
1. 上面Node原型的方法，有可能在不经意间被覆盖，所以安全起见我们可以再重写一个Node构造函数，比如Node2
2. 如何使传入节点即使是选择器也能够匹配
``` 
window.Node2 = function(nodeOrSelector){
    let node
    if(typeof nodeOrSelector === 'string'){
        node = document.querySelector(nodeOrSelector)
    }else{
        node = nodeOrSelector
    }
    return {
        getSiblings: function(){
            var allChildren = node.parentNode.children

            var array = {
                length: 0
            }
            for(let i = 0; i < allChildren.length; i++){
                if(allChildren[i] !== node){
                    array[array.length] = allChildren[i]
                    array.length += 1
                }
            }
            return array
        },
        addClass: function(classes){
            classes.forEach((value)=> node.classList.add(value))
        }
    }
}
```
最后就可以直接使用：
var node2 = Node2(node) //node2存了新建构造函数的地址
node2.getSiblings()
node2.addClass('a','b','c')

用jQuery封装AJAX

API: jQuery.ajax(url, method, body, success, fail)
```
window.jQuery.ajax = function(url, method, body, success, fail){
    let xhr = new XMLHttpRequest()
    xhr.open(method, url)
    xhr.onreadystatechange = ()=>{
        if(xhr.readyState === 4){
            if(xhr.status >= 200 && xhr.status <= 300){
                success.call(undefined, xhr.responseText)
            }else if(xhr.status >= 400){
                fail.call(undefined, xhr)
            }
        }
    }
    xhr.send(body)
}
function success(responseText){}
function fail(responseText){}
```
