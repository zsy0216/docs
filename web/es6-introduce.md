# ES6 介绍

ES6：ECMScript6

首先，一个常见的问题是，ECMAScript 和 JavaScript 到底是什么关系？

- ECMAScript是一个国际通过的标准化脚本语言；

- JavaScript由ECMAScript和DOM、BOM三者组成；
- 可以简单理解为：ECMAScript是JavaScript的语言规范，JavaScript是ECMAScript的实现和扩展；

2011 年，ECMAScript 5.1 版发布。之前我们大部分人用的也就是ES5。

2015 年 6 月，ECMAScript 6 正式通过，成为国际标准。

# 块级作用域

- ES5 中作用域有：全局作用域、函数作用域（function scope）。没有块作用域（block scope）的概念。
- ES6 中新增了块级作用域。块作用域由 { } 包括，if语句和 for语句里面的{ }也属于块作用域。

# 变量声明

变量声明的三种方式：`var`、`let`、`const`

`var`声明的变量属于函数作用域（function scope），函数内部有效，可以在同一个作用域重复声明和赋值；

`let`声明的变量属于块级作用域（block scope），块作用域内部有效；

`const`声明的变量定义为常量，使用时必须初始化（赋值），属于块级作用域，块级作用域内部有效；

|         比较         |    var     |      let       |     const      |
| :------------------: | :--------: | :------------: | :------------: |
|    **所属作用域**    | 函数作用域 |   块级作用域   |  块级级作用域  |
| **是否可以重复声明** |    可以    | 作用域内不可以 | 作用域内不可以 |
| **是否可以重复赋值** |    可以    |      可以      |     不可以     |
|   **未赋值时打印**   | undefined  |   undefined    |    必须赋值    |

## 临时性死区和变量提升

临时性死区：Temporal Dead Zone（TDZ）；

变量提升：Hoisting；

```js
console.log(color); //undefined 变量提升
var color = 'yellow';
```

在JavaScript中，function中variables（变量）会被提升；

变量提升是指JavaScript将声明移至当前作用域(scope)顶部的行为；

所以以上代码可以理解为：

```js
var color;
console.log(color); //undefined
color = 'yellow';
```

对于let和const关键字来说也有变量提升的概念，但是在let和const中还存在一个临时性死区的概念，即在当前变量的作用域开始到变量声明之前都是处在临时性死区中的，处于临时性死区的变量引用时会报referenceError错误；

```js
{
    console.log(color); //referenceError 临时性死区
	let color = 'yellow';
}
```

## var、let、const取舍之道

- 默认使用`const`声明变量（use `const` by default)
- 当需要改变变量值时使用`let`（only use `let` if rebinding is needed）
- 尽量不使用`var`（`var `shouldn't be used in ES6）

# 箭头函数

`arrow function`

示例：

```js
const numbers = [5,6,13,0,1,18,23];

//一般方式
const double = numbers.map(function(number){
	return number * 2;
})

//箭头函数,当且仅当只有一个参数时可以省略小括号，无参数也不可省略
const double2 = numbers.map(number => {
	return number * 2;
})

//箭头函数,当函数内部只有一句时可以省略大括号，称为隐式返回
const double2 = numbers.map((number, i)=> number * 2);
```

箭头函数属于匿名函数，可以通过赋值给变量得到匿名函数引用；

```js
const greet = number => {alert('The number is ${number}')};
```

箭头函数没有其this值，它的this值是继承于它的父作用域的；

```js
const Jelly = {
	name: 'jelly',
    hobbies: ['coding', 'Sleeping', 'Reading'],
    printHobbies: function(){
    	this.hobbies.map(function(){
        	console.log(this) // Window,全局变量,严格模式下undefined	
        })
        //修改为箭头函数即可
        this.hobbies.map(() => {
        	console.log(this) // Jelly
        })
    }
}
```

# 模板字符串

ES6提供了模板字符串，使用模板字符串可以更简洁的进行字符串的拼接，也可以直接在模板字符串中书写html代码，由此可以方便的引用模板；

