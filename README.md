# ECMAScript6 编码规范--广发证券前端团队

> 本规范是基于JavaScript规范拟定的，只针对ES6相关内容进行约定

> 如变量命名，是否加分号等约定的请参考JavaScript规范

> 应注意目前的代码转换工具(如Babel，traceur)不够完善,有些特性须谨慎使用

## 规范内容

1. [声明 Declarations](#声明)
2. [字符串 Strings](#字符串)
3. [解构 Destructuring](#解构)
4. [数组 Arrays](#数组)
5. [函数 Functions](#函数)
6. [类 Classes](#类)
7. [模块 Modules](#模块)

### 声明
- 1.1 变量

> 对于只在当前作用域下有效的变量，应使用let来代替var

> 对于全局变量声明，采用var，但应避免声明过多全局变量污染环境

```js
// 不好
const variables; 
const globalObj = null; // 不是定义一个常量
let globalObj = null; 

for (var i=0; i<5; i++) {
  console.log(i);
}
console.log(i);

// 好
let variables;
var globalObj = null; 

for (let i=0; i<5; i++) {
  console.log(i);
}
console.log(i);
```

- 1.2 常量

> 对于常量应使用const进行声明，命名应遵循字母全大写的通俗约定

> 对于immutable数据（不再变化的值）应使用const进行声明

> 应注意const与let只在声明所在的块级作用域内有效

```js
// 不好
const someNum = 123;
const AnotherStr = '不变的字符串';
let SOME_ARR = ['不','变','数','组'];
var ANOTHER_OBJ = {
  '不变对象': true
};


// 好
const SOME_NUM = 123;
const ANOTHER_STR = '不变的字符串';
const SOME_ARR = ['不','变','数','组'];
const ANOTHER_OBJ = {
  '不变对象': true
};

```

#### 字符串

- 2.1 模板字符串

> 对于多行字符串，拼接变量的情况适用模板字符串，以反引号( ` )标示

> 可读性更强，代码更易编写

```js
  // 不好
  const multiStr = '多行字符串换行表示,\n 这是换行了。';
  function sayHi(name) {
    return 'How are you, ' + name + '?';
  }


  // 好
  const multiStr = `多行字符串换行表示,
                  这是换行了。`;
  function sayHi(name) {
    return `How are you, ${name}?`;
  }
```

#### 解构

> 嵌套结构的对象层数不能超过3层

> 解构语句中统一不使用圆括号

```js 
// 不好
let obj = {
  'one': [
    { 'newTwo': [
        { 'three': [
            'four': '太多层了，头晕晕'
          ]
        }
      ]
  ]
};

[(a)] = [11]; // a未定义
let { a: (b) } = {}; // 解析出错


// 好
let obj = {
  'one': [
    'two',
    {'twoObj': '结构清晰' }
  ]
};

let [a, b] = [11, 22];

```


- 3.1 对象解构

> 若函数形参为对象时，使用对象解构赋值

> 若函数有多个返回值时，使用对象解构，不使用数组解构，避免添加顺序的问题

> 对象指定默认值时仅对恒等于undefined(!== null)的情况生效

> 已声明的变量不能用于解构赋值（语法错误）

```js
// 不好
function someFun(opt) {
  let opt1 = opt.opt1;
  let opt2 = opt.opt2;
  console.log(op1);
}

function anotherFun(){
  const one = 1, two = 2, three = 3;
  return [one, two, three];
}

const [one, three, two] = anotherFun(); // 顺序乱了


// 语法错误
let a;
{a} = {a: 123};


// 好
function someFun(opt){
  let {opt1, opt2} = opt;
  console.log( `$(opt1) 加上 $(opt2)` );
}

function someFun( {opt1, opt2} ){
  console.log(opt1);
}

function anotherFun(){
  const one = 1, two = 2, three = 3;
  return { one, two, three };
}

const { one, three, two } = anotherFun(); // 不用管顺序

```

- 3.2 数组解构

> 交换变量的值

> 将数组成员赋值给变量时，使用数组解构

> 数组元素与顺序相关

```js
let x = 1;
let y = 2;
const arr = [1,2,3,4,5];

// 不好
let temp;
temp = x;
x = y;
y = temp;

const one = arr[0];
const two = arr[1];


// 好
[x, y] = [y, x]; // 交换变量

const [one, two] = arr;

```

#### 数组

- 4.1 将类数组(array-like)对象与可遍历对象(如Set,Map)转为真正数组

> 采用Array.from进行转换

```js
// 不好
function foo(){
  let args = Array.prototype.slice.call(arguments);
}


// 好
function foo(){
  let args = Array.from(arguments);
}

```
- 4.2 数组去重

> 结合Set结构与Array.from

```js
// 不好
// 使用indexOf，HashTable等形式，不够简洁清晰


// 好
function deduplication(arr){
  return Array.from(new Set(arr));
}

```
- 4.3 数组拷贝

> 采用数组扩展...形式

```js
const items = [1,2,3];

// 不好
const len = items.length;
let copyTemp = [];
for (let i=0; i<len; i++) {
  copyTemp[i] = items[i];
}


// 好
let copyTemp = [...items];
```
- 4.4 将一组数值转为数组

> 采用Array.of进行转换

```js
// 不好
let arr1 = new Array(2); // [undefined x 2]
let arr2 = new Array(1,2,3); // [1, 2, 3]


// 好
let arr1 = Array.of(2);  // [2]
let arr2 = Array.of(1,2,3); // [1, 2, 3]
```

#### 函数

- 5.1 当要用函数表达式或匿名函数时，使用箭头函数(Arrow Functions)

> 箭头函数更加简洁，并且绑定了this

```js
// 不好
const foo = function(x){
  console.log('存在函数提升问题');
  console.log(foo.name); // 返回'' ，函数表达式不可命名，函数声明可以
};

[1, 2, 3].forEach(function(x){
  return x + 1;
});


// 好
const foo = x => {
  console.log('不存在函数提升问题');
  console.log(foo.name); // 返回'foo'
};

[1, 2, 3].forEach( x => {
  return x + 1;
});
```

- 5.1.1 箭头函数书写约定

> 函数体只有单行语句时，允许写在同一行并去除花括号

> 当函数只有一个参数时，允许去除参数外层的括号

```js
// 好
const foo = x => x + x; // 注意此处会默认return x + x，有花括号语句块时不会return

[1, 2, 3].map(x => x * x);

```
- 5.1.2 用箭头函数返回一个对象，应用括号包裹

```js
// 不好
let test = x => {x:x}; // 花括号会变成语句块，不表示对象


// 好
let test = x => ({x:x}); // 使用括号可正确return {x:x}
```

- 5.2 立即调用函数 IIFE

> 使用箭头函数

```js
// 不好
(function(){
  console.log('哈');
})();


// 好
(() => {
  console.log('哈');
})();

```

- 5.3 不使用 `arguments`, 采用rest语法...代替

> rest参数是真正的数组，不需要再转换

> 注意：箭头函数中不能使用arguments对象

```js
// 不好
function foo(){
  let args = Array.prototype.slice.call(arguments);
  return args.join('');
}


// 好
function foo(...args) {
  return args.join('');
}

```

- 5.4 函数参数指定默认值

> 采用函数默认参数赋值语法

```js
// 不好
function foo(opts) {
  opts = opts || {};// 此处有将0，''等假值转换掉为默认值的副作用
}


// 好
function foo( opts = {}) {
  console.log('更加简洁，安全');
}
```

- 5.5 对象中的函数方法使用缩写形式

> 更加简洁

```js
// 不好
const shopObj = {
  des: '对象模块写法',
  foo: function(){
    console.log('对象中的方法');
  }
};

// 好
const shopObj = {
  des: '对象模块写法',
  foo(){
    console.log('对象中的方法');
  }
};
```


#### 类

- 6.1 类名应使用帕斯卡写法(PascalCased)

```js
class SomeClass{
}
```

- 6.2 定义类时，方法的顺序如下：

  - `constructor`

  - public `get/set` 公用访问器，set只能传一个参数

  - public methods 公用方法，以函数命名区分，不带下划线

  - private `get/set` 私有访问器，私有相关命名应加上下划线`_`为前缀

  - provate methods 私有方法 

```js
class SomeClass {
  constructor() {
    // constructor
  }
  
  get aval() {
    // public getter
  }
  
  set aval(val) {
    // public setter
  }
  
  doSth() {
    // 公用方法
  }
  
  get _aval() {
    // private getter
  }
  
  set _aval() {
    // private setter
  }
  
  _doSth() {
    // 私有方法
  }
}

```

- 6.3 如果不是class类，不使用new

```js
// 不好
function Foo() {
}
const foo = new Foo();


// 好
class Foo() {
}
const foo = new Foo();
```


- 6.4 使用真正意思上的类Class写法，不使用prototype进行模拟扩展

> Class更加简洁，易维护

```js
// 不好
function Dog(names = []) {
  this._names = [...names];
}
Dog.prototype.bark = function(){
  const currName = this._names[0];
  alert(`one one ${currName}`);
}

// 好
class Dog {
  constructor(names = []){
    this._name = [...names];
  }
  bark() {
    const currName = this._names[0];
    alert(`one one ${currName}`);
  }
}
```

- 6.5 class应先定义后使用

> 虽然规范里class不存在hoist问题，但转换工具如babel，只是转换为函数表达式，此处仍有hoist

> 使用继承时，应先定义父类再定义子类

```js
// 不好
let foo = new Foo();
class Foo { } 


// 好
class Foo { }
let foo = new Foo();

class SubFoo extends Foo {
  
}
```

- 6.6 this的注意事项
 
> 子类使用super关键字时，this应在调用super之后才能使用

> 可在方法中return this来实现链式调用写法

```js
class Foo {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

// 不好
class SubFoo extends Foo {
  constructor(x, y, z) {
    this.z = z; // 引用错误
    super(x, y);
  }
}


// 好
class SubFoo extends Foo {
  constructor(x, y, z) {
    super(x, y);
    this.z = z; // this 放在 super 后调用
  }
  setHeight(height) {
    this.height = height;
    return this;
  }
}
```


#### 模块

- 7.1 使用`import / export`来做模块加载导出，不使用非标准模块写法

```js
// 不好
const colors  = require('./colors');
module.exports = color.lightRed;


// 好
import { lightRed } from './colors';
export default lightRed;

```

- 7.2 import 不使用统配符 `* `进行导入

- 7.3 不要将import与export混合在一行

> 分开导入与导出，让结构更清晰一些




