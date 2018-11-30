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

### 常见的类型  
#### 数字  
Dart 中的数字是 num，它有两个子类型 int 和 double，int 是任意长度的整数，double 是双精度浮点数。一般情况下，主要应该使用 num 和 int 类型，double 很少使用（因为它限制输入只能是 double 而不能是 int）。注意，数字类型也是对象，因此可以在数字上调用各种方法。  

#### 布尔  
Dart 中的 bool 类型只有 true 和 false。在检查模式下，任何其它类型的对象都不能当作 bool 类型来使用；在生产模式下，非 bool 类型的对象都被当作 false。这点与 JavaScript 不同，但这样的规则更简单、清晰，不容易犯错。  

#### List 和 Map  
Dart 语言中内置了常用的 List 和 Map 。List 是 Collection 的子类型，另外 Queue 和 Set 也是 Collection 的子类型 。  
List和Map的使用和Java差不多，就不说了。  
Map 的字面量语法要求 key 必须字符串，但如果是用构造函数创建的，则任何对象都可以是 key。  

	var map = {
  		'one': 1,
  		'two': 2
	};  
	var m=new Map<int,int>(){1:1};

### 三、函数  
Dart中的最普通的函数在定义的时候和Java中的方法在定义的时候别无二致，**在Dart中如果一个函数没有return语句指定返回值的话，并不想Java一样需要声明void，这时候会默认返回null**  
由于Dart中的类型标注是可选的，所以函数的参数类型以及返回值类型都是可以省略的。  

如果函数体中仅仅是简单地返回一个表达式的值，那么可以使用更简洁的箭头语法**=>expr;**，它等价于**{return expr;}**。  

	String sayHello(String name){
		return 'hello $name';
	}
	sayHello(name){
		return 'hello $name';
	}
	sayhello(name)=>'hello $name';

上面三种函数的定义是等价的。  

你不仅可以定义一个有名字的函数，还可以使用**匿名函数、把函数作为参数传递给其它函数、把函数赋值给一个变量、返回一个函数(这时候返回类型就是Function)等**。  

	main() {
  		var sayHello = (name) => 'hello $name'; // 把一个匿名函数赋值给变量
  		sayHello('Guokai'); // 利用该变量调用函数
	}  

>理解上面的把函数赋值给一个对象：之前说过，在Dart中任何东西都是对象，函数也是，函数是Function类型的对象，所以讲一个Function类型的对象赋值给一个var变量就说的过去了。  

#### 可选参数  
**Dart 支持两种类型的可选参数，命名(named)可选参数和位置(positional)可选参数，但不能同时使用。可选的参数可以设置默认值，默认值要求是编译时常量，没有设置的可选参数默认值是 null 。**  

##### 命名可选参数  
命名可选参数使用大括号的语法，默认值用冒号指定。在使用的时候，多个可选参数可以任意指定其中的 0 个或多个，并且与指定的先后顺序无关，但参数值前必须指定参数名。所以参数名也成了 API 的一部分，就像函数的命名一样，你需要多花点时间认真考虑参数名。  

	foo (a, {b, c: 3, d: 4, e}) {
  		print("$a  $b  $c  $d  $e");
	}  
	main() {
  		// 指定的顺序无关，但必需用参数名指定
  		// 这里 a 是必选参数，可选参数 b,c,d,e 分别是 null,1,4,5
  		foo(1, e: 5, c: 1);
	}  

##### 位置可选参数  
位置可选参数使用方括号的语法，默认值用等号指定。在使用的时候，不能定参数名，参数的先后顺序必需与定义的顺序一致，**只能省略后面的参数。**  

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

## 四、面向对象  
### 4.1 类和接口  

Dart 中的类和 Java 类似，用 class 关键字定义一个类，用 extends 关键字继承一个类，用 implements 关键字实现一个或多个接口。但和 Java 不同的是，**在 Dart 中类和接口是统一的，类就是接口**。因为每个类都有一个隐式接口，包括类的所有（可见的）实例成员（字段和方法）和它所实现的所有接口，但**类的构造函数不属于它的隐式接口**。一个只有抽象方法的类就相当于传统意义上的接口（比如 Java 的接口）。所以，**你可以继承一个类，也可以实现一个“类”**。如果你想复用已有类的一些实现，那么你可以继承一个类；如果你只是想具有该类/接口的外在行为，那么你可以实现这个类。因此，你不必再考虑是使用接口还是抽象类的问题了，它们没有区别。

