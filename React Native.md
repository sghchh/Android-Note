# JavsScript基础  
## 一、基础语法  
### 1. null和undefined  
两者的区别很简单，前者是定义了没有赋值，后者是压根没定义过。比如var a；后面如果直接访问a的话会显示a为null，而如果访问b的话，会提示undefined  

### 2. 数组  
JavaScript里的数组很灵活，它的数组变量默认有一个**length**属性，关键是手动去改这个length的值，数组的大小真的会发生改变！  
	
	var arr=[1,2,3,4};
	arr.length=5  //arr变为了[1,2,3,4,undefined]  

这种情况同样适用于为越界的数组索引元素赋值：  

	arr[5]=0; // 数组变为了[1,2,3,4,undefined,0]  
因此，在JS中访问或者遍历数组的时候一定要保证不会越界  

### 3. 函数  
在JavaScript中函数也是一个对象，所以定义一个函数就有两种形式：  

	function abs(x) {
	    if (x >= 0) {
	        return x;
	    } else {
	        return -x;
	    }
	}  

	var abs=function(x){
		if (x >= 0) {
	        return x;
	    } else {
	        return -x;
	    }
	};

奇特的是，JS中调用函数的时候参数传入多少个都没有问题：如果传入的参数多于定义时的参数，那么多的部分会自动忽略；如果少于的话则补为undefined。  

#### argument  
JavaScript还有一个免费赠送的关键字arguments，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。arguments类似Array但它不是一个Array：  

	function foo(x) {
	    console.log('x = ' + x); // 10
	    for (var i=0; i<arguments.length; i++) {
	        console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
	    }
	}
	foo(10, 20, 30);
 
#### rest参数  
由于JavaScript函数允许接收任意个参数，于是我们就不得不用arguments来获取所有参数：  

	function foo(a, b) {
	    var i, rest = [];
	    if (arguments.length > 2) {
	        for (i = 2; i<arguments.length; i++) {
	            rest.push(arguments[i]);
	        }
	    }
	    console.log('a = ' + a);
	    console.log('b = ' + b);
	    console.log(rest);
	}  

为了获取除了已定义参数a、b之外的参数，我们不得不用arguments，并且循环要从索引2开始以便排除前两个参数，这种写法很别扭，只是为了获得额外的rest参数，ES6标准引入了rest参数，上面的函数可以改写为：  
	
	function foo(a, b, ...rest) {
	    console.log('a = ' + a);
	    console.log('b = ' + b);
	    console.log(rest);
	}
	
	foo(1, 2, 3, 4, 5);
	// 结果:
	// a = 1
	// b = 2
	// Array [ 3, 4, 5 ]
	
	foo(1);
	// 结果:
	// a = 1
	// b = undefined
	// Array []

rest参数只能写在最后，前面用...标识，从运行结果可知，传入的参数先绑定a、b，多余的参数以数组形式交给变量rest，所以，不再需要arguments我们就获取了全部参数。  
如果传入的参数连正常定义的参数都没填满，也不要紧，**rest参数会接收一个空数组（注意不是undefined）**

### 4. 方法  
绑定到一个对象上的函数就叫做方法：  

	var xiaoming = {
	    name: '小明',
	    birth: 1990,
	    age: function () {
	        var y = new Date().getFullYear();
	        return y - this.birth;
	    }
	};

在JS中，方法里面用到了**this**时，这个this到底指向谁要视情况而定：**如果方法是以对象的形式调用的比如xiaoming.age()那么this就执行这个对象；如果是单独的调用函数（是函数不是方法）那么该this指向全局对象window**。而且：  

	var xiaoming = {
	    name: '小明',
	    birth: 1990,
	    age: function () {
	        function getAgeFromBirth() {
	            var y = new Date().getFullYear();
	            return y - this.birth;
	        }
	        return getAgeFromBirth();
	    }
	};  

	xiaoming.age(); // Uncaught TypeError: Cannot read property 'birth' of undefined

