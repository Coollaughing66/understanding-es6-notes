# Symbol和Symbol属性

## 新建Symbol

```javascript
let firstName=Symbol();
let person={};
person[firstName]="axuebin";
console.log(person[firstName]);//axuebin
```

`Symbol`函数接受一个可选参数，添加一段文本描述。

```javascript
let firstName=Symbol("first name");
```

`typeof`可以用来检测`Symbol`类型。

## Symbol共享体系

ES6提供一个可以随时访问的全局Symbol注册表。

如果想创建一个可共享的`Symbol`,要使用`Symbol.for()`方法。接受一个参数表示即将创建的`Symbol`的字符串标识符。

这个方法会在全局Symbol注册表中搜索对应的`Symbol`是否存在，如果存在则返回；否则，就创建一个新的`Symbol`，注册并返回。

## Symbol与类型强制转换