在语法上，如果一个方法没有实现，那么直接以分号结束即可，这就是一个抽象方法。**(如果构造函数非常简单、没有额外的代码实现，那么直接用分号结束即可。构造函数与方法不同，这并不说明构造函数是抽象的。构造函数也不属于隐式接口的一部分。)**如果一个类中包含抽象方法，那么“应该”用 abstract 关键字声明这个类为抽象类，但并不强制要求，没有声明只会给出警告。抽象类表明它不应该被实例化，而是期望由其它类来实现，但这也不是强制的，不过调用一个未实现的方法在运行时肯定会报错。  

### 4.2 方法  

和其它语言一样，Dart 使用点号(.)引用一个对象上的方法或字段。另外，Dart 还支持使用两个点号**(..)进行级联调用（cascade operator）**，级联调用和普通方法调用一样，但是它**并不返回方法的返回值，而是返回调用对象自身**，这样你就可以连续调用同一个对象上的多个方法了。级联调用是语言层面的支持，不需要你特意设计 API 让方法返回自身。  

### 4.3 字段和getter/setter  

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

### 4.4 静态方法和静态变量  

静态变量和静态方法的定义和一般使用同Java一样，有一点不一样的是**Dart中普通的方法内部也可以访问静态成员**  

### 4.5 构造函数  

构造函数的执行顺序：  

1. 创建对象
2. 设置声明时初始化的实例变量的值
3. 执行构造函数初始化列表，以及显示或隐式调用超类的构造函数
4. 执行构造函数

初始化变量的特殊用法：**this.field**  

	Rectangle(num height, num width) {
  		this.height = height;
  		this.width = width;
	}  
	Rectangle.rec(this.height,this.width);

上面两种初始化变量的构造函数是等价的。

#### 命名构造函数  

Dart不支持方法重载，所以，构造函数就无辜躺枪了，即**只能有一个标准构造函数(以类名命名的构造函数)**，为了提供适应更多的创建对象的场景，Dart提供了**命名构造函数(以ClassName.xxx命名)**。**另外，命名构造函数和方法在相同的命名空间内，所以它们不能重名。**  

#### 调用超类构造函数  

Dart 中的构造函数不会被继承。如果子类的构造函数没有显示地调用超类的构造函数，那么会自动调用超类的默认的无参数的构造函数。如果超类没有定义默认的无参数构造函数（比如定义了一个命名构造函数而没有定义标准构造函数，或者标准构造函数是有参数的），那么子类必需显示地调用超类的构造函数。(这里有点像kotlin)  

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

>**注意：创建一个常量对象时，不使用 new constructor，而是使用 const constructor **  

#### 工厂构造函数  

前面介绍的构造函数都属于生成式的构造函数，即语言替你完成对象的创建和返回，你只需要在构造函数中对这个刚刚创建出来的对象做一些初始化工作即可。但有时候为了更加灵活地创建一个对象，比如返回一个之前已经创建过的缓存对象，那么生成式构造函数就不能满足要求了。工厂模式已经在其它语言中被广泛使用，一般是通过一个静态方法实现的，由你自己来负责创建对象并返回它。在 Dart 中，使用关键字 factory 就可以定义一个工厂构造函数，也是由你自己来负责创建对象并返回它。但好处是它依然使用 new constructor() 的形式，这样使用工厂构造函数和使用普通构造函数在形式上没有区别  

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

## 五、异步编程  

有时候我们需要让一个调用异步执行，比如一个比较耗时的操作，好继续后面的处理，而不是一直等待处理完成。异步执行就是调用本身立即返回，并在稍后的某个时候执行完成时再获得返回结果。在 Dart 中，这是通过 Future<T> 对象实现的。我们可以通过 then() 方法注册一个回调函数在成功执行完成时调用，并获得返回值。如果执行失败，则会抛出异常。另外也可以通过 handleException() 注册遇到异常时执行的回调函数，如果这个回调函数返回 true ，那么 Future 认为异常已被很好地处理了就不再抛出异常，否则还是会抛出异常。但一般不必注册 handleException，因为异常会正常抛出给外面的代码。onComplete 注册的回调函数不管成功还是失败都会执行。  

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

## 六、并发编程  

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