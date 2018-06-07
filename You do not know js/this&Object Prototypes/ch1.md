# this 是什么
this 代表特殊的关键字

```javascript
function identify() {
  return this.name.toUpperCase();
}

function speak() {
  var greeting = "Hello, I'm " + identify.call( this );
  console.log( greeting );
}

var me = {
  name: "Kyle"
};

var you = {
  name: "Reader"
}

identify.call(me);
identify.call(you);

speak.call(me);
speak.call(you);
```
---
```javascript
function foo(num) {
  console.log("foo:" + num);
  this.count++;
}

foo.count = 0;

var i;

for(i=0; i<10; i++){
  if (i>5) {
    foo(i);
  }
}

console.log(foo.count);
```
`this` 不是编写时绑定，而是运行时绑定。它依赖于函数调用的上下文条件。this绑定与函数声明的位置没有任何关系，而与函数被调用的方式紧密相连。
`this` 实际上是在函数被调用是建立的一个绑定， 它指向什么完全是由函数被调用的调用点来决定的。