```js
const user = 'tom';
const pass = '123';

const sentence = 'username: '+ user + ', password: '+ pass;
console.log(sentence);

const sentence2 = `username: ${user} , password: ${pass}`; //使用模板字符串
console.log(sentence2);
```

# 对象解构

```js
const user =  {
	name : 'tom',
    pass : '123'  
}
const {name : n, pass, age = 18 } = user
console.log(n) // 'tom'
console.log(name) // undefined
console.log(age)  //18
```

我们在结构对象变量后面可以设置变量的别名，此时初始化的为别名变量，原名变量为声明即为undefined；

如果我们在解构声明变量时，定义了对象中不存在的属性，那么这个变量的值为`undefined`。我们可以给变量设置默认值，当对象中没有对应的属性时，这个变量的值就是设置的默认值。

# 数组解构

```js
const numbers = ['one', 'two', 'three', 'four'];
const [one, two] = numbers; // 获得第一个和第二个位置的值
const [one, ,three] = numbers; // 获得第一、三位置的值
const [one, ...others] = numbers; // 获得第一和后面所有的值
console.log(one, others) // one ['two', 'three', 'four']
```

数组解构和对象解构类似，也可以在变量声明的后面起别名，赋初值等。。。

# for of循环

```js
const numbers = ['one', 'two', 'three', 'four'];

//普通for循环
for(let i = 0; i< numbers.length; i++){
	console.log(numbers[i]);
}
//forEach遍历
numbers.forEach(number => {
	console.log(number);
})
//for in循环
for(let index in numbers){ //每次循环的是索引值，且遍历过程中不可中断(break)
	console.log(numbers[index]);
}
//for of循环
for(let number of numbers){ //每次循环的是属性值；可使用break和continue
	console.log(number)
}
```

for of 循环可以利用entries()同时获得索引值和属性值；

```js
for(let [index, number] of numbers.entries()){
	console(index,number)
}
```

# 对象转数组

当你需要使用数组的方法迭代一个可迭代对象时，而该对象又不是数组类型，你可以使用Arrat.from()方法把对象转化为数组类型的对象；

Arrat.from(object)可以把其他对象转化为数组；

# Array.of()创建数组

Array.of()弥补了Array构造函数的不足，不管传入多少参数返回的都是这些参数组成的数组；

```js
new Array(4) // (3) [empty × 3]
new Array(4,1) // [4, 1]

Array.of(4) //[4]
Array.of(4,1) //[4, 1]
```

# 数组新方法

| 方法                              | 说明                                                         |
| :-------------------------------- | ------------------------------------------------------------ |
| find((element, index, array)=>{}) | 该方法接收的参数是一个函数，其中有三个**可选**参数，element表示要遍历的元素，index表示索引，array表示调用方法的数组，当找到数组中的元素或索引后立即返回该元素或索引； |
| findIndex()                       | 该方法与find方法类似，返回的是寻找元素的索引值；             |
| some()                            | 参数是一个函数，返回布尔值，true的条件是数组中含有至少一个条件满足 |
| every()                           | 参数、返回值同some，true的条件是数组中所有条件都满足         |

```js
const fruits = [
    {name: 'apple', quantity: 2},
    {name: 'banana', quantity: 0},
    {name: 'orange', quantity: 5}
];
// find()
const banana = fruits.find(fruit => {
	if(fruit.name === 'banana'){
    	return true;
    }
    return false;
});

// findIndex()
const bananaIndex = fruits.findIndex(fruit => fruit.name === 'banana');

//some()
const isEnough = fruits.some(fruit => fruit.quantity > 0); //true

//every()
const isAllEnough = fruits.every(fruit => fruit.quantity > 0); //false
```

# 剩余参数

ES6中可以使用`...params`接收参数列表中的从声明位置到最后的所有参数，param返回为一个数组

```js
function sum(...numbers){
    return numbers.reduce((prev,curr) => prev + curr, 0)
} //reduce()方法接收一个函数callbackfn作为累加器(accumulator)，数组中的每一个值（开始合并），最后成为一个值。
console.log(sum(1,2,3,4)) //10
console.log(sum(1,2,3)) //6
```

# 扩展运算符

扩展运算符和剩余参数作用相反，剩余参数是把参数序列整合成一个数组；

