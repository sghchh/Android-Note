# Google Flutter  
>Flutter中文网：[https://flutterchina.club/tutorials/layout/#alignment](https://flutterchina.club/tutorials/layout/#alignment)  


## 一、初尝Flutter--从阅读官方实例开始  
  
	import 'package:flutter/material.dart';

	void main() => runApp(new MyApp());

	class MyApp extends StatelessWidget {
  		// This widget is the root of your application.
  		@override
  		Widget build(BuildContext context) {
    	return new MaterialApp(
      	title: 'Flutter Demo',
      	theme: new ThemeData(
        	// This is the theme of your application.
        	//
        	// Try running your application with "flutter run". You'll see the
        	// application has a blue toolbar. Then, without quitting the app, try
        	// changing the primarySwatch below to Colors.green and then invoke
        	// "hot reload" (press "r" in the console where you ran "flutter run",
        	// or press Run > Flutter Hot Reload in IntelliJ). Notice that the
        	// counter didn't reset back to zero; the application is not restarted.
        	primarySwatch: Colors.blue,
      	),
      	home: new MyHomePage(title: 'Flutter Demo Home Page'),
    	);
  		}
	}

	class MyHomePage extends StatefulWidget {
  		MyHomePage({Key key, this.title}) : super(key: key);

  		// This widget is the home page of your application. It is stateful, meaning
  		// that it has a State object (defined below) that contains fields that affect
  		// how it looks.

  		// This class is the configuration for the state. It holds the values (in this
  		// case the title) provided by the parent (in this case the App widget) and
  		// used by the build method of the State. Fields in a Widget subclass are
  		// always marked "final".

  		final String title;

  		@override
  		_MyHomePageState createState() => new _MyHomePageState();
	}

	class _MyHomePageState extends State<MyHomePage> {
  		int _counter = 0;

  		void _incrementCounter() {
    		setState(() {
      		// This call to setState tells the Flutter framework that something has
      		// changed in this State, which causes it to rerun the build method below
      		// so that the display can reflect the updated values. If we changed
      		// _counter without calling setState(), then the build method would not be
      		// called again, and so nothing would appear to happen.
      		_counter++;
    		});
  		}

  		@override
  		Widget build(BuildContext context) {
    		// This method is rerun every time setState is called, for instance as done
    		// by the _incrementCounter method above.
    		//
    		// The Flutter framework has been optimized to make rerunning build methods
    		// fast, so that you can just rebuild anything that needs updating rather
    		// than having to individually change instances of widgets.
    		return new Scaffold(
      		appBar: new AppBar(
        		// Here we take the value from the MyHomePage object that was created by
        		// the App.build method, and use it to set our appbar title.
        		title: new Text(widget.title),
      		),
      		body: new Center(
        		// Center is a layout widget. It takes a single child and positions it
        		// in the middle of the parent.
        		child: new Column(
          		// Column is also layout widget. It takes a list of children and
          		// arranges them vertically. By default, it sizes itself to fit its
          		// children horizontally, and tries to be as tall as its parent.
          		//
          		// Invoke "debug paint" (press "p" in the console where you ran
          		// "flutter run", or select "Toggle Debug Paint" from the Flutter tool
          		// window in IntelliJ) to see the wireframe for each widget.
          		//
          		// Column has various properties to control how it sizes itself and
          		// how it positions its children. Here we use mainAxisAlignment to
          		// center the children vertically; the main axis here is the vertical
          		// axis because Columns are vertical (the cross axis would be
          		// horizontal).
          		mainAxisAlignment: MainAxisAlignment.center,
          		children: <Widget>[
            		new Text(
              		'You have pushed the button this many times:',
            		),
            		new Text(
              		'$_counter',
              		style: Theme.of(context).textTheme.display1,
            		),
          		],
        		),
      		),
      		floatingActionButton: new FloatingActionButton(
        		onPressed: _incrementCounter,
        		tooltip: 'Increment',
        		child: new Icon(Icons.add),
      		), // This trailing comma makes auto-formatting nicer for build methods.
    		);
  		}
	}  
  
结合简单看的一点官方文档，简单说说自己看完这个官方实例的理解：  
### 1.1 main方法  
这个程序的入口是**main**方法，这有点像C语言。其中**main**方法里面调用的**runApp**函数接受指定的**widget**并使其成为**widget树**的树根。在此例中为**MyApp**。**框架强制根widget覆盖整个屏幕**。  
根据上面所说我们最少需要一个**根widget**，所以在创建新的widget的时候我们需要继承一个widget，有两种选择，在例子中也有体现：**StatelessWidget和StatefulWidget**，两者的区别是后者适用于**需要管理状态的widget**。widget的主要工作是实现一个**build**函数，用以构建自身，通常由一些较低级别的widget组成。  

>理解：首先要显示出一个UI界面，要在main方法中调用**runApp**函数并且传入widget对象，**根widget对象**就像Android中的**DecorView**；然后widget对象通过*build函数*构建，里面可以**嵌套其他的widget**共同组成复杂的界面；最后：在Flutter中，widget对应于Android中的View。而在创建一个widget的时候传入的类似于Map类型的参数应该是各种配置，类似于Android的XML中的各种属性    

### 1.2 widget  
常见的基础widget：  

* **Text:**该 widget 可让您在应用程序中创建一个带格式的文本  
* **Row， Column**： 这些富有弹性的widget可让您在水平（Row）和垂直（Column）方向上创建灵活的布局。其设计是基于web开发中的Flexbox布局模型。分别对应于**水平的LinearLayout和垂直的LinearLayout**  
* **Stack**： 取代线性布局 (译者语：水平或垂直，和Android中的LinearLayout相似)，Stack允许子 widget 堆叠， 你可以使用 Positioned 来定位他们相对于Stack的顶部、右边、底部或左边缘的位置。Stacks是基于web开发中的绝度定位（absolute positioning )布局模型设计的。**相当于RelativeLayout**  
* **Container**： Container widget 可让您**创建矩形视觉元素**。container 可以**装饰为一个BoxDecoration, 如 background、一个边框、或者一个阴影。** Container 也可以具有边距（margins），填充(padding)和应用于其大小的约束(constraints)。另外， Container可以使用矩阵在三维空间中对其进行变换。可以*通过这个实现分割线等*。  

### 1.3 Metrial组件  
Flutter提供了许多widgets，可帮助您构建遵循Material Design的应用程序。Material应用程序以**MaterialApp widget开始**， 该widget在应用程序的根部创建了一些有用的widget，其中包括一个Navigator， 它管理由字符串（也称为路由）标识的小部件堆栈。Navigator可以让您的应用程序在屏幕之间的平滑的过渡。 是否使用MaterialAppwidget 完全是可选的，但是使用它是一个很好的做法。  
要想使用Metrial 控件首先要保证在**pubspec.yaml**设置： 

	 flutter:
  		uses-material-design: true    

## 二、widget学习  
在看了官方文档的布局教程后，对Flutter中的布局的构建和一些常用的widget以及其属性有了认识  

将Widget分为两类，一类是**Layout Widget，这类Widget含有child或者children属性**，也就是可以含有子Widget，对应于原生中的layout控件；另一类是**Visiable Widget**，就是不含child不含children属性的Widget，对应于原生中普通的View。

### 2.1 布局中的常客--Row(行)、Column(列)  
在教程中没有将这两个widget列入**布局widget**中，但是我觉得这两个和其他的布局widget一样，都是**管理普通的widget**。    
将普通的widget添加在Row或者Column中可以通过**设置对齐、调整、聚集**来对包含在其中的widget进行管理。  

#### 2.1.1 对齐widget  

您可以控制行或列如何使用**mainAxisAlignment和crossAxisAlignment属性**来对齐其子项。 对于行(Row)来说，主轴是水平方向，横轴垂直方向。对于列（Column）来说，主轴垂直方向，横轴水平方向。  

