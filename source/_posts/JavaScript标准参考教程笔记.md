---
title: JavaScript标准参考教程笔记
date: 2018-01-10 20:33:25
tags:
---
阅读阮一峰JavaScript标准参考教程的读书笔记，写下来，加深记忆。
# 语法
## 对象
### 生成对象
对象就是一组“键值对”（key-value）的集合，是一种无序的符合数据集合。
```
var obj = {
  foo: 'Hello',
  bar: 'World'
};
//第一个键值对是foo: 'Hello'，其中foo是“键名”（成员的名称），字符串Hello是“键值”（成员的值）
//键名与键值之间用冒号分隔
//两个键值对之间用逗号分隔
```
上面代码中，大括号定义了一个对象，被赋值给变量 `obj`，所以变量`obj`就指向一个对象。
### 键名
对象的所有键名都是字符串，所以加不加引号都可以。
日过键名是数值，会被自动转化为字符串。
```
var  obj = {
  1: 'A'，
  1e2: true
};
obj[1] //A
obj['1'] //A
```
上面代码中，对象`obj`的键名虽然看上去像数值，实际上被自动转成了字符串。
**如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符） 且也不是数字，则必须加上引号，否则会报错**
<!-- more -->
```
var obj = {
  1p: 'Hello World'
};
//报错
var obj = {
  '1p': 'Hello World',
  'h w': 'Hello World',
  'p+q': 'Hello World'
};
//不报错
```
不符合标识名条件的键名，必须加上引号。
对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为“方法”，它可以像函数那样调用。
```
var obj = {
  p: function(x){
    return 2 * x;
  }
};
obj.p(1)
//2
```
如果属性的值还是一个对象就形成了链式引用。
```
var o1 = {};
var o2 = {bar: 'hello'};

o1.foo = o2;
o1.foo.bar
//'hello'
```
上面代码中，对象`o1`的属性`foo`指向对象`o2`，就可以链式引用`o2`的属性。
属性可以动态创建，不必在对象声明时就指定。
```
var obj = {};
obj.foo = 123;
obj.foo
//123
```
上面代码中，直接对`obj`对象的`foo`属性赋值，结果就在运行时创建了`foo`属性。
### 对象的引用
如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有的变量。
如果取消某一个变量对于原对象的引用，不会影响到另一个变量。
```
var o1 = {};
var o2 = o1;

o1 = 1;
o2 // {}
```
上面代码中，`o1`和`o2`指向同一个对象，然后`o1`的值变为1，这时不会对`o2`产生影响，`o2`还是指向原来的那个对象。
但是，这种引用只局限于对象，如果两个变量指向同一个原始类型的值。那么，变量这时都是值的拷贝。
```
var x = 1;
var y = x;

x = 2;
y // 1
```
上面代码中，当`x`的值发生变化后，`y`的值并不变，这就表示`y`和`x`并不是指向同一个内存地址。
### 表达式还是语句
对象采用大括号表示，这导致了一个问题：如果行首是一个大括号，它到底时表达式还是语句？
```
{foo: 123}
```
JavaScript引擎读到上面这行代码，会发现可能有两种含义。第一种，这是一个表达式，表示一个包含`foo`属性的对象；第二种是，这是一个语句，表示一个代码区块，里面有一个`foo`标签，指向表达式123。
**为避免歧义，JavaScript规定，如果行首是大括号，一律解释为语句（代码块）。如果解释为表达式（对象），必须在大括号前加上圆括号。**
### 读取属性
读取对象属性，有两种方法，一种是使用点运算符，还有一种是使用方括号运算符。
```
var obj = {
  p: 'Hello World'
};

obj.p // "Hello World"
obj['p'] // "Hello World"
```
**如果使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理。**
```
var foo = 'bar';

var obj = {
  foo: 1,
  bar: 2
};

obj.foo  // 1
obj[foo]  // 2
```
方括号运算符内部还可以使用表达式。
数字键可以不加引号，因为会自动转成字符串。
**数值键名不能使用点运算符（因为会被当成小数点），只能使用方括号运算符。**
### 属性的赋值
点运算符和方括号运算符，不仅可以用来读取值，还可以用来赋值。
JavaScript允许属性的“后绑定”，也就是说，你可以在任意时刻新增属性，没必要在定义对象的时候就定义好属性。
```
var obj = { p: 1 };

// 等价于

var obj = {};
obj.p = 1;
```
### 查看所有属性
查看一个对象本身所有的属性，可以用`Object.keys`方法。
```
Object.keys(obj);
//obj为对象
```
### delete 命令
`delete`命令用于删除对象的属性，删除成功后返回`true`。
```
delete obj.p
//true
//删除obj对象的p属性
```
**删除一个不存在的属性，`delete`不报错，且返回`true`，所以不能根据`delete`命令的结果，认定某个属性是存在的。另，`delete`命令只能删除对象本身属性，无法删除继承的属性。**
（只有一种情况，`delete`命令返回`false`，那就是该元素存在，不得删除。）
### in 运算
`in`运算符用于检查对象是否包含某个属性（键名），如果包含就返回`true`，否则返回`false`。
```
var obj = { p: 1 };
'p' in obj // true
```
`in`运算符的一个问题是，它不能识别哪些属性是对象自身的，哪些是继承的。
### for...in 循环
`for...in`循环用来遍历一个对象的全部属性。
下面是一个使用`for...in`循环，提取对象属性名的例子。
```
var obj = {
  x: 1,
  y: 2
};
var props = [];
var i = 0;

for (var p in obj) {
  props[i++] = p
}

props // ['x', 'y']
```
**遍历的对象是所有可遍历的属性，会跳过不可遍历属性。（toString不可遍历）**
**不仅遍历自身的属性，还遍历继承的属性。**
结合`hasOwnProperty`方法使用，在循环内部判断一下，某个属性是否为自身属性。
```
obj.hasOwnProperty(key)
```
## 数组
### 定义
数组是按照次序排列的一组值。每个值的位置都有编号（从0开始），整个数组用方括号表示。
除了在定义时赋值，数组也可以先定义后赋值。
```
var arr = [];

arr[0] = 'a';
arr[1] = 'b';
arr[2] = 'c';
```
任何类型的数据，都可以放入数组。
```
var arr = [
  {a: 1},
  [1, 2, 3],
  function() {return true;}
];
```
如果数组的元素还是数组，就形成了多维数组。
```
var a = [[1, 2], [3, 4]];
a[0][1] // 2
a[1][1] // 4
```
### 数组的本质
本质上，数组属于一种特殊的对象。
```
typeof [1, 2, 3] // "object"
```
数组的特殊性体现在，它的键名是按照次序排列的一组整数（0，1，2。。。）。
**对于数值键名，不能使用点结构**
```
var arr = [1, 2, 3];
arr.0 // SyntaxError
```
上面代码中，arr.0的写法不合法，因为单独的数值不能作为标识符（identifier）。所以，数组成员只能用方括号arr[0]表示（方括号是运算符，可以接受数值）。
### length 属性
数组的`length`属性，返回数组成员数量。
只要是数组，就一定有`length`属性。**该属性是一个动态的值，等于键名中最大整数加上1。**
数组是一种动态数据结构，可以随时增减数组成员。
`length`属性是可写的。
```
var arr = [ 'a', 'b', 'c' ];
arr.length // 3

arr.length = 2;
arr // ["a", "b"]
//该组成员自动减少到length设置的值
```
清空数组的方法，可以将`length`属性设为0。
*值得注意的是，由于数组本质上是一种对象，所以可以为数组添加属性，但是这不影响length属性的值。*
### in运算符
检查某个键名是够存在的运算符`in`，适用于对象，也适用于数组。
### for...in 循环和数组的遍历
`for...in`循环不仅可以遍历对象，也可以遍历数组，毕竟数组只是一种特殊对象。
但是，`for...in`不仅会遍历数组所有数字键，还会遍历非数字键。
```
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```
所以不推荐使用`for...in`遍历数组。
可以考虑使用`for`循环或者`while`循环。
```
var a = [1, 2, 3];

// for循环
for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}

// while循环
var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}

var l = a.length;
while (l--) {
  console.log(a[l]);
}
```
数组的`forEach`方法也可以用来遍历数组。
```
var colors = ['red', 'green', 'blue'];
colors.forEach(function (color) {
  console.log(color);
});
// red
// green
// blue
```
### 数组的空位
当数组的某个位置是空元素，即两个逗号之间没有任何值，我们称该数组存在空位。
```
var a = [1, , 1];
a.length // 3
```
上面代码表明，数组的空位不影响length属性。
需要注意的是，如果最后一个元素后面有逗号，并不会产生空位。也就是说，有没有这个逗号，结果都是一样的。
数组的空位是可以读取的，返回undefined。
```
var a = [, , ,];
a[1] // undefined
```
如果是空位，使用数组的forEach方法、for...in结构、以及Object.keys方法进行遍历，空位都会被跳过(undefined不会)。
### 类数组对象
如果一个对象的所有键名都是正整数或零，并且有`length`属性，那么这个对象很像数组，语法上称为“类数组对象”。
遍历：可以将“类数组对象”转为真正额数组，然后直接调用`forEach`方法。
```
var arr = Array.prototype.slice.call('abc');
arr.forEach(function (chr) {
  console.log(chr);
});
// a
// b
// c
```
## 函数
函数是一段可以反复调用的代码块。
### 概述
#### 函数的声明
JavaScript有三种声明函数的方法。
（1）function 命令
（2）函数表达式（采用变量赋值的写法）
```
var print = function(s) {
  console.log(s);
};
//此时匿名函数称为函数表达式
```
（3）Function 构造函数
```
var add = new Function(
  'x',
  'y',
  'return x + y'
);

// 等同于
function add(x, y) {
  return x + y;
}
```
不推荐此方法，了解即可。
#### 函数重复声明
如果一个函数被多次声明，后面的声明就会覆盖前面的声明。
#### 圆括号运算符，return 语句和递归
调用函数时。要使用圆括号运算符。圆括号中加入函数参数。
```
function add(x, y) {
  return x + y;
}

add(1, 1) // 2
```
上面代码中，函数名后面紧跟一对圆括号，就会调用这个函数。

