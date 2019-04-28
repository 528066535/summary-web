
    开心，一个继承可以写三章

 ### 一. Class 介绍

 #### 简单的例子

 ES5 的继承前面用了两章来描述，其实现方式确实很复杂，对于新手来说上手难。

 ES6 引入 Class 概念, 用来实现继承, ES6 的 Class 可以实现绝大部分 ES5中的继承, Class 更加注重面向对象，并且和 c++ java 语言开始有共性之处。

 ES6 的类，完全可以看作构造函数的另一种写法。

 ```
 class Point {
   doSomeThing() {
       console.log('doSomeThing');
   }
 }

 // 1) class 声明的对象也是 function 对象
 typeof Point // "function"
 // 2) 和 ES5 保持一致 原型对象的 constructor 指向构造函数
 Point === Point.prototype.constructor // true

 var b = new Point();
 b.Point() // "doSomeThing"
 ```

 注意：构造函数的prototype属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的prototype属性上面。
 另外，类的内部所有定义的方法，都是不可枚举的（non-enumerable）。

 #### constructor

 constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，
 一个空的constructor方法会被默认添加。constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。

 ```
 class Foo {
   constructor() {
     return Object.create(null);
   }
 }

 new Foo() instanceof Foo
 // false
 ```

 注意：类必须使用new调用，否则会报错。

 #### 类的实例

  与 ES5 一样，实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）。

  ```
  //定义类
  class Point {

    constructor(x, y) {
      this.x = x;
      this.y = y;
    }

    toString() {
      return '(' + this.x + ', ' + this.y + ')';
    }

  }

  var point = new Point(2, 3);

  point.toString() // (2, 3)

  point.hasOwnProperty('x') // true
  point.hasOwnProperty('y') // true
  point.hasOwnProperty('toString') // false
  point.__proto__.hasOwnProperty('toString') // true
  ```

 #### Class 表达式

 与函数一样，类也可以使用表达式的形式定义。

 ```
 const MyClass = class Me {
   getClassName() {
     return Me.name;
   }
 };
 ```

 注意：上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是Me，但是Me只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用MyClass引用。

 #### static 静态方法

 (1) 类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

 ```
 class Foo {
   static classMethod() {
     return 'hello';
   }
 }

 Foo.classMethod() // 'hello'

 var foo = new Foo();
 foo.classMethod()
 // TypeError: foo.classMethod is not a function
 ```

 (2) 如果静态方法包含this关键字，这个this指的是类，而不是实例。

 ```
 class Foo {
   static bar() {
     this.baz();
   }
   static baz() {
     console.log('hello');
   }
   baz() {
     console.log('world');
   }
 }

 Foo.bar() // hello
 ```

 (3) 父类的静态方法，可以被子类继承。

 (4) 静态方法也是可以从super对象上调用的。

 ```
 class Foo {
   static classMethod() {
     return 'hello';
   }
 }

 class Bar extends Foo {
   static classMethod() {
     return super.classMethod() + ', too';
   }
 }

 Bar.classMethod() // "hello, too"
 ```

 #### 实例属性的新写法

 实例属性除了定义在constructor()方法里面的this上面，也可以定义在类的最顶层。

 ```
 class IncreasingCounter {
   constructor() {
     this._count = 0;
   }
   get value() {
     console.log('Getting the current value!');
     return this._count;
   }
   increment() {
     this._count++;
   }
 }

 // 同上
 class IncreasingCounter {
   _count = 0;
   get value() {
     console.log('Getting the current value!');
     return this._count;
   }
   increment() {
     this._count++;
   }
 }

 // 最佳写法
 class foo {
   bar = 'hello';
   baz = 'world';

   constructor() {
     // ...
   }
 }
 ```

 #### new.target 属性

 new是从构造函数生成实例对象的命令。ES6 为new命令引入了一个new.target属性，该属性一般用在构造函数之中，返回new命令作用于的那个构造函数。
 如果构造函数不是通过new命令或Reflect.construct()调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数是怎么调用的。

 ```
 function Person(name) {
   if (new.target !== undefined) {
     this.name = name;
   } else {
     throw new Error('必须使用 new 命令生成实例');
   }
 }

 // 另一种写法
 function Person(name) {
   if (new.target === Person) {
     this.name = name;
   } else {
     throw new Error('必须使用 new 命令生成实例');
   }
 }

 var person = new Person('张三'); // 正确
 var notAPerson = Person.call(person, '张三');  // 报错
 ```

 子类继承父类时，new.target会返回子类。利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。

 ```
 class Shape {
   constructor() {
     if (new.target === Shape) {
       throw new Error('本类不能实例化');
     }
   }
 }

 class Rectangle extends Shape {
   constructor(length, width) {
     super();
     // ...
   }
 }

 var x = new Shape();  // 报错
 var y = new Rectangle(3, 4);  // 正确
 ```

 ### 二. Class 继承

 #### extends

 可以通过extends关键字实现继承，相比于ES5，这种方式要简单很多。

 注意：

 1) 子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，
 得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。

 2) 在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。

 如果子类没有定义 constructor 方法，则方法会被默认添加。

 ```
 class ColorPoint extends Point {
   constructor(x, y, color) {
     super(x, y); // 调用父类的constructor(x, y)
     this.color = color;
   }

   toString() {
     return this.color + ' ' + super.toString(); // 调用父类的toString()
   }
 }

 let cp = new ColorPoint(25, 8, 'green');

 cp instanceof ColorPoint // true
 cp instanceof Point // true
 ```

 #### super

 super这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

 (1) super 作为函数调用时，代表父类的构造函数。

 注意：子类的构造函数必须执行一次super函数。super执行时，this表示子类实例。super() 只能用在子类的构造函数之中，用在其他地方就会报错。

 (2) super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

 注意：由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。

 ```
 //例1
 class A {
   constructor() {
     this.p = 2;
   }
 }

 class B extends A {
   get m() {
     return super.p;
   }
 }

 let b = new B();
 b.m // undefined

 //例2
 class A {}
 A.prototype.x = 2;

 class B extends A {
   constructor() {
     super();
     console.log(super.x) // 2
   }
 }

 let b = new B();
 ```




 ### 三. 总结

 ES5中的继承：

 利用 prototype 赋值原型对象 实现继承 实例属性和方法的继承，原型上属性的修改会印象到所有实例，实例上基础类型的修改会覆盖原型对象上的实例，
 而修改实例上引用类型属性会影响到其他实例；

 借用构造函数实现,实例属性和方法的继承，这里新建的实例的属性互不影响；

 利用寄生式继承实现。

 ES6中：

 利用class定义类，extends实现类的继承；

 子类constructor里调用super()（父类构造函数）实现 实例属性和方法的继承；

 子类原型继承父类原型，实现 原型对象上方法的继承。