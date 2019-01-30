# Dart语言  
## 一、动态性  
Dart语言是动态类型的。我们可以编写和运行**完全没有类型标注**的程序。  

当然，你可以选择在程序中添加类型标注：  

* 添加的类型不会阻止你程序的编译和运行--**即使标注不完整或者错误(在生产模式下)**  
* 不论你添加了什么类型的标注，你的程序都**具有完全相同的语义**  

虽然Dart语言不添加类型标注是完全可以的，而且不会有任何影响，但是在某些情况下，类型标注还是有一定的好处的：  

* 给人看的文档。明智地放置类型标注可以使别人更容易地阅读你的代码
* 给机器看的文档。工具可以有多种方式利用类型标注。特别是，它们可以在 IDE 中帮助提供很好的特性，如名称补全和增强的导航。
* 早期的错误检测。Dart 提供了静态检查器，它可以警告你潜在的问题，而不用你自己查。另外，在检查模式下，Dart 自动把类型标注转换为运行时断言检查来辅助调试。
* 有时，在编译到 JavasSript 时，类型可以帮助改进性能。  

> 总之：Dart是一门类型可选语言：  
> 1. 类型在语法层面来说是可选的  
> 2. 类型对于运行时语义没有影响

### 静态检查器  
静态检查器（static checker）行为很像 C 中的链接。它在编译时警告你潜在的问题。这些警告中的很多是和类型相关的。静态检查器不会产生错误——不论检查器说什么，你总是可以编译和运行你的代码。  

检查器不会对每个可能的类型违反都敏感。它不是类型检查器（typechecker），因为 Dart 并不是按照典型的类型系统那样使用类型。检查器会抱怨那些非常可能是真正问题的地方，而不会强迫你去满足严格的类型系统。  

例如：  

	int a=new B("b1")+new B("b2");    //这里的B是我们定义的一个类型，并且重载了"+"运算符  

在Java语言中，这里肯定是编译通过不了的，但是在Dart语言中就可以，a会赋值为B类型，同时**静态检查器会产生一个警告**。  

### dynamic类型  
没有提供类型的时候，Dart 如何避免抱怨呢？这其中的关键就是 dynamic 类型，这是程序员没有明确给出类型时候的默认类型。使用 dynamic 类型让检查器闭嘴。  

>也就是说，在没有类型标注的时候默认是dynamic类型  

### 泛型  
**Dart支持具体化泛型**，也就是说不像Java那样，Dart中的泛型在**运行时会携带他们的类型参数**。  

### 检查模式  
在开发过程中，Dart程序可以在**检查模式**下运行。如果你在检查模式下运行程序，在参数传递、返回结果和执行赋值时，系统将自动执行某些类型的检查。如果检查失败，程序将在该处停止执行，并带有清晰的错误信息。(**这时候类型标注就不能够错误了**)  

## 二、基础语法  
Dart 中的所有东西都是对象，包括数字、函数等，它们都继承自 Object，并且对象的默认值都是 null**（包括数字）**。  

Dart中声明变量：  

* var关键字声明变量，这时候该变量可以被赋予不同类型的对象  
* 使用类型来声明变量，就行静态语言那样  
* 像动态语言那样，不指定类型(不指定类型的情况要立马赋初值)
* 使用dynamic声明(这时候不用立马赋初值)  

>上面说的赋初值是我的猜想  

Dart支持顶层的变量和函数，并不一定把所有的东西都放到类中。  

Dart中以**下划线开头的标识符是私有的**，**除此之外都是公有的**。  
>这里的**私有单元不是类，而是库**。也就是说，在同一个库中可见，私有只是对库的外部不可见。     

在Dart中也可以定义final变量，**final类变量必须在定义的时候初始化；final实例变量可以在定义的时候初始化，也可以在构造器中初始化**  

> **final和const辨析：Dart支持final和const：final表示赋值之后不可变，而const修饰的量表示编译时常量(const隐含final的不可变特征，这就限定了const修饰的只能是num和string的变量)**。const的作用就好像C语言中的宏定义一样，将一个使用多次的值声明为一个变量名，方便后续的使用

> 另外注意一下名称区别：const test = 1；这个语句中**test为常量(constant variable)，而1位常量值(constant value)**.这就引出了const关键字的另一个作用：**除了修饰变量表示常量外，const还可以修饰一个对象，表示常量值**
> 
> var val = const [];//--1
> 
> const val2 = [];//--2
> 
> final val3 = const [];//--3
> 
> 上面的3个语句中2和3是等价的。