函数体内部的`return`语句，表示返回。JavaScript 引擎遇到`return`语句，就直接返回`return`后面的那个表达式的值，后面即使还有语句，也不会得到执行。也就是说，`return`语句所带的那个表达式，就是函数的返回值。`return`语句不是必需的，如果没有的话，该函数就不返回任何值，或者说返回`undefined`。

函数调用自身就是递归（recursion）。
```
//计算斐波那契数列
function fib(num) {
  if (num === 0) return 0;
  if (num === 1) return 1;
  return fib(num - 2) + fib(num - 1);
}

fib(6) // 8
//fib函数内部又调用了fib，计算得到斐波那契数列的第6个元素是8。
```
#### 函数名的提升
JavaScrip 引擎将函数名视同变量名，所以采用`function`命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。
```
f();

function f() {}
```
由于变量提升，函数`f`被提升到代码头部，所以不会报错。
**如果采用赋值语句定义函数，会报错**
```
f();
var f = function (){};
// TypeError: undefined is not a function
```
等同于
```
var f;
f();
f = function () {};
//调用f时，f只是被声明，未赋值，等于undefined
```
**所以如果同时采用`function`命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义。**
#### 不能在条件语句总声明函数
### 函数的属性和方法
#### name 属性
函数的name属性返回紧跟在function关键字之后的那个函数名。
```
function f1() {}
f1.name // 'f1'
//函数的name属性总是返回紧跟在function关键字之后的那个函数名
```
#### length 属性
函数的`length`属性返回函数预期传入的参数个数（定义的参数个数）。
```
function f(a, b) {}
f.length // 2
```
#### toString()
函数的`toString()`方法返回一个字符串，内容是函数源码。
```
function f() {/*
  这是一个
  多行注释
*/
  a();
  b();
  c();
}

f.toString()
// function f() {/*
//   这是一个
//   多行注释
//*/
//  a();
//  b();
//  c();
// }
```
### 函数作用域
#### 定义
作用域指的是变量存在的范围。
全局作用域：变量在整个程序中一直存在，所有地方都可以读取（在函数外部声明）。
函数作用域：变量只在函数内部存在（在函数内部定义）。
块级作用域
**函数内部定义的变量，会在该作用域内覆盖同名全局变量。**
```
var v = 1;

function f(){
  var v = 2;
  console.log(v);
}

f() // 2
v // 1
```
**注意，对于`var`命令来说，局部变量只能在函数内部声明，在其他区块中声明，一律都是全局变量。**
```
if (true) {
  var x = 5;
}
console.log(x);  // 5
```
上面代码中，变量`x`在条件判断区块之中声明，结果就是一个全局变量，可以在区块之外读取。
### 函数内部的变量提升
与全局作用域一样，函数作用域内部也会产生“变量提升”现象。`var`命令声明的变量，不管在什么位置，变量声明都会被提升到函数体头部。
```
function foo(x) {
  if (x > 100) {
    var tmp = x - 100;//可以理解为这一区块声明的全局变量
  }//在这个区块又成为局部变量，因为在函数体中
}

// 等同于
function foo(x) {
  var tmp;
  if (x > 100) {
    tmp = x - 100;
  };
}
```
#### 函数本身的作用域
函数本身也是一个值，也有自己的作用域。它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在作用域无关。
```
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f() // 1
```
上面代码中，函数`x`是在函数f的外部声明的，所以它的作用域绑定外层，内部变量`a`不会到函数`f`体内取值，所以输出`1`，而不是`2`。
**函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。**
**同样，函数体内声明的函数，作用域绑定函数体内部。**
### 参数
#### 概述
函数运行时，有时候需要提供外部数据，不同的外部数据会得到不同结果，这种外部数据就叫做参数。
```
function square(x) {
  return x * x;
}

square(2) // 4
square(3) // 9
```
参数可省略。
```
function f(a, b) {
  return a;
}

f(1, 2, 3) // 1
f(1) // 1
f() // undefined

f.length // 2
```
#### 同名参数
如果有同名参数，则取最后出现的那个值。
```
function f(a, a) {
  console.log(a);
}

f(1, 2) // 2
```
调用函数`f`时，没有提供第二个参数，`a`的值就变成了`undefined`。
```
function f(a, a) {
  console.log(a);
}

f(1) // undefined
```
#### arguments 对象
由于 JavaScript 允许函数有不定数目的参数。所以需要一种机制，可以在函数体内部读取所有参数，这就是`arguments`对象。
`arguments`对象包含了函数运行时的所有参数，`arguments[0]`就是第一个参数，`arguments[1]`就是第二个参数，以此类推。
**这个对象只有在函数体内部才能使用。**
```
var f = function (one) {
  console.log(arguments[0]);
  console.log(arguments[1]);
  console.log(arguments[2]);
}

f(1, 2, 3)
// 1
// 2
// 3
```
通过`arguments`对象的`length`属性，可以判断函数调用时到底带几个参数。
```
function f() {
  return arguments.length;
}

f(1, 2, 3) // 3
f(1) // 1
f() // 0
```
将`arguments`转为真正的数组
```
//slice方法
var args = Array.prototype.slice.call(arguments);

// 逐一填入数组
var args = [];
for (var i = 0; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```
### eval命令
eval命令的作用是，将字符串当作语句执行。
（此处不做过多介绍）
## 运算符
### 加法运算符
#### 基本规则
- 数值求和
- 布尔值转数值求和
- 字符串拼接

运算子（数值，字符串）的不同，导致了不同的语法行为，这种现象称为“重载”（overload）。
加法运算符存在重载，需注意：
```
'3' + 4 + 5 // "345"
3 + 4 + '5' // "75"
```
由于由左到右运算，字符串的位置不同会导致不同的结果。
**除了加法运算符，其他算术运算符（比如减法、除法和乘法）都不会发生重载。它们的规则是：所有运算子一律转为数值，再进行相应的数学运算。**
#### 对象的相加
如果运算子是对象，必须先转成原始类型的值，然后再相加。
```
var obj = { p: 1 };
obj + 2 // "[object Object]2"
```
对象转原始类型的值：
1. 调用对象valueOf方法（返回对象自身）
```
var obj = { p: 1 };
obj.valueOf() // { p: 1 }
```
2. 调用对象toString方法（转为字符串）
```
var obj = { p: 1 };
obj.valueOf().toString() // "[object Object]"
//对象的toString方法默认返回[object Object]
```
这里有一个特例，如果运算子是一个Date对象的实例，那么会优先执行toString方法。
```
var obj = new Date();
obj.valueOf = function () { return 1 };
obj.toString = function () { return 'hello' };

obj + 2 // "hello2"
```
toString方法优先执行。
### 算数运算符
#### 余数运算符
运算结果的正负号由第一个运算子的正负号决定。
```
-1 % 2 // -1
1 % -2 // 1
```
为了得到负数的正确余数值，可以先使用绝对值函数.
```
function isOdd(n) {
  return Math.abs(n % 2) === 1;
}
isOdd(-5) // true
isOdd(-4) // false
```
#### 自增自减运算符
自增和自减运算符有一个需要注意的地方，就是放在变量之后，会先返回变量操作前的值，再进行自增/自减操作；放在变量之前，会先进行自增/自减操作，再返回变量操作后的值。
```
var x = 1;
var y = 1;

x++ // 1
++y // 2
```
上面代码中，x是先返回当前值，然后自增，所以得到1；y是先自增，然后返回新的值，所以得到2。
#### 数值运算符，负数值运算符
数值运算符的作用在于可以将任何值转为数值（与Number函数的作用相同）。
```
+true // 1
+[] // 0
+{} // NaN
```
负数值运算符（-），也同样具有将一个值转为数值的功能，只不过得到的值正负相反。连用两个负数值运算符，等同于数值运算符。
```
var x = 1;
-x // -1
-(-x) // 1
```
**数值运算符号和负数值运算符，都会返回一个新的值，而不会改变原始变量的值。**
### 比较运算符
#### 字符串的比较
字符串按照字典顺序进行比较。
```
'cat' > 'dog' // false
'cat' > 'catalog' // false
```
JavaScript 引擎内部首先比较首字符的 Unicode 码点。如果相等，再比较第二个字符的 Unicode 码点，以此类推。
#### 非字符串的比较
##### 原始类型的值
两个原始类型的值的比较，除了相等运算符（==）和严格相等运算符（===），其他比较运算符都是先转成数值再比较。

