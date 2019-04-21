
    我就想知道,你们项目里面是不是很少用到原型相关知识?

 ### 三. 原型和原型链

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

 **** __proto__ 和 原型链

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
