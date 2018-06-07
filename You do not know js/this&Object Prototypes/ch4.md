# 混合对象“类”
面向类的设计模式：实例化、继承、（相对）多态。

## 类理论
### “类”设计模式
面向过程和面向类
### JavaScript中的“类”
类是一种设计模式，其他语言中的类和Javascript中使用的类并不一样。
## 类的机制
### 建造
一个类就是一张蓝图。 为了获得真正可以交互的对象，我们必须按照类来建造一个东西，这个东西通常被称为实例，有需要的话，我们可以直接在实例上调用方法并访问其所有共有数据属性。
### 构造函数
类实例是有一个特殊的类方法构造的，这个方法通常和类名相同，被称为构造函数。
类构造函数属于类，而且通常和类同名。此外，构造函数大多需要用new来调，这样语言引擎才知道你想要构造一个新的类的实例。
## 类的继承
### 多态
子类可以重写从父类继承过来的属性和方法，就称为类的多态性。
super的含义是“超类”，表示当前类的父类/祖先类。
在JavaScript中“类”是属于构造函数的。由于JavaScript中父类和子类的关系只存在于两者构造函数对应的.prototype对象中，因此它们的构造函数之间并不存在直接联系，从而无法简单地实现两者的相对引用。
类继承意味着拷贝。
### 多重继承
JavaScript 更简单：它不为“多重继承”提供原生机制。
### 混合(Mixin)
在JavaScript中没有“类”可以拿来实例化，只有对象。而且对象也不会被拷贝到另一个对象中，而是被链接在一起。
开发者用mixins(混合)来模拟这种缺失的类的拷贝行为。有两种mixin：明确的和隐含的。
#### 明确的Mixin
```javascript
  function mixin( sourceObj, targetObj ) {
    for (var key in sourceObj) {
      // 仅拷贝非既存内容
      if (!(key in targetObj)) {
        targetObj[key] = sourceObj[key];
      }
    }

    return targetObj;
  }

  var Vehicle = {
    engines: 1,

    ignition: function() {
      console.log( "Turning on my engine." );
    },

    drive: function() {
      this.ignition();
      console.log( "Steering and moving forward!" );
    }
  };

  var Car = mixin( Vehicle, {
    wheels: 4,

    drive: function() {
      Vehicle.drive.call( this );
      console.log( "Rolling on all " + this.wheels + " wheels!" );
    }
  } );
```
`Car`现在拥有了一份从`Vehicle`得到的属性和函数的拷贝。技术上讲，函数实际上没有被复制，而是指向函数的引用被复制了。
`Car`已经有了`drive`属性(函数)， 所以这个属性引用没有被覆盖。

```javascript
  function mixin(sourceObj, targetObj){
    for(var key in sourceObj){
      if(!(key in targetObj)){
        targetObj[key] = sourceObj[key];
      }
    }
    return targetObj;
  }
```
由于两个对象还共享它们的共通函数的引用， 这意味着即便手动将函数从一个对象拷贝到另一个对象中，也不能实际上模拟发生在面向类的语言中的从类到实例的真正的复制。

```javascript
  function Vehicle(){
    this.engines = 1;
  }

  Vehicle.prototype.ignition = function() {
    console.log("Turning on my engine");
  }
  Vehicle.prototype.drive = function() {
    this.ignition();
    console.log("Steering and moving forward!");
  }

  //"寄生类"Car
  function Car(){
    var car = new Vehicle();

    car.wheels = 4;

    var vehDrive = car.drive;

    car.drive = function() {
      vehDrive.call(this);
      console.log("Rolling on all" + this.wheels + "wheels!");
    }

    return car;
  }

  var myCar = new Car();
  myCar.drive();
```
当我们调用`new Car()`时，一个新对象被创建并被`Car`的`this`所引用。但是由于我们没有使用这个对象，而是返回我们自己的`car` 对象，所以这个初始化创建的对象就被丢弃了。

#### 隐含的Mixin
```javascript
  var Something = {
    cool: function() {
      this.greeting = "Hello World";
      this.count = this.count ? this.count + 1 : 1;
    }
  };

  Something.cool();
  Something.greeting; //"Hello World"
  Something.count; //1

  var Another = {
    cool: function(){
      //隐式地将`Something`混入`Another`
      Something.cool.call(this);
    }
  };

  Another.cool();
  Another.greeting; //"Hello world"
  Another.count; //1 (不会和Something共享状态)
```
## 复习
类意味着拷贝。
JavaScript不会自动地在对象之间创建拷贝。
mixin模式常用于在某种程度上模拟类的拷贝行为。
明确的mixin和类拷贝又不完全相同，因为对象仅仅是共享的引用被复制，不是对象/函数自身被复制。

