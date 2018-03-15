---
title: jQuery不过如此
date: 2018-03-15 16:54:54
tags: jQuery
---
JS这门语言神经质大家都已经心照不宣了，要说jQuery，那必须得从像老太婆又臭又长裹脚布一样的DOM原生API开始说起啊。
想要获取页面上的一个节点，对它做一些为所欲为的事情我需要：
```
var 这个节点 = document.getElementById('这个节点的id')
```
先别说对它做什么了，仅仅是一个变量声明，我得敲一会。。。接下来，要是我想点击它，让他藏起来我需要：
```
这个节点.onclick = function(){
    这个节点.style.display = 'none'
}
```
太费劲了，更别说更加复杂一点的操作了，比如我想看看这个节点有哪些兄弟元素：
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
或者想给这个节点添加多个Class
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
这样看来，这些方法可以重复使用，那么我再来封装一下，缺点啥？
命名空间：
```
window.newDom = {}
newDom.getSiblings(node)
newDom.addClass(node,{a:true,b:false,c:true})
```
为了方便使用，试试添加方法到Node原型上面：
```
Node.prototype.getSiblings = function(){
    ...
}
Node.prototype.addClass = function(){
    ...
}
```
这样我就可以直接这样使用：
```
node.getSiblings()
node.addClass('a','b','c')
```
但是方法并不能获取节点，再做一点改进，将所有的node改成this，建立连接（省略node参数）
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

Node.prototype.addClass = function(){
    classes.forEach((value)=> this.classList.add(value))
}
```
开起来貌似没啥毛病了，但是：
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