方法里面的嵌套的方法里面的this就不再是指向该对象了（xiaoming），又指向了window，我们可以做出如下改动：  

	var xiaoming = {
	    name: '小明',
	    birth: 1990,
	    age: function () {
	        var that = this; // 在方法内部一开始就捕获this
	        function getAgeFromBirth() {
	            var y = new Date().getFullYear();
	            return y - that.birth; // 用that而不是this
	        }
	        return getAgeFromBirth();
	    }
	};
	
	xiaoming.age(); // 25  

#### 控制this指向  
为了解决上面提到的this指向的问题，JS提供了两个装饰器：**apply()和call()**  

apply方法，它接收两个参数，第一个参数就是需要绑定的this变量，第二个参数是Array，表示函数本身的参数。  

	function getAge() {
	    var y = new Date().getFullYear();
	    return y - this.birth;
	}
	
	var xiaoming = {
	    name: '小明',
	    birth: 1990,
	};

	getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空  

可以看到，getAge方法成功的通过this访问到了birth属性。  

另一个与apply()类似的方法是call()，唯一区别是： 

* apply()把参数打包成Array再传入；  
* call()把参数按顺序传入  

比如调用Math.max(3, 5, 4)，分别用apply()和call()实现如下：  

	Math.max.apply(null, [3, 5, 4]); // 5
	Math.max.call(null, 3, 5, 4); // 5  

### 5. 闭包  
所谓闭包，就是一个函数返回的是另一个函数，这就导致了首先访问的那个函数中的一些变量保存到了返回的那个函数中，而这些变量我们是没法访问的；同样，可以把闭包看做是某些变量的存放处，例如：  

	function create_counter(initial) {
	    var x = initial || 0;
	    return {
	        inc: function () {
	            x += 1;
	            return x;
	        }
	    }
	}

	var c2 = create_counter(10);
	c2.inc(); // 11
	c2.inc(); // 12
	c2.inc(); // 13
  
**“创建一个匿名函数并立刻执行”的语法是需要用括号把整个函数定义括起来：**  

	(function (x) {
	    return x * x;
	})(3);
  
### 6， 创建对象  
在JS中除了直接的使用类似于JSON的键值对来创建一个对象以外，也可以像大多数的语言一样通过new关键字，来创建一个对象：  

	function Student(name) {
	    this.name = name;
	    this.hello = function () {
	        alert('Hello, ' + this.name + '!');
	    }
	}  

	var xiaoming=new Student("xiaoming");  

在JS中就是通过一个函数来实现的，只不过在调用这个函数的时候前面要加上**new关键字**，这时候普通函数就变成了一个构造函数，**它绑定的this指向新创建的对象，并默认返回this**  
上面那种形式创建对象有一个问题就是，所有的对象他们的属性都是私有的，如果想要一个属性是他们共有的怎么办：

	function Student(name) {
	    this.name = name;
	}
	
	Student.prototype.hello = function () {
	    alert('Hello, ' + this.name + '!');
	};  

上面这样的修改的原理是什么呢？我们来认识一下**原型链**  

#### 原型链  
**JavaScript对每个创建的对象都会设置一个原型，指向它的原型对象。**  
当我们用obj.xxx访问一个对象的属性时，JavaScript引擎先在当前对象上查找该属性，如果没有找到，就到其原型对象上找，如果还没有找到，就一直上溯到Object.prototype对象，最后，如果还没有找到，就只能返回undefined。  

例如，创建一个Array对象，其原型链是：  

	arr ----> Array.prototype ----> Object.prototype ----> null  

Array.prototype定义了indexOf()、shift()等方法，因此你可以在所有的Array对象上直接调用这些方法。  
而上面我们实例对应的原型链是：  

	xiaoming ----> Student.prototype ----> Object.prototype ----> null  

因此我们之前做的修改只是把hello这个属性移到了对象的原型对象上。  

### 7. class  
为了创建和定义类型更加简单，更重要的是解决原型继承的繁琐，JS引入了class来定义类：  

	class Student {
	    constructor(name) {
	        this.name = name;
	    }
	
	    hello() {
	        alert('Hello, ' + this.name + '!');
	    }
	}  

	var xiaoming = new Student('小明');
	xiaoming.hello();