任何值（包括NaN本身）与NaN比较，返回的都是false。
##### 对象
如果运算子是对象，会转为原始类型的值，再进行比较。
#### 严格相等运算符
两个复合类型（对象、数组、函数）的数据比较时，不是比较它们的值是否相等，而是比较它们是否指向同一个地址。
```
var v1 = {};
var v2 = v1;
v1 === v2 // true
```
**注意，对于两个对象的比较，严格相等运算符比较的是地址，而大于或小于运算符比较的是值**

undefined和null与自身严格相等。
undefined和null与其他类型的值比较时，结果都为false，它们互相比较时结果为true。

由于变量声明后默认值是undefined，因此两个只声明未赋值的变量是相等的。
### 布尔运算符
#### 取反运算符
对于非布尔值，取反运算符会将其转为布尔值。可以这样记忆，以下六个值取反后为true，其他值都为false。
```
!undefined // true
!null // true
!false //true
!0 // true
!NaN // true
!"" // true

!54 // false
!'hello' // false
![] // false
!{} // false
```
不管什么类型的值，经过取反运算后，都变成了布尔值。
#### 且运算符（&&）
如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值。
#### 或运算符（||）
如果第一个运算子的布尔值为true，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为false，则返回第二个运算子的值。

或运算符常用于为一个变量设置默认值。
```
function saveText(text) {
  text = text || '';
  // ...
}

// 或者写成
saveText(this.text || '')
```
上面代码表示，如果函数调用时，没有提供参数，则该参数默认设置为空字符串。
#### 三元运算符（?:）
与`if...else...`的区别：
if...else是语句，没有返回值；三元条件表达式是表达式，具有返回值。
所以，在需要返回值的场合，只能使用三元条件表达式，而不能使用if..else。
### 位运算
### 其他运算符
### 运算顺序
#### 优先级
记住所有运算符优先级，没有必要，查阅即可。
## 数据类型转换
### 强制转换
强制转换主要指使用Number、String和Boolean三个函数，手动将各种类型的值，分布转换成数字、字符串或者布尔值。
#### Number()
Number函数将字符串转为数值，要比parseInt函数严格很多。基本上，只要有一个字符无法转成数值，整个字符串就会被转为NaN。
```
parseInt('42 cats') // 42
Number('42 cats') // NaN
```
上面代码中，parseInt逐个解析字符，而Number函数整体转换字符串的类型。
Number方法的参数是对象时，将返回NaN，除非是包含单个数值的数组。
#### String()
String方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。
```
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```
#### Boolean()
它的转换规则相对简单：除了以下五个值的转换结果为false，其他的值全部为true。
```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```
**所有对象的布尔值都是true**
### 自动转换
- 不同类型的数据互相运算
```
123 + 'abc' // "123abc"
```
- 非布尔值类型的数据求布尔值
```
if ('abc') {
  console.log('hello')
}  // "hello"
```
- 对非数值类型的值使用一元运算符（即+和-）
```
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```
# 标准库
## Object对象
### 概述
Object对象的原生方法分成两类：Object本身的方法与Object的实例方法。
- Object对象本身的方法
```
Object.print = function (o) { console.log(o) };
```
- Object的实例方法
```
//定义在Object原型对象Object.prototype上的方法
Object.prototype.print = function () {
  console.log(this);
};

var obj = new Object();
obj.print() // Object
```
obj直接继承了Object.prototype的属性和方法。也就是说，obj对象的print方法实质上就是调用Object.prototype.print方法。

**凡是定义在Object.prototype对象上面的属性和方法，将被所有实例对象共享**
### Object()
```
var obj = Object();
obj instanceof Object // true
```
obj instanceof Object返回true，就表示obj对象是Object的实例。
### Object 构造函数
```
var obj = new Object();
```
### Object 的静态方法
遍历属性
- `Object.keys()`
- `Object.getOwnPropertyNames()`

原型链相关
- `Object.create()`
- `Object.getPrototypeOf()`
其他方法这里不做介绍

### Object 的实例方法
定义在Object.prototype对象的方法，所有Object的实例对象都继承了这些方法。
主要有以下六个
- `Object.prototype.valueOf()`：返回当前对象对应的值。
- `Object.prototype.toString()`：返回当前对象对应的字符串形式。
- `Object.prototype.toLocaleString()`：返回当前对象对应的本地字符串形式。
- `Object.prototype.hasOwnProperty()`：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
- `Object.prototype.isPrototypeOf()`：判断当前对象是否为另一个对象的原型。
- `Object.prototype.propertyIsEnumerable()`：判断某个属性是否可枚举。

**Object.prototype.toString方法返回对象的类型字符串，因此可以用来判断一个值的类型。**
```
var obj = {};
Object.prototype.toString.call(obj) // "[object Object]"
//第二个Object表示该值的构造函数
```
**也就是说，Object.prototype.toString可以看出一个值到底是什么类型。**
```
Object.prototype.toString.call(2) // "[object Number]"
Object.prototype.toString.call('') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(Math) // "[object Math]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call([]) // "[object Array]"
```
**利用这个特性，可以写出一个比`typeof`更准确的类型判断函数。**
```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};
type({}); // "object"
type([]); // "array"
type(5); // "number"
```
## Array 对象
### 构造函数
生成新数组
```
var arr = new Array(2);
arr.length // 2
arr // [ undefined x 2 ]
```
上面代码中，Array构造函数的参数2，表示生成一个两个成员的数组，每个位置都是空值。

Array构造函数有一个很大的问题，就是不同的参数，会导致它的行为不一致。
因此，不建议使用它生成新数组，直接使用数组字面量是更好的做法。
```
// bad
var arr = new Array(1, 2);

// good
var arr = [1, 2];
```
### Arra.isArray()
判断一个值是否为数组
```
var a = [1, 2, 3];
Array.isArray(a) // true
```
### Array实例的方法
- `valueOf()`：返回数组本身
```
var a = [1, 2, 3];
a.valueOf() // [1, 2, 3]
```
- `toString()`：返回数组的字符串形式
```
var a = [1, 2, 3];
a.toString() // "1,2,3"

```
- `push()`：在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度（该方法会改变原数组）
- `pop()`：删除数组的最后一个元素（该方法会改变原数组）
- `join()`：以参数作为分隔符，将所有数组成员组成一个字符串返回（如果不提供参数，默认用逗号分隔）
```
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"
```
- `concat()`：多个数组的合并，将新数组的成员，添加到原数组成员的后部，然后返回一个新数组（原数组不变）
```
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]
```
- `shift()`：删除数组的第一个元素，并返回该元素（该方法会改变原数组）
- `unshift()`：在数组的第一个位置添加元素，并返回添加新元素后的数组长度（该方法会改变原数组）
- `reverse()`：颠倒数组中元素的顺序，返回改变后的数组（该方法将改变原数组)
- `slice()`：提取原数组的一部分，返回一个新数组
```
// 格式
arr.slice(start_index, stop_index);

// 用法
var a = ['a', 'b', 'c'];

a.slice(0) // ["a", "b", "c"]
a.slice(1) // ["b", "c"]
a.slice(1, 2) // ["b"]
a.slice(2, 6) // ["c"]
a.slice() // ["a", "b", "c"]
```
如果slice方法的参数是负数，则表示倒数计算的位置。
```
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
```
- `splice()`：删除原数组的一部分成员，并可以在被删除的位置添加入新的数组成员，返回值是被删除的元素（该方法会改变原数组）

