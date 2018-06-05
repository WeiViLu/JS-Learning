# 对象

## 语法

两种形式：字面形式和构造形式。
字面形式：

```javascript
var myObject = {
    key: value
}
```

构造形式：
```javascript
var myObject = new Object();
myObject.key = value;
```
## 类型

六种基本类型:

* `String`
*  `Number`
*  `Undefined`
*  `Object`
*  `Null`
*  `Boolean`
*  `String`

## 内建对象

* `String`
* `Number`
* `Boolean`
* `Object`
* `Function`
* `Array`
* `Date`
* `RegExp`
* `Error`

## 内容

引擎会会根据自己的实现存储值，实际上这些值并不是存储在对象的内部，在容器内存储的是这些属性的名称，它们像指针一样指向存储这些值的地方。

```javascript
 var myObject = {
    a: 2
};

myObject.a; //2
myObject["a"]; //2
```

## 计算型属性名

将一个表达式作为键名称，即为计算型属性名。

```javascript
var suffix = "name";
var myObject = {
    ["girl"+suffix]: "Vivi",
    ["boy"+suffix]: "Jack",
};

myObject.girlname; //Vivi
myOject.boyname; //Jack
```

## 属性 vs 方法

对象的访问分为属性访问和方法访问。
如果访问的值恰好是个函数就是方法访问。

## 数组

数组也采用[]访问，数组采用数字索引，这就是值被存储的位置，也就是数组的下标，下标是不小于0的数字。

```javascript
 var myArray = ["foo",42,"bar"];
 
 myArray.length; //3
 myArray[0]; //"foo"
 myArray[2]; //bar
```

也可以给数组添加属性，但是不会改变数组的长度。
```javascript
    myArray.baz = "baz";
    myArray.baz; // "baz"
    myArray.length; //3
    myArray; //["foo",42,"bar",baz:"baz"]
```

## 复制对象

拷贝分深拷贝和浅拷贝，有待深究。

## 属性描述符


```javascript
 var myObject = {};
 Object.defineProperty(myObject,a,{
    value:2,
    writable:true,
    configurable:true,
    enumerable:true
 });
 
 myObject.a; //2
```

以上这段代码还包含另外三个性质：writable、configurable、enumerable.

#### 可写性（writable）
```javascript
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: false, // 不可写！
	configurable: true,
	enumerable: true
} );

myObject.a = 3;

myObject.a; // 2
```

#### 可配置性（configurable）

将`configurable`属性设置为`false`是一个单向操作，不可撤销！
注：虽然`configurable:false`，但是`writable`可以从`true`设置为`false`，但是不是可以从`flase`设置为`true`。此外，delete属性将不起作用。看一段代码：

```javascript
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true, 
	configurable: false, //不可配置
	enumerable: true
} );

myObject.a; //2
myObject.a = 5; 
myOject.a; //5

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: false, 
	configurable: false, //不可配置
	enumerable: true
} );

myObject.a = 6;
myObject.a; //5

delete myObject.a; //false
myObject.a; //5

```


#### 可枚举性（enumerable）

如果将对象属性的`enumerable`设置为`false`，遍历该对象时将取不到该属性。

```javascript
 var myObject = {a:5,b:6,c:7,d:8};
 
 Object.defineProperty(myObject,"d",{
    value:8,
    writable:true,
    configurable:true,
    enumerable:false,
 });
 
 for(var i in myObject){
    console.log(i);
 } // a b c
```

#### 不可变性

##### 对象常量

通过将`writable:false`与`configurable:false`组合，可以创建一个对象属性的常量，既不可以更改也不可以配置。如：

```javascript
var myObject = {};

Object.defineProperty( myObject, "FAVORITE_NUMBER", {
	value: 42,
	writable: false,
	configurable: false
} );
```

##### 防止扩展
`Object.preventExtensions(...)`

```javascript
var myObject = {
    a:2
}；
Object.preventExtensions(myObject);
myObject.b = 3;
myObject.b; //undefined
```

##### 封印
`Object.seal(...)`创建一个封印的对象。
该对象既不能添加更多属性，也不能配置或者删除属性。

##### 冻结
`Object.freeze(...)`创建一个冻结的对象。
在`Object.seal(...)`的基础上，加一个`writable:false`

#### [[Get]]
```javascript
var myObject = {
    a: undefined
}

myObject.a; //undefined
myObject.b; //undefined
```

如果仅仅是考察值，无法判断属性是不存在还是值为undefined。

#### [[Put]]
如果属性存在，[[Put]]算法大致如下：
1、这个是属性访问符吗？如果是，而且是setter，就调用setter。
2、如果`writable:false`，在非 strict mode 下无声地失败，或者在 strict mode 下抛出 TypeError。
3、否则，像往常一样设置既存属性。

## 迭代

ES5 还为数组加入了几个迭代帮助方法，包括 forEach(..)、every(..)、和 some(..)。这些帮助方法的每一个都接收一个回调函数，这个函数将施用于数组中的每一个元素，仅在如何响应回调的返回值上有所不同。

注意： 我们使用一个 ES6 的 Symbol：Symbol.iterator 来取得一个对象的 @@iterator 内部属性。我们在本章中简单地提到过 Symbol 的语义（见“计算型属性名”），同样的原理也适用于这里。你总是希望通过 Symbol 名称，而不是它可能持有的特殊的值，来引用这样特殊的属性。另外，尽管这个名称有这样的暗示，但 @@iterator 本身不是迭代器对象， 而是一个返回迭代器对象的方法。
