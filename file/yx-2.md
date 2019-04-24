
    我就想知道,你们项目里面是不是很少用到原型相关知识?

 ### 一. 原型

 #### 构造函数

 ```
 function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() { alert(this.name) }
 }
 var person1 = new Person('Zaxlct', 28, 'Software Engineer');
 var person2 = new Person('Mick', 23, 'Doctor');

 console.log(person1.constructor == Person); //true
 console.log(person2.constructor == Person); //true
 ```

 上面的例子中 person1 和 person2 都是 Person 的实例。这两个实例都有一个 constructor （构造函数）属性,
 该属性（是一个指针）指向 Person . 实例的构造函数属性（constructor）指向构造函数.

 #### 原型对象和 prototype

 每个对象都有 __proto__ 属性，但只有函数对象才有 prototype 属性, 而函数对象的 prototype 就是原型对象.

 ```
 Person.prototype = {
    name:  'Zaxlct',
    age: 28,
    job: 'Software Engineer',
    sayName: function() {
      alert(this.name);
    }
 }
 ```

 上面的原型对象就是 Person.prototype .

 默认情况下,所有的原型对象都会都会自动获得一个 constructor (构造函数)属性,这个属性,是一个指针,指向 prototype 所在的函数 (Person).

 ```
 person1.constructor == Person
 Person.prototype.constructor == Person
 ```

 那么原型对象有什么作用呢?主要作用是用于继承.

 ```
  var Person = function(name){
    this.name = name; // tip: 当函数执行时这个 this 指的是谁？
  };
  Person.prototype.getName = function(){
    return this.name;  // tip: 当函数执行时这个 this 指的是谁？
  }
  var person1 = new person('Mick');
  person1.getName(); //Mick
 ```

 从这个例子可以看出, 通过给 Person.prototype 设置了一个函数对象的属性, 那么 Person 的实例(person1) 就继承了这个属性.

 #### 构造器

 复习: 函数里面有提到 typeof 返回"function"的对象都是函数对象.也称这样的对象为构造器.

 新对象 obj 是使用 new 操作符后跟一个构造函数来创建的. 构造函数（Object）本身就是一个函数（就是上面说的函数对象）,
 它和上面的构造函数 Person 差不多. 只不过该函数是出于创建新对象的目的而定义的. 所以不要被 Object 吓倒.

 #### 原型规则

  (1) 所有的引用类型（数组、对象、函数）都具有对象特性，即可自由扩展属性（除了“null”）

  ![layout viewport](/img/yx-5.png)

  (2) 所有的引用类型（数组、对象、函数）都有一个 _proto_ 属性(隐式原型属性），属性值是一个普通的对象

  (3) 所有的函数，都有一个 prototype(显式原型）属性，属性值也是一个普通的对象

  (4) 所有的引用类型（数组、对象、函数）， _proto_ 属性值(隐式原型属性）指向它的构造函数的“prototype”属性值

 #### 总结

 new 的过程分为三步

 var p = new Person('张三',20);

 1) var p={}; 初始化一个对象p

 2) p._proto_=Person.prototype;，将对象p的 __proto__ 属性设置为 Person.prototype

 3) Person.call(p,”张三”,20);调用构造函数Person来初始化p。关于call/apply使用

 ### 二. __proto__

 #### __proto__ 和 原型链

 复习: 函数对象的 __proto__ 都指向 Function.prototype.

 JS 在创建对象(不论是普通对象还是函数对象)的时候,都有一个叫做 __proto__ 的内置属性, 用于指向创建它的构造函数的原型对象.

  ```
  Person.prototype.constructor == Person;
  person1.__proto__ == Person.prototype;
  person1.constructor == Person;
  ```

 ![layout viewport](/img/yx-2.webp)

 由于 __proto__ 是任何对象都有的属性, 而js里万物皆对象, 所以会形成一条 __proto__ 连接起来的链条, 递归 __proto__ 最终会访问到
 Object 的 prototype , Object.prototype.__proto__ 为 null.

 ![layout viewport](/img/yx-3.png)

 #### 举例

 ```
 function Person(){}
 var person1 = new Person();
 console.log(person1.__proto__ === Person.prototype); // true
 console.log(Person.prototype.__proto__ === Object.prototype) //true
 console.log(Object.prototype.__proto__) //null

 Person.__proto__ == Function.prototype; //true
 console.log(Function.prototype)// function(){} (空函数)

 var num = new Array()
 console.log(num.__proto__ == Array.prototype) // true
 console.log( Array.prototype.__proto__ == Object.prototype) // true
 console.log(Array.prototype) // [] (空数组)
 console.log(Object.prototype.__proto__) //null

 console.log(Array.__proto__ == Function.prototype)// true
 ```

 1. Object.__proto__ === Function.prototype // true
  Object 是函数对象, 是通过 new Function() 创建的, 所以Object.__proto__ 指向 Function.prototype. (所有函数对象的 __proto__ 都指向
  Function.prototype)

 2. Function.__proto__ === Function.prototype // true
  Function 也是对象函数, 也是通过 new Function() 创建, 所以 Function.__proto__ 指向 Function.prototype .

 3. 原型和原型链是JS实现继承的一种模型。

 4. 原型链的形成是真正是靠__proto__ 而非prototype

 ```
  var animal = function(){};
  var dog = function(){};

  animal.price = 2000;
  dog.prototype = animal;
  var tidy = new dog();
  console.log(dog.price) //undefined
  console.log(tidy.price) // 2000
 ```

 ### 三. 实现继承的几类办法

  #### js原型（prototype）实现继承

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

    // 4) 原型对象的任何属性修改，都会影响到实例。
    Student.prototype.head = '笨的脑袋瓜子';
    console.log(stu1.head); //笨的脑袋瓜子
    console.log(stu2.head); //笨的脑袋瓜子
  ```

 这种方式隐藏一个问题是在创建 stu1 和 str2 实例的时候没办法传递参数。

 #### call , apply 实现继承 ( 借用构造函数实现继承 )

    为了解决原型中包含引用类型值的问题，开始使用借用构造函数，也叫伪造对象或经典继承

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

 #### 组合继承

    也叫伪经典继承，将原型链和借用构造函数的技术组合到一块。使用原型链实现对原型属性和方法的继承，而通过构造函数来实现对实例属性的继承。

 ```
     function Person () {
       this.head = '脑袋瓜子';
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

     // 3) 子类：学生，继承了“人”这个类。 每次新创建 Student 实例的时候都会调用 Student 构造方法，则每个子类实例都会创建各自的 emotion属性，互补影响。
     function Student(studentID) {
       this.studentID = studentID;
       Person.call(this);
     }

     // 2) 设置 Student 的原型对象 Student 可以访问到 Person.prototype 的方法了
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

     // 4) 最后加了一个例子，如果这里的time改成数组，则stu1.time和stu2.time 的接口过又会相同。
     console.log(stu1.time);  //0
     console.log(stu2.time);  //0
     stu1.time ++
     console.log(stu1.time);  //1
     console.log(stu2.time);  //0
 ```

 首先，我们将 Person 类中需要复用的方法提取到 Person.prototype 中，然后设置 Student 的原型对象为 Person 类的一个实例，这样 stu1
 就能访问到 Person 原型对象上的属性和方法了。其次，为保证 stu1 和 stu2 拥有各自的父类属性副本，我们在 Student 构造函数中，还是使用了
 Person.call ( this ) 方法。如此，结合原型链继承和借用构造函数继承，就完美地解决了之前这二者各自表现出来的缺点。

 但是实际上原理也是每个 Student 的实例，在新建的时候，都会在构造函数里创建一个新的 Person 对象，所以Person中的属性都互不影响，并且继承了
 Person 的原型对象，而 Person 的原型对象的继承又和 js原型（prototype）实现继承 有相同的特性。只是一般不会这么去用了。