splice的第一个参数是删除的起始位置，第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。
```
// 格式
arr.splice(index, count_to_remove, addElement1, addElement2, ...);

// 用法
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2, 1, 2) // ["e", "f"]
a // ["a", "b", "c", "d", 1, 2]
```
起始位置如果是负数，就表示从倒数位置开始删除。
```
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(-4, 2) // ["c", "d"]
```
如果只是单纯地插入元素，splice方法的第二个参数可以设为0。
```
var a = [1, 1, 1];

a.splice(1, 0, 2) // []
a // [1, 2, 1, 1]
```
如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。
```
var a = [1, 2, 3, 4];
a.splice(2) // [3, 4]
a // [1, 2]
```
- `sort()`：对数组成员进行排序，默认是按照字典顺序排序（原数组将被改变）
```
['d', 'c', 'b', 'a'].sort()
// ['a', 'b', 'c', 'd']
```
**sort方法不是按照大小排序，而是按照对应字符串的字典顺序排序**
```
[10111, 1101, 111].sort()
// [10111, 1101, 111]
```
如果想让sort方法按照自定义方式排序，可以传入一个函数作为参数，表示按照自定义方法进行排序。
```
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
```
- `map()`：对数组的所有成员依次调用一个函数，根据函数结果返回一个新数组
```
var numbers = [1, 2, 3];

numbers.map(function (n) {
  return n + 1;
});
// [2, 3, 4]

numbers
// [1, 2, 3]
```
如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位。
- `forEach()`：遍历数组的所有成员，不返回值，只用来操作数据
```
function log(element, index, array) {
  console.log('[' + index + '] = ' + element);
}

[2, 5, 9].forEach(log);
// [0] = 2
// [1] = 5
// [2] = 9
```
forEach方法不会跳过undefined和null，但会跳过空位。
- `filter`：filter方法的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。
```
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```
- `some()`，`every()`：判断数组成员是否符合某种条件
`some`方法是只要有一个数组成员的返回值是`true`，则整个`some`方法的返回值就是`true`，否则`false`。
`every`方法则是所有数组成员的返回值都是`true`，才返回`true`，否则`false`。
- `reduce()`，`reduceRight()`
- `indexOf()`：返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1
- `lastIndexOf()`：返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1
**注意，如果数组中包含NaN，这两个方法不适用，即无法确定数组成员是否包含NaN。**
```
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```
## 包装对象
### 包装对象的定义
所谓“包装对象”，就是使用构造函数（new）将以下三个原始类型的值转为对象。
```
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);
```
生成的对象与原始值的类型不同。这用typeof运算符就可以看出来。
```
typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```
通过设计包装对象，可以方便原始类型的值调用特定的方法。

Number、String和Boolean如果不作为构造函数调用（即调用时不加new），常常用于将任意类型的值转为数值、字符串和布尔值。
```
Number(123) // 123
String('abc') // "abc"
Boolean(true) // true
```
### 包装对象实例的方法
包装对象实例可以使用Object对象提供的原生方法，主要是`valueOf`和`toString`方法。
- `valueOf`：返回包装对象实例对应的原始类型的值。
```
new Number(123).valueOf()  // 123
new String("abc").valueOf() // "abc"
new Boolean("true").valueOf() // true
```
- `toString()`：返回实例对应的字符串形式
```
new Number(123).toString() // "123"
new String("abc").toString() // "abc"
new Boolean("true").toString() // "true"
```
### 原始类型的自动转换
比如，字符串调用`length`属性
```
'abc'.length // 3
```
JavaScript引擎自动将其转为包装对象，在这个对象上调用length属性。

除了`valueOf`和`toString`方法，还包括包装对象各自定义在原型上的方法。
```
'abc'.charAt === String.prototype.charAt
// true
//定义在String对象原型上的方法
```
### 自定义方法
包装对象还可以在原型上添加自定义方法和属性，供原始类型的值直接调用。
```
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double()
// 246
```
**这种自定义方法和属性的机制，只能定义在包装对象的原型上，如果直接对原始类型的变量添加属性，则无效。**
```
var s = 'abc';

s.p = 123;
s.p // undefined
//包装对象是自动生成的，赋值后自动销毁
```
### Boolean 对象
主要用于生成布尔值的包装对象的实例。
```
var b = new Boolean(true);

typeof b // "object"
b.valueOf() // true
```
上面代码的变量b是一个Boolean对象的实例，它的类型是对象，值为布尔值true。（此方法几乎无人使用，了解原理即可）
```
//此方法更加直观清晰
var b = true;
```
## Number 对象
Number对象是数值对应的包装对象，可以作为构造函数使用，也可以作为工具函数使用。
- 构造函数的使用，生成值为数值的对象
```
var n = new Number(1);
typeof n // "object"
//返回一个值为1的对象
```
- 工具函数的使用，将任何类型的值转为数值
```
Number(true) // 1
```
### Number 对象实例的方法
- `Number.prototype.toString()`：将数值转为字符串
```
(10).toString() // "10"
等同于
10['toString'](2) // "1010"
```
`toString`接受一个参数，表示输出的进制（默认十进制）。
```
(10).toString(2) // "1010"
(10).toString(8) // "12"
(10).toString(16) // "a"
```
- `Number.prototype.toFixed()`：将一个数转为指定位数的小数，返回这个小数对应的字符串
```
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01" 
```
10.005就不用放在括号里，第一个点被解释为小数点，第二个点就只能解释为点运算符。
**参数为指定的小数位数，有效范围为0到20，超出这个范围将抛出RangeError错误。**
- `Number.prototype.toExponential()`：将一个数转为科学计数法形式
- `Number.prototype.toPrecision()`：将一个数转为指定位数的有效数字

### 自定义方法
与其他对象一样，Number.prototype对象上面可以自定义方法，被Number的实例继承。
```
Number.prototype.add = function (x) {
  return this + x;
};
8['add'](2) // 10
等同于
(8).add(2) // 10
```
上面代码为Number对象实例定义了一个add方法。

