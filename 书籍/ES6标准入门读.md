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