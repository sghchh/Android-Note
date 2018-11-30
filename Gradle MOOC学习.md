# Gradle 慕课学习  
Gradle是一种**自动化构建工具**，自动化构建工具的作用有：**自动化依赖管理**、**自动化测试、打包、发布**。  

就拿依赖管理来说，如果没有自动化构建工具的话，当我们想要使用第三方的库或者插件的时候，我们需要将其jar等文件放到我们项目的lib目录下，最头疼的是当我们使用的几个第三方的库之间有依赖关系的时候，那么其版本就可能会产生冲突，想要找到并解决这个冲突是非常麻烦且啰嗦的过程；再有，没有自动化构建工具的话，我们编写测试代码、打包工程、发布等一系列也是非常的麻烦。    

自动化构建工具的出现就很好的将我们从这些问题中解放了出来，能够专注于需求代码的构建。

## 一、Gradle基础认识  
### 1. 构建脚本  
Gradle构建中两个基本的概念是项目(Project)和任务(Task)，每一个构建至少包含一个项目，项目中包含一个或者多个任务。**项目之间可以依赖，任务之间也可以依赖(任务之间的依赖表现就是任务一依赖任务二，那么在任务一执行之前，任务二一定先要被执行)。**  

#### 项目  
一个项目就代表了一个正在构建的组件(比如一个jar文件)，当构建启动后，**Gradle会基于build.gradle实例化一个org.gradle.api.Project类，并且能够通过project变量使其隐式可用**。

> 也就是说，当我们创建一个基于gradle管理的项目的时候，build.gradle这个文件就代表了我们的这个项目，而其内容就是**我们对这个org.gradle.api.Project类的对象的配置(设置属性、实现方法)**。比如Project含有的三个属性：group、name、version；这三个属性作为一个签名，共同确定了一个唯一的组件，比如：compile 'com.squareup.retrofit2:retrofit:2.0.2'中，com.squareup.retrofit2就是group，retrofit就是name，2.0.2就是version；这三个共同确定了我们具体要依赖的一个组件。而项目中的**这三个属性就是这样的作用：当我们的项目打包发布后，如果别人或者自己想要依赖这个包，就可以通过这三个属性的值来实现，就像上面的Retrofit依赖一样**    
> Project类中常见的方法比如：**apply、dependencies、repositories**等，在构建脚本中，我们**传入的都是闭包Closure**，只不过Groovy语法可以省略括号。  


#### 任务  
Task对应也有一个类：**org.gradle.api.Task**，其主要包括**任务动作(就是任务的具体实现代码)和任务依赖**。  

> 任务中也有属性或者方法需要我们去实现、配置；比如dependsOn方法用来确定该任务依赖于其他的任务，doFirst、doSelf和doLast则是任务列表前和后面执行的代码。    

### 2. 构建的生命周期  
构建的生命周期依次是：初始化阶段、配置阶段、执行阶段  

#### 初始化阶段  
Gradle会根据构建脚本创建一个Project类，并在这个构建脚本中隐式可用；如果是多项目构建的情况下，Gradle会为每一个项目或者模块创建一个Project类。  

#### 配置阶段  
这个阶段的主要任务是**生成所有Task的依赖顺序和执行顺序**，这是根据配置代码完成的(可以认为只要不是一个Task中的doFirst或者doLast中的代码，那它就是一个配置代码)  

#### 执行阶段  
这个阶段就是执行Task中的动作代码  

> 配置代码与执行代码：  
> task test{  
>    project.name = 'hello'    //配置代码  
>    doFirst {                       //动作代码  
>        println ('world')  
>    }  
> }    

### 3. 依赖管理  
依赖就是我们可以使用已经存在的，第三方的库来实现我们的功能，而避免重复的造轮子。  

#### 组件标识  
想要唯一的确认一个组件，只需要三个参数就可以，就是之前说到的：group、name、version  

#### 仓库  
仓库的作用就是存放各种组件，我们可以通过组件标识来定位一个组件，但是我们去哪里找呢？答案就是：**仓库**  

常用的公共仓库有：**mavenCentral和jcenter**  
远程仓库的作用就是，如果本地没有我们需要的依赖的话，会直接从公共仓库上下载。  

### 4. 多项目构建  
我们通常将一个项目拆分成不同的模块来提高开发效率，我们可以新建moudle来实现  

