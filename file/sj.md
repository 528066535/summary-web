
    稍微给原型原型链做了一点铺垫

### 一. 数据类型

 #### 动态类型

 JavaScript是一种弱类型, 并且是动态语言. 所以你不用提前声明数据类型, 而在程序运行的过程中, 数据的类型会被自动确认. 简单的说, 同一个变量
 也可以改变数据类型.

 #### 概念

 (1). 堆（heap）是没有结构的，数据可以任意存放，它是用于存放复杂数据类型（引用类型）的，例如数组对象、object对象等。简单地说，堆
 动态分配的内存，大小不定也不会自动释放，存放引用类型。

 (2). 栈（stack）是有结构的，每个区块按照一定次序存放（后进先出），栈中主要存放的是基本类型的变量的值以及指向堆中的数组或者对象的地址，
 存在栈中的数据大小与生存期必须是确定的。除此之外，还可以明确知道每个区块的大小。栈会自动分配内存空间，会自动释放。

 (3). js中基本数据类型的值不可变,也就是说基本数据类型的值的改变是重新再赋值.

 (4). 原始值的比较是值的比较

  用 const 定义的变量是不能改变的，这里其实分两种情况：如果定义的是基本数据类型的变量，那么的确是不能对其做任何操作来改变其值的；但如果定义
  的是引用类型的变量，这个变量存储的其实只是一个地址，也就是说我们不能改变的是这个地址，但是地址中的内容我们还是可以改变的。

 #### 种类

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

  * Error

  六种错误：

  1) ReferenceError:找不到对象时

  2) TypeError:错误的使用了类型或对象的方法时

  3) RangeError:使用内置对象的方法时，参数超范围

  4) SyntaxError:语法写错了

  5) EvalError:错误的使用了Eval

  6) URIError:URI错误

  ```
  try{
      可能发生错误的代码
  }catch(err){
      只有发生错误时才执行的代码
  }finally{
      无论是否出错，肯定都要执行的代码
  }
  ```

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
  ```

  #### 函数

  (1). 函数对象概念

  所有typeof返回'function'的对象都是函数对象.也就是说通过 new Function() 创建的对象是函数对象,Function Object也是通过 New Function()创建的.

  函数对象与其他用户所定义的对象有着本质的区别,这一类对象被称之为内部对象,例如日期对象(Date)、数组对象(Array)、字符串对象(String)都属于内部对象.
  这些内置对象的构造器是由JavaScript本身所定义的:通过执行new Array()这样的语句返回一个对象,JavaScript内部有一套机制来初始化返回的对象,
  而不是由用户来指定对象的构造方式.

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

 ### 二. 数据类型值的判断

 #### 隐式转换

 在js中，当运算符在运算时，如果两边数据不统一，CPU就无法计算，这时我们编译器会自动将运算符两边的数据做一个数据类型转换，转成一样的数据类型再计算
 这种无需程序员手动转换，而由编译器自动转换的方式就称为隐式转换.

 (1) 当加号运算符时, String和其他类型时, 其他类型都会转为 String ; 其他情况, 都转化为Number类型.
 注: undefined 转化为Number是 为’NaN‘, 任何Number与NaN相加都为NaN.

 (2) Number类型会先调用valueOf(), String类型会先调用toString().

 (3) 复杂数据类型在隐式转换时会先转成String, 然后再转成Number运算.

 ![layout viewport](/img/yx-4.png)

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

 #### 隐式类型转换相关问题

 (1) 为什么 []==false ?

 首先, 我们知道 [] 是对象, false 是布尔类型, 类型不同, 需要转换后才能比较. 在 == 的判断中, 如果有 布尔值, 会转化为数值 Number(false)
 结果为 0 ,

 (2) 为什么 []==0 ？

 []在此时调用toString()方法, 相当于调用 join('') , 结果为 ''. ''==0 所以相等了.

 (3) 为什么 !![] 是 true

 !![] 这里并没有用到两个值的比较 , 只是看他是不是为空 , 而我们要知道 Falsy 的值有 0 , '' , false , NaN , null , undefined .
 所以 !![] 为true .

 (4) 为什么 null == 0 是 false ？

 因为 null 和 0 是原始的数据类型了, 不能再转化成原始数据类型进行比较了 , 所以只能为 false 了.

 (5) 为什么 null == undefined ？

 JS的规定.注意:

  console.log( 1 + undefined) 结果是 NaN , 由于 Number(undefined) 结果是 NaN

  console.log( 1 + null) 结果是 1, Number(null) 结果是 0

 (6) 为什么 NaN !== NaN ？

 先看另外一个问题 'abc' == NaN , 因为 NaN 是数字类型 , 'abc' 需要转化成 数字类型 Number('abc') , 返回的确实是 NaN , 但是假设
 NaN 和 NaN 相等 , 那是不是 'abc' 也和 NaN 相等了 , 所以就有了这个结果 .

 ### 三. 实现一个深拷贝

 我们知道基本数据类型是存储在栈内，复制的时候直接在栈内开辟一个新的内存，放数据的key和value，当你修改其中一个值的时候，另外一个值不会改变。

 对象数据类型的key存在栈中，value存了堆中的地址，简单的赋值对象，只是复制了一个对象的引用，而引用指向地址的值改变，引用自身的地址并没有改变。

 以下几个深拷贝不考虑RegExp、Error、Function 的数据类型。

 #### 根据上述情况，可以有一种深拷贝的方式。新建一个对象，并且用递归的方式复制对象中的深层基础数据类型的值。

 ```
  function clone (obj, map = new WeakMap()) {
      let o = null;
      if (map.get(target)) {
          return map.get(target);
      }

      map.set(target, cloneTarget);

      if (typeof obj === "object") {
          if(getType.call(obj) == '[object Date]') {
              o = new Date(obj.valueOf());
          }
          else if (getType.call(obj) == '[object Array]') {
              o = [];
              for (let i = 0, len = obj.length; i < len; i++) {
                  o.push(clone(obj[i]));
              }
          } else {//普通对象
              o = {};
              for (let j in obj) {
                  o[j] = clone(obj[j]);
              }
          }
      } else {    //基本类型
          o = obj;
      }
      return o;
  }
 ```

 这里需要注意 Date 对象直接赋值结果是赋值了toString()后的结果，valueOf的结果相当于getTime()后的时间戳，所以复制 Date对象需要重新定义
 Date 对象。

 #### 借用JSON对象的parse和stringify

 ```
  function deepClone(obj){
      let _obj = JSON.stringify(obj),
          objClone = JSON.parse(_obj);
      return objClone
  }
 ```

 注意（缺点）：

 (1) Date对象也会被改成字符串形式。所以这里可以考虑时间用时间戳来存储。否则 copy出的时间对象会变成字符串。

 (2) 如果有 undefined 和 函数，则会默认删除当前的 key。

 (3) NaN、Infinity 和 -Infinity，会变成 null。

 (4) JSON.parse()不允许最后一个键值对后面存在逗号。

 (5) 如果对象中存在循环引用的情况也无法正确实现深拷贝。

 ```
  var jsObject = JSON.parse("[1, 2, 3, 4, ]");
  //Uncaught SyntaxError: Unexpected token ] in JSON at position 13
  var jsObject = JSON.parse("{ \"foo\" : 1, }");
  //Uncaught SyntaxError: Unexpected token } in JSON at position 13
 ```

 #### Object.assign(a,b) 只能深拷贝第一层, 深层的还是浅拷贝.


