
    开心，一个继承可以写三章

 ### 一. Class

 #### Class 介绍

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


 ### 总结

 ES5中的继承：

 利用 prototype 赋值原型对象 实现继承 实例属性和方法的继承，原型上属性的修改会印象到所有实例，实例上基础类型的修改会覆盖原型对象上的实例，
 而修改实例上引用类型属性会影响到其他实例；

 借用构造函数实现,实例属性和方法的继承，这里新建的实例的属性互不影响；

 利用寄生式继承实现。

 ES6中：

 利用class定义类，extends实现类的继承；

 子类constructor里调用super()（父类构造函数）实现 实例属性和方法的继承；

 子类原型继承父类原型，实现 原型对象上方法的继承。