### 固有的类型    

Dart中有几个**固有类型(build-in type):numbers、lists、maps、booleans、runes、symbols、strings**  
固有类型和普通的类型之间的区别我们可见简单的理解为：**固有类型的对象可以直接通过一个字面量来创建**，而其他的类型必须显示的使用构造函数来创建。例如：  

	var test = 1;  //1是一个字面量，同时也是一个int对象
	var a = new Parent();  //a是一个普通的类型的对象，必须使用构造函数来创建

#### 数字  
Dart 中的数字是 num，它有两个**子类型 int 和 double**，int 是任意长度的整数，double 是双精度浮点数。一般情况下，主要应该使用 num 和 int 类型，double 很少使用（因为它限制输入只能是 double 而不能是 int）。注意，数字类型也是对象，因此可以在数字上调用各种方法。 **num 类型定义了基本的操作符，例如 +, -, /, 和 *， 还定义了 abs()、 ceil()、和 floor() 等 函数;不仅如此还有类函数int.prase(String)等。**    

	var one = int.prase('1');
	int two = int.prase('2');

> **Dart中的int类型范围是-2的63次幂至2的63次幂-1**
  
#### 字符串  
Dart中的字符串是**Strings**,Dart中的字符串是UTF-16编码的字符串，可以使用单引号也可以使用双引号，且支持三个单引号的多行字符串。**字符串前面加上r前缀代表原始字符串(任何转义字符都失效)**  

	var s = r"In a raw string, even \n isn't special.";    

同样字符串也支持内嵌一个表达式，想Kotlin那样使用**${expression}**的形式来使用，如果表达式只是一个变量的话，可以省略掉花括号：  

	var test = "this is expression";
	print("${test.toUpperCase()} !!!!");
	//输出的结果就是：THIS IS EXPRESSION !!!!

#### 布尔  
Dart 中的 bool 类型只有 true 和 false。**在检查模式下，任何其它类型的对象都不能当作 bool 类型来使用；在生产模式下，非 bool 类型的对象都被当作 false(不管哪种模式表示为true的只有bool中的true)**。这点与 JavaScript 不同，但这样的规则更简单、清晰，不容易犯错。  

#### List 和 Map  
Dart 语言中内置了常用的 List 和 Map 。List 是 Collection 的子类型，另外 Queue 和 Set 也是 Collection 的子类型 。  
List和Map的使用和Java差不多，就不说了。  
Map 的字面量语法要求 key 必须字符串，但如果是用构造函数创建的，则任何对象都可以是 key。  

	var map = {
  		'one': 1,
  		'two': 2
	};  
	var m=new Map<int,int>(){1:1};

#### Runes  

> 补充：Unicode、UTF-8、UTF-16、UTF-32关系  
> **unicode 只是一种字符码表， 而在计算机中进行存储时， 必须指定一种具体的存储方式。常见的如utf8, utf16, utf32。而UTF-8会将一个unicode字符扩充为1至4个字节来保存到计算机中；而UTF-16则扩充为2个或者4个字节来保存；UTF-32将其扩充为4个字节来保存**

在Dart中，**符文(Runes)是字符串的UTF-32代码点，而Dart中的字符串却是以UTF-16来保存的**，Strings对象的**codeUnits和runes属性分别返回其UTF-16码和UTF-32码的十进制值**  

	var clapping = '\u{1f44f}';
	  print(clapping.codeUnits); //[55357, 56399]
	  print(clapping.runes);  //(128079)


### 三、函数  
Dart中的最普通的函数在定义的时候和Java中的方法在定义的时候别无二致，**在Dart中如果一个函数没有return语句指定返回值的话，并不像Java一样需要声明void，这时候会默认返回null**   
由于Dart中的类型标注是可选的，所以函数的参数类型以及返回值类型都是可以省略的。  

如果**函数体中仅仅是简单的一个表达式**，那么可以使用更简洁的箭头语法**=>expr;**，它等价于**{return expr;}**。  

	String sayHello(String name){
		return 'hello $name';
	}
	sayHello(name){
		return 'hello $name';
	}
	sayhello(name)=>'hello $name';

上面三种函数的定义是等价的。  

你不仅可以定义一个有名字的函数，由于**在Dart中函数也是对象**，还可以使用**匿名函数、把函数作为参数传递给其它函数、把函数赋值给一个变量、返回一个函数(这时候返回类型就是Function)等**。  

	main() {
  		var sayHello = (name) => 'hello $name'; // 把一个匿名函数赋值给变量
  		sayHello('Guokai'); // 利用该变量调用函数
	}  