> 虽然叫模块儿，但是他们的build.gradle也是会生成一个Project类，而根项目(rootProject)对于他们的管理也含有subproject、allproject等含有Project的字眼。  

## 二、Gradle常用命令  
命令行使用Gradle是一种很主要的方式，用起来既便捷，又显得高逼格。  

所有的Gradle命令都符合以下形式：  

	gradle [taskName ...] [--optionsName ...]  
	gradle [--optionsName ...] [taskName ...] 

可以看到，**打头儿的一定是gradle(官方墙裂推荐使用Gradle Wrapper，所以将gradle替换为gradlew也行)，而且taskName和optionsName的位置可互换**  

> 如果**指定了多个任务，则应该使用空格分隔他们**；同样如果Options需要接受一个值的话，那么需要**使用=连接Options和值**  

### 多项目构建下的任务执行  
在多项目构建下如果想要执行子项目(subProject)的某一个任务，则需要指定子项目名和任务名：  

	gradlew subProjectName:taskName
	gradlew :subProjectName:taskName  

当然，上面说的情况应该是值，当前的路径为根项目的路径下，如果进入到子项目的路径了，则可以省略命令中的子项目名：  

	cd subProjectName
	gradle taskName

> 眼尖的你可能会注意到，上面的命令我写成了gradle而不是gradlew，如果想要使用Gradle Wrapper从子项目下执行子项目的任务的话，需要**使用相对路径形式的命令  ./gradlew taskName**  

### 特定需求下的任务执行  
#### 1. 跳过依赖的任务  
一些插件提供的任务中，很多都是有相互的依赖关系的，牵一发而动全身，执行一个任务，可能有很多个任务跟着也被执行了，如果我们想要不执行某一个依赖的任务的话，需要添加一个Options参数了：  

	gradlew dist --exclude-task test

	output:
	
	> Task :compile
	compiling source
	
	> Task :dist
	building the distribution
	
	BUILD SUCCESSFUL in 0s
	2 actionable tasks: 2 executed  