由于add方法返回的还是数值，所以可以链式运算。
```
Number.prototype.subtract = function (x) {
  return this - x;
};

(8).add(2).subtract(4)
// 6
```
在Number对象的原型上部署了iterate方法，将一个数值自动遍历为一个数组。
```
Number.prototype.iterate = function () {
  var result = [];
  for (var i = 0; i <= this; i++) {
    result.push(i);
  }
  return result;
};

(8).iterate()
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```
**数值的自定义方法，只能定义在它的原型对象Number.prototype上面，数值本身是无法自定义属性的。**
```
var n = 1;
n.x = 1;
n.x // undefined
```
## String 对象
String对象是JavaScript原生提供的三个包装对象之一，用来生成字符串的包装对象。
```
var s1 = 'abc';
var s2 = new String('abc');

typeof s1 // "string"
typeof s2 // "object"

s2.valueOf() // "abc"
```
实际上，字符串的包装对象是一个类似数组的对象（即很像数组，但是实质上不是数组）。
```
new String("abc")
// String {0: "a", 1: "b", 2: "c", length: 3}
```
String对象的工具方法：
```
String(true) // "true"
String(5) // "5"
```
### Strng.fromCharCode()
该方法的参数是一系列Unicode码点，返回对应的字符串。
```
String.fromCharCode(104, 101, 108, 108, 111)
// "hello"
```
### 实例对象的属性和方法
- `length`属性：length属性返回字符串的长度
- `charAt()`：charAt方法返回指定位置的字符，参数是从0开始编号的位置
```
var s = new String('abc');

s.charAt(1) // "b"
s.charAt(s.length - 1) // "c"
```
这个方法完全可以用数组下标代替
```
'abc'.charAt(1) // "b"
'abc'[1] // "b"
```
如果参数为负数，或大于等于字符串的长度，charAt返回空字符串。
- `charCodeAt()`：返回给定位置字符的Unicode码点（十进制表示），相当于String.fromCharCode()的逆操作
```
'abc'.charCodeAt(1) // 98
```
- `concat()`：连接两个字符串，返回一个新字符串，不改变原字符串
- `slice()`：用于从原字符串取出子字符串并返回，不改变原字符串
- `substring()`：用于从原字符串取出子字符串并返回，不改变原字符串（不建议使用，建议使用`slice()`）
- `substr()`：用于从原字符串取出子字符串并返回，不改变原字符串
- `indexOf()，lastIndexOf()`：用于确定一个字符串在另一个字符串中的位置，都返回一个整数，表示匹配开始的位置
- `trim()`：用于去除字符串两端的空格，返回一个新字符串，不改变原字符串
- `toLowerCase()，toUpperCase()`：用于将一个字符串全部转为小写，大写，返回一个新字符串，不改变原字符串
```
'Hello World'.toLowerCase()
// "hello world"

'Hello World'.toUpperCase()
// "HELLO WORLD"

String.prototype.toUpperCase.call(['a', 'b', 'c'])
// 'A,B,C'
```
- `localeCompare()`：用于比较两个字符串
```
'apple'.localeCompare('banana')
// -1

'apple'.localeCompare('apple')
// 0
```
上面方法返回一个整数，如果小于0，表示第一个字符串小于第二个字符串；如果等于0，表示两者相等；如果大于0，表示第一个字符串大于第二个字符串。
- `match()`：用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串
```
'cat, bat, sat, fat'.match('at') // ["at"]
'cat, bat, sat, fat'.match('xt') // null
```
- `search()`：用法等同于match，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回-1
```
'cat, bat, sat, fat'.search('at') // 1
```
- `replace()`：用于替换匹配的子字符串，一般情况下只替换第一个匹配（除非使用带有g修饰符的正则表达式）
```
'aaa'.replace('a', 'b') // "baa"
```
- `split()`：按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组
```
'a|b|c'.split('|') // ["a", "b", "c"]
'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
```
split方法还可以接受第二个参数，限定返回数组的最大成员数。
```
'a|b|c'.split('|', 1) // ["a"]
```
## Math 对象
`Math`是JavaScript的内置对象，提供一系列数学方法。
```
new Math()
// TypeError: object is not a function
```
**`Math` 不能当作构造函数使用**
`Math` 对象提供一些只读数字常数
```
<!--
Math.E：常数e
Math.LN2：2的自然对数
Math.LN10：10的自然对数
Math.LOG2E：以2为底的e的对数
Math.LOG10E：以10为底的e的对数
Math.PI：常数Pi
Math.SQRT1_2：0.5的平方根
Math.SQRT2：2的平方根
-->
```
### 方法
`Math` 对象提供一些数学方法
```
<!--
Math.abs()：绝对值
Math.ceil()：向上取整
Math.floor()：向下取整
Math.max()：最大值
Math.min()：最小值
Math.pow()：指数运算
Math.sqrt()：平方根
Math.log()：自然对数
Math.exp()：e的指数
Math.round()：四舍五入
Math.random()：随机数
-->
```
- `Math.random()`：返回0到1之间的一个伪随机数，可能等于0，但是一定小于1
```
Math.random() // 0.7151307314634323
```
可以写一个任意范围的随机数生成函数
```
function getRandom(min, max){
    return Math.random() * (max - min) + min
}
getRandom(1.5, 6.5)
// 2.4942810038223864
```
还可以写成
```
function getRandom(min, max){
    return Math.floor(Math.radom() * (max -min + 1)) + min
}
getRandom(1, 6)
//5
```
返回一个随机字符的函数
```
function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i=0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"
```
### 三角函数方法
`Math` 对象还提供一系列三角函数方法。
```
<!--
Math.sin()：返回参数的正弦
Math.cos()：返回参数的余弦
Math.tan()：返回参数的正切
Math.asin()：返回参数的反正弦（弧度值）
Math.acos()：返回参数的反余弦（弧度值）
Math.atan()：返回参数的反正切（弧度值）
-->
```
## Date 对象
`Date`对象是 JavaScript 提供的日期和时间的操作接口。
![作为普通函数直接调用](https://i.loli.net/2018/01/20/5a631288266b7.png)
**无论有没有参数，直接调用`Date`总是返回当前时间**
### new Date()
`Date`还可以作为构造函数使用。
```
var today = new Date();
```
### 日期的运算
类型转换时，`Date`对象的实例如果转为数值，则等于对应的毫秒数；如果转为字符串，则等于对应的日期字符串。（相减返回毫秒数，相加返回拼接字符串）
### `Date`对象的静态方法
- `Date.now()`：返回当前距离1970年1月1日 00:00:00 UTC 的毫秒数
- `Date.parse()`：解析日期字符串，返回距离1970年1月1日 00:00:00的毫秒数
- `Date.UTC`：返回 UTC 时间（）世界标准时间。

### [Date 实例对象的方法](http://javascript.ruanyifeng.com/stdlib/date.html)
## RegExp对象
正则表达式（regular expression），通常用来匹配文本。比如，Email地址。
新建正则表达式有两种方法：
-  使用字面量，以斜杠开始和结束
```
var regex = /xyz/;
```
- RegExp 构造函数
```
var regex = new RegExp('xyz');
```
RegExp 构造函数接收第二个参数，修饰符`i`。
```
var regex = new RegExp('xyz', "i");
// 等价于
var regex = /xyz/i;
```
以上两种写法有一个细微区别。采用字面量写法，正则对象在代码载入时生成；采用构造函数写法，正则对象在代码运行时生成。（通常使用字面量写法）
### 正则对象的属性和方法
#### 属性
- 修饰符
```
var r = /abc/igm;

r.ignoreCase // true
r.global // true
r.multiline // true
```
- lastIndex：返回下一次开始搜索的位置。
- source：返回正则表达式字符串形式
```
var r = /abc/igm;

r.lastIndex // 0
r.source // "abc"
```
#### test()
返回一个布尔值，表示当前模式是否能匹配参数字符串。
```
/cat/.test('cats and dogs') // true
```
 如果正则表达式带有`g`修饰符，则每一次`test`方法都从上一次结束的位置向后匹配。
```
var r = /x/g;
var s = '_x_x';

r.lastIndex // 0
r.test(s) // true

r.lastIndex // 2
r.test(s) // true
```
上面代码每一次搜索的位置都是上一次匹配的后一个位置。

带有`g`修饰符时，可以通过正则对象的`lastIndex`属性指定开始搜索的位置。
```
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false
```
上面代码指定从字符串的第五个位置开始搜索，这个位置没有字符，所以返回 `false`。

**如果正则模式是一个空字符串，则匹配所有字符串。**
```
new RegExp('').test('abc')
// true
```
#### exce()
将匹配结果返回一个数组，否则返回`null`。
```
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

r1.exec(s) // ["x"]
r2.exec(s) // null
```
如果正则对象是一个空字符串，则`exec`方法会匹配成功，但返回的也是空字符串。
```
var r1 = new RegExp('');
var a1 = r1.exec('abc');
a1 // ['']
a1.index // 0
r1.lastIndex // 0
```
### 字符串对象的方法
#### String.prototype.match()
返回一个数组，成员是所有匹配的字符串。
```
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

s.match(r1) // ["x"]
s.match(r2) // null
```
该方法与`exce`方法非常类似。

如果正则表达式带有`g`修饰符，则与`exce`不同，会一次性返回所有匹配成功的结果。
```
var s = 'abba';
var r = /a/g;

s.match(r) // ["a", "a"]
r.exec(s) // ["a"]
```
#### String.prototype.search()
返回第一个匹配结果在整个字符串中的位置。若无匹配，返回`-1`。
```
'_x_x'.search(/x/)
// 1
```
#### String.prototype.replace()
按照给定的正则表达式进行替换，返回替换后的字符串。
```
str.replace(search, replacement)
```
搜索模式如果不加`g` 修饰符，就替换第一个匹配成功的值，否则替换所有匹配成功的值。
```
'aaa'.replace('a', 'b') // "baa"
'aaa'.replace(/a/g, 'b') // "bbb"
```
消除字符串首尾两端空格。
```
var str = '  #id div.class  ';

str.replace(/^\s+|\s+$/g, '')
// "#id div.class"
```
replace方法的第二个参数还可以是一个函数，将每一个匹配内容替换为函数返回值。
```
'3 and 5'.replace(/[0-9]+/g, function(match){
  return 2 * match;
})
// "6 and 10"
```
#### String.prototype.split()
按照正则规则分割字符串，返回数组。
接受两个参数，分隔规则和返回数组的最大成员数。
```
// 非正则分隔
'a,  b,c, d'.split(',')
// [ 'a', '  b', 'c', ' d' ]

// 正则分隔，去除多余的空格
'a,  b,c, d'.split(/, */)
// [ 'a', 'b', 'c', 'd' ]

// 指定返回数组的最大成员
'a,  b,c, d'.split(/, */, 2)
[ 'a', 'b' ]
```
上面代码使用正则表达式，去除了子字符串的逗号后面的空格。
### [匹配规则](http://javascript.ruanyifeng.com/stdlib/regexp.html)
#### 修饰符
表示模式的附加规则，放在正则模式的最尾部。
修饰符可单个使用，也可多个一起使用。
- `g`修饰符：表示全局匹配，匹配全部符合条件的结果。
- `i`修饰符：正则对象默认区分大小写，加上`i`修饰符后表示忽略大小写。
```
/abc/.test('ABC') // false
/abc/i.test('ABC') // true
```
- `m`修饰符：表示多行模式。默认情况`^`和`$`匹配字符串的开始和结尾处，加上`m`修饰符后，`^`和`$`还会匹配行首行尾（识别换行符）。
 ```
/world$/.test('hello world\n') // false
/world$/m.test('hello world\n') // true
```
看另一个例子
```
/^b/m.test('a\nb') // true
```
上面代码要求匹配行首是`b`，加上`m`修饰符，可识别换行符，`b`在行首，返回`true`。
#### 组匹配
正则表达式的括号表示分组匹配，括号中的模式可以用来匹配分组的内容。
```
/fred+/.test('fredd') // true
/(fred)+/.test('fredfred') // true
```
上面代码，第一个匹配重复字母`d`，第二个表示匹配"fred"这个词。

匹配网页标签：
```
var tagName = /<([^>]+)>[^<]*<\/\1>/;

tagName.exec("<b>bold</b>")[1]
// 'b'
```
上面代码中，圆括号匹配尖括号之中的标签，而\1就表示对应的闭合标签。

略加修改，就能捕获带有属性的标签
```
var html = '<b class="hello">Hello</b><i>world</i>';
var tag = /<(\w+)([^>]*)>(.*?)<\/\1>/g;

var match = tag.exec(html);

match[1] // "b"
match[2] // "class="hello""
match[3] // "Hello"

match = tag.exec(html);

match[1] // "i"
match[2] // ""
match[3] // "world"
```
## JSON 对象
JSON 格式：
- 书写简单，一目了然
- 符合 JavaScript 原生语法，由解释器引擎直接处理。
### JSON.stringify()
用于将一个值转为字符串。该字符串符合
 JSON 格式，能够被`JSON.parse`方法还原。
```
JSON.stringify('abc') // ""abc""
JSON.stringify(1) // "1"
JSON.stringify(false) // "false"
JSON.stringify([]) // "[]"
JSON.stringify({}) // "{}"

JSON.stringify([1, "false", false])
// '[1,"false",false]'

JSON.stringify({ name: "张三" })
// '{"name":"张三"}'
```
**对于原始类型的字符串，转换结果会带双引号**
```
JSON.stringify('foo') === "foo" // false
JSON.stringify('foo') === "\"foo\"" // true
```
上面代码中，字符串foo，被转成了""foo""。这是因为将来还原的时候，双引号可以让 JavaScript 引擎知道，foo是一个字符串，而不是一个变量名。

如果原始对象中，有一个成员值是`undefined`、函数或 XML
对象，这个成员会被过滤。
```
var obj = {
  a: undefined,
  b: function () {}
};

JSON.stringify(obj) // "{}"
```

如果数组成员是`undefined`、函数或 XML 对象，则这些值被转成`null`。
```
var arr = [undefined, function () {}];
JSON.stringify(arr) // "[null,null]"
```
正则对象会被转成空对象。
```
JSON.stringify(/foo/) // "{}"
```
#### JSON.stringify() 的第二个参数
可以是一个函数，更改`JSON.stringify`的默认行为
```
function f(key, value) {
  if (typeof value === "number") {
    value = 2 * value;
  }
  return value;
}

JSON.stringify({ a: 1, b: 2 }, f)
// '{"a": 2,"b": 4}'
```
递归处理所有的键
```
var o = {a: {b: 1}};

function f(key, value) {
  console.log("["+ key +"]:" + value);
  return value;
}

JSON.stringify(o, f)
// []:[object Object]
// [a]:[object Object]
// [b]:1
// '{"a":{"b":1}}'
```
上面代码中，对象o一共会被f函数处理三次。第一次键名为空，键值是整个对象o；第二次键名为a，键值是{b: 1}；第三次键名为b，键值为1。
### toJSON 方法
将正则对象自动转为字符串（JSON.stringify默认不能转换正则对象，但是设置了toJSON方法以后，就可以转换正则对象了）
```
var obj = {
  reg: /foo/
};

// 不设置 toJSON 方法时
JSON.stringify(obj) // "{"reg":{}}"

// 设置 toJSON 方法时
RegExp.prototype.toJSON = RegExp.prototype.toString;
JSON.stringify(/foo/) // ""/foo/""
```
在正则对象的原型上部署`toJSON`方法，将其指向`toString`方法。因此当遇到转换成 JSON 时，就会先调用`toJSON`转为字符串，在被`JSON.stringify`处理。
### JSON.parse()
JSON.parse方法用于将JSON字符串转化成对象。
```
JSON.parse('{}') // {}
JSON.parse('true') // true
JSON.parse('"foo"') // "foo"
JSON.parse('[1, 5, "false"]') // [1, 5, "false"]
JSON.parse('null') // null

var o = JSON.parse('{"name": "张三"}');
o.name // 张三
```
结合`JSON.stringify()`和`JSON.parse()`方法可以实现对一个对象的深拷贝
```
var source = {
    name:"source",
    child:{
        name:"child"
    }
}
var target = JSON.parse(JSON.stringify(source));
target.name = "target"; //改变target的name属性
console.log(source.name);   //source
console.log(target.name);   //target
target.child.name = "target child"; //改变target的child
console.log(source.child.name);  //child
console.log(target.child.name);  //target child
```
赋值后的`target`和`source`二者不会互相影响。但是`JSON.stringify()`默认不能转换正则对象，需要再次结合`toJSON`方法。
## [console 对象](http://javascript.ruanyifeng.com/stdlib/console.html)
# 面向对象编程
## 构造函数与 new 命令
### 对象是什么
面向对象编程将各种复杂的关系抽象为对象，再对其进行操作。

所有事物都可以是对象，事物之间存在的种种联系建立了面向对象编程的基础。

属性是对于对象的描述，方法是对象的行为。比如，可以将动物抽象成`animal`对象，使用“属性”描述是哪一种动物，使用“方法”表示动物的行为（奔跑）。
### 构造函数
面向对象编程的第一步就是要生成对象。

我们可以通过一个模板来生成具有共同特征的的对象。

JavaScript 语言是基于构造函数和原型链。这个构造函数就是上句话所说的模板，是专门用来生成对象的函数。

这个构造函数，提供模板描述对象的基本结构，这样就能生成多个具有相同结构的对象，这些对象也成为实例。
```
var Vehicle = function () {
  this.price = 1000;
};
```
上面代码中，Vehicle就是构造函数，它提供模板，用来生成实例对象。为了与普通函数区别，构造函数名字的第一个字母通常大写。
构造函数的特点：
- 函数体内部使用`this`关键字，代表了所要生成的对象实例
- 生成对象的时候，必须使用`new`命令，调用构造函数

### new 命令
`new`命令的作用就是执行构造函数，返回一个实例对象。
```
var Vehicle = function () {
  this.price = 1000;
};

var v = new Vehicle();
v.price // 1000
```
上面代码生成一个实例对象保存在变量`v`中。这个新生成的实例对象，继承了`price`属性。`new`命令执行时，构造函数内部额`this`，代表了新生成的实例对象，`this.price`表示实例对象有一个`price`属性，值是1000。
构造函数也可以接受参数
```
var Vehicle = function (p) {
  this.price = p;
};

var v = new Vehicle(500);
```
`new`命令本身就可以执行构造函数，所以后面的括号可省略
```
var v = new Vehicle();
//等价于
var v = new Vehicle;
```
如果忘记使用`new`命令，直接调用构造函数。构造函数会变成普通函数，并不会生成实例，而`this`此时代表全局对象。
```
var Vehicle = function (){
  this.price = 1000;
};

var v = Vehicle();
v.price
// Uncaught TypeError: Cannot read property 'price' of undefined

price
// 1000
```
上面代码，`price`属性变成了全局变量，而变量`v`变成了`undefined`。
因此为避免以上情况，可以在构造函数内部使用严格模式
```
function Fubar(foo, bar){
  'use strict';
  this._foo = foo;
  this._bar = bar;
}

Fubar()
// TypeError: Cannot set property '_foo' of undefined
```
由于在严格模式中，函数内部的`this`不能指向全局对象，默认等于`undefined`导致不加`new`会报错。
#### new 命令的原理
1. 创建一个空对象，作为将要返回的对象实例
2. 将这个空对象的原型，指向构造函数的`prototype`属性
3. 将这个空对象赋值给函数内部的`this`关键字
4. 开始执行构造函数内部的代码
也就是说，构造函数内部，`this`指的是一个新生成的空对象，所有针对`this`的操作都会发生在这个空对象上。

构造函数的目的，就是为了操作`this`，将其变成期望的样子。

如果构造函数内部有`return`语句，而且`return`后面跟着一个对象，`new`命令会返回`return`语句指定的对象；否则返回`this`对象
```
var Vehicle = function (){
  this.price = 1000;
  return { price: 2000 };
};

(new Vehicle()).price
// 2000
```
如果普通函数（内部没有`this`关键字的函数）使用`new`命令，则会返回一个空对象。
```
function getMessage() {
  return 'this is a message';
}

var msg = new getMessage();

msg // {}
typeof msg // "object"
```
判断函数调用时，是否使用`new`命令：
使用`new.target`属性。
```
function f() {
  console.log(new.target === f);
}

f() // false
new f() // true
使用new，返回true
```
举个栗子：
```
function f() {
  if (!new.target) {
    throw new Error('请使用 new 命令调用！');
  }
  // ...
}

f() // Uncaught Error: 请使用 new 命令调用！
```
#### 使用 Object.create() 创建实例对象
以一个不是构造函数生成的实例对象为模板，生成一个新的实例对象。
```
var person1 = {
  name: '张三',
  age: 38,
  greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
  }
};

var person2 = Object.create(person1);

person2.name // 张三
person2.greeting() // Hi! I'm 张三.
```
上面代码中，对象`person1`是`person2`的模板，后者继承了前者的属性和方法。
## this 关键字
`this`总是返回一个对象，就是返回属性或方法“当前”所在的对象。
```
var person = {
  name: '张三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

person.describe()
// "姓名：张三"
```
上面代码，`this`指向当前对象，也就是`person`。

函数f内部使用了this关键字，随着f所在的对象不同，this的指向也不同。
```
function f() {
  return '姓名：'+ this.name;
}

var A = {
  name: '张三',
  describe: f
};

var B = {
  name: '李四',
  describe: f
};

A.describe() // "姓名：张三"
B.describe() // "姓名：李四"
```
如果一个函数在全局环境中运行，那么`this`就是指顶层对象（浏览器中为`window`对象）。
```
function f() {
  return this;
}

f() === window // true
```
**`this`是所有函数运行时的一个隐藏参数，指向函数运行环境。**
### 使用场合
#### 全局环境
在全局环境使用this，它指的就是顶层对象window。
#### 构造函数
构造函数中的this，指的是实例对象。
#### 对象的方法
将某个对象的方法赋值给另一个对象，会改变this的指向。
```
var obj ={
  foo: function () {
    console.log(this);
  }
};

obj.foo() // obj
```
上面代码中，obj.foo方法执行时，它内部的this指向obj。
但是，只有这一种用法（直接在obj对象上调用foo方法），this指向obj；其他用法时，this都指向代码块当前所在对象（浏览器为window对象）。
```
// 情况一
(obj.foo = obj.foo)() // window

// 情况二
(false || obj.foo)() // window

// 情况三
(1, obj.foo)() // window
```
上面代码中，obj.foo先运算再执行，即使值根本没有变化，this也不再指向obj了。这是因为这时它就脱离了运行环境obj，而是在全局环境执行。

如果某个方法位于多层对象的内部，这时`this`只是指向当前一层的对象，而不会继承更上面的层。
```
var a = {
  p: 'Hello',
  b: {
    m: function() {
      console.log(this.p);
    }
  }
};

a.b.m() // undefined
```
上面代码中，`a.b.m`方法在`a`对象的第二层，该方法内部的`this`不是指向`a.b`。
如果要达到预期效果，只有写成下面这样
```
var a = {
  b: {
    m: function() {
      console.log(this.p);
    },
    p: 'Hello'
  }
};
```
### 使用注意点
#### 避免多层this
由于`this`的指向不确定，所以勿在函数中包含多层的`this`。
```
var o = {
  f1: function () {
    console.log(this);
    var f2 = function () {
      console.log(this);
    }();
  }
}

o.f1()
// Object
// Window
```
上面代码实际上是执行下面的代码
```
var temp = function () {
  console.log(this);
};

var o = {
  f1: function () {
    console.log(this);
    var f2 = temp();
  }
}
```
解决方法，在第二层改用一个指向外层`this`的变量。
```
var o = {
  f1: function() {
    console.log(this);
    var that = this;
    var f2 = function() {
      console.log(that);
    }();
  }
}

o.f1()
// Object
// Object
```
这样就不会改变`this`的指向。
**使用一个变量固定`this`的值，然后内层函数调用这个变量**
#### 避免数组处理方法中的this
数组的`map`和`foreach`方法，允许提供一个函数作为参数。这个函数内部不应该使用`this`。
```
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    });
  }
}

o.f()
// undefined a1
// undefined a2
```
上面代码中，`foreach`方法的回掉函数中的`this`，其实是指`window`对象，因此取不到`o.v`的值。因为内层的`this`不指向外部，而指向顶层对象。

解决方法，使用中间变量。
```
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    var that = this;
    this.p.forEach(function (item) {
      console.log(that.v+' '+item);
    });
  }
}

o.f()
// hello a1
// hello a2
```
另一种解决方法，将`this`当作`foreach`方法的第二个参数，固定它的运行环境。
```
var o = {
  v: 'hello',
  p: [ 'a1', 'a2' ],
  f: function f() {
    this.p.forEach(function (item) {
      console.log(this.v + ' ' + item);
    }, this);
  }
}

o.f()
// hello a1
// hello a2
```
#### 避免回调函数中的`this`
回调函数中的`this`往往会改变指向，最好避免使用。
```
var o = new Object();

o.f = function () {
  console.log(this === o);
}

o.f() // true
```
上面代码表示，如果调用`o`对象的 f 方法，其中`this`就是指向`o`对象。
但是，如果将`f`方法指定给某个按钮的`click`事件，this的指向就变了。
```
$('#button').on('click', o.f);
```
点击按钮后，控制台会显示`false`，原因是此时`this`不再指向`o`对象，而是指向按钮的DOM对象，因为`f`方法是在按钮对象的环境中被调用。
### 绑定 this 的方法
#### function.prototype.call()
可以指定函数内部`this`的指向（函数执行时所在作用域），然后在所指定的作用域中，调用该函数。
```
var obj = {};

var f = function () {
  return this;
};

f() === this // true
f.call(obj) === obj // true
```
上面代码在全局环境下运行`f`时，`this`指向全局环境；`call`方法，将`this`指向对象`obj`，这样就能在对象`obj`的作用域中运行函数`f`。
`call`方法的参数应该是一个对象。如果参数为空、`null`和`undefined`则默认传入全局对象。
```
var n = 123;
var obj = { n: 456 };

function a() {
  console.log(this.n);
}

a.call() // 123
a.call(null) // 123
a.call(undefined) // 123
a.call(window) // 123
a.call(obj) // 456
```
上面代码，如果用`call`方法将`this`关键字指向`obj`对象，则在`obj`中调用`this`，返回456。

`call`方法还可以接受多个参数。
`func.call(thisValue, arg1, arg2, ...)`
`call`的第一个参数就是`this`所要指向的那个对象，后面的参数则是函数调用是所需的参数。
```
function add(a, b) {
  return a + b;
}

add.call(this, 1, 2) // 3
```
上面代码中，`call`方法指定函数`add`内部的`this`绑定当前环境。

`call`方法的一个应用是调用对象的原生方法。
```
var obj = {};
obj.hasOwnProperty('toString') // false

// 覆盖掉继承的 hasOwnProperty 方法
obj.hasOwnProperty = function () {
  return true;
};
obj.hasOwnProperty('toString') // true

Object.prototype.hasOwnProperty.call(obj, 'toString') // false
```
上面代码将`hasOwnProperty`方法的**原始定义**放到`obj`对象上执行，这样不论`obj`上面有没有同名方法，都不会影响结果。
#### function.prototype.apply()
`apply`方法与`call`方法类似，区别是接受一个数组作为函数执行时的参数。
`func.apply(thisValue, [arg1, arg2, ...])`
第一个参数也是`this`所指向的那个对象，如果设为 null 或 undefined ，则等于指定全局对象。第二个参数是一个数组。
```
function f(x,y){
  console.log(x+y);
}

f.call(null,1,1) // 2
f.apply(null,[1,1]) // 2
```
可以使用`apply`方法做一些扩展

- 找出数组最大元素
结合使用`apply`方法和`Math.max`方法，返回数组最大元素
```
var a = [10, 2, 4, 15, 9];

Math.max.apply(null, a)
// 15
```
- 将数组的空元素变为undefined
通过`apply`方法，利用Array构造函数将数组的空元素变成undefined
```
Array.apply(null, ["a",,"b"])
// [ 'a', undefined, 'b' ]
```
空元素与`undefined`的差别在于，数组的`forEach`方法会跳过空元素，但是不会跳过`undefined`。
- 转换类数组对象
另外，利用数组的`slice`方法，可以将类数组对象转为真正的数组。
```
Array.prototype.slice.apply({0:1,length:1})
// [1]

Array.prototype.slice.apply({0:1})
// []

Array.prototype.slice.apply({0:1,length:2})
// [1, undefined]

Array.prototype.slice.
```
上面代码的`apply`方法参数都是对象，但是返回结果都是数组，这就达到了对象转数组的目的。前提是，被处理对象必须有length属性，以及对应数字键。
-  绑定回调函数的对象
```
var o = new Object();

o.f = function () {
  console.log(this === o);
}

var f = function (){
  o.f.apply(o);
  // 或者 o.f.call(o);
};

$('#button').on('click', f);
```
apply方法（或者call方法）不仅绑定函数执行时所在的对象，还会立即执行函数

#### function.prototype.bind()
`bind`方法用于将函数体内的`this`绑定到某个对象，然后返回一个新函数。
`bind`方法用于将函数体内的`this`绑定到某个对象返回一个新函数。
```
var d = new Date();
d.getTime() // 1481869925657

var print = d.getTime;
print() // Uncaught TypeError: this is not a Date object.
```
上面代码调用`print()`时报错，因为`getTime`方法内部的`this`绑定`Date`对象的实例，赋值给`print`以后，内部的`this`已经不指向`Date`对象实例了。
```
var print = d.getTime.bind(d);
print() // 1481869925657
```
上面代码中，`bind`方法将`getTime`方法内部的`this`绑定到`d`对象，这样就可以安全的将这个方法赋值给其他变量了。

`bind`方法除了绑定`this`之外，还可以绑定原函数的参数。
如果`bind`方法的第一个参数是`null`或`undefined`，等于将`this`绑定到全局对象，函数运行时`this`指向顶层对象（在浏览器中为window）。
```
function add(x, y) {
  return x + y;
}

var plus5 = add.bind(null, 5);
plus5(10) // 15
```
上面代码中，函数`add`内部并没有`this`，使用`bind`方法的主要目的是绑定参数`x`，以后每次运行新函数`plus5`，就只需要提供另一个参数`y`就够了。

`bind`方法有一些使用注意点
- 每一次返回一个新函数
bind方法每运行一次，就返回一个新函数，这会产生一些问题。比如，监听事件的时候，不能写成下面这样。
```
element.addEventListener('click', o.m.bind(o));
element.removeEventListener('click', o.m.bind(o));
```
上面代码中，click事件绑定bind方法生成的一个匿名函数。这样会导致无法取消绑定，所以，下面的代码是无效的。

正确的方法是写成下面这样：
```
var listener = o.m.bind(o);
element.addEventListener('click', listener);
//  ...
element.removeEventListener('click', listener);
```
- [结合回调函数使用](http://javascript.ruanyifeng.com/oop/this.html)
- [结合call方法使用](http://javascript.ruanyifeng.com/oop/this.html)
## prototype对象
JavaScript语言的继承是通过“原型对象”（prototype）实现的。
### 原型对象
#### 构造函数
JavaScript 通过构造函数生成新对象，因此构造函数可以视为对象的模板。可以在构造函数内部定义实例的属性和方法。
```
function Cat (name, color) {
  this.name = name;
  this.color = color;
}

var cat1 = new Cat('大毛', '白色');

cat1.name // '大毛'
cat1.color // '白色'
```
上面代码中，`Cat`就是一个构造函数，所有的实例对象（cat1）都会继承构造函数的属性和方法。

但是，同一个构造函数的实例之间，无法共享属性，每新建一个实例，就生成了同样的属性和方法，浪费系统资源。

我们需要一个方法，把这些完全相同的属性，共享起来。就是JavaScript的原型对象（prototype）。
#### prototype 属性的作用
JavaScript 继承机制的设计思想就是，原型对象的所有属性和方法，都能被实例对象共享。
```
function f() {}
typeof f.prototype // "object"
```
上面代码中，函数`f`默认具有`prototype`属性，指向一个对象。
```
function Animal(name) {
  this.name = name;
}
Animal.prototype.color = 'white';

var cat1 = new Animal('大毛');
var cat2 = new Animal('二毛');

cat1.color // 'white'
cat2.color // 'white'
```
上面代码，构造函数`Animal`的`prototype`属性，就是实例对象`cat1`和`cat2`的原型对象。原型对象上添加了`color`属性，所有实例对象都共享了该属性。

就是因为原型对象的属性是共享的，所以只要更改原型对象，变动就会体现在所有实例对象上。

因为实例对象本身没有`color`这个属性，它就会去原型对象上读取`color`属性。也就是说，当实例对象本身没有某个属性或方法的时候，它会到原型对象上找该属性或方法。

但是，如果实例对昂本身就有某个属性或方法，它就不会再到原型上寻找了。
```
cat1.color = 'black';

cat1.color // 'black'
cat2.color // 'white'
Animal.prototype.color // 'white';
```
所以说，原型对象的作用就是为实例对象提供一些共有的方法和属性。这些被定义子在原型上的方法可以被所有对象实例调用。
#### 原型链
JavaScript规定，所有对象都有自己的原型对象（prototype）。而原型对象也是对象，所以它也有自己的原型。因此，就会形成一“原型链”。

一层一层向上追溯，会发现所有对象的原型最终会找到`Object.prototype`，也就是`Object`构造函数的`prototype`属性。所有对象都继承了`Object.prototype`的属性。这就是为什么所有对象都有`valueOf`和`toString`方法的原因，因为这都是从`Object.prototype`继承的。

那么`Object.prototype`对象有没有原型呢？
`Object.prototype`的原型是null。`null`没有任何属性和方法，也没有自己的原型。因此，原型链的尽头就是`null`。
```
Object.getPrototypeOf(Object.prototype)
// null
```
读取对象的某个属性时，JavaScript 引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。如果直到最顶层的Object.prototype还是找不到，则返回undefined。
#### constructor 属性
`prototype`对象有一个`constructor`属性，默认指向`prototype`对象所在的构造函数。
```
function P() {}
P.prototype.constructor === P // true
```
由于`constructor`属性定义在`prototype`对象上面，意味着可以被所有实例对象继承。
```
function P() {}
var p = new P();

p.constructor === P // true
p.constructor === P.prototype.constructor // true
p.hasOwnProperty('constructor') // false
```
上面代码，`p`是构造函数`P`的实例对象，但是`p`本身没有`constructor`属性，但是可以通过原型链调用`P.prototype.constructor`属性。

`constructor`属性的作用是，可以得知某个实例对象到底是由哪一个构造函数产生的。
通过这个特性我们可以从一个实例对象新建另一个实例：
```
function Constr() {}
var x = new Constr();

var y = new x.constructor();
y instanceof Constr // true
```
上面代码，从`x.constructor`间接调用构造函数。

constructor属性表示原型对象与构造函数之间的关联关系，如果修改了原型对象，一般会同时修改constructor属性，防止引用的时候出错。
```
function Person(name) {
  this.name = name;
}

Person.prototype.constructor === Person // true

Person.prototype = {
  method: function () {}
};

Person.prototype.constructor === Person // false
Person.prototype.constructor === Object // true
```
可以看到上面代码，直接修改了`Person`的原型对象，却没有连同`constructor`属性一起修改，导致这个属性不再指向`Person`，而是指向新原型。这个新原型只是一个普通对象，它的`constructor`指向Object构造函数，导致`Person.prototype.constructor`变成了Object。
所以，修改原型对象时，一般要同时修改`constructor`属性指向。
```
// 坏的写法
C.prototype = {
  method1: function (...) { ... },
  // ...
};

// 好的写法
C.prototype = {
  constructor: C,
  method1: function (...) { ... },
  // ...
};

// 更好的写法
C.prototype.method1 = function (...) { ... };
```
上面代码中，要么将constructor属性重新指向原来的构造函数，要么只在原型对象上添加方法，这样可以保证instanceof运算符不会失真。
### instanceof 运算符
instanceof运算符返回一个布尔值，表示对象是否为某个构造函数的实例。
```
var v = new Vehicle();
v instanceof Vehicle // true
```
instanceof运算符的左边是实例对象，右边是构造函数。它会检查右边构建函数的原型对象（prototype），是否在左边对象的原型链上。因此，下面两种写法是等价的。
```
v instanceof Vehicle
// 等同于
Vehicle.prototype.isPrototypeOf(v)
```
由于`instanceof`检查整个原型链，因此同一个实例对象，可能会对多个构造函数都返回`true`
```
var d = new Date();
d instanceof Date // true
d instanceof Object // true
```
上面代码中，d同时是Date和Object的实例，因此对这两个构造函数都返回true。

`instanceof`运算符的一个用处，是判断值的类型。
```
var x = [1, 2, 3];
var y = {};
x instanceof Array // true
y instanceof Object // true
```
**注意，instanceof运算符只能用于对象，不适用原始类型的值。**
```
var s = 'hello';
s instanceof String // false
```
利用instanceof运算符，还可以巧妙地解决，调用构造函数时，忘了加new命令的问题。
```
function Fubar (foo, bar) {
  if (this instanceof Fubar) {
    this._foo = foo;
    this._bar = bar;
  } else {
    return new Fubar(foo, bar);
  }
}
```
上面代码使用instanceof运算符，在函数体内部判断this关键字是否为构造函数Fubar的实例。如果不是，就表明忘了加new命令。
## Object 对象的相关方法
### Object.getPrototypeOf()
Object.getPrototypeOf方法返回参数对象的原型。这是获取原型对象的标准方法。
```
var F = function () {};
var f = new F();
Object.getPrototypeOf(f) === F.prototype // true

// 空对象的原型是 Object.prototype
Object.getPrototypeOf({}) === Object.prototype // true

// Object.prototype 的原型是 null
Object.getPrototypeOf(Object.prototype) === null // true

// 函数的原型是 Function.prototype
function f() {}
Object.getPrototypeOf(f) === Function.prototype // true
```
### Object.setPrototypeOf()
Object.setPrototypeOf方法为参数对象设置原型，返回该参数对象。它接受两个参数，第一个是现有对象，第二个是原型对象。
```
var a = {};
var b = {x: 1};
Object.setPrototypeOf(a, b);

Object.getPrototypeOf(a) === b
a.x // 1
```
上面代码，将`a`的原型设置为`b`，这样`a`就可以调用`b`的属性和方法。
### Object.create()
除了使用new命令生成实例，还可以使用`Object.creat()`由一个实例对象生成另一个实例对象
该方法接受一个对象作为参数，然后以它为原型，返回一个实例对象。（该实例完全继承原型对象的属性）
```
// 原型对象
var A = {
  print: function () {
    console.log('hello');
  }
};

// 实例对象
var B = Object.create(A);

Object.getPrototypeOf(B) === A // true
B.print() // hello
B.print === A.print // true
```
下面三种方式生成的新对象是等价的
```
var obj1 = Object.create({});
var obj2 = Object.create(Object.prototype);
var obj3 = new Object();
```
**使用Object.create方法的时候，必须提供对象原型，即参数不能为空，或者不是对象，否则会报错。**