![](https://flutterchina.club/tutorials/layout/images/row-diagram.png)
  
![](https://flutterchina.club/tutorials/layout/images/column-diagram.png)
	  
**MainAxisAlignment 和CrossAxisAlignment**类提供了很多控制对齐的常量(这里只列出了我知道的)：  

* star：左对齐  
* spaceEvenly：方向上的子widget平均占据空间    

#### 2.1.2 调整widget  
调整widget的场景是，想要实现类似于match_parent的效果、想要实现LinearLayout的layout_weight的功能。  
这些可以通过将widget包裹在**Expanded**中来实现：  
首先，Expanded自动就有**填充方向上的空余空间的效果**，其次可以通过设置**flex(LinearLayout的layout_weight)属性**来设置widget的弹性系数。  
> **Row和Column子控件有灵活与不灵活的两种，Row和Column首先列出不灵活的子控件，减去它们的总宽度，计算还有多少可用的空间。然后Row按照Flexible.flex属性确定的比例在可用空间中列出灵活的子控件。要控制灵活子控件,需要使用Flexible控件**  

	Row(
	  crossAxisAlignment: CrossAxisAlignment.center,
	  children: [
	    Expanded(
	      child: Image.asset('images/pic1.jpg'),
	    ),
	    Expanded(
	      flex: 2,
	      child: Image.asset('images/pic2.jpg'),
	    ),
	    Expanded(
	      child: Image.asset('images/pic3.jpg'),
	    ),
	  ],
	);  

![](https://flutter.io/assets/ui/layout/row-expanded-visual-fa9a01df7b96ba5cb33162b91369658fea86554139953e3cc0357de83281133d.png)

#### 2.1.3 聚集widget  
默认情况下，行或列沿着其主轴会尽可能占用尽可能多的空间，但如果要将孩子紧密聚集在一起，可以将**mainAxisSize**设置为MainAxisSize.min。 以下示例使用此属性将星形图标聚集在一起（如果不聚集，五张星形图标会分散开）。  

	class _MyHomePageState extends State<MyHomePage> {
  		@override
  		Widget build(BuildContext context) {
    		var packedRow = new Row(
      		mainAxisSize: MainAxisSize.min,
      		children: [
        		new Icon(Icons.star, color: Colors.green[500]),
        		new Icon(Icons.star, color: Colors.green[500]),
        		new Icon(Icons.star, color: Colors.green[500]),
        		new Icon(Icons.star, color: Colors.black),
        		new Icon(Icons.star, color: Colors.black),
      			],
    		);

  			// ...
		}  

![](https://flutterchina.club/tutorials/layout/images/packed.png)  

### 2.2 常用的布局widgets  
标准的widget：  
* **Container**：添加 padding, margins, borders, background color, 或将其他装饰添加到widget.  
* **GirdView**：将 widgets 排列为可滚动的网格.  
* **ListView**：将widget排列为可滚动列表  
* **Stack**：将widget重叠在另一个widget之上.  

Metrial Components：  
* **Card**：将相关内容放到带圆角和投影的盒子中。  
* **ListTile**：将最多3行文字，以及可选的行前和和行尾的图标排成一行  

#### 2.2.1 Container  
许多布局会自由使用容器来使用padding分隔widget，或者添加边框（border）或边距（margin）。您可以通过将整个布局放入容器并更改其背景颜色或图片来更改设备的背景。
> **添加背景和border好像是通过decoration传入的Decoration对象的内部属性控制的**  

**Container概要：**  

*  添加padding, margins, borders  
*  改变背景颜色或图片  

![](https://flutterchina.club/tutorials/layout/images/margin-padding-border.png)  

#### 2.2.2 GirdView  
使用GridView将widget放置为二维列表。 GridView提供了两个预制list，或者您可以构建自定义网格。当GridView检测到其内容太长而不适合渲染框时，它会自动滚动。  

**GirdView：**  

* 在网格中放置widget  
* 检测列内容超过渲染框时自动提供滚动  
* 构建您自己的自定义grid，或使用一下提供的grid之一:  
	* GridView.count 允许您指定列数
	* GridView.extent 允许您指定项的最大像素宽度

#### 2.2.3 ListView  
ListView是一个类似列的widget，它的内容对于其渲染框太长时会自动提供滚动。  

**ListView概要：**  

* 用于组织盒子中列表的特殊Column
* 可以水平或垂直放置
* 检测它的内容超过显示框时提供滚动
* 比Column配置少，但更易于使用并支持滚动   

#### 2.2.4 Card  
Material Components 库中的Card包含相关内容块，可以由大多数类型的widget构成，但通常与ListTile一起使用。Card有一个孩子， 但它可以是支持多个孩子的列，行，列表，网格或其他小部件。默认情况下，Card将其大小缩小为0像素。您可以使用**SizedBox**来限制Card的大小。

在Flutter中，Card具有圆角和阴影，这使它有一个3D效果。更改Card的**elevation属性**允许您控制投影效果。 例如，将elevation设置为24.0，将会使Card从视觉上抬离表面并使阴影变得更加分散。 有关支持的elevation值的列表，请参见**Material guidelines中的Elevation and Shadows**。 **如果指定不支持的值将会完全禁用投影** 。  

**Card：**  

* 实现了一个 Material Design card
* 接受单个孩子，但该孩子可以是Row，Column或其他包含子级列表的widget
* 显示圆角和阴影
* Card内容不能滚动
* Material Components 库的一个widget

#### 2.2.5 ListTitle  
ListTile是Material Components库中的一个专门的行级widget，用于创建包含最多3行文本和可选的行前和行尾图标的行。ListTile在Card或ListView中最常用，但也可以在别处使用。  

**ListTitle概要：**  

* 包含最多3行文本和可选图标的专用行
* 比起Row不易配置，但更易于使用
* Material Components 库里的widget  

实例：  
![](https://flutterchina.club/tutorials/layout/images/card.png)    

### 3.感悟   
看完了官方文档中的实例，自己动手去实现一下，有些**感悟**：  

* 在容器widget的child属性添加子widget之前，把所有的该容器想要配置的属性都配置完后最后去添加child属性  
* 在容器widget添加children属性的时候，先把children中所有的widget都写好，然后回过头来从第一个开始进行完善，否则容易看花眼  

### 4. 有状态的Widget  
之前的Widget都是没有状态的，不能和用户有任何交互，如果想要有一些交互，那么必须去**实现StatefulWidget**或者用现有的StatefulWidget  

#### 4.1 理解什么是状态  
widget的状态是一些**可以更改的值**，如一个checkbox是否被选中等；widget的状态保存在一个**State对象**中，它和widget的布局显示分离；当widget状态改变的时候，**State对象调用setState()方法告诉框架去重绘widget**；当然，这个状态必须能够影响widget的显示，要不然即使状态改变了，也没有什么区别可供发现。  

![](https://flutterchina.club/tutorials/interactive/images/favorited-not-favorited.png)  

看看官方文档中的示例：  

**首先定义一个Widget继承StatefulWidget：**  

	class FavoriteWidget extends StatefulWidget {
  		@override
  		_FavoriteWidgetState createState() => new _FavoriteWidgetState();
	}  

>ps：这好像是套路写法  

可以看到，这个Widget中连**build方法都没有**，只是返回了一个State的实现类，所以重点是State类的定义，接下来就看看：  
**State<FavouriteWidget>:**  

	class _FavoriteWidgetState extends State<FavoriteWidget> {
  		bool _isFavorited = true;
  		int _favoriteCount = 41;

  		void _toggleFavorite() {
    		setState(() {
      		// If the lake is currently favorited, unfavorite it.
      		if (_isFavorited) {
        		_favoriteCount -= 1;
        		_isFavorited = false;
        		// Otherwise, favorite it.
      		} else {
        		_favoriteCount += 1;
        		_isFavorited = true;
      		}
    		});
  		}

  		@override
  		Widget build(BuildContext context) {
    		return new Row(
      		mainAxisSize: MainAxisSize.min,
      		children: [
        		new Container(
          		padding: new EdgeInsets.all(0.0),
          		child: new IconButton(
            		icon: (_isFavorited
                		? new Icon(Icons.star)
                		: new Icon(Icons.star_border)),
            		color: Colors.red[500],
            		onPressed: _toggleFavorite,
          		),
        		),
        		new SizedBox(
          		width: 18.0,
          		child: new Container(
            		child: new Text('$_favoriteCount'),
          		),
        		),
      		],
    		);
  		}
	}  

重点就是这个**State类**，首先定义的时候泛型要是之前定义的**StatefulWidget的实现类**；而**State实现类**中的**build**方法用来决定绘制出来是什么样子；要想实现重绘，必须通过**setState()方法通知框架进行重绘**。
> **为什么StatefulWidget和State是单独的对象呢？在Flutter中，这两种类型的对象有不同的生命周期。控件是临时对象，用于在当前状态下构建应用程序的呈现。State对象在被build()调用时是持久的，允许它们记住信息。**

>PS:这里的IconButton的onPressed属性类似于setOnClickListener，是一个点击事件的监听；我们发现，当状态发生改变的时候(一般通过点击事件等触发，在setState方法中去改变状态)，onPressed属性指定的方法被调用，里面又调用了setState方法，最后实现了重绘。  

>通过学习，暂时体会到的Flutter构建UI的优点：  
>1. widget虽然麻烦，但是却让我们一步步去解剖一个布局，这种锻炼是通过xml学不来的  
>2. widget是通过代码实现的，所以可以更好的实现复用。当有两个布局有相同的、或者类似的模块儿的时候，可以直接当做复用；在原来的Android中，我们还得再次构建一个xml  
>3. StatefulWidget以及StatelessWidget的分工让我们使得UI更加规范，StatefulWidget将widget对动作的响应(State类)和widget本身分开，降低了耦合，使得业务更加分明  
>4. State这种实现有更好的优点：想像一个场景：当我文本框内有文本的时候发送按钮才会点亮并且可点；如果没有文本则显示灰色并且不可点；在传统的Android我们可以通过EditText的TextWACher来监听文本的变化，通过TextWatcher来改变发送按钮的状态；或者可以通过自定义控件来实现。但是在Flutter中我们就可以在在State的实现类中添加一个变量作为记录按钮状态，当根据文本的改变来通过setState来更改这个变量(当然，前提是在设置按钮的时候该变量要能够影响按钮外观才行)。所以说：这个setState方法可以实现Android中invalidate()方法更多的功能，后者要求必须是定义控件时能够影响控件外观的值发生改变的时候才能有效果；**而setState则可以通过改变在设置控件的时候添加的一些能够影响控件外观的变量的值来重绘控件，就好比是对控件的一种扩展属性**

### 5. 对齐Widget  
#### 5.1 定位对齐  
**Align控件即对齐控件，能将子控件所指定方式对齐，并根据子控件的大小调整自己的大小。**  
对齐子控件的方式：  

	bottomCenter    (0.5, 1.0)    底部中心
	bottomLeft    (0.0, 1.0)    左下角
	bottomRight    (1.0, 1.0)    右下角
	center    (0.5, 0.5)    水平垂直居中
	centerLeft    (0.0, 0.5)    左边缘中心
	centerRight    (1.0, 0.5)    右边缘中心
	topCenter    (0.5, 0.0)    顶部中心
	topLeft    (0.0, 0.0)    左上角
	topRight    (1.0, 0.0)    右上角  

用法：  

	new Align(
            alignment: new FractionalOffset(0.0, 0.0),
            child: new Image.network('http://up.qqjia.com/z/25/tu32710_10.jpg'),
          ),
          new Align(
            alignment: FractionalOffset.bottomRight,
            child: new Image.network('http://up.qqjia.com/z/25/tu32710_11.jpg'),
          ),  

#### 5.2 填充对齐
Padding控件即填充控件，能给子控件插入给定的填充。  

	new Padding(
        padding: const EdgeInsets.all(50.0),
        child: new Image.network('http://up.qqjia.com/z/25/tu32710_4.jpg'),
      ),  

#### 5.3 比例大小  
SizedBox控件能强制子控件具有特定宽度、高度或两者都有  

	new SizedBox(
        width: 250.0,
        height: 250.0,
        child: new Container(
          decoration: new BoxDecoration(
            backgroundColor: Colors.lightBlueAccent[100],
          ),
        ),
      )...  
相对于Container，该控件更适合单独包裹一个小widget  
**AspectRatio**控件能强制子小部件的宽度和高度具有给定的**宽高比**，以宽度与高度的比例表示。

	 new AspectRatio(
        aspectRatio: 3.0 / 2.0,
        child: new Container(
          decoration: new BoxDecoration(
            backgroundColor: Colors.lightBlueAccent[100],
          ),
        ),
      )...  

### 6. 绘画效果  
#### 6.1 背景  
DecoratedBox控件会在子控件绘制**之前或之后**(我的理解是取决于是它包裹其他widget还是子widget把它作为一个属性传入)绘制一个装饰。  

	new DecoratedBox(
        decoration: new BoxDecoration(
          gradient: new LinearGradient(
            begin: const FractionalOffset(0.0, 0.0),
            end: const FractionalOffset(1.0, 1.0),
            colors: <Color>[const Color(0xffff2cc), const Color(0xffff6eb4)],
          )
        )  

#### 6.2 透明度  
Opacity控件能调整子控件的不透明度，使子控件部分透明，不透明度的量从0.0到1.1之间，0.0表示完全透明，1.1表示完全不透明。  

	new Opacity(
          opacity: 0.1,
          child: new Container(
            width: 250.0,
            height: 100.0,
            decoration: new BoxDecoration(
              backgroundColor: const Color(0xff000000),
            ),
          ),
        )  


### 7. 质感设计
#### 7.1 Input  
Input控件是质感设计的文本输入控件，它在用户**每次输入时都会调用onChanged回调**时，都会更新字段值，还可以实时的对用户输入进行响应,是Flutter中的EidtText+TextWatcher。  

	new Input(
            // value：文本输入字段的当前状态
            value: _phoneValue,
            // keyboardType：用于编辑文本的键盘类型
            keyboardType: TextInputType.number,
            // icon：在输入字段旁边显示的图标
            icon: new Icon(Icons.account_circle),
            // labelText：显示在输入字段上方的文本
            labelText: '手机',
            // hintText：要在输入字段中内嵌显示的文本
            hintText: '请输入手机号码',
            // onChanged：正在编辑的文本更改时调用
            onChanged: (InputValue value) {
              setState((){
                _phoneValue = value;
              });
            }
			// onSubmitted：当用户在键盘上点击完成编辑时调用
			onSubmitted: (InputValue value) {
              if(value.text.length<6){
                _showMessage('密码不少于6位');
              }
            }
          )  
	其中：value属性传入一个InputValue，该属性里面封装了Input的内容，所以在onChange中通过将value属性指定的InputValue重新赋值来实现内容的刷新，可以说用法很固定  


## 三、杂项  

### 3.1 Flutter中的动画  
在Flutter中，实现动画有两个工具类：**AnimationController和Animation**。前者用来**控制动画的时长、开始、结束**，后者有众多的**实现类**。  

文档中的淡入效果：  

	import 'package:flutter/material.dart';

	void main() {
  		runApp(new FadeAppTest());
	}

	class FadeAppTest extends StatelessWidget {
  		// This widget is the root of your application.
  		@override
  		Widget build(BuildContext context) {
    		return new MaterialApp(
      		title: 'Fade Demo',
      		theme: new ThemeData(
        		primarySwatch: Colors.blue,
      		),
      		home: new MyFadeTest(title: 'Fade Demo'),
    		);
  		}
	}

	class MyFadeTest extends StatefulWidget {
  		MyFadeTest({Key key, this.title}) : super(key: key);
  		final String title;
  		@override
  		_MyFadeTest createState() => new _MyFadeTest();
	}

	class _MyFadeTest extends State<MyFadeTest> with TickerProviderStateMixin {
  		AnimationController controller;
  		CurvedAnimation curve;

  		@override
  		void initState() {
    		controller = new AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
    		curve = new CurvedAnimation(parent: controller, curve: Curves.easeIn);
  		}

  		@override
  		Widget build(BuildContext context) {
    		return new Scaffold(
      		appBar: new AppBar(
        		title: new Text(widget.title),
      		),
      		body: new Center(
          		child: new Container(
              		child: new FadeTransition(
                  		opacity: curve,
                  		child: new FlutterLogo(
                   	 	size: 100.0,
                  		)))),
      		floatingActionButton: new FloatingActionButton(
        		tooltip: 'Fade',
        		child: new Icon(Icons.brush),
        		onPressed: () {
          		controller.forward();
        		},
      		),
    		);
  		}
	}  

### 3.2 自定义Widget  
Flutter中自定义widget一般是通过组合现有的不同widget来实现，不想Android中那样用继承来实现。   

但是Flutter中存在**CustomPaint和CustomPainter两个类**供我们来继承实现：  

	 class CustomPainterSample extends CustomPainter {

  		double progress;

  		CustomPainterSample({this.progress: 0.0});

  		@override
  		void paint(Canvas canvas, Size size) {
    		Paint p = new Paint();
    		p.color = Colors.green;
    		p.isAntiAlias = true;
    		p.style = PaintingStyle.fill;
    		canvas.drawCircle(size.center(const Offset(0.0, 0.0)), size.width / 2 * progress, p);
  		}

  		@override
  		bool shouldRepaint(CustomPainter oldDelegate) {
    		return true;
  		}

	}  

使用的时候做为参数传入**CustomPaint**:  

	Widget build(BuildContext context) {
    	return new Container(
      	color: Colors.white,
      	child: new CustomPaint(
        	painter: new CustomPainterSample(progress: this.progress),
      	)
    	);
  	}  

同样，也有相对应的**ClipXXX**对应的实现作为辅助:  

	Widget build(BuildContext context) {
    	return new Container(
      	color: Colors.white,
      	child: new Align(
        	alignment: Alignment.center,
        	child: new ClipRRect(
          	borderRadius: const BorderRadius.all(const Radius.circular(30.0)),
          	child: new Container(
            	width: 180.0,
            	height: 180.0,
            	color: Colors.red,
          	),
        	),
      	),
    	);
  	}  

效果图是：  
![](https://segmentfault.com/img/bVYgSs?w=1440&h=2560)

### 3.3 AsyncTask和IntentService在Flutter  
Dart是单线程执行模型，支持Isolates(在另一个线程上运行Dart代码的方式)、事件循环和异步编程。除非您启动一个Isolate，否则您的代码将在主UI线程中运行，并由事件循环驱动。  
由于Flutter是单线程的，运行一个事件循环（如Node.js），所以您不必担心线程管理或者使用AsyncTasks、IntentServices。

要异步运行代码，可以将函数声明为异步函数，并在该函数中等待这个耗时任务  

	loadData() async {
  		String dataURL = "https://jsonplaceholder.typicode.com/posts";
  		http.Response response = await http.get(dataURL);
  		setState(() {
    		widgets = JSON.decode(response.body);
  		});
	}  

## 四、其他  
### 4.1 手势  
在Flutter中，一些widget已经实现了点击事件，这时候给对应的属性传入一个函数即可，如onPressed属性；如果一个widget没有实现点击事件呢？那么就需要将该widget放到一个**GestureDetector**中，该**GestureDetector监视该区域**，并且可以处理对应的动作：    

	AnimationController controller;
	CurvedAnimation curve;

	@override
	void initState() {
  		controller = new AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
  		curve = new CurvedAnimation(parent: controller, curve: Curves.easeIn);
	}

	class SampleApp extends StatelessWidget {
  		@override
  		Widget build(BuildContext context) {
    		return new Scaffold(
        		body: new Center(
          		child: new GestureDetector(
            		child: new RotationTransition(
                		turns: curve,
                		child: new FlutterLogo(
                  		size: 200.0,
                		)),
            		onDoubleTap: () {
              		if (controller.isCompleted) {
                		controller.reverse();
              		} else {
                		controller.forward();
              		}
            		},
        		),
    		));
  		}
	}  

**GestureDetector**可以监视的动作有很多： 

* Tap  
	*  onTapDown 指针已经在特定位置与屏幕接触
	*  onTapUp 指针停止在特定位置与屏幕接触
	*  onTap tap事件触发
	*  onTapCancel 先前指针触发的onTapDown不会在触发tap事件
* 双击  
	* onDoubleTap 用户快速连续两次在同一位置轻敲屏幕
* 长按  
	* onLongPress 指针在相同位置长时间保持与屏幕接触
* 垂直拖动  
	* onVerticalDragStart 指针已经与屏幕接触并可能开始垂直移动
	* onVerticalDragUpdate 指针与屏幕接触并已沿垂直方向移动
	* onVerticalDragEnd 先前与屏幕接触并垂直移动的指针不再与屏幕接触，并且在停止接触屏幕时以特定速度移动
* 水平移动  
	* onHorizontalDragStart 指针已经接触到屏幕并可能开始水平移动
	* onHorizontalDragUpdate 指针与屏幕接触并已沿水平方向移动
	* onHorizontalDragEnd 先前与屏幕接触并水平移动的指针不再与屏幕接触，并在停止接触屏幕时以特定速度移动  

### 4.2 动画  
动画的原理：  

>AnimationController.forward()作为起点，内部经过animateTo、_startSimulation()、_tiker.start()、_scheduleTick()等一些列回调,Ticker将自己的_tick方法注册到了Scheduler中，一旦注册完，随着屏幕的刷新，_tick将会被不停的调用。**_tick方法中最后会调用notifyListeners()和_checkStatusChanged()方法。**       

## 五、资源  
### 5.1 介绍  
就像Android中的Resource一样，Flutter也可以包含资源(**assets**).**asset是打包到程序安装包中的，可在运行时访问。常见类型的asset包括静态数据（例如JSON文件）**，配置文件，图标和图片（JPEG，WebP，GIF，动画WebP / GIF，PNG，BMP和WBMP）。  

### 5.2 指定assets  
之前学习UI布局的时候，有用到一个图片的资源，需要在**pubspec.yaml**中声明：  

	flutter:  
		assets:  
			- images/lake.jpg  

这里是通过**assets**声明了资源，每个资源都通过**相对于pubspec.yaml文件所在位置的显式路径进行标识。**如果想要导入某一个目录下所有的资源，那么以/结尾即可：  

	flutter:
	  assets:
	    - assets/  

以上代码就表示导入assets目录下所有的资源

在构建期间，Flutter将asset放置到称为 **asset bundle** 的特殊存档中，应用程序可以在运行时读取它们。

#### Asset变体(variant)  

构建过程支持asset变体的概念：不同版本的asset可能会显示在不同的上下文中。 在pubspec.yaml的assets部分中指定asset路径时，构建过程中，会在**相邻子目录**中查找**具有相同名称的任何文件**。这些文件随后会与指定的asset**一起被包含在asset bundle中**。  

例如，如果您的应用程序目录中有以下文件:  

* …/pubspec.yaml
* …/graphics/my_icon.png
* …/graphics/background.png
* …/graphics/dark/background.png

然后您的pubspec.yaml文件包含:

	flutter:
  		assets:
    		- graphics/background.png  


那么这两个**graphics/background.png和graphics/dark/background.png 将包含在您的asset bundle中**。前者被认为是**_main asset_(主资源)**，后者被认为是一种**变体（variant）**。  

### 5.3 加载assets  
您的应用可以通过**AssetBundle对象**访问其asset 。

有两种主要方法允许从Asset bundle中加载字符串/text（loadString）或图片/二进制（load）。

这两种方式访问资源都需要提供一个key来映射到yaml中的路径。

#### 加载文本assets  
每个Flutter应用程序都有一个**rootBundle对象**， 可以轻松访问主资源包。可以直接使用**package:flutter/services.dart**中全局静态的**rootBundle**对象来加载asset。

但是，建议使用**DefaultAssetBundle**来获取当前BuildContext的**AssetBundle**。 这种方法不是使用应用程序构建的默认asset bundle(当前app构建时对应的AssetBundle)，而是使父级widget在运行时替换的不同的AssetBundle，这对于本地化或测试场景很有用。

通常，可以使用**DefaultAssetBundle.of()**从应用运行时间接加载asset（例如JSON文件）。

在Widget上下文之外，或AssetBundle的句柄不可用时，您可以使用**rootBundle**直接加载这些asset，例如：  

	import 'dart:async' show Future;
	import 'package:flutter/services.dart' show rootBundle;

	Future<String> loadAsset() async {
  		return await rootBundle.loadString('assets/config.json');
	}  

#### 加载图片资源  
要加载图片，请在widget的build方法中使用 **AssetImage类**。  

	Widget build(BuildContext context) {
  		// ...
  		return new DecoratedBox(
    		decoration: new BoxDecoration(
      		image: new DecorationImage(
        		image: new AssetImage('graphics/background.png'),
        		// ...
      		),
      		// ...
    		),
  		);
  		// ...
	}  

**依赖包中的资源图片：**  
要加载依赖包中的图像，必须给**AssetImage提供package参数**。  

例如，假设您的应用程序依赖于一个名为“my_icons”的包，它具有如下目录结构：  

* …/pubspec.yaml
* …/icons/heart.png
* …/icons/1.5x/heart.png
* …/icons/2.0x/heart.png

然后加载图像，使用:

	new AssetImage('icons/heart.png', package: 'my_icons')  

包使用的本身的资源也应该加上package参数来获取。  

#### 打包package assets  
如果在pubspec.yaml文件中声明了了期望的资源，它将会打包到响应的package中。特别是，包本身使用的资源必须在pubspec.yaml中指定。  

包也可以选择在其lib/文件夹中包含未在其pubspec.yaml文件中声明的资源。在这种情况下，对于要打包的图片，应用程序必须在pubspec.yaml中指定包含哪些图像。 例如，一个名为“fancy_backgrounds”的包，可能包含以下文件：  

* …/lib/backgrounds/background1.png
* …/lib/backgrounds/background2.png
* …/lib/backgrounds/background3.png

要包含第一张图像，必须在pubspec.yaml的assets部分中声明它：  

	flutter:
  		assets:
    		- packages/fancy_backgrounds/backgrounds/background1.png  

**lib/是隐含的，所以它不应该包含在资产路径中。**

>在**pubspec.yaml**中声明的资源会默认被打包到响应的package中，上面的方法是在lib目录下，**还没有在pubspec.yaml里声明的资源**，这时候如果想要在运行的时候添加该资源的话，就得按照上面的操作来。实际上都是因为资源在lib文件下

## 六、导航和路由  

在传统的Android中，可以通过intent来实现Activity之间的跳转，来达到切换界面的效果，接下来就看看在Flutter中是怎么实现的。  

管理多个用户界面有两个核心概念和类：**路由（Route）和导航器（Navigator）**，路由（Route）是应用程序的**“屏幕”**或**“页面”**的抽象，导航器（Navigator）是**管理路由的控件**。导航器（Navigator）可以推送（push）和弹出（pop）路由来帮助用户从当前屏幕移动到另一个屏幕。  

他们俩的关系有点像Animation和AnimationController的关系，一个是抽象，一个是控制。  

### 6.1 使用导航器  
移动应用通常通过称为“屏幕”或“页面”的全屏元素显示其内容，在Flutter中，这些元素称为路由，它们由导航器（Navigator）控件管理。**导航器管理一组路由（Route）对象，并提供了管理堆栈的方法，例如Navigator.push和Navigator.pop。**  

虽然您可以直接创建导航器，但最常见的是使用由**WidgetsApp**或**MaterialApp**控件创建的导航器，您可以使用**Navigator.of**引用该导航器。  

要在堆栈上推送（push）一个新路由，您可以**创建一个具有构建器功能的MaterialPageRoute实例，它可以创建您想要显示在屏幕上的任何内容。**

MaterialPageRoute是一种模态路由，可以通过平台自适应过渡来切换屏幕。  

	Navigator.of(context).push(new MaterialPageRoute<Null>(
        builder: (BuildContext context) {
          return new Scaffold(
            appBar: new AppBar(
              title: new Text('新的页面')
            ),
            body: new Center(
              child: new Text(
                '点击浮动按钮返回首页',
              ),
            ),
            floatingActionButton: new FloatingActionButton(
              onPressed: () {
                Navigator.of(context).pop();
              },
              child: new Icon(Icons.replay),
            ),
          );
        },
      )  

注意这里实现的用法：  

* 首先通过Navigator.of(context)方法获取导航器对象  
* 然后通过push方法将一个Route压栈  
* 在MaterialPageRoute中传入的参数是一个builder(ps:此builder是一个**构建器(WidgetBuilder)**，不过看例子中传入的像是一个Widget的build方法)  

上面这种方式启动新的页面像是安卓中的Intent显式启动Activity  

### 6.2 使用命名导航器路由  

上面用的是导航器的push方法切换界面的，导航器还有一个**pushName(String)**通过名字来管理路由：  

	import 'package:flutter/material.dart';

	void main() {
  		runApp(new MyApp());
	}

	class MyApp extends StatelessWidget {
  		@override
  		Widget build(BuildContext context) {
    		return new MaterialApp(
      		title: 'Flutter Demo',
      		home: new MyHomePage(title: '应用程序首页'),
      		routes: <String, WidgetBuilder> {
        		'/a': (BuildContext context) => new MyPage(title: 'A 页面'),
        		'/b': (BuildContext context) => new MyPage(title: 'B 页面'),
        		'/c': (BuildContext context) => new MyPage(title: 'C 页面')
      		},
    		);
  		}
	}

	class MyHomePage extends StatefulWidget {
  		MyHomePage({Key key, this.title}) : super(key: key);

  		final String title;

  		@override
  		_MyHomePageState createState() => new _MyHomePageState();
	}

	class _MyHomePageState extends State<MyHomePage> {
  		@override
  		Widget build(BuildContext context) {
    		return new Scaffold(
      		appBar: new AppBar(
        		title: new Text(widget.title),
      		),
      		body: new Row(
        		children: <Widget>[
          		new RaisedButton(
            		child: new Text('A按钮'),
            		onPressed: () { Navigator.of(context).pushNamed('/a'); },
          		),
          		new RaisedButton(
            		child: new Text('B按钮'),
            		onPressed: () { Navigator.of(context).pushNamed('/b'); },
          		),
          		new RaisedButton(
            		child: new Text('C按钮'),
            		onPressed: () { Navigator.of(context).pushNamed('/c'); },
          		)
        		]
      		)
    		);
  		}
	}

	class MyPage extends StatelessWidget {
  		MyPage({Key key, this.title}) : super(key: key);
  		final String title;

  		Widget build(BuildContext context) {
    		return new Scaffold(
      		appBar: new AppBar(
        		title: new Text(title)
      		),
    		);
  		}
	}  

**Map<String, WidgetBuilder> routes**是应用程序的顶级路由表,当使用**Navigator.pushNamed**推送命名路由时，将在此映射中查找路由名称。如果名称存在，则相关联的**WidgetBuilder**将用于构造一个**MaterialPageRoute**，该新的路由执行适当的过渡。

如果应用程序只有一个页面，那么可以使用**home**指定它，如果指定了**home**，那么在此映射中为**Navigator.defaultRouteName**提供路由是一个错误。如果请求没有在此表中指定的路由（或通过**home**），则会调用**onGenerateRoute**回调来构建页面。  

### 6.3 弹出路由  

路由不一定需要掩盖整个屏幕，**PopupRoutes覆盖屏幕，屏幕颜色只能部分不透明，以允许当前屏幕显示**。弹出路由是模态的，因为它们阻止了下面的控件的输入。

框架有创建和显示弹出路由的功能，例如：**showDialog、showMenu和showModalBottomSheet**。这些功能如上所述返回其推送的路由的**Future**，调用时可以等待返回的值在路由弹出时采取行动，或者发现路由的值。

还有一些创建弹出路由的控件，如**PopupMenuButton和DropdownButton**。这些控件创建PopupRoute的内部子类，并使用路由的push和pop方法来显示和关闭它们。

	Future<Null> _openNewPage() async {
    bool value = await showDialog(
      context: context,
      barrierDismissible: true,
      child: new Center(
        child: new GestureDetector(
          child: new Text("确定"),
          onTap: () { Navigator.of(context).pop(true); },
        ),
      )
    );  

看到，无非就是在**showDialog**方法里去实现布局。  

### 6.4 自定义路由  

您可以创建自己的控件库路由类，比如**PopupRoute、ModalRoute或PageRoute的子类**，以控制用于显示路由，包括路由模态屏障的颜色和行为以及路由其他方面的动画转换。

**PageRouteBuilder类**可以根据回调来**定义自定义路由**，下面是一个示例，当路由出现或消失时，它会旋转和淡化其子对象。此路由不会遮蔽整个屏幕，因为它指定了**opaque: false**，就像弹出路由一样。  

	void _openNewPage() {
    Navigator.of(context).push(new PageRouteBuilder(
      opaque: false,
      pageBuilder: (BuildContext context, _, __) {
        return new Center(child: new Text('定制页面路由'));
      },
      transitionsBuilder: (_, Animation<double> animation, __, Widget child) {
        return new FadeTransition(
          opacity: animation,
          child: new RotationTransition(
            turns: new Tween<double>(begin: 0.5, end: 1.0).animate(animation),
            child: child,
          ),
        );
      }
    ));
  	}

## 七、Cookbook  

### 7.1 显示图片  

 Flutter提供了**Image** Widget来显示不同类型的图片。  
Flutter中的Image一个强大的功能是能够显示**网络图片，而且支持gif动态图。**  

	new Image.network(
  	'https://raw.githubusercontent.com/flutter/website/master/_includes/code/layout/lakes/images/lake.jpg',
	)  

**用占位符淡入图片：**  
当使用默认**Image** widget显示图片时，您可能会注意到它们在加载完成后会直接显示到屏幕上。这可能会让用户产生视觉突兀。

相反，如果你最初显示一个占位符，然后在图像加载完显示时淡入，那么它会不会更好？我们可以使用**FadeInImage**来达到这个目的！

**FadeInImage**适用于任何类型的图片：内存、本地Asset或来自网上的图片。  

	new FadeInImage.memoryNetwork(
  		placeholder: kTransparentImage,
  		image: 'https://github.com/flutter/website/blob/master/_includes/code/layout/lakes/images/lake.jpg?raw=true',
	);  

**使用缓存图片：**  
在某些情况下，在从网上下载图片后缓存图片可能会很方便，以便它们可以脱机使用。为此，我们可以使用**cached_network_image包**来达到目的。

除了缓存之外，cached_image_network包在加载时还支持**占位符和淡入淡出图片**！  

	new CachedNetworkImage(
  		placeholder: new CircularProgressIndicator(),
  		imageUrl: 'https://github.com/flutter/website/blob/master/_includes/code/layout/lakes/images/lake.jpg?raw=true',
	);  

### 7.2 List列表组件  

在之前的实例中有用到过ListView这个列表组件，用法很简单，在官方文档中说道**标准的ListView构造函数适用于数据少的时候**，当数据量大的时候，使用**ListView.builder构造函数** ，**而且默认构造函数不会自动检测其children的突然变化** 

**ListView的构造函数需要一次创建所有项目，但ListView.builder的构造函数不需要，它将在列表项滚动到屏幕上时创建该列表项**  

**1.首先创建一个数据源**  
**2.将数据源转换为Widgets**  
这里正是**ListView.builder**发挥作用的地方   

	new ListView.builder(
  		itemCount: items.length,
  		itemBuilder: (context, index) {
    		return new ListTile(
      		title: new Text('${items[index]}'),
    		);
  		},
	)  

注意到itemBuilder属性传入的值:和我们之前遇到的**Route(路由)**是一样的，都是一个**构建器**，类似于build函数的实现。  

### 7.3 滑动删除  
Flutter通过提供**Dismissable** Widget 使这项任务变得简单。  
实现：  

1. 创建item列表  
2. 将每个item**包装在一个Dismissable中**  
3. 提供滑动背景提示  

重点是第二和第三步  

**将item包装到Dismissible中**  

	new Dismissible(
  		// Each Dismissible must contain a Key. Keys allow Flutter to
  		// uniquely identify Widgets.
  		key: new Key(item),
  		// We also need to provide a function that will tell our app
  		// what to do after an item has been swiped away.
  		onDismissed: (direction) {
    		// Remove the item from our data source
    		items.removeAt(index);

    		// Show a snackbar! This snackbar could also contain "Undo" actions.
    		Scaffold.of(context).showSnackBar(
        		new SnackBar(content: new Text("$item dismissed")));
  		},
  		child: new ListTile(title: new Text('$item')),
	);  

注意：**在onDismissed中去实现真正的数据删除**

可以通过**background属性**为Dismissible提供一个背景

## 八、教程项目跟进学习  
>从这里开始就记一记根据[https://blog.csdn.net/column/details/13593.html?&page=2](https://blog.csdn.net/column/details/13593.html?&page=2) 中的聊天引用练习的时候学习的一些Flutter的知识   

### 8.1 TextField  
我们之前显示文本用的都是**Text**这个**StatelessWidget**。这就大大局限了我们的操作，没法和用户交互就只能显示文本而已；而Flutter的MatrialWidget为我们提供了一个**TextField**，这是一个**StatefulWidget**。

来看看我们在项目中是如何使用它的：  

	new TextField(
                  controller: _textcontroller,
                  decoration: new InputDecoration.collapsed(hintText: "发送消息"),
                  onSubmitted:_handleSubmitted,
                ),  

看这个构造方法：  

* **controller**：指定一个**TextEditingController**实例(通过这个Controller我们可以获取该Controller绑定的TextField的文本内容，进行想要的操作，如清空等)  
* **decoration**：不知道具体干啥的，但是这里显然是设置了类似于Android的hint属性(原来在Flutter中hint作为一种Decoration)  
* **onSubmitted**：就像IconButton的onPressed属性一样，这是它与用户进行交互的接口(该参数对应的方法的形参必须为一个String)  
* **onChange**：监听文本的改变,要求同onSubmitted一样

这个控件的效果就是：实现了一个Android中的**EditText**的效果和功能  

### 8.2 IconTheme  
在该项目中用到了一个IconButton，在为其指定Icon的时候，Icon都是默认的黑色，想要改变Icon的颜色，就需要使用**IconTheme**。  

**IconTheme**的作用就是其**包含的所有Widget的图标的透明度、颜色和大小都会组从IconTheme的设置**，而IconTheme使用**IconThemeData**来定义这些特征。  

> **Theme.of(context)**用来获取当前的**ThemeData**。该实例中封装了IconTheme、TextTheme等风格信息。

使用：  

	Widget _buildTextComposer() {
    return new IconTheme(
      data: new IconThemeData(color: Theme.of(context).accentColor),
      child: 
			...
      )
    );
  	}  

使用前：  

![](https://img-blog.csdn.net/20170605195932788?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVrYWl5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用后：

![](https://img-blog.csdn.net/20170605195222089?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVrYWl5b3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  

### 8.3 CircleAvatar  
该控件可以将包含的widget修饰成一个圆形，可以实现类似于头像框的效果  

	child: new CircleAvatar(child: new Text(_name[0])),  

### 8.4 Divider  
该Widget作用就是添加一个**分隔条**  

	new Divider(height: 1.0)  

### 8.5 ListView  
我们可以通过**ListView.Builder**对象来创建一个ListView，**ListView.Builder**的参数是：

*  padding对于消息文本周围的空白 
*  reverse使ListView从屏幕底部开始：属性是boolean
*  itemCount指定列表中的消息数
*  
itemBuilder对于在[index]中构建每个窗口控件的函数。因为我们不需要当前的构建上下文，所以我们可以忽略IndexedWidgetBuilder的第一个参数。命名参数_（下划线）是一个表示不会被使用的约定。  

实例： 

	child: new ListView.builder(
                  padding: new EdgeInsets.all(8.0),
                  reverse: true,
                  itemBuilder: (_, int index) => _messages[index],
                  itemCount: _messages.length,
                )
 
### 8.6 自定义主题  
可以在MatrialApp的theme属性中传递一个ThemeData对象来实现自定义主题，自定义主题的时候变量都是有特定的名称的，可查阅相关文档了解：  

	final ThemeData kDefaultTheme = new ThemeData(
  		primarySwatch: Colors.purple,
  		accentColor: Colors.orangeAccent[400],
	);  
	return new MaterialApp(
      title: "谈天说地",
      theme: kDefaultTheme,
      home: new ChatScreen()
    );  

## 九、状态管理  
Flutter中对于状态的管理的做法非常好，避免了对象的乱传造成的bug或者内存泄漏等问题。  

想想我们在原生的Android中是如何实现记事本功能的：写完内容之后点击确认按钮，实际上是之前传过来RecyclerView的Adapter对象，然后调用该对象的回调，填写内容的界面和展示内容的界面是两个不同的界面，这样将对象传来传去势必会导致很多问题。对此我们并没有好的解决办法(或许依赖出入可以优化)。  

Flutter就可以很优雅的解决这个问题：这一切都要依靠**scoped_model**这个包中的三个类：**Model、ScopedModel、ScopedModelDescendant**。  

### 9.1 Model  
Model类的作用是**封装状态(State)**的，目的是统一管理、多个Widget共享该类中封装的状态；多个Widget共同响应状态的改变。  

实例就按照官方文档中的例子来说：  

	class CartModel extends Model {
	  /// Internal, private state of the cart.
	  final List<Item> _items = [];
	
	  /// An unmodifiable view of the items in the cart.
	  UnmodifiableListView<Item> get items => UnmodifiableListView(_items);
	
	  /// The current total price of all items (assuming all items cost $1).
	  int get totalPrice => _items.length;
	
	  /// Adds [item] to cart. This is the only way to modify the cart from outside.
	  void add(Item item) {
	    _items.add(item);
	    // This call tells [Model] that it should rebuild the widgets that
	    // depend on it.
	    notifyListeners();
	  }
	}  

> **注意：当状态变量发生改变后，一定要调用notifyListeners()这个方法，该方法的作用就是通知所有使用了该Model中的状态的Widget进行rebuild(也就是后面要说的ScopedModelDescendant中的builder参数)**  

### 9.2 ScopedModel  
该类是一个**StatelessWidget**，其作用就是将Model和其child绑定起来，这样一来其child就可以使用Model中封装的状态来展示自己，同时也会根据状态的改变响应Model中的notifyListeners方法进行rebuild：  

	void main() {
	  final cart = CartModel();
	
	  // You could optionally connect [cart] with some database here.
	
	  runApp(
	    ScopedModel<CartModel>(
	      model: cart,
	      child: MyApp(),
	    ),
	  );
	}  

> **如果一个App很复杂，需要多个Model的话，可以通过ScopedModel的嵌套来实现，即外层ScopedModel的child是另一个ScopedModel**  

### 9.3 ScopedModelDescendant  
Model的作用是封装状态，ScopedModel的作用是将状态绑定给child，那么child如何使用这个Model呢？其中的一种方法就是使用ScopedModelDescendant，**而ScopedModelDescendant也是一个Widget**：  

	return ScopedModelDescendant<CartModel>(
	  builder: (context, child, cart) => Stack(
	        children: [
	          // Use SomeExpensiveWidget here, without rebuilding every time.
	          child,
	          Text("Total price: ${cart.totalPrice}"),
	        ],
	      ),
	  // Build the expensive widget here.
	  child: SomeExpensiveWidget(),
	);  

介绍一下builder的三个参数：第一个参数就是上下文对象，第二个参数是child其实就是ScopedModelDescendant的第二个参数child，就是没有用到Model中状态的Widget，我们可以把他们创建到ScopedModelDescendant的child参数中，这样一来在Model中的状态发生改变的时候builder就可以直接通过其第二个参数得到这些不会因状态的改变而发生变化的部分布局直接使用；第三个参数就是封装装态的Model了  

## 十、JSON  
现在的App基本都要与网络打交道，JSON就是很好的传输数据的格式。  

Flutter中实现JSON有两种大的方面：  

* Use manual serialization for smaller projects  
* Use code generation for medium to large projects  

### 10.1 manual serialization  
该中方式是使用Flutter中内置的**dart：convert**包来实现序列化和反序列化的  

手动序列化也有两种形式：Serializing JSON inline和Serializing JSON inside model classes  

#### 10.1.1  Serializing JSON inline  
这种形式进行JsON是使用**dart：convert**包下的**Map<String,dynamic>  jsonDecode(String jsonString)**来讲Json字符串解析出来  

	Map<String, dynamic> user = jsonDecode(jsonString);

	print('Howdy, ${user['name']}!');
	print('We sent the verification link to ${user['email']}.');  

该方法用来测试JSON还好，在使用的时候有一个重大的问题就是，生成的**Map<String,dynamic>中的value类型是未知的**，也就**丧失了：类型安全等一些列优点**  

#### 10.1.2 Serializing JSON inside model classes  
看名字就可以猜到这是类似于原生中的讲JSON解析成一个JavaBean的形式：  

	class User {
	  final String name;
	  final String email;
	
	  User(this.name, this.email);
	
	  User.fromJson(Map<String, dynamic> json)
	      : name = json['name'],
	        email = json['email'];
	
	  Map<String, dynamic> toJson() =>
	    {
	      'name': name,
	      'email': email,
	    };
	}

	Map userMap = jsonDecode(jsonString);
	var user = new User.fromJson(userMap);
	
	print('Howdy, ${user.name}!');
	print('We sent the verification link to ${user.email}.');  

这样就解决了第一种方法中map的value类型未知的问题，在class model中对应字段的类型都已经定义了，也就是说如果json中name传递的是一个int，就会发生json解析异常  

另外将User解析成JSON格式也非常简单：  

	String json = jsonEncode(user);  
  
并没有用到User中的toJson()方法  

该中方法实现JSON最大的毛病就是需要手动维护，可能会出现bug  

### 10.2 code generation  
该方法相较于之前的优点是**自动生成**，该方法使用的是**json_serializable**包  

**pubspec.yaml**

	dependencies:
	  # Your other regular dependencies here
	  json_annotation: ^2.0.0
	
	dev_dependencies:
	  # Your other dev_dependencies here
	  build_runner: ^1.0.0
	  json_serializable: ^2.0.0  

使用起来也非常简单：  

	import 'package:json_annotation/json_annotation.dart';

	/// This allows the `User` class to access private members in
	/// the generated file. The value for this is *.g.dart, where
	/// the star denotes the source file name.
	part 'user.g.dart';
	
	/// An annotation for the code generator to know that this class needs the
	/// JSON serialization logic to be generated.
	@JsonSerializable()
	
	class User {
	  User(this.name, this.email);
	
	  String name;
	  String email;
	
	  /// A necessary factory constructor for creating a new User instance
	  /// from a map. Pass the map to the generated `_$UserFromJson()` constructor.
	  /// The constructor is named after the source class, in this case User.
	  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
	
	  /// `toJson` is the convention for a class to declare support for serialization
	  /// to JSON. The implementation simply calls the private, generated
	  /// helper method `_$UserToJson`.
	  Map<String, dynamic> toJson() => _$UserToJson(this);
	}  

还可以将一个JSON的字段**重命名**：  

	/// Tell json_serializable that "registration_date_millis" should be
	/// mapped to this property.
	@JsonKey(name: 'registration_date_millis')
	final int registrationDateMillis;  

解析的时候和上一个方法是一样的：  

	Map userMap = jsonDecode(jsonString);
	var user = User.fromJson(userMap);  

## 十一、Flutter中的动画  

看了官网的教程，依然很迷茫，再看着中文网的翻译，有了点简单的理解  

### 1. Animation  

Animation这个类是动画的核心类，但是Animation并不直接去参与到UI动画的构建中去，**Animation中保存了状态(State)和值(value)**，我们可以获取Animation的值来**指导UI的构建或者变化**，这就形成了我们看到的动画效果。  

	abstract class Animation<T> extends Listenable implements ValueListenable<T> {
	 2  const Animation();
	 3
	 4  @override
	 5  void addListener(VoidCallback listener);
	 6
	 7  @override
	 8  void removeListener(VoidCallback listener);
	 9
	10  void addStatusListener(AnimationStatusListener listener);
	11
	12  void removeStatusListener(AnimationStatusListener listener);
	13
	14  AnimationStatus get status;
	15
	16  @override
	17  T get value;
	18
	19  bool get isDismissed => status == AnimationStatus.dismissed;
	20
	21  bool get isCompleted => status == AnimationStatus.completed;
	22}     

值得注意的是**外部的对象只能够读State和value，并不能修改他们，因为只提供了getter**。  

State是一个num类，其值有四个：  

* **dismiss：**代表动画结束了，而且当前的帧停留在了动画过程中的第一个帧  
* **complete**：同样代表动画结束，但是当前的帧停留在了动画过程中的最后一个帧  
* **forward**：代表动画正在进行，而且是从starting到ending的中间某一个  
* **reverse**：代表动画正在进行，但是是反方向运行  



#### addListener  

Animation中这个值的变化是指导UI动画效果实现的重点，但是当动画开始的时候，我们如何知道Animation的value发生了变化呢？答案就是调用addListener，传入一个无参数的普通Function，在这个Function中去任意的做事情，通常我们是在这个Function中调用setState方法来通知Widget重新绘制的  

#### addStateListener  

Animation也有自己的状态，状态是指动画是否开始、是否已经执行完毕等各种状态；其用法同addListener一样，通过设置这个监听器，我们可以实现在不同的状态下对动画进行一些改变，比如当动画执行完时让其再次执行，这就实现了一种循环动画的效果 

### 2. AnimationController  

上面说到Animation中保存的值，可以有很多类型：double、int、Color等，因此Animation有很多子类；而**AnimationController就是一个Animation<double>类型，其值的范围是0.0-1.0**，尽管如此，AnimationController的作用是**控制动画的持续时间、执行状态**，为了实现这些功能，AnimationController有很多方法：**forward()、stop()等** 。而如果AnimationController想要改变区间、不再是0.0-1.0的话，需要使用Tween来实现。  

	AnimationController({
	    double value,
	    this.duration,
	    this.debugLabel,
	    this.lowerBound: 0.0,
	    this.upperBound: 1.0,
	    @required TickerProvider vsync,
	  })  

**duration就代表着动画的执行时间**，而AnimationController的value被定义为了double，这也就印证了上面；**lowerBound和upperBound分别代表了initial value和final value**

### 3. Tween  

**Tween为补间动画,即通过给定初始和末尾的状态，由系统自行计算从初始到末尾中间值的变化**，意思就是Tween通过插值的形式，来将动画的value弄为特定的对象的变化。其是**继承自Animatable<T>**,而**不是Animation<T>.**    

	Tween _tween = new AlignmentTween(
     begin: new Alignment(-1.0, 0.0),
     end: new Alignment(1.0, 0.0),
    );  
    _animation = _tween.animate(_controller);
	_controller.forward();

上面就可以将Animation中value的变化映射为Alignment的变化。

同样，Tween也有很多子类，ColorTween的begin和end必须是两个颜色，IntTween则要求是两个int等等。    

但是我们之前说过，**指导UI构建动画效果的是Animation的value属性**，Tween是Animatable的子类，这该如何是好？Tween的**animate方法**，通过传入的**Animation<double>**参数，**返回一个Animation对象**，这样就可以通过这个返回的Animation对象的value属性来指导UI的动画效果的实现过程了。  

### 4. Curve  

**Animation的value的类型和区间都是可选的**，这里以IntTween举例，AnimationController规定了Animation的value在一个Duration时间内，完成从IntTween的begin到end的变化过程，但是又有一个变量空了出来，这个变化过程该如何进行？全程匀速？还是忽快忽慢？这就涉及到一个**映射的关系**了。  

**Curve是Flutter中提供的一种非线性的曲线型映射关系**，Curves下有很多内置的曲线映射，我们也可以自己实现一个类继承Curve，**重写它的transform方法来定制自己的曲线**    

在使用时我们通过**CurvedAnimation**来将一个Curve和Animation<double>绑定到一起，对没错，**CurvedAnimation的作用就是将一个AnimationController和一个Curve绑定到一起，返回的是一个Animation<double>**  

#### Demo  

	class MyApp extends StatefulWidget {

	  @override
	  State<StatefulWidget> createState() {
	    // TODO: implement createState
	    return _AnimationTestState();
	  }
	}
	
	class _AnimationTestState extends State<MyApp> with SingleTickerProviderStateMixin{
	
	  AnimationController controller;
	  Animation<double>  animation;
	  CurvedAnimation curve;
	
	  @override
	  void initState() {
	    // TODO: implement initState
	    super.initState();
	    controller = AnimationController(vsync: this, duration: Duration(seconds: 10));
	    var tween = Tween(begin: 0.0, end: 10.0);
	    curve = CurvedAnimation(parent: controller, curve: Curves.elasticIn);
	    animation = tween.animate(curve);
	    animation.addListener((){
	      setState(() {
	
	      });
	    });
	    controller.forward();
	  }
	
	  @override
	  Widget build(BuildContext context) {
	    return Center(
	      child: Container(
	        margin: EdgeInsets.all(20),
	        child: Text("${animation.value}"),
	      ),
	    );
	  }
	}  

以上代码就可以看到Text中的内容是：*区间为0.0-10.0的数，按照Curves.elasticIn的映射规则在10秒从0.0增加到10.0*  








	