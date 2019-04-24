
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

    // 4) 原型对象的任何属性修改，都会影响到实例。
    Student.prototype.head = '笨的脑袋瓜子';
    console.log(stu1.head); //笨的脑袋瓜子
    console.log(stu2.head); //笨的脑袋瓜子
  ```

 这种方式隐藏一个问题是在创建 stu1 和 str2 实例的时候没办法传递参数。

 ### 二. call , apply 实现继承 ( 借用构造函数实现继承 )

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

 ### 三. 组合继承

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

 ### 四.