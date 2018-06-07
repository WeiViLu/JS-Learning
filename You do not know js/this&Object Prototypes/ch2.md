# this 豁然开朗
## 调用点
调用点：函数在代码中被调用的位置

## 默认绑定
```javascript
function foo(){
  console.log(this.a);
}

var a = 2;
foo(); //2

----
function foo(){
  "use strict"
  console.log(this.a);
}

var a = 2;
foo(); // TypeError: `this` is `undefined`
```

对于默认绑定来说，全局对象是唯一合法的。 `foo()` 的调用点的strict mode状态与此无关。
```javascript
function foo(){
  console.log(this.a);
}

var a = 2;
(function(){
  "use strict";

  foo();
})();

## 隐含绑定
function foo(){
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo();
```
调用点使用`obj`环境来引用函数，`obj`对象在函数被调用的时间点上“拥有”或“包含”这个函数引用。
因为`obj`是`foo()`调用的`this`， 所以`this.a`是`obj.a`的同义词。

只有对象属性引用链的最后一层是影响调用点的。

```javascript
function foo(){
  console.log(this.a);
}

var obj2 = {
  a: 42,
  foo: foo
};

var obj1 = {
  a: 42,
  obj2: obj2
};

obj1.obj2.foo(); //42
```

### 隐含丢失
隐含丢失就是隐含绑定丢失了它的绑定，这就意味着它会会退到默认绑定，根据`strict mode`的状态，其结果不是全局对象就是`undefined`。

```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

var bar = obj.foo;

var a = "oops, global";

bar(); // "oops, global"
```

因为起作用的调用点是`bar`,所以默认绑定在这里，在全局。
```javascript
function foo() {
  console.log(this.a);
}

function doFoo(fn) {
  fn(); //调用点
}

var obj = {
  a: 2,
  foo: foo
};

var a = "oops, global";

doFoo(obj.foo);
```
参数的传递还是一个隐含的赋值，隐含的引用赋值，所以结果同上。

```javascript
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

var a = "oops, global"; // `a` 也是一个全局对象的属性

setTimeout( obj.foo, 100 ); // "oops, global"
```

## 明确绑定
```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

foo.call(obj);
```
通过`call`强制将`this`指向`obj`。
如果传递一个简单基本类型值(`string`,`boolean`,或者`number`类型)作为`this`绑定，那么这个基本类型值会被包装在它的对象类型中(分别是`new String(..)`, `new Boolean(..)`, 或`new Number(..)`)这通常称为"封箱"

### 硬绑定
```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

var bar = function() {
  foo.call(obj);
};

bar(); //2
setTimeout(bar, 100); //2
bar.call(window); //2
```

无论你过后怎样调用函数 bar，它总是手动使用`obj`调用`foo`。这种绑定即明确又坚定，所以我们称之为`硬绑定`(hard binding）

用硬绑定讲一个函数包装起来的最典型的方法是为所传入的参数和传出的返回值创建一个通道。

```javascript
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}

var obj = {
  a: 2
};

var bar = function() {
  return foo.apply(obj, arguments);
}

var b = bar(3);
console.log(b); //5
```

## `new`绑定
当在函数前面被加入`new`调用时，也就是构造器调用时，下面事情会自动完成：
1. 一个全新的对象会凭空创建(就是被构建)
2. 这个新构建的对象会被接入原型链
3. 这个新构建的对象被设置为函数调用的`this`绑定
4. 除非函数返回一个它自己的其他对象，否则这个被`new`调用的函数将自动返回这个新构建的对象。

```javascript
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log(bar.a);
```

通过在前面使用`new`来调用`foo(..)`，我们构建了一个新的对象并把这个新对象作为`foo(..)`调用的`this`。

## 一切皆有顺序
明确绑定的优先权高于隐含绑定

```javascript
function foo(someting){
  this.a = something;
}

var obj1 = {
  foo: foo
};

var obj2 = {};

obj1.foo(2);
console.log(obj1.a);

obj1.foo.call(obj2, 3);
console.log(obj2.a);

var bar = new obj1.foo(4);
console.log(obj1.a);
console.log(bar.a);
```

new绑定优先权高于隐含绑定
注意：`new` 和 `call/apply`不能同时使用。

## 判定this
判定规则：
1. 如果函数是通过`new`被调用的，`this`是新构建的对象。 var bar = new foo();
2. 如果函数是通过`call`或者`apply`被调用的，`this`是那个被明确指定的对象。 var bar = foo.call(obj2);
3. 如果函数是通过环境对象被调用的，`this`就是那个环境对象。 var bar = obj1.foo();
4. 否则，使用默认的`this`。如果在严格模式下，就是`undefined`，否则就是`global`对象。

## 词法this
箭头函数从封闭它的(函数或全局)作用域采用`this`绑定。一个箭头函数的词法绑定是不能被覆盖的(就连`new`也不行)。
