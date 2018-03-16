---
title: jQuery不过如此
date: 2018-03-15 16:54:54
tags: jQuery
---
JS这门语言有多神经质想必大家都已经心照不宣了，要说jQuery，那必须得从像老太婆又臭又长裹脚布一样的DOM原生API开始说起啊。
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
我想把节点放在前面，这样可以大大提高代码的可读性。为了方便使用，试试添加方法到Node原型上面：
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
但是方法并不能和节点建立联系，再做一点改进，将所有的node改成this（省略node参数）
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
看起来貌似没啥毛病了，但是，上面Node原型的方法，有可能在不经意间被覆盖。所以安全起见我们可以再重写一个Node构造函数，比如Node2
``` 
window.Node2 = function(node){
    ...
}
```
最后就可以直接使用：
```
var node2 = Node2(node) //node2存了新建构造函数的地址
node2.getSiblings()
node2.addClass('a','b','c')
```
等等，这个Node2真的和jQuery很像，既然这样，干脆就改成jQuery吧
```
function jQuery(node){
    return {
        element: node,
        getSiblings: function(){

        },
        addClass: function(){

        }
    }
}
let node = document.getElementById('这个节点')
let node2 = jQuery(node)
node2.getSiblings()
node2.addClass()
```
**这样看来，jQuery实际上就是一个构造函数，接受一个节点当作参数，返回一个新的对象。**
而这个对象上有我封装好的新的API（getSiblings,addClass...），说白了还是在调用DOM的API，但是代码却精简了不少。
现在我想看看这个节点的兄弟元素有哪些：
```
这个节点.getSiblings()
```
给这个节点添加多个class：
```
这个节点.addClass()
```
就搞定了。。。
但是jQuery更强大，它不仅仅可以传递node,还可以直接传递选择器去找到对应的元素，再试着往下写：
```
window.jQuery = function(nodeOrSelector){
    let node
    if(typeof nodeOrSelector === 'string'){
        node = document.querySelector(nodeOrSelector)//找到字符串对应的节点
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
不仅仅是这样，看看这个封装的jQuery能不能操作多个节点：
```
window.jQuery = function(nodeOrSelector){
    let nodes = {}
    if(typeof nodeOrSelector === 'String'){
        let temp = document.querySelectorAll(nodeOrSelector)//伪数组
        for(let i = 0; i < temp.length; i++){
            nodes[i] = temp[i]
        }
        nodes.length = temp.length
    }else if(nodeOrSelector instanceof Node){ //如果是一个节点，还是返回一个伪数组
        nodes = {
            0: nodeOrSelector,
            length: 1
        }
    }
    return {
        getSiblings: function(){
            ...
        },
        addClass: function(classes){
            classes.forEach((value)=>{
            for(let i = 0; i < nodes.length; i++){ //因为可能是多个元素添加class
                nodes[i].classList.add(value)
            }
        })
        }
    }
    return nodes
}
var node2 = jQuery('ul>li')
node2.addClass()
```
再试着添加几个常用API：
获取元素的内容：
```
node.getText = function(){
    var texts = []
    for(let i =0; i < nodes.length; i++){
        texts.push(nodes[i].textContent)
    }
    return texts    
}

var node2 = jQuery('ul>li')
node2.getText()
```
编辑元素内容：
```
nodes.setText = function(text){
    for(let i =0; i <nodes.length; i++){
        node[i].textContent = text
    }
}

var node2 = jQuery('ul>li')
node2.getText('hello word')
```
再把这两个新功能合并优化：
```
nodes.text = function(text){
    if(text === undefined){
        var texts = []
        for(let i = 0; i < nodes.length; i++){
            texts.push(nodes[i].textContent)
        }
        return texts
    }else{
        for(let i = 0; i < nodes.length; i++){
            nodes[i].textContent = text
        }
    }
}

var node2 = jQuery('ul>li')
node2.text('hello word')
```
如果想对被选择的好几个元素中的一个元素操作，就可以：
```
node2[0].classList.add('')
node2[1].text('hello word')
```
因为node2实际上就是一个对象：
```
var node2 = jQuery('ul > li')

//{0: li, 1: li, length: 2, addClass: f, text:f}
```

那么既然这个构造函数的名字能够被自定义，干脆一不做二不休直接改成`$`吧！
```
window.$ = jQuery
```
这个假的jQuery就被封装出来了，便宜好用多实惠，还能自己封装一些API上去，拒绝使用DOM裹脚布式API（开玩喜）！