>理解上面的把函数赋值给一个对象：之前说过，在Dart中任何东西都是对象，函数也是，函数是Function类型的对象，所以讲一个Function类型的对象赋值给一个var变量就说的过去了。  

#### 可选参数  
**Dart 支持两种类型的可选参数，命名(named)可选参数和位置(positional)可选参数，但不能同时使用。可选的参数可以设置默认值，默认值要求是编译时常量(Strings、num的字面量或者const修饰的常量值)，没有设置的可选参数默认值是 null 。**

##### 命名可选参数  
命名可选参数在**定义时使用大括号的语法，默认值用等号(老的版本用冒号)指定**。在**使用的时候，多个可选参数可以任意指定其中的 0 个或多个，并且与指定的先后顺序无关，但参数值前必须指定参数名**。所以参数名也成了 API 的一部分，就像函数的命名一样，你需要多花点时间认真考虑参数名。  

	foo (a, {b, c= 3, d= 4, e}) {
  		print("$a  $b  $c  $d  $e");
	}  
	main() {
  		// 指定的顺序无关，但必需用参数名指定
  		// 这里 a 是必选参数，可选参数 b,c,d,e 分别是 null,1,4,5
  		foo(1, e: 5, c: 1);
	}  

> 命名可选参数也可以在前面加上**@Required注释来规定为必传参数**  

##### 位置可选参数  
位置可选参数**在定义时使用方括号的语法，默认值用等号指定**。在**使用的时候，不能定参数名，参数的先后顺序必需与定义的顺序一致**，**只能省略后面的参数。**  

	foo (a, [b, c=3, d=4, e]) {
  		print("$a  $b  $c  $d  $e");
	}

	main() {
  		// 所有可能的调用形式
  		foo(1);
  		foo(1, 2);
  		foo(1, 2, 3);
  		foo(1, 2, 3, 4);
  		foo(1, 2, 3, 4, 5);
	}  

#### 匿名函数  
匿名函数也可以被叫做lambda、closure闭包，其在定义的时候和普通的函数是一样的，只是没有名字而已。  

闭包有一个用处就是保存变量：  

	Function makeAdder(num addBy) {
	  return (num i) => addBy + i;
	}
	
	void main() {
	  // Create a function that adds 2.
	  var add2 = makeAdder(2);
	
	  // Create a function that adds 4.
	  var add4 = makeAdder(4);
	
	  assert(add2(3) == 5);
	  assert(add4(3) == 7);
	}

## 四、操作符
### 1. 相等  
Dart中判断内容相等与否使用**==**，而判断是否是同**identical()方法**  

### 2. 类型判定  
类型转换为as，判断是否为某一类型(或者子类型)为is  

### 3. 特殊的赋值操作符  
??=的意思是如果值为null则进行赋值操作，否则原来的变量保持不变  

	b ??= 1  

### 4. 测试是否为null
Dart中还有一个独特的二元操作符：**expr1 ？？ expr2**。这个操作符的意思是，如果expr1的结果是null，则返回expr2的结果，否则返回expr1的结果

## 五、面向对象  
### 5.1 类和接口  

Dart 中的类和 Java 类似，用 class 关键字定义一个类，用 extends 关键字继承一个类，用 implements 关键字实现一个或多个接口。但和 Java 不同的是，**在 Dart 中类和接口是统一的，类就是接口**。因为每个类都有一个隐式接口，包括类的所有（可见的）实例成员（字段和方法）和它所实现的所有接口，但**类的构造函数不属于它的隐式接口**。一个只有抽象方法的类就相当于传统意义上的接口（比如 Java 的接口）。所以，**你可以继承一个类，也可以实现一个“类”**。如果你想复用已有类的一些实现，那么你可以继承一个类；如果你只是想具有该类/接口的外在行为，那么你可以实现这个类。因此，你不必再考虑是使用接口还是抽象类的问题了，它们没有区别。

在语法上，如果一个方法没有实现，那么直接以分号结束即可，这就是一个抽象方法。**(如果构造函数非常简单、没有额外的代码实现，那么直接用分号结束即可。构造函数与方法不同，这并不说明构造函数是抽象的。构造函数也不属于隐式接口的一部分。)**如果一个类中包含抽象方法，那么“应该”用 abstract 关键字声明这个类为抽象类，但并不强制要求，没有声明只会给出警告。抽象类表明它不应该被实例化，而是期望由其它类来实现，但这也不是强制的，不过调用一个未实现的方法在运行时肯定会报错。  

