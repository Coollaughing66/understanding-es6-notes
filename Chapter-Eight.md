# 迭代器和生成器

## 循环语句的问题

多个循环嵌套需要追踪多个变量，代码的复杂度会大大增加，迭代器旨在消除这种复杂性并减少循环中的错误。

## 什么是迭代器

> 迭代器是一种特殊对象，它具有一些专门为迭代过程设计的专有接口，所有的迭代器对象都有一个`next()`方法，每次调用都返回一个结果对象。

结果对象有两个属性：

- value：表示下一个将要返回的值
- done：当没有更多可返回值的时候返回true

当最后一个值被返回之后再调用`next()`，迭代器会返回`undefined`。

可以用ES5来编写一个迭代器：

```javascript
function createIterator(items){
  var i=0;
  return{
    next:function(){
      var done=(i>=items.length);
      var value=!done?items[i++]:undefined;
      return{
        done:done,
        value:value
      }
    }
  }
}
var iterator=createIterator([1,2,3]);
console.log(iterator.next()); // "{value:1,done:false}"
console.log(iterator.next()); // "{value:2,done:false}"
console.log(iterator.next()); // "{value:3,done:false}"
console.log(iterator.next()); // "{value:undefined,done:true}"
```

当最后一个值返回之后，无论执行几次`iterator.next()`，都只是会返回`"{value:undefined,done:true}"`。

## 什么是生成器

> 生成器是一种返回迭代器的函数，通过`function`关键字后的星号`*`来表示，函数中会用到新的关键字`yield`。

```javascript
function *createIterator(items){
  for(let i=0;i<items.length;i++){
    yield items[i];
  }
}
let iterator=createIterator([1,2,3]);
console.log(iterator.next()); // "{value:1,done:false}"
console.log(iterator.next()); // "{value:2,done:false}"
console.log(iterator.next()); // "{value:3,done:false}"
console.log(iterator.next()); // 
```

每次遇到`yield`语句就会停止，每次调用`next()`方法，循环会继续运行并执行下一条`yield`语句。

### 生成器函数表达式

通过函数表达式来创建生成器，只需在`function`关键字和小括号中间添加一个星号(`*`)即可。

注意：不能用箭头函数来创建生成器。

## 可迭代对象和for-of循环

在ES6中，所有的集合对象（数组，Set和Map）和字符串都是可迭代对象，这些对象中都有默认的迭代器。

可以通过`for of`来遍历这些对象。

`for-of`循环每执行一次都会调用可迭代对象的`next()`方法。并将返回的结果对象的`value`属性存储在一个变量中，直到返回对象的`done`属性值为`true`。

```javascript
let values=[1,2,3];
for(let item of values){
  console.log(item); // 1,2,3
}
```

### 访问默认迭代器

可以通过`Symbol.iterator`来访问对象默认的迭代器，这样就可以用来检测对象是否为可迭代对象：

```javascript
function isIterable(object){
  return typeof object[Symbol.iterable]==="function";
}
```

### 创建可迭代对象

默认情况下，开发者定义的对象都是不可迭代对象，但如果给`Symbol.iterator`属性添加一个生成器，则可以将其变为可迭代对象。

```javascript
let c={
  items:[1,2,3]
}
for(let item of c){
  console.log(item) //c[Symbol.iterator] is not a function
}

let c={
  items:[1,2,3],
  *[Symbol.iterator](){
    for(let item of this.items){
      yield item;
    }
  }
}
for(let item of c){
  console.log(item) // 1,2,3
}
```

## 内建迭代器

### 集合对象迭代器

在ES6中有三种类型的集合对象：数组、Map集合与Set集合，都内建了以下三种迭代器：

- entries()：多个键值对
- values()：集合的值
- keys()：集合的键名

#### entries()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
console.log(array.entries()) // Array Iterator {}
for(let item of array.entries()){
  console.log(item) // [0,1],[1,2],[2,3]
}
for(let item of set.entries()){
  console.log(item) // [1,1],[2,2],[3,3]
}
for(let item of map.entries()){
  console.log(item) // ["name","axuebin"],["age","25"]
}
```

输出`array.entries()`是`Array Iterator {}`，说明是输出一个迭代器，并不是直接是值，还需要通过`for-of`来循环遍历元素。

#### values()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
for(let item of array.values()){
  console.log(item) // array.values(...)[Symbol.iterator] is not a function
}
for(let item of set.values()){
  console.log(item) // 1,2,3
}
for(let item of map.values()){
  console.log(item) // axuebin,25
}
```

在对数组元素执行`values()`时报错了。。不知道为什么。。

#### keys()迭代器

```javascript
let array=[1,2,3];
let set=new Set([1,2,3]);
let map=new Map([["name","axuebin"],["age","25"]]);
for(let item of array.keys()){
  console.log(item) // 0,1,2
}
for(let item of set.keys()){
  console.log(item) // 1,2,3
}
for(let item of map.keys()){
  console.log(item) // name,age 
}
```

#### 不同集合类型的默认迭代器

- 数组、Set集合：values()
- Map集合：entries()

所以直接用`for-of`对集合进行循环遍历即可。

### NodeList迭代器

> DOM标准中有一个NodeList类型，document对象中的所有元素都通过这个类型来表示。

```javascript
var divs=document.getElementsByTagName("div");
for(let div of divs){
  console.log(div.id);  
}
```

## 高级迭代器功能

> 未完待续~



