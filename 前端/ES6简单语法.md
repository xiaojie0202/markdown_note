# let
1. let 声明的变量是块级作用域
```javascript
{
    let a = 10;
}
console.log(a); //Uncaught ReferenceError: a is not defined
```
2. let 声明的变量不能重新声明
```javascript
let a = 10;
let a = 20; // Uncaught SyntaxError: Identifier 'a' has already been declared
```
3. let 与 var的区别
```javascript
/*  var  */
var a = [];
for(var i = 0; i< 10; i++){
    a[i] = function(){console.log(i)};
}
a[1](); //10
/* let */
var a = [];
for(let i = 0; i< 10; i++){
    a[i] = function(){console.log(i)};
}
a[1](); // 1
a[2](); // 2
```
4. 变量提升
```javascript
/* var */
console.log(a); // undefined
var a = 12;
// 上两句代理相当于
var a;
console.log(a);
a = 12;

/*  let  */
console.log(a); // 报错 ReferenceError
let a = 12;
```
# const
    声明一个只读的常量，一旦声明，常量的值不能改变, 而且const声明变量的时候必须初始化，不能留到以后赋值
```javascript
const PI = 3.14;
PI = 0; // TypeError: Assignment to constant variable.
```
# 模板字符串
    类似于python的字符串格式化
```javascript
var name = 'xiaojie';
var age = 18;
// 老套路
var str = "姓名：" + name + "年龄：" + age;
// 模板字符串套路
var str = `姓名:${name}年龄${age}`
```
# 箭头函数
    另类的函数声明
1. 简单应用
```javascript
// 老套路
var f = function(a) {
  return a;
};
// 箭头函数套路
var f0 = () => 5;
var f1 = (a) => a;
var f2 = (a, b) => a+b; // var f2 = function(a, b){return a+b}
```
2. 箭头函数之坑-1
```javascript
var person = {
    name:'小杰',
    age:18,
    fun1: function(age) {
      //老套路定义的方法，this对象指向本对象
        return this.age + age;
    },
    func2:(age)=>{
        // 箭头函数有个坑，this指向window对象
        return this.age + age;
    }
};
```
# 对象的单体模式
```javascript
var person = {
    name:'小杰',
    age:18,
    fun1(age) {
      //老套路定义的方法，this对象指向本对象
        return this.age + age;
    }
};
```
# ES6面向对象

```javascript
//ES5构造函数的方式创建对象
function Person(name, age) {
  this.name = name;
  this.age = age;
}
// 给对象添加方法
Person.prototype.showName = function() {
  return this.name;
};
Person.prototype.showage = function() {
  return this.age;
};
var a = new Person('xiaojie', 18);

// ES6 创建对象

class Person{
    // 构造函数
    constructor(name, age){
        this.name = name;
        this.age = age;
    }// 注意此处不能加逗号
    showName(){
        return this.name;
    }
    showAge(){
        return this.age
    }
}
var a = new Person('xiaojie', 18);
```