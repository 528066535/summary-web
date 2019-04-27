
    继承又可以扯一章了

 ### 一. js原型（prototype）实现继承

    将构造函数的原型设置为另一个构造函数的实例对象，这样就可以继承另一个原型对象的所有属性和方法，可以继续往上，最终形成原型链。

  ```
    function Person () {
       this.head = '脑袋瓜子';
       this.emotion = ['喜', '怒', '哀', '乐']; //人都有喜怒哀乐
    }

    // 1) 子类：学生，继承了“人”这个类
    function Student(studentID) {
       this.studentID = studentID;
    }
    Student.prototype = new Person();

    var stu1 = new Student(1001);
    console.log(stu1.head); //脑袋瓜子
    console.log(stu1.emotion); //['喜', '怒', '哀', '乐']

    var stu2 = new Student(1002);
    console.log(stu2.head); //脑袋瓜子
    console.log(stu2.emotion); //['喜', '怒', '哀', '乐']

    // 2) 修改了实例上继承的基本属性，相当于屏蔽了原型对象原有的属性。这里用屏蔽表示再次创建stu2实例，会保持原有的原型对象的继承。
    stu1.head = '聪明的脑袋瓜子';
    console.log(stu1.head); //聪明的脑袋瓜子
    console.log(stu2.head); //脑袋瓜子

    // 3) 修改了实例上继承的引用类型的值，会影响到其他实例。
    stu1.emotion.push('愁');
    console.log(stu1.emotion); //["喜", "怒", "哀", "乐", "愁"]
    console.log(stu2.emotion); //["喜", "怒", "哀", "乐", "愁"]

    // 4) 原型对象的任何属性修改，都会影响到实例。stu1在第二步设置过，所以那时候已经屏蔽了原型对象原有的属性。
    Student.prototype.head = '笨的脑袋瓜子';
    console.log(stu1.head); //聪明的脑袋瓜子
    console.log(stu2.head); //笨的脑袋瓜子
  ```

 这种方式隐藏一个问题是在创建 stu1 和 str2 实例的时候没办法传递参数。

 ### 二. call , apply 实现继承 ( 借用构造函数实现继承 )

    为了解决原型中包含引用类型值的问题，开始使用借用构造函数，也叫伪造对象或经典继承。将原型链和借用构造函数的技术组合到一块，
    使用原型链实现对原型属性和方法的继承，而通过构造函数来实现对实例属性的继承。

 ```
      function Person () {
        this.head = '脑袋瓜子';
        this.emotion = ['喜', '怒', '哀', '乐']; //人都有喜怒哀乐
      }

      // 1) 子类：学生，继承了“人”这个类, 并且可以附带参数
      function Student(studentID) {
        this.studentID = studentID;
        //Person.call(this,name,age); 这里可以附带参数
        Person.call(this);
      }

      var stu1 = new Student(1001);
      console.log(stu1.emotion); //['喜', '怒', '哀', '乐']

      // 这里修改了 emotion 并没有对其他实例有影响，说明每个实例都会创建自己的 emotion 属性，他们互相没有影响。
      stu1.emotion.push('愁');
      console.log(stu1.emotion); //["喜", "怒", "哀", "乐", "愁"]

      var stu2 = new Student(1002);
      console.log(stu2.emotion); //["喜", "怒", "哀", "乐"]
  ```

  这种方式优点是简单易懂些，实例的属性都不会互相影响，也可以向 Person 传递参数。缺点是如果实例想公共同一参数，没有办法实现，而且如果相同
  的方法每次都会创建新的对象，更消耗内存。

 ### 三. 组合继承

    也叫伪经典继承，将原型链和借用构造函数的技术组合到一块。使用原型链实现对原型属性和方法的继承，而通过构造函数来实现对实例属性的继承。

 ```
     function Person () {
       this.head = '脑袋瓜子';
       this.time = [0,1];
       this.emotion = ['喜', '怒', '哀', '乐']; //人都有喜怒哀乐
     }

     // 1）将 Person 类中需共享的方法放到 prototype 中，实现复用
     Person.prototype.eat = function () {
       console.log('吃吃喝喝');
     }
     Person.prototype.sleep = function () {
       console.log('睡觉');
     }
     Person.prototype.run = function () {
       console.log('快跑');
     }
     Person.prototype.time = 0;

     // 3) 子类：学生，继承了“人”这个类。 每次新创建 Student 实例的时候都会调用 Student 构造方法，则每个子类实例都会创建各自的 emotion，head
     //等属性，这里的属性互补影响，并且会覆盖 Student 原型对象上的属性。
     function Student(studentID) {
       this.studentID = studentID;
       Person.call(this);
     }

     // 2) 设置 Student 的原型对象 Student 可以访问到 Person 的方法和属性。
     Student.prototype = new Person();  //此时 Student.prototype 中的 constructor 被重写了，会导致 stu1.constructor === Person
     // 注意: 设置 Student 的 constructor 的原因是：上方代码导致 Student.constructor 指向了 Person。
     Student.prototype.constructor = Student;  //将 Student 原型对象的 constructor 指针重新指向 Student 本身

     var stu1 = new Student(1001);
     console.log(stu1.emotion); //['喜', '怒', '哀', '乐']

     stu1.emotion.push('愁');
     console.log(stu1.emotion); //["喜", "怒", "哀", "乐", "愁"]

     var stu2 = new Student(1002);
     console.log(stu2.emotion); //["喜", "怒", "哀", "乐"]

     stu1.eat(); //吃吃喝喝
     stu2.run(); //快跑
     console.log(stu1.constructor);  //Student
 ```

 首先，我们将 Person 类中需要复用的方法提取到 Person.prototype 中，然后设置 Student 的原型对象为 Person 类的一个实例，这样 stu1
 就能访问到 Person 原型对象上的属性和方法了。其次，为保证 stu1 和 stu2 拥有各自的父类属性副本，我们在 Student 构造函数中，还是使用了
 Person.call ( this ) 方法。如此，结合原型链继承和借用构造函数继承，就完美地解决了之前这二者各自表现出来的缺点。

 但是实际上原理也是每个 Student 的实例，在新建的时候，都会在构造函数里创建一个新的 Person 对象，所以Person中的属性都互不影响，并且继承了
 Person 的原型对象。

 ### 四. 原型式继承

 #### 原型式继承本质其实就是个浅拷贝，以一个对象为模板复制出新的对象

 ```
    function object( o ){
       var G = function(){};
       G.prototype = o;
       return new G();
    }
    var obj = {
       name : 'ghostwu',
       age : 22,
       show : function(){
         return this.name + ',' + this.age;
       }
    };
    var obj2 = object( obj );
    console.log( obj2.name, obj.age, obj.show() );
 ```

 #### 因为原型式继承是个浅拷贝，所以引用类型的数据共享在不同的实例之间

 ```
    function object( o ){
    var G = function(){};
       G.prototype = o;
       return new G();
     }
     var obj = {
       skills : [ 'php', 'javascript' ]
     };
     var obj2 = object( obj );
     obj2.skills.push( 'python' );
     var obj3 = object( obj );
     console.log( obj3.skills ); //php,javascript,python
 ```

 #### 在es5中，新增了一个函数Object.create()实现了原型式继承

 ```
    var obj = {
       skills : [ 'php', 'javascript' ]
    };
    var obj2 = Object.create( obj );
    obj2.skills.push( 'python' );
    var obj3 = Object.create( obj );
    console.log( obj3.skills ); //php,javascript,python
 ```

 ### 五. 寄生式模式

 #### 一. 工厂模式 、 寄生构造函数模式 、 稳妥构造函数模式 创建对象

 (1) 工厂模式：

 ```
 function Factory(name, age) {
    let obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.sayName = function(){
        alert(obj.name);
    }
    return obj;
 }

 let obj1 = Factory('Bob', 11);
 let obj2 = Factory('Tom', 8);
 ```

 (2) 寄生构造函数模式

 ```
 function Factory(name, age) {
     let obj = new Object();
     obj.name = name;
     obj.age = age;

     obj.sayName = function(){
         alert(obj.name);
     }
     return obj;
 }

 let obj1 = new Factory('Bob', 11);
 let obj2 = new Factory('Tom', 8);
 ```

 这两个例子很像只是new了出来，名字就变成寄生构造函数模式了。

 寄生构造函数模式具体的应用：

 ```
  function SpecialArray() {
      let arr = new Array();
      arr.push.apply(arr, arguments);

      arr.toPipeString = function(){
        return arr.join('|')
      }

      return arr;
  }

  let colors = new SpecialArray('red', 'blue');
  console.log(colors.toPipeString());
  console.log(colors instanceof SpecialArray);  //false
  console.log(colors instanceof Array);         //true
 ```

 注意：colors 并不是 SpecialArray 的实例，而且是 SpecialArray 返回的 arr 是相等的。

 (3) 稳妥构造函数模式

 ```
 function Factory(name) {
      let obj = new Object();

      obj.sayName = function(){
          alert(name);
      }
      return obj;
 }
 ```

 可以看到，在这种模式创建的对象中，只可以使用sayName方法，没有其他办法访问name的值。

 #### 寄生式继承

 增强了原型式继承，在原型式继承的基础上，扩展了方法，再返回

 ```
 function object(o){
  　　function F(){

  　　}
 　　 F.prototype=o;
 　　 return new F();
  };

 function createAnother(original){
     var clone = object(original); // 通过调用函数创建一个新对象
     clone.sayHi = function(){ //以某种方式增强真个对象
         alert("hi");
     }
     return clone; //返回这个对象
 }

 var anotherPerson = createAnother(person);
 anotherPerson.sayHi();     //hi~
 ```

 * 思路与寄生构造函数和工厂模式类似。

 * 新的对象中不仅具有original的所有属性和方法，而且还有自己的sayHi()方法。

 * 在主要考虑对象而不是自定义类型和构造函数的情况下，寄生式继承也是一种有用的模式。

 * 由于寄生式继承为对象添加函数不能做到函数复用，因此效率降低。

 ### 寄生组合式继承

    组合继承是JS中最常用的继承模式，但其实它也有不足，组合继承无论什么情况下都会调用两次超类型的构造函数，并且创建的每个实例中都要屏蔽超
    类型对象的所有实例属性。寄生组合式继承就解决了上述问题，被认为是最理想的继承范式。

 ```
 function object(o) {
     function F(){}
     F.prototype = o;
     return new F();
 }

 function inheritPrototype(superType, subType) {
     // 1) 这么做的目的是 superType 的原型对象拿来继承，并不包括其他多余的属性，object 返回的对象只是一个空的对象。
     var prototype = object(superType.prototype);
     prototype.constructor = subType;
     subType.prototype = prototype;
 }

 function SuperType(name) {
     this.name = name;
     this.colors = ["red", "blue", "green"];
 }

 SuperType.prototype.sayName = function() {
     alert(this.name);
 };

 // 2) 这么做的目的是 每个 SubType 的实例对象都会有各自单独的来自 SuperType 的属性，和继承的方法分开区分，并且这里 调用了 SuperType()
 function SubType(name, age) {
     SuperType.call(this, name);
     this.age = age;
 }

 inheritPrototype(SuperType, SubType);   // 这一句，替代了组合继承中的SubType.prototype = new SuperType()

 // 3) SubType 可以扩展自己的原型对象的方法用来公用。
 SubType.prototype.sayAge = function() {
     alert(this.age);
 };
 ```

 #### 再梳理一下
 a. 在inheritPrototype函数中用到了原型式继承中的object()方法，将超类型的原型指定为一个临时的空构造函数的原型，并返回构造函数的实例。

 b. 此时由于构造函数内部为空（不像SuperType里面有实例属性），所以返回的实例也不会自带实例属性，这很重要！因为后面用它作为SubType的原型时，
 就不会产生无用的原型属性了，借调构造函数也就不用进行所谓的“重写”了。

 c. 然后为这个对象重新指定constructor为SubType，并将其赋值给SubType的原型。这样，就达到了将超类型构造函数的实例作为子类型原型的目的，
 同时没有一些从SuperType继承过来的无用原型属性。
