# ES6标准入门

ECMAScript是JavaScript语言的国际标准，JavaScript是ECMAScript的实现

### 第二章 let和const命令

ES6新增了let、const命令，

1.  解决了var的变量提升问题
2.  存在暂时性死区的概念
3.  不允许重复声明
4.  const不允许重复赋值，本质是变量指向的内存地址不允许改动

### 第三章 变量的解构赋值

##### 数组的解构赋值

1.  本质上，属于模式匹配，按照对应的位置对变量赋值 `let [a  , b] = [1 , 2]`
2.  允许不完全解构 `let [a ] = [1 , 2] `
3.  支持设置默认值 `let [foo = true ]` = [] , 右边数组中的值需要严格等于`undefined`,默认值才会生效

###### 对象的解构赋值

1.  对象的解构赋值，变量必须与属性同名才能取到正确的值`let {foo , bar} = {foo :'a' ,bar:'b'}`
2.  不一样时 需要写成如下形式`let {foo : bar} = {foo:'a',bar :'b'}   bar// 'a'` , 注意模式与变量的区分
3.  支持设置默认值

##### 字符串的解构赋值

##### 数值和布尔值的解构赋值

##### 函数参数的解构赋值

```js
function move({x = 0 , y = 0}={}){
    return [x , y];
}
function move({x , y} = {x:0 , y:0}){
    return [x ,y]
}
```

注意这两种写法的区别，上面的函数是为了move的参数指定默认值，下面的函数是为了move这个方法提供默认值

##### 圆括号问题

赋值和声明的操作注意区分

### 第四章 字符串的扩展

##### 字符的unicode的扩展

##### codePointAt()

JavaScript中，字符以UTF16的格式储存，每个字符为两个字节

测试一个字符是2个字节还是4个字节的方法

```js
function is32bit(c){
    return c.codePointAt(0) > 0xFFFF
}
```

##### String.fromCodePoint()

##### 字符串的遍历器接口

##### includes()、startsWith()、endsWith()、repeat()、padStart()、padEnd()

##### 模板字符串

##### 标签模板

一个重要的应用就是过滤html字符串，防止用户恶意输入内容

### 第五章 正则的扩展

### 第六章 数值的扩展

##### `Number`

-   `0b`、`0o`的表现形式
-   `Number.isFinite()`、`Number.isNaN()`
-   `Number.parseInt 、Number.parseFloot`属于全局移植方法，这样做的目的是逐步减少全局性方法，使得语言逐步模块化
-   `Number.EPSILON`其实就是可以接受的误差范围
-   JS能够准确表示的整数范围是`2^-53 2^53`

##### `Math`

-   新增了一些计算方法

##### Integer

-   经典问题`0.1+0.2 不能得到准确0.3的原因`
    -   `iEEE 754`
    -   浮点数的二进制表示问题
    -   精度取值问题 `toPrecision(16)`

### 第七章 函数的扩展

##### 函数参数的默认值

##### 箭头函数

-   箭头函数的`this`指向作用域问题