### 5.2 方法  

和其它语言一样，Dart 使用点号(.)引用一个对象上的方法或字段。另外，Dart 还支持使用两个点号**(..)进行级联调用（cascade operator）**，级联调用和普通方法调用一样，但是它**并不返回方法的返回值，而是返回调用对象自身**，这样你就可以连续调用同一个对象上的多个方法了。级联调用是语言层面的支持，不需要你特意设计 API 让方法返回自身。  

Dart中定义抽象方法的形式为：**普通方法名+参数列表+；**，即没有方法体，取而代之的是以分号结尾

### 5.3 字段和getter/setter  

在 Dart 中，**字段和方法是统一的**。**每个字段隐式对应了一对 getter 和 setter 方法，访问字段就是调用 getter/setter 方法**。getter/setter 方法是用 **get 和 set 关键字**定义的特殊方法，但只是以使用字段的形式调用（如 obj.x，而不是 obj.x() ）。如果字段是 final 或 const 的，那么它只有一个 getter 方法，没有setter 方法。当你读取一个实例变量时（如 obj.x），实际上是调用了 x 对应的 getter 方法，设置该实例变量的值时（如 obj.x = 1）实际是调用了它的 setter 方法。你也**可以利用 getter/setter 方法构造出伪字段，它并不是真是的字段，但使用起来二者无异**。  

	class Rectangle {
  		num left, top, width, height;

  		num get right             => left + width;
      		set right(num value)  => left = value - width;
  		num get bottom            => top + height;
      		set bottom(num value) => top = value - height;

  		Rectangle(this.left, this.top, this.width, this.height);
	}


	main() {
  		var rect = new Rectangle(3, 4, 20, 15);
  		print(rect.left);
  		print(rect.bottom); // 获取 bottom 
  		rect.top = 6;
  		rect.right = 12; // 设置 right
	}  

上面也可以通过rect.right访问一个right变量，虽然我们没有在定义类的时候显示的定义这个成员。  

> 注意：getter和setter在定义的时候和普通的函数有一个差别就是get(作为方法名)后面试变量名，之后才是参数列表  
> **关于getter和setter的思考：**getter和setter的存在像是一种语法糖，方便我们维护代码，**所有的属性都由编译器默认添加或者认为显示定义getter和setter(常量没有setter)，getter和setter对类变量和实例变量都有效**

### 5.4 静态方法和静态变量  

静态变量和静态方法的定义和一般使用同Java一样，有一点不一样的是**Dart中普通的方法内部也可以访问静态成员**   

还有一点是：**Dart中的静态变量(类变量和顶层变量)有延迟初始化的特点，即在getter第一次被调用的时候才执行初始化**，延迟初始化可能会带来一些问题，例如下面：  

	class Cat{}
	class DeadCat extends Cat{}
	class LiveCat extends Cat{
		LiveCat(){print("I am alive");}
	} 
	var test = new LiveCat();
	main() {
		test = new DeadCat();
	}

这里的test是一个顶层变量，所以由于延迟初始化，LiveCat的构造器并不会被访问，其中的print也不会被调用

### 5.5 构造函数  

构造函数的执行顺序：  

1. 创建对象
2. 设置声明时初始化的实例变量的值
3. 执行构造函数初始化列表
4. 显示或隐式调用超类的构造函数
5. 执行构造函数

**构造函数没有继承性**

初始化变量的特殊用法：**this.field**  

	Rectangle(num height, num width) {
  		this.height = height;
  		this.width = width;
	}  
	Rectangle.rec(this.height,this.width);

上面两种初始化变量的构造函数是等价的。  

> **注意：对于final修饰的成员变量，想要通过构造器进行初始化必须得使用简写的形式，而不是传统的类似于Java那种在构造函数的函数体中通过this.赋值操作进行，原因是final没有setter方法，所以赋值操作不可行。所以二者其实并不等价。说白了就是简写形式对变量进行初始化并没有调用setter**

#### 初始化列表  
在构造函数体执行之前除了可以调用超类构造函数之外，还可以初始化实例参数，使用逗号分隔，**使用的形式是在构造函数的参数列表之后、函数体之前，以冒号开始进行赋值操作**。  