继承也变得简单了：  

	class PrimaryStudent extends Student {
	    constructor(name, grade) {
	        super(name); // 记得用super调用父类的构造方法!
	        this.grade = grade;
	    }
	
	    myGrade() {
	        alert('I am at grade ' + this.grade);
	    }
	}

# React Native学习  
## 一、基础使用  
### 1. 自定义样式  
就像传统的Android一样，React Native中的样式也是通过style来实现的，所有的“控件”元素都支持**style属性**：

	export default class LotsOfStyles extends Component {
	  render() {
	    return (
	      <View>
	        <Text style={styles.red}>just red</Text>
	        <Text style={styles.bigblue}>just bigblue</Text>
	        <Text style={[styles.bigblue, styles.red]}>bigblue, then red</Text>
	        <Text style={[styles.red, styles.bigblue]}>red, then bigblue</Text>
	      </View>
	    );
	  }
	}
	
	const styles = StyleSheet.create({
	  bigblue: {
	    color: 'blue',
	    fontWeight: 'bold',
	    fontSize: 30,
	  },
	  red: {
	    color: 'red',
	  },
	});  

就像传统的Android中将所有的style都写在一个xml文件中一样，RN也推荐通过StyleSheet、.create的形式集中的定义组件的形式。  

### 2. 布局  
RN中使用**FlexBox规则**来进行布局：一般来说使用**flexDirection、alignItems、justifyContent、flexWrap四个样式属性**就可以满足大多数情况了。    

#### 2.1 flexDirection  
这个属性是控制子控件的**主轴（排列方向）**的，有**row（水平）**和**Column（数值）**两个值，默认为Column  

#### 2.2 justifyContent  
这个属性控制的是子控件沿着主轴的排列方式的，有：**flex-start、center、flex-end、space-around、space-between以及space-evenly**  

![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)
#### 2.3 alignItems  
这个属性控制的是子元素沿着**次轴（与主轴垂直的那个方向）**方向上的排列方式，可选项有：**flex-start、center、flex-end和stretch**

![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)
> **注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸。**    

#### 2.4 flexWrap  
这个属性用来决定某一方向上如果控件内容超出了屏幕的尺寸是否自动换行：**‘nowrap(默认)、‘wrap’、‘wrap-reverse’**  

#### 2.5 alignSelf  
前四个属性都是**控制子控件在父控件中的位置，作用于所有的子控件**，而alignSelf属性值作用于自己，用来**控制自己在其父容器中（次轴方向上）的位置：'auto','flex-start','flex-end','center','stretch'**  

#### 2.6 绝对布局  
绝对布局由**top、left、width、height**一起控制
  
### 3. 属性  

* borderWidth:设置控件边框的宽度  
* borderRadius:设置边框四角的弧度
* borderColor:设置边框颜色

### 4. 处理文本输入  
**TextInput**是一个允许用户输入文本的基础组件。它有一个名为**onChangeText**的属性，此属性接受一个函数，而此函数会在文本变化时被调用。另外还有一个名为**onSubmitEditing*的属性，会在文本被提交后（用户按下软键盘上的提交键）调用。  

### 5. ScrollView
这个控件作为一个**滚动视图**，其子控件可以是任何的，相互之间也可以是不同的，由于**一次性渲染所有的元素（不管一屏是否装得下），所以适合显示数量不多的滚动元素**   
常用的属性：  

* horizontal={true}：设置方向为水平滚动  
* ref="" :类似于传统Android中的id
* scrollResponderScrollTo方法实现轮播效果  

### 6. 长列表  
FlatList这个控件不同于ScrollView，该控件一次只渲染屏幕上可见的元素  
FlatList组件必须的两个属性是**data和renderItem**。data是列表的数据源，而renderItem则从数据源中逐个解析数据，然后返回一个设定好格式的组件来渲染。
  
	export default class FlatListBasics extends Component {
	  render() {
	    return (
	      <View style={styles.container}>
	        <FlatList
	          data={[
	            {key: 'Devin'},
	            {key: 'Jackson'},
	            {key: 'James'},
	            {key: 'Joel'},
	            {key: 'John'},
	            {key: 'Jillian'},
	            {key: 'Jimmy'},
	            {key: 'Julie'},
	          ]}
	          renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
	        />
	      </View>
	    );
	  }
	}
	
	const styles = StyleSheet.create({
	  container: {
	   flex: 1,
	   paddingTop: 22
	  },
	  item: {
	    padding: 10,
	    fontSize: 18,
	    height: 44,
	  },
	})  