而扩展运算符是把一个可遍历对象的每一个元素扩展为一个新的参数序列；

```js
const youngers = ['George', 'John', 'Thomas'];
const olders = ['James', 'Adrew', 'Martin'];

//const members = [];
//members.concat(youngers);
//members.concat(olders);
const members = [...youngers, 'mary', ...olders];

//数组之间的赋值
const currentMembers = [...members];
// const currentMembers = members 此时传递的是引用，修改其中一个值，另一个数组也会随之改变；
// const currentMembers = [].concat(members) //可以替换为这种方式
```

# 对象字面量的扩展

在ES6中，当对象属性名和参入参数名相同时，不需要再重复两次；

```js
const name = 'Tom';
const age = 12;
const sex = 'male';

const Tom = {
	//name : name,
    name,
    age,
    sex,
    func(){} //对象方法的简写
    //func:function(){} //简写前
}
```

# promise解决回调地狱

需求：确保第二个函数是在第一个函数执行之后执行（函数可能是发起的ajax请求）；

回调地狱是指在上述需求中当需要嵌套很多函数时，不得不把下一个函数写在上一个函数的回调函数中，这样层层嵌套就会导致回调地狱；

ES6中提供了promise来解决回调地狱的问题，提高代码的可读性；

**回调地狱**：

```js
$.get('http://api.github.com/users', data =>{
	user = data[0].login;
    
    //第二个请求要使用第一个的内容，所以要在第一个函数执行完再执行
    $.get(`http://api.github.com/users/${user}/repos`, data =>{
    	//业务逻辑
        //或许还需要第三个请求在第二个请求执行后执行
        //这里就形成了回调地狱
    })
})
```

**promise**：

axios 提供了promise功能

```js
//返回promise
const usersPromise = axios.get('https://api.github.com/users');

userPromise
    .then(response => { //response是请求成功后返回的响应对象
	user = response.data[0].login;
    //返回的promise
    return axios.get(`http://api.github.com/users/${user}/repos`);
})
    .then(response =>{ //可以直接在后面链接调用
    	//业务逻辑
    	//其他请求
	})
	.catch(err =>{ //可以使用catch获取错误信息
		console.error(err);	
	})
```

## 自定义promise

可以自定义promise来完成需要的异步操作，在js中所有代码都是单线程执行的。

可以通过Promise的构造方法来自定义一个Promise，构造方法中是一个回调函数，函数有两个参数；

- resolve保存的是成功之后返回的信息；
- reject保存的是失败的信息；

```js
const p = new Promise((resolve, reject) => {
	resolve('这里是成功之后返回的信息');
    reject(Error('这里是调用失败返回的信息'));
});

p.then(data => {console.log(data) }) //这里是成功之后返回的信息
    .catch(err => {console.log(err) }) //这里是调用失败返回的信息
```

# 基本数据类型

ES6中引入了一种新的基本数据类型：`symbol`

之前还有五种基本数据类型：`undefined`,`boolean`,`string`,`number`,`object`,`null`

Symbol用来解决属性名冲突的问题；

```js
const tom = Symbol('tom'); //定义方法

const people = {
	[Symbol('mary')] : {age: 12, sex:'famale' },
    [Symbol('mary')] : {age: 15, sex:'famale' }, //不会覆盖上面的mary
    [Symbol('jack')] : {age: 13, sex:'male' }
}
```

注意：symbol类型的值不能够被获取遍历，除非使用ES6提供的Object.getOwnPropertySymbols(people);

# ES6模块

```js
import moduleName from 'module'
```

定义模块

`name.js`

```js
const myname = 'tom';

export default myname; //默认导出,只能有一个默认导出

export const myname = 'tom'; //命名导出，其他文件引入时命名需要保持一致性，且需要使用大括号包裹，可以有多个

export function getname(name){ //函数导出同命名导出
	console.log(name);
}
```

引用模块

`app.js`

```js
import myname from './name'; //默认导出引用方式

import {myname,getname} from './name'; //命名导出+函数导出引用方式

console.log(myname) //tom
```

注意：相同的导出和引用使用一个即可，上面代码是为了简化书写；