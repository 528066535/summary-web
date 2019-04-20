
    我就想知道,你们项目里面是不是很少用到原型相关知识?

### 一.数据类型

 #### 动态类型

 JavaScript是一种弱类型,并且是动态语言.所以你不用提前声明数据类型,而在程序运行的过程中,数据的类型会被自动确认.简单的说,一个同一个变量
 也可以改变数据类型.

 #### 概念

 (1). 堆（heap）是没有结构的，数据可以任意存放，它是用于存放复杂数据类型（引用类型）的，例如数组对象、object对象等。

 (2). 栈（stack）是有结构的，每个区块按照一定次序存放（后进先出），栈中主要存放的是基本类型的变量的值以及指向堆中的数组或者对象的地址，
 存在栈中的数据大小与生存期必须是确定的。除此之外，还可以明确知道每个区块的大小。

 (3). js中基本数据类型的值不可变,也就是说基本数据类型的值的改变是重新再赋值.

 (4). 原始值的比较是值的比较

  用 const 定义的变量是不能改变的，这里其实分两种情况：如果定义的是基本数据类型的变量，那么的确是不能对其做任何操作来改变其值的；但如果定义
  的是引用类型的变量，这个变量存储的其实只是一个地址，也就是说我们不能改变的是这个地址，但是地址中的内容我们还是可以改变的。

 #### 数据类型

 (1). 原始数据类型(基本数据类型):存储在栈

  * Boolean

   任意的JavaScript的值都可以转换为布尔值。undefined,null, 0,-0,NaN,""这6个值,再加上false本身共7个值都会转换为false

  * Null

    在这里要说明的是：在JavaScript中，null是基础数据类型，但是typeof判断为object，这是由于历史原因造成的。
    在JavaScript最初的实现中，JavaScript中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是0。由于null代表的是空指针(大多数平台下值为0x00），
    因此，null使用0作为类型标签，typeof null就返回了”object”。

  * Undefined

  当声明的变量未初始化时,该变量的默认值为undefined.

  * Number

  可以用来表示进制数,浮点数等.还有一个特殊的值NaN,一般来说出现这种值的时候是发生类型转换失败的时候,例如把不带'a'转化成数字,并且NaN也不等于自己本身.
  可以用Number.isNaN()或者 isNaN() 来判断是否为NaN.

  * String

  String类型也是基本数据类型,并且不可被修改,只能被覆盖.

  * Symbol (ES6)

  符号(Symbols)是ECMAScript 6新定义的,Symbol()函数返回symbol类型的值,都是唯一的,不相等的,并且具有静态属性和静态方法.
  symbol值可以用来作为对象属性的标识符,这是Symbol数据类型的唯一目的.

 (2). Object类型:存储在堆

  * Object

  * Array

  * Date

  * RegExp

  * Function

  注意 typeof(new Function()) 返回function

  (3). 其他判断数据类型的方法

  A instanceof B 判断A是否是B的实例.

  ```
  var a=new Array();
  alert(a instanceof Array); // true

  D.prototype = new C(); // 继承
  var o3 = new D();
  o3 instanceof D; // true
  o3 instanceof C; // true
  ```

  Object.prototype.toString

  ```
  var getType = Object.prototype.toString
  getType.call('aaaa')                  输出      [object String]
  getType.call(2222)                    输出      [object Number]
  getType.call(true)                    输出      [object Boolean]
  getType.call(undefined)               输出      [object Undefined]
  getType.call(null)                    输出      [object Null]
  getType.call({})                      输出      [object Object]
  getType.call([])                      输出      [object Array]
  getType.call(function(){})            输出      [object Function]
  getType.call(new Date())              输出      [object Date]

  #### 函数

  (1). 函数对象概念

  所有typeof返回'function'的对象都是函数对象.也就是说通过 new Function() 创建的对象是函数对象,Function Object也是通过 New Function()创建的.

  函数对象与其他用户所定义的对象有着本质的区别,这一类对象被称之为内部对象,例如日期对象(Date)、数组对象(Array)、字符串对象(String)都属于内部对象.
  这些内置对象的构造器是由JavaScript本身所定义的:通过执行new Array()这样的语句返回一个对象,JavaScript内部有一套机制来初始化返回的对象,
  而不是由用户来指定对象的构造方式.

  函数对象的 __proto__ 都指向 Function.prototype.

  ```
  alert(typeof(Function)));             //function
  alert(typeof(new Function()));        //function
  alert(typeof(Array));                 //function
  alert(typeof(Object));                //function
  alert(typeof(new Array()));           //object
  alert(typeof(new Date()));            //object
  alert(typeof(new Object()));          //object

  let fun = function () {};
  console.log(typeof(fun));             //function
  console.log(typeof(new fun()));       //object
  ```

  可见new一个function实际上是返回一个函数.这与其他的对象有很大的不同.其他的类型Array、Object等都会通过new操作符返回一个普通对象.
  尽管函数本身也是一个对象,但它与普通的对象还是有区别的,因为它同时也是对象构造器,也就是说,可以new一个函数来返回一个对象（typeof( new fun())
  返回一个object对象）,这在前面已经介绍.所有typeof返回“function”的对象都是函数对象.也称这样的对象为构造器（constructor）,因而,
  所有的构造器都是对象,但不是所有的对象都是构造器.

  (2). 函数的apply、call方法 与 bind.

  * JavaScript为函数对象定义了两个方法:apply和call,它们的作用都是将函数绑定到另外一个对象上去运行,两者仅在定义参数的方式有所区别:

    Function.prototype.apply(thisArg,argArray);  注意argArray并不是Array所以也不能直接用Array的方法.

   ```
   function log(){
     console.log.apply(console, arguments);
   };
   log(1);    //1
   log(1,2);    //1 2
   ```

    Function.prototype.call(thisArg[,arg1[,arg2…]]);

  从函数原型可以看到,第一个参数都被取名为thisArg,即所有函数内部的this指针都会被赋值为thisArg,这就实现了将函数作为另外一个对象的方法运行的目的.


    bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的
    第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

 * apply、call、bind比较

 ```
 var obj = {
    x: 81,
 };

 var foo = {
    getX: function() {
        return this.x;
    }
 }

 console.log(foo.getX.bind(obj)());  //81
 console.log(foo.getX.call(obj));    //81
 console.log(foo.getX.apply(obj));   //81
 ```

 也就是说，区别是，当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。

 * apply 、 call 、bind 三者都是用来改变函数的this对象的指向的；
 * apply 、 call 、bind 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
 * apply 、 call 、bind 三者都可以利用后续参数传参；
 * bind 是返回对应函数，便于稍后调用；apply 、call 则是立即调用 。

 ### 二. 判断是否相等

 #### == 和 ===

 (1) 如何判断 ==

 * 如果两个值类型相同，再进行三个等号(===)的比较

 * 如果两个值类型不同，也有可能相等，需根据以下规则进行类型转换在比较：

  1) 如果一个是null, 一个是undefined, 那么相等

  2) 如果一个是字符串, 一个是数值, 把字符串转换成数值之后再进行比较

  3) 如果任一值是 true , 把它转换成 1 再比较; 如果任一值是 false , 把它转换成 0 再比较

  4) 如果一个是对象, 另一个是数值或字符串, 把对象转换成基础类型的值再比较. 对象转换成基础类型, 利用它的toString或者valueOf方法.
  js核心内置类, 会尝试valueOf先于toString; 例外的是Date, Date利用的是toString转换. 非js核心的对象

 (2) 如何判断 ===

  * 如果类型不同, 就一定不相等

  * 如果两个都是数值, 并且是同一个值, 那么相等; 如果其中至少一个是NaN, 那么不相等. (判断一个值是否是NaN, 只能使用isNaN() 来判断).

  * 如果两个都是字符串, 每个位置的字符都一样, 那么相等, 否则不相等.

  * 如果两个值都是true, 或是false, 那么相等.

  * 如果两个值都引用同一个对象或是函数, 那么相等, 否则不相等.

  * 如果两个值都是null, 或是undefined, 那么相等

 #### 隐式类型转换

 * 为什么 []==false ?

 首先, 我们知道 [] 是对象, false 是布尔类型, 类型不同, 需要转换后才能比较. 在 == 的判断中, 如果有 布尔值, 会转化为数值 Number(false)
 结果为 0 ,

 * 为什么 []==0 ？

 []在此时调用toString()方法, 相当于调用 join('') , 结果为 ''. ''==0 所以相等了.

 * 为什么 !![] 是 true

 !![] 这里并没有用到两个值的比较 , 只是看他是不是为空 , 而我们要知道 Falsy 的值有 0 , '' , false , NaN , null , undefined .
 所以 !![] 为true .

 * 为什么 null == 0 是 false ？

 因为 null 和 0 是原始的数据类型了, 不能再转化成原始数据类型进行比较了 , 所以只能为 false 了.

 * 为什么 null == undefined ？

 JS的规定.哈哈

 * 为什么 NaN !== NaN ？

 先看另外一个问题 'abc' == NaN , 因为 NaN 是数字类型 , 'abc' 需要转化成 数字类型 Number('abc') , 返回的确实是 NaN , 但是假设
 NaN 和 NaN 相等 , 那是不是 'abc' 也和 NaN 相等了 , 所以就有了这个结果 .


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

 ![layout viewport](/img/yx-1.png)
 ![layout viewport](/img/yx-2.webp)

 由于 __proto__ 是任何对象都有的属性, 而js里万物皆对象, 所以会形成一条 __proto__ 连接起来的链条, 递归 __proto__ 最终会访问到
 Object 的 prototype , Object.prototype.__proto__ 为 null.

 ![layout viewport](/img/yx-3.png)

 #### 总结

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