SectionList则用来渲染一组需要分组的数据：  

	export default class SectionListBasics extends Component {
	  render() {
	    return (
	      <View style={styles.container}>
	        <SectionList
	          sections={[
	            {title: 'D', data: ['Devin']},
	            {title: 'J', data: ['Jackson', 'James', 'Jillian', 'Jimmy', 'Joel', 'John', 'Julie']},
	          ]}
	          renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
	          renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}
	          keyExtractor={(item, index) => index}
	        />
	      </View>
	    );
	  }
	}
	
	const styles = StyleSheet.create({
	  container: {
	   flex: 1,
	   paddingTop: 22
	  },
	  sectionHeader: {
	    paddingTop: 2,
	    paddingLeft: 10,
	    paddingRight: 10,
	    paddingBottom: 2,
	    fontSize: 14,
	    fontWeight: 'bold',
	    backgroundColor: 'rgba(247,247,247,1.0)',
	  },
	  item: {
	    padding: 10,
	    fontSize: 18,
	    height: 44,
	  },
	})  

### 7. 网络  
React Native 提供了和 web 标准一致的Fetch API，用于满足开发者访问网络的需求。

#### 7.1 网络请求  
要从任意地址获取内容的话，只需简单地将网址作为参数传递给 fetch 方法即可（fetch 这个词本身也就是获取的意思）：
	
	fetch("https://mywebsite.com/mydata.json");  

Fetch 还有可选的第二个参数，可以用来定制 HTTP 请求一些参数。你可以指定 header 参数，或是指定使用 POST 方法，又或是提交数据等等：  

	fetch("https://mywebsite.com/endpoint/", {
	  method: "POST",
	  headers: {
	    Accept: "application/json",
	    "Content-Type": "application/json"
	  },
	  body: JSON.stringify({
	    firstParam: "yourValue",
	    secondParam: "yourOtherValue"
	  })
	});  

提交数据的格式关键取决于 headers 中的Content-Type。Content-Type有很多种，对应 body 的格式也有区别。到底应该采用什么样的Content-Type取决于服务器端，所以请和服务器端的开发人员沟通确定清楚。常用的'Content-Type'除了上面的'application/json'，还有传统的网页表单形式，示例如下：  

	fetch("https://mywebsite.com/endpoint/", {
	  method: "POST",
	  headers: {
	    "Content-Type": "application/x-www-form-urlencoded"
	  },
	  body: "key1=value1&key2=value2"
	});
  
#### 7.2 处理服务器的响应数据  
Fetch 方法会返回一个Promise，这种模式可以简化异步风格的代码（译注：同样的，如果你不了解 promise，建议使用搜索引擎补课）：  

	function getMoviesFromApiAsync() {
	  return fetch("https://facebook.github.io/react-native/movies.json")
	    .then(response => response.json())
	    .then(responseJson => {
	      return responseJson.movies;
	    })
	    .catch(error => {
	      console.error(error);
	    });
	}  

也可以在 React Native 应用中使用 ES2017 标准中的async/await 语法：  

	// 注意这个方法前面有async关键字
	async function getMoviesFromApi() {
	  try {
	    // 注意这里的await语句，其所在的函数必须有async关键字声明
	    let response = await fetch(
	      "https://facebook.github.io/react-native/movies.json"
	    );
	    let responseJson = await response.json();
	    return responseJson.movies;
	  } catch (error) {
	    console.error(error);
	  }
	}  

### 8. 页面导航  
React Native推荐使用Navigation实现导航，使用该控件需要在初始化项目后，进入根目录后执行：  

	yarn add react-navigation  
  
按使用的形式分为三部分：  

* **StackNavigator：**类似于普通的Navigator，屏幕上方的导航栏  
* **TabNavigator：**屏幕下方导航栏  
* **DrawerNavigator：**抽屉效果，侧边滑出  

