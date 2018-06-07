# 对象

## 语法
对象两种形式： 声明形式和构造形式。

```javascript
  var myObj = {
    key:value
  };
```

```javascript
  var myObj = new Objec();
  myObj.key = value;
```
## 类型
string、number、Object，null、undefined、boolean

## 内建对象

```javascript
  var strPrimitive = "Hello, javascript!";
  typeof stPrimitive;
  strPrimitive instanceof String; //false

  var strObject = new String("Hello, javascript!");
  typeof strObject;
  strObject instanceof String; //true

  Object.prototype.toString.call(strObject); //[object String]
```
在必要的时候语言会自动地将 `"String"` 基本类型强制转换为 `String` 对象类型。
`null` 和 `undefined` 没有对象包装的形式，仅有它们的基本类型值。相比之下， `Date` 的值仅可以由它们的构造对象形式创建，因为它们没有对应的书面形式。

## 内容
对象的内容由存储在特定命名的位置上的值组成， 我们称这些值为属性。
属性访问： `.` 和 `[".."]`。
由于 `[".."]` 可以动态组建字符串的值。
在对象中， 属性名总是字符串。 如果你使用`string`以外的(基本)类型值，它会首先被转换为字符串。

```javascript
var myObject = {};
myObject[myObject] = "baz";

myObject["[object Object]"]; //"baz"
```

## 计算型属性名
在一个字面对象声明的键名称位置，你可以指定一个表达式， 用`[]`括起来：
```javascript
var prefix = "foo";
var myObject = {
  [prefix + "bar"]: "hello",
  [prefix + "baz"]: "world",
};

myObject["foobar"]; //hello
myObject["foobaz"]; //world
```
## 属性 vs 方法
每次你访问一个对象的属性都是一个属性访问， 无论你得到什么类型的值。如果你恰好从属性访问中得到一个函数，它也没有魔法般地在那时成为一个"方法".


就算声明一个函数表达式作为字面对象的一部分，那个函数都不会魔法般地属于这个对象，仍然仅仅是同一个函数对象的多个引用罢了。

```javascript
  var myObject = {
    foo: function foo(){
      console.log("foo");
    }
  };

  var someFoo = myObject.foo;

  someFoo; //function foo(){...}
  myObject.foo; //function foo(){...}
```
## 数组
下标是一个非负整数。
数组也是对象，所以虽然每个索引都是正整数， 我们还可以在数组上添加属性：

```javascript
  var myArray = ["foo", 42, "bar"];
  myArray.baz = "baz";
  myArray.length; //3
  myArray.baz; //"baz"
```
注意： 如果试图在一个数组上添加属性，但是属性名看起来像一个数字，那么最终它会成为一个数字索引(也就是改变了数组的内容)：

```javascript
  var myArray = ["foo", 42, "bar"];
  myArray["3"] = "baz";
  myArray.length; //4
  myArray[3]; //"baz"
```
## 复制对象
拷贝分为浅拷贝和深拷贝。
提取一个函数源代码的`toString()`序列化表达：
```javascript
  var newObj = JSON.parse(JSON.stringfy(someObj));
```
ES6的`Object.assign(..)`，在源对象上迭代所有的可枚举，并把它们拷贝到目标对象上(仅通过赋值)。

## 属性描述符
```javascript
  var myObject = {
    a: 2
  };

  Object.getOwnPropertyDescriptor(myObject, "a");
  //{
  // configurable: true
  // enumerable: true
  // value: 2
  // writable: true
  //}
```
属性描述符包含4个性质： value,enumerable,value,writable
用`Object.defineProperty(..)`来添加新属性。

## 不可变性

### 对象常量
通过将`writable:false`与`configurable:false`组合，可以实质上创建了一个作为对象属性的常量。
### 防止扩展
如果你想防止一个对象被添加新的属性，但另一方面保留其他既存得的对象属性，可以调用`Object.preventExtensions(...)`:
```javascript
  var myObject = {
    a: 2
  };

  Object.preventExtensions(myObject);

  myObject.b = 3;
  myObject.b; //undefined
```
### 封印
`Object.seal(..)`创建一个"封印"的对象，这意味着它实质上在当前的对象上调用`Object.preventExtensions(..)`,并且将它所有既存属性标记为`configurable:false`
所以既不能添加新的属性，也不能删除和重新配置已有属性
### 冻结
`Object.freeze(..)`创建一个冻结的对象。这意味着它调用`Object.seal(..)`，并且将所有的"数据访问属性"设置为`writable:false`，所以它们的值不可改变。

## 存在性
`in`操作符会检查属性是否存在于对象中，或者是否存在于`[[Prototype]]`链对象遍历的最高层中。而`hasOwnProperty(..)`仅仅检查对象是否拥有该属性，但不会查询`[[Prototype]]`链。

### 枚举
```javascript
  var myObject = {};

  Object.defineProperty(
    myObject,
    "a",
    { enumerable: true, value: 2 }
  );

  Object.defineProperty(
    myObject,
    "b",
    { enumerable: false, value: 3 }
  );

  myObject.b; //3
  myObject.hasOwnProperty("b"); // true
  ("b" in myObject); //true

  for(var k in myObject){
    console.log(k, myObject[k]);
  }//"a" 2

  Object.keys(myObject); //["a"]
  Object.getOwnPropertyNames(myObject); //["a", "b"]
```
`Object.keys(..)`返回一个所有可枚举属性的数组，`Object.getOwnPropertyNames(..)`返回一个所有属性的数组，不论能不能枚举。