![](https://docs.gradle.org/current/userguide/img/commandLineTutorialTasks.png)  

可以看到，不光是指定的任务被跳过了，就连**传递依赖的任务也被跳过了**  

#### 2. 遇到问题不停下继续执行  
Gradle默认情况下，当执行一个Task的时候，任何其他的Task出现了问题，那么Gradle就会停下来，抛出异常，但是，如果我们想要build一下，就把所有的问题找出来的话，就需要遇到问题时继续执行：  

	gradlew taskName --continue    

### 调试用参数  

* **--full-stacktrace:**打印出所有异常详细的信息
* **--stacktrace**：
* **-Dorg.gradle.debug=true**:调试Gradle 客户端进程(非Daemon进程)，Gradle会等待，除非链接一个debugger到5005端口
* **-Dorg.gradle.daemon.debug=true**:调试Daemon进程  

### Daemon进程参数  

* **--daemon:**使用守护进程去启动构建，默认是打开的
* **--foreground:**以foreground的形式启动守护进程

### 打印参数  

* **-Dorg.gradle.logging.level=(quiet,warn,lifecycle(默认),info,debug)**  
* **-q,--quiet**
* **-w,--warn**
* **-i,--info**
* **-d,--debug**    

### 环境参数  
这些参数在开发过程中，可以直接配置到**gradle.properties**里面：  

* **--project-dir:**设置Gradle开始的目录  
* **--project-cache-dir:**设置项目缓存的目录，默认为**.gradle**  
* **-D,--system-prop:**为JVM设置系统属性，使用形式为**-DpropName = value**,例如-Dorg.gradle.jvmargs就代表设置jvm的启动参数，-Dorg.gradle.java.home设置JDK路径
* **-P,--project-prop:**设置项目属性,使用形式为**-PpropName = value**    

## 三、Gradle Wrapper  
官方推荐我们使用Gradle Wrapper来进行Gradle项目的构建，而不是使用gradle命令。  

> The Wrapper is a script that invokes a declared version of Gradle, downloading it beforehand if necessary. As a result, developers can get up and running with a Gradle project quickly without having to follow manual installation processes saving your company time and money.  

上面是官方对Wrapper的一个说明，说白了Wrapper就是一个脚本，管理Gradle的版本等信息，必要时自动预先帮我们下载对应的Gradle版本，节省了我们的时间。  

![](https://docs.gradle.org/current/userguide/img/wrapper-workflow.png)  

可以看到，**Gradle Wrapper先从服务器上下载好Gradle，然后解压到本地，Gradle工程再从本地调用**。  

#### 1. Gradle Wrapper文件结构   
最值得我们注意的就是gradle-wrapper.properties这个文件了，其中最重要的就是：  

	distributionUrl=https\://services.gradle.org/distributions/gradle-4.3.1-bin.zip  

其中Gradle的版本默认是当前使用的版本(在执行wrapper任务的时候可以通过--gradle-version Options来指定版本)，而后面的**-bin**是说明，我们下载的这个zip文件中只包含了runtime，并不包含实例代码，文档这些东西，与之相对的就是**-all**了；如果我们想要更新Gradle Wrapper的版本的话，只需要执行命令：  

	gradlew wrapper --gradle-wrapper {version}  

而查看当前版本的命令是：  

	gradlew -v  

#### 2. Gradle 构建缓存  
  
> The Gradle build cache is a cache mechanism that aims to save time by reusing outputs produced by other builds. The build cache works by storing (locally or remotely) build outputs and allowing builds to fetch these outputs from the cache **when it is determined that inputs have not changed**, avoiding the expensive work of regenerating them.

构建缓存默认是关闭的，开启的方式有两种：  

	gradle taskName --build-cache  
	org.gradle.caching = true  //gradle.properties中添加  

Gradle是如何判断一个缓存是否能够重用呢？  

> Since a task describes all of its inputs and outputs, Gradle can compute a build cache key that uniquely defines the task’s outputs based on its inputs. That build cache key is used to request previous outputs from a build cache or store new outputs in the build cache.  

## 四、认识Task  
Gradle中的所有东西都是基于两个概念：project和Task；其中Project有多重具体的东西，比如一个Web Application、一个jar压缩包等；而Project则是由多个Task构成的。  

### 4.1 创建Task  
在Gradle的构建脚本中创建一个Task很简单，就像在Java中声明一个引用类型的变量一样简单：  

	//build.gradle
	task Hello {
		doLast {
			println 'hello world'
		}
	}  

上面就定义了一个简单的Task，想要执行这个task，首先要进入Gradle Project的目录下，然后执行命令：  
	
	gradle -q Hello  

> 这里-q这个Option控制的是Logging信息，这里是quiet的意思，而println函数的输出就是这个级别的；Groovy语法的性质，可以不加括号、分号，所以程序看起来就是这个样子  

#### doLast简写  
还可以使用doLast的简写形式**<<**,上面的程序和下面是等价的：  

	task Hello << {
		println 'hello world'
	}

#### Task中的动作是可以多次执行的  
Task中的**doFirst、doSelf、doLast**都是一个个**Action**，而每一个Task都会有一个Action list，所以这三个是可以多次执行的：  

	task hello {
	    doLast {
	        println 'Hello Earth'
	    }
	}
	hello.doFirst {
	    println 'Hello Venus'
	}
	hello.doLast {
	    println 'Hello Mars'
	}
	hello {
	    doLast {
	        println 'Hello Jupiter'
	    }
	}  

执行命令的输出为：  

	> gradle -q hello
	Hello Venus
	Hello Earth
	Hello Mars
	Hello Jupiter  

可见，所有的Action都被加入到了Action list中，最后一起被执行了。

#### 添加依赖  
一个Task要想依赖其他的task，在定义的时候加上形参**dependsOn:taskName** 就好  

### 4.2 添加属性  
可以使用ext.的形式为Task添加属性，就行Java一样：  

	task myTask {
	    ext.myProperty = "myValue"
	}
	
	task printTaskProperties {
	    doLast {
	        println myTask.myProperty
	    }
	}  

执行第二个task，会输出myValue，除此之外，Task还有几个默认的属性，比如name、doFirst、doLast、doSelf等      

> ext 定义的拓展属性在Gradle中只有一些重量级的Object才可使用，比如Project、Task这些，被ext声明的属性在同一区域内可以被随便引用，如果属于不同的区域，需要先将ext属性所在的project或者task导入；而def也是一个可以定义属性的关键字，只不过该关键字定义的属性只能在定义的scope(范围)访问。  
> 可简单的理解为ext声明的是属性，而def声明的是局部变量




 
















