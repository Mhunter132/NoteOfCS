# 第一章 块级绑定

## 1.1 ES6 变量声明

ES6上流行的方式：默认情况使用let，值不改变的时候使用const

1. ### var 声明与变量绑定

在函数内var声明的变量会提升到顶部，这也就是所说的**变量提升**

2. ### let 声明

参考**局部变量**，且let与var二者禁止重复定义

不允许在定义之前使用变量

3. ### const 常量

const 声明的变量不可以更改，声明及要求初始化，也是块级声明，const声明的对象内部的值可以改变

## 1.2 for内作用域

**var版本**

```
var funcs = [];
for(var i = 0; i<10 ;i++){
 	funcs.push(func(){
 	console.log(i);
 	})
}
funcs.forEach(){
	func();
}
```

会输出10个10，因为变量共享导致。

[PS] 三种函数表达式：

```
 +function () {   
 };  

(function () {  
  });  

void function() {  
 };  
```

**let版本**

```javascript
for (let key in object) {  // let创建的是副本
funcs.push(function() {
console.log(key);
});
}
```

类似python

## 1.3 全局块的绑定

全局不安全

```javascript
var RegExp = "Hello!";
	console.log(window.RegExp); // "Hello!"
var ncz = "Hi!";
	console.log(window.ncz); // "Hi!"
```

全局安全

```javascript
let RegExp = "Hello!";
	console.log(RegExp); // "Hello!"
	console.log(window.RegExp === RegExp); // false
const ncz = "Hi!";
	console.log(ncz); 
```

## 总结

1. let const更像是局部变量

2. for内循环 

   ```
   let key in words
   ```

3. let const 不影响全局变量



# 第二章 

# 第四章

### 4.1 初始化变量与参数重名

这是重名问题

```java
function createPerson(name, age) {
return {
name: name,
age: age
	};
}
```

这是办法

```java
function createPerson(name, age) {
return {
name,
age
	};
}
```