上面说过，初始化列表的执行时间在构造函数方法体之前，但是比在类中定义成员时初始化要晚：  

	class Parent {
	  var x = 1;
	  Parent(): x = 2;
	  Parent.test(): x = 2{
	    this.x = 3;
	  }
	}
	main(List<String> args) async {
	  var p = Parent();
	  print("p.x = ${p.x}");
	  var pp = Parent.test();
	  print("pp.x = ${pp.x}");
	}  
	------------
	output：p.x = 2
			pp.x = 3

#### 命名构造函数  

Dart不支持方法重载，所以，构造函数就无辜躺枪了，即**只能有一个标准构造函数(以类名命名的构造函数)**，为了提供适应更多的创建对象的场景，Dart提供了**命名构造函数(以ClassName.xxx命名)**。**另外，命名构造函数和方法在相同的命名空间内，所以它们不能重名。**  

#### 重定向构造函数  
在平时有时候我们某一个构造函数没有自己的实现，只是调用另一个构造函数，这时候我们可以使用重定向构造函数  

	Point.alongXAxis(num x) : this(x, 0);

#### 调用超类构造函数  

Dart 中的**构造函数不会被继承**。如果子类的构造函数没有显示地调用超类的构造函数，那么会自动调用超类的默认的无参数的构造函数。如果超类没有定义默认的无参数构造函数（比如定义了一个命名构造函数而没有定义标准构造函数，或者标准构造函数是有参数的），那么子类必需显示地调用超类的构造函数。(这里有点像kotlin)  

	class Employee extends Person {
  		Employee.fromJson(Map data) : super.fromJson(data{
    		print('in Employee');
  		}
	}  

#### 常量构造函数  

如果要创建一个不可变的对象，你可以把它**定义为编译时常量的对象。要求定义一个 const 构造函数，并且所有的实例变量都是 final 或 const 的**  

	class ImmutablePoint {
  		final num x;
  		final num y;
  		const ImmutablePoint(this.x, this.y); // 常量构造函数
  		static final ImmutablePoint origin = const ImmutablePoint(0, 0); // 创建一个常量对象
	}  

通过const constructor的方法调用常量构造函数是，如果多次调用该构造函数**传入的参数都是一样**的，那么这多次调用**生成的只有同一个对象**

>**注意：创建一个常量对象时，不使用 new constructor，而是使用 const constructor **    

#### 工厂构造函数  

前面介绍的构造函数都属于生成式的构造函数，即语言替你完成对象的创建和返回，你只需要在构造函数中对这个刚刚创建出来的对象做一些初始化工作即可。但有时候为了更加灵活地创建一个对象，比如返回一个之前已经创建过的缓存对象，那么生成式构造函数就不能满足要求了。工厂模式已经在其它语言中被广泛使用，一般是通过一个静态方法实现的，由你自己来负责创建对象并返回它。在 Dart 中，使用关键字 factory 就可以定义一个工厂构造函数，也是由你自己来负责创建对象并返回它。但好处是它**依然使用 new constructor() 的形式**，这样使用工厂构造函数和使用普通构造函数在形式上没有区别  

	class Symbol {
  		final String name;
  		static Map<String, Symbol> _cache;

  		factory Symbol(String name) { // 通过 factory 关键字定义工厂构造函数
    		if (_cache == null) {
      			_cache = {};
    		}

    		if (_cache.containsKey(name)) {
      			return _cache[name];
    		} else {
      			final symbol = new Symbol._internal(name);
      			_cache[name] = symbol;
      			return symbol;
    		}
  		}

  		Symbol._internal(this.name);
	}

工厂构造函数不仅可以返回一个缓存的对象，也可以返回一个类的子类型。所以一个抽象类/接口可以定义一个工厂构造函数，返回它的默认实现。比如在 Dart 的核心库中就普遍采用这种形式，Map 被认为是个接口，可以有多种实现，new Map() 会返回 Map 的一个默认实现的实例。其实，在**工厂构造函数中你可以返回任何对象，不过在检查模式下要求返回一个类的子类型。**   

> **注意：工厂构造函数里面不能通过this来访问任何成员** 

## 六、异步编程  

有时候我们需要让一个调用异步执行，比如一个比较耗时的操作，好继续后面的处理，而不是一直等待处理完成。异步执行就是调用本身立即返回，并在稍后的某个时候执行完成时再获得返回结果。在 Dart 中，这是通过 Future<T> 对象实现的。  

### 总述  
Asynchrony support（异步支持）
Dart 有一些语言特性来支持 异步编程。 最常见的特性是 **async 方法和 await 表达式**。

Dart 库中有很多返回**Future 或者 Stream 对象**的方法。 这些方法是 **异步的： 这些函数在设置完基本的操作 后就返回了， 而无需等待操作执行完成**。 例如读取一个文件，在打开文件后就返回了。

有两种方法可以使用Future对象中的数据：  

* 使用**async和await**  
* 使用Future API  

同样，从Stream中获取数据也有两种方式：  

* 使用async和一个异步for循环(await for)  
* 使用Stream API  

**要使用await，其所在的方法必须带有async关键字**  

	checkVersion() async {
	  var version = await lookUpVersion();
	  if (version == expectedVersion) {
	    // Do something.
	  } else {
	    // Do something else.
	  }
	}  

**await表达式中，表达式的值通常是一个Future对象，如果不是就自动封装到Future对象中(作为Future对象的泛型)**  

	Future<String> lookUpVersion() async => '1.0.0';

我们可以通过 then() 方法注册一个回调函数在成功执行完成时调用，并获得返回值。如果执行失败，则会抛出异常。另外也可以通过 handleException() 注册遇到异常时执行的回调函数，如果这个回调函数返回 true ，那么 Future 认为异常已被很好地处理了就不再抛出异常，否则还是会抛出异常。但一般不必注册 handleException，因为异常会正常抛出给外面的代码。onComplete 注册的回调函数不管成功还是失败都会执行。  

当 Future 完成时，依次执行以下动作：  

1. 如果成功完成，那么执行 then 方法中注册的回调函数
2. 如果执行失败，那么依次执行 handleException 方法中注册的回调函数，直到其中一个返回 true
3. 执行 onComplete 方法中注册的回调函数
4. 如果执行失败，并且 then 方法中至少注册了一个回调函数，并且没有一个 handleException 方法中注册的回调函数返回 true，那么就抛出异常。

使用 Completer 对象可以创建一个 Future，并在之后为 Future 提供一个返回值。  

	Future<bool> longExpensiveSearch() {
  		var completer = new Completer();
  		// 这里执行较为耗时的操作
  		// ...
  		completer.complete(true);
  		return completer.future;
		}

	main() {
  		var future = longExpensiveSearch(); // 立即返回
  		future.then((value) { // 注册成功时的回调函数并立即返回
    		print(value);
  		});
	}  

>实现异步很简单：  
>**1. 创建一个方法，返回一个Future对象，这个Future对象通过Completer对象创建，在Completer对象执行complete(true)方法之前进行耗时操作；在compete(true)方法之后返回Future既可**。  
>**2. 调用上面的方法得到future对象，通过该对象调用then()方法注册回调函数**   

## 七、并发编程  

Dart 没有并发时的共享状态，所有 Dart 代码都是 Isolate 中运行的，包括最初的 main() Isolate（也称为 root Isolate）。Dart 内建了 Isolate 机制，类似于 Actor ，仅在端口（Port）上通过消息进行通信。 每个 Isolate 有它自己的堆（Heap）和栈（Stack），彼此隔离。消息在接收前被复制，这样 Isolate 之间就无法操作相同的对象了。因为状态是彼此隔离的，所以这种并发编程模式不需要锁、互斥量什么的。

每个 Isolate 有它自己的堆内存，这意味着其中所有内存中的值，包括全局数据，都仅对该 Isolate 可见。Isolate 之间的通信只能通过传递消息的机制完成。消息通过端口（port）收发。

Isolate 只是一个概念，具体一个 Isolate 是什么取决于如何实现。比如，在 Dart VM 中一个 Isolate 可能是会是一个线程，在 Web 中可能会是一个 Web Worker 。  

### 基本概念  

要使用 Isolate， 需要导入 dart:isolate 库。它主要包括：  

	* 顶层变量 port ，即一个 ReceivePort 对象实例，代表当前 Isolate 的初始接收端口
	* 顶层函数 spawnUri() 和 spawnFunction() ，用于创建新的 Isolate
	* SendPort 和 ReceivePort 类，分别代表发送端口和接收端口

发送消息使用 SendPort ，接收消息使用 ReceivePort 。每个 Isolate 在建立之初都有一个默认的 ReceivePort，包括最初 main() 所在的 root Isolate。这个默认的 ReceivePort 通过 dart:isolate 中的顶层变量 port 获得。每个 Isolate 除了默认的 ReceivePort 外，还可以随时使用构造函数 new ReceivePort() 创建其它  ReceivePort 。通过 ReceivePort 的 toSendPort() 方法又可以创建一个 SendPort ，通过发送端口发送的消息都会传递给对应的接收端口。一个接收端口可以有多个对应的发送端口。发送端口本身也可以作为消息发送给其它的 Isolate，其它 Isolate 使用发送端口给对应 Isolate 发送消息。  

小结：每个 Dart 程序由一个或多个 Isolate 组成，Isolate 又可以创建其它 Isolate。每个 Isolate 可以有一个或多个接收端口，每个接收端口又可以有一个或多个对应的发送端口，Isolate 之间依靠发送端口和接收端口收发消息进行通信。  

### 创建Isolate  

dart:isolate 中的顶层函数 spawnFunction() 用于创建一个新的 Isolate，它**接收一个函数作为新 Isolate 的入口点，新的 Isolate 与当前 Isolate 使用相同的代码，但是从传递的函数开始执行**。  

任何顶层函数都可以作为 Isolate 的入口点，但不能用闭包函数。这个入口点应该是一个无参数并返回 void 的函数。把入口点作为参数传给spawnFunction() 函数就创建了一个 Isolate，并开始执行这个函数。  

	echo() {
  		print('hello');
	}

	main() {
  		spawnFunction(echo); // 以 echo 函数为入口，创建一个新的 Isolate
	}  

>注意：上面的程序还有点问题，echo() 函数可能执行了，也可能没执行。这是因为 main() 执行完 spawnFunction() 后就立即结束了，这样 root Isolate 退出，整个 Dart 程序便退出了。具体请见后面的生命周期介绍。  

### 发送消息  

调用 **spawnFunction() 会返回一个与新 Isolate 进行通信用的发送端口 SendPort ，这个发送端口来自于新 Isolate 的默认接收端口**。有了这个发送端口，我们便可以向新的 Isolate 发送消息了。  

消息内容可以是：  

* 基本类型的值（null、num、bool、double、String）
* SendPort 对象实例
* 以上这些元素组成的 List 或 Map，其中可以继续嵌入 List 或 Map
* 特定情况下可以是任意对象

特定情况是指：如果两个 Isolate 共享相同的代码，并且运行在同一个进程中，比如使用 spawnFunction() 创建的 Isolate ，那么也可以发送任意对象。当然，发送的同样是对象的副本，而不会共享对象。发送任意对象目前只被 Dart VM 支持，dart2js 只支持上面前三种说的消息类型。  

	echo() {
  		// 使用 port 接收消息
	}

	main() {
  		var sendPort = spawnFunction(echo);
  		sendPort.send('Hello from main');
	}  

send() 方法的第一个参数就是要发送的消息。另外，send() 方法还有一个可选的 SendPort 参数作为回复地址一起发送给接收者。这样接收者便可以使用这个发送端口回复消息了。你也可以传递另一个 Isolate 的发送端口，让接收者直接回复给别人而不是自己。  

	echo() {
  		// 使用 port 接收并且回复消息
	}

	main() {
  		var sendPort = spawnFunction(echo);

  		// 这里省略了接收的回复消息的代码
  		sendPort.send('Hello from main', port.toSendPort());

	}  

这个回复地址 SendPort 是通过默认端口 port 的 toSendPort() 方法创建的，当然，我们也可以新建一个接收端口，再用这个接收端口创建回复地址 SendPort 。  

实际上，如果你要发送一个消息并等待接收回复，还可以使用 SendPort 提供的另一个方法 call() ，它可以让你省去编写接收代码。call() 方法会发送一条消息，并返回一个 Future 对象用于接收 Isolate 的回复消息。  

	main() {
  		var sendPort = spawnFunction(echo);
  		sendPort.call('Hello from main').then((reply) {
    	print(reply);
  		});
	}  

实际上，call() 方法创建了一个新的接收端口，然后用这个接收端口的发送端口作为回复地址，当收到回复时，接收端口就关闭并返回 Future 完成。之后，我们就可以用 Future 的 then() 方法来处理收到的回复消息了。  

### 接收消息  

使用 ReceivePort 的 receive() 方法就可以接收消息，这个方法接收一个回调函数参数，回调函数中有两个参数：接收的消息和回复地址，回复地址就是发送消息时提供的 SendPort 。  

	echo(){
  		print('start echo');
  		port.receive((msg, replyTo){ // 接收消息
    		print('echo receive: $msg');
    		if(replyTo != null){
      			replyTo.send('hello $msg');
    		}
  		});
	}  

	main() {
  		var sendPort = spawnFunction(echo);
  		sendPort.call('hanguokai').then((reply) {
    	print(reply);
  		});
	}  

>看了上面的分析，可能对这种sendPort和ReceivePort的方式有点蒙蔽，实际上在我看来这是一种代理的方式：在send的示例中，可以指定第二个参数来指明接受者的发送地址，注意到那个发送的sendPort是来自spawnFunction函数创建的新Isolate的默认接收端口；而且，指定给接收方的返回端口是发送方Isolate内部的一个ReceivePort生成的一个sendPort()；也就是说，**实现通信的sendPort都是最终接收方(ReceivePort)生成的**，就像代理一样，最终是实现功能的都是别人的东西，这样一来，用别人提供的端口来和人家进行通信就看的合情合理了

## 八、Throw、Catch、Finally  
Dart中也支持抛出、抓取异常，Dart和java相反，**所有的异常都是unchecked的异常**，而且**Dart中可以抛出任何非空的对象**，不局限于Exception及其子类的对象：  

	throw Exception("this is normal exception");  //典型的抛出
	throw "this is Dart`s special";  //这里抛出了一个字符串    

还有一点是：**throw是一个语句，所以可以在任何地方出现**  

	void distanceTo(Point other) => throw UnimplementedError();  

### 8.1 捕获异常  
Dart中捕获异常的有两个关键字：**on和catch**  

我们可以使用其中的一个，也可以同时使用，区别就是：on后面必须紧跟一个类型，表示捕获该类型的异常；而catch后面有两个参数e和s,e表示捕获的异常对象，s表示StackTrace对象。总之**on实现处理特定的异常；catch则可以得到异常对象本身，因此可以获取异常对象的信息或对其进行处理**。因此传统的Java中catch的功能在Dart中就得同时使用on和catch才能实现：  

	try {
	  breedMoreLlamas();
	} on OutOfLlamasException {
	  // A specific exception
	  buyMoreLlamas();
	} on Exception catch (e) {
	  // Anything else that is an exception
	  print('Unknown exception: $e');
	} catch (e) {
	  // No specified type, handles all
	  print('Something really unknown: $e');
	}  

在dart中如果catch没有处理完，可以**使用rethrow直接将该异常继续抛出**：  

	void misbehave() {
	  try {
	    dynamic foo = true;
	    print(foo++); // Runtime error
	  } catch (e) {
	    print('misbehave() partially handled ${e.runtimeType}.');
	    rethrow; // Allow callers to see the exception.
	  }
	}   

## 九、泛型  
Dart中的泛型和Java大体上用法和特性都一样，这里只说几个不同点  

#### 不同点一  
Dart中的泛型是**保存有类型信息的，相反java中使用类型擦除来实现泛型**，这就意味着，下面的代码在dart中是完全有意义的：  

	var list = List<String>();
	assert(list is List<String>);

#### 不同点二  
在Dart中直接使用List、Map的字面量的时候也可以指定泛型：  

	var list = <String>["name","name2"];
	var map = <String,String>{"key1":"value1","key2":"value2"};

## 十、import  
Dart中导入包使用import来导入，以dart作为scheme时导入的是内置的包，以package为scheme时导入的是对应目录下的文件：  

	import 'dart:html';
	import 'package:lib1/lib1.dart';
	import 'package:lib2/lib2.dart' as lib2;  //as后面的名字用来解决两个模块中类的冲突  
	// Import only foo.
	import 'package:lib1/lib1.dart' show foo;

	// Import all names EXCEPT foo.
	import 'package:lib2/lib2.dart' hide foo;  

Dart中的import中一个很大的亮点是可以实现懒加载(也叫延迟加载)，即只有在用到的时候才去加载对应的库，这就节省了程序加载的时间，import实现懒加载只要在后面加上**deferred as**就行了，在使用的时候要先调用该**库的loadLibrary()方法**，之后就可以使用了。  

	import 'package:greetings/hello.dart' deferred as hello;
	Future greet() async {
	  await hello.loadLibrary();
	  hello.printGreeting();
	}  

> **提示：loadLibrary()方法可以被调用多次，但是不会出现问题，而且即使调用了多次，实际上也只会被加载一次**    
> 1. Dart隐式地将loadLibrary（）插入到使用deferred as namespace定义的命名空间中,loadLibrary（）函数返回Future。  
> 2. 不可以使用延迟加载的库中的类  

 





	