#### 8.1 StackNavigator  
这个Navigator的使用API为**StackNavigator(RouteConfigs,StackNavigatorConfig)**  

	// 注册导航
	const Navs = StackNavigator({
	    Home: { screen: Tabs },
	    HomeTwo: {
	        screen: HomeTwo,  // 必须, 其他都是非必须
	        path:'app/homeTwo', 使用url导航时用到, 如 web app 和 Deep Linking
	        navigationOptions: {}  // 此处设置了, 会覆盖组件内的`static navigationOptions`设置. 具体参数详见下文
	    },
	    HomeThree: { screen: HomeThree },
	    HomeFour: { screen: HomeFour }
	}, {
	    initialRouteName: 'Home', // 默认显示界面
	    navigationOptions: {  // 屏幕导航的默认选项, 也可以在组件内用 static navigationOptions 设置(会覆盖此处的设置)
	        header: {  // 导航栏相关设置项
	            backTitle: '返回',  // 左上角返回键文字
	            style: {
	                backgroundColor: '#fff'
	            },
	            titleStyle: {
	                color: 'green'
	            }
	        },
	        cardStack: {
	            gesturesEnabled: true
	        }
	    }, 
	    mode: 'card',  // 页面切换模式, 左右是card(相当于iOS中的push效果), 上下是modal(相当于iOS中的modal效果)
	    headerMode: 'screen', // 导航栏的显示模式, screen: 有渐变透明效果, float: 无透明效果, none: 隐藏导航栏
	    onTransitionStart: ()=>{ console.log('导航栏切换开始'); },  // 回调
	    onTransitionEnd: ()=>{ console.log('导航栏切换结束'); }  // 回调
	});

第一个参数是配置各种路由，第二个参数就是配置该导航栏了。  

##### navigationOptions参数  

* title: 导航栏的标题

* header: 导航栏设置对象
	* visible: 导航栏是否显示  
	* title: 导航栏的标题, 可以是字符串也可以是个组件
	* backTitle: 左上角的返回键文字, 默认是上一个页面的title
	* right: 导航栏右按钮
	* left: 导航栏左按钮
	* style: 导航栏的style
	* titleStyle: 导航栏的title的style
	* tintColor: 导航栏颜色
* cardStack: 配置card stack
	* gesturesEnabled: 是否允许右滑返回，在iOS上默认为true，在Android上默认为false  

##### StackNavigatorConfig参数  

* initialRouteName: 设置默认的页面组件，必须是上面已注册的页面组件
* initialRouteParams: 初始路由的参数
* navigationOptions: 屏幕导航的默认选项
* paths: RouteConfigs里面路径设置的映射
* mode: 页面切换模式:
	* card: 普通app常用的左右切换
	* modal: 上下切换

* headerMode: 导航栏的显示模式:
	* float: 无透明效果, 默认
	* screen: 有渐变透明效果, 如微信QQ的一样
	* none: 隐藏导航栏

* cardStyle: 样式
* onTransitionStart: 页面切换开始时的回调函数
* onTransitionEnd: 页面切换结束时的回调函数  

##### navigation  
在StackNavigator中注册后的组件都有**navigation**这个属性. navigation又有5个参数**:navigate, goBack, state, setParams, dispatch**, 可以在组件下console.log一下this.props就能看到。  

* this.props.navigation.navigate('Two', { name: 'two' }): push下一个页面
	* routeName: 注册过的目标路由名称
	* params: 传递的参数
	* action: 如果该界面是一个navigator的话，将运行这个sub-action
* this.props.navigation.goBack(): 返回上一页
* this.props.navigation.state: 每个界面通过这去访问它的router，state其中包括了：
	* routeName: 路由名
	* key: 路由身份标识
	* params: 参数
* this.props.navigation.setParams: 该方法允许界面更改router中的参数，可以用来动态的更改导航栏的内容
* this.props.navigation.dispatch: 可以dispatch一些action，主要支持的action有：
	* Navigate:
	* Reset: Reset方法会清除原来的路由记录，添加上新设置的路由信息, 可以指定多个action，index是指定默认显示的那个路由页面, 注意不要越界了
	* SetParams: 为指定的router更新参数，该参数必须是已经存在于router的param中



