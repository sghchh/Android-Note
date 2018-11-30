# Gradle  
## 1. Gradle知识相关  
### 1.1 搭载环境  
搭载Gradle环境和Java一样，将下载的Gradle包解压后，将其**bin文件夹**的路径添加到环境变量中，命名为**GRADLE_HOME**  

先来看看Gradle版Hello World：  

首先创建一个**build.gradle脚本文件：**  

	task hello{
	doLast{
		println "hello world"
	}
	}  
然后在命令行下将当前的路径移动到build.gradle文件所在的路径下执行命令  

	gradle -q hello  
就可以看到输出了“hello world”  
接下来分析一下这些步骤的原因：  

1. 首先文件之所以命名为build.gradle是因为这个名字是gradle默认的构建脚本文件，也就是说命令行执行的时候会执行build.gradle文件中的hello这个task；当然可以在命令行中添加**-b来指定文件**：  

		gradle -b my.gradle -q task1  
该命令执行的结果就是**my.gradle**文件里的**task1**  
2. 脚本文件中的task是脚本的一个元素，可以把它类比为Java中的main函数，是程序执行的入口，但是gradle脚本文件中的task可以有任意个。  
3. 这里的**-q**参数是用来控制输出日志的级别的，就像AS中的log系列打印方法。  
### 1.2 Gradle相关普及  
#### 1.2.1 Gradle Wrapper  
Wrapper，顾名思义就是对Gradle的一层包装；便于团队开发过程中统一Gradle构建的版本，这样就避免了因为Gradle版本不同带来的问题。**当你使用Wrapper启动Gradle的时候，Wrapper会检查Gradle有没有被下载关联，如果没有将会从配置的地址进行下载并构建运行。**  
##### 生成Wrapper  
生成Wrapper在Gradle内部也**内置了一个task(Wrapper task)**帮助我们自动生成Wrapper所需的目录文件，在一个项目的**根目录**下输入 **gradle wrapper**就行了。  
执行完毕后可以看到工程下生成了几个文件：  

* gradle
	* wrapper
		* gradle-wrapper.jar
		* gradle-wrapper.properties
* gradlew
* gradlew.bat  

其中gradlew和gradlew.bat分别是Linux和Windows下的可执行脚本；那个gradle-wrapper.jar是具体业务逻辑实现的jar包，**gradlew的命令都是使用Java来执行这个jar包来执行相关的操作的。**      

>  gradlew 命令和gradle命令的用法一样，但是在Android Studio中更加推荐使用Wrapper提供的gradlew命令来执行


##### Wrapper配置  
Wrapper的相关配置都是在那个生成的**gradle-wrapper.properties文件**中的，以下是AS中的该文件的内容：  

	distributionBase=GRADLE_USER_HOME
	distributionPath=wrapper/dists
	zipStoreBase=GRADLE_USER_HOME
	zipStorePath=wrapper/dists
	distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
下面就说说这几个条目的意思：  
* distributionBase：下载的Gradle压缩包解压后存储的主目录  
* distributionPath：相比于distributionBase的解压后的Gradle压缩包的路径  
* zipStoreBase：相较于distributionBase只不过是存放zip压缩包的  
* zipStorePath：相较于distributionPath只不过是存放zip压缩包的  
* distributionUrl：Gradle发行版压缩包的下载地址  

>AS中Gradle的默认安装地址是C盘中的 **用户->.gradle 对应distributionBase=GRADLE_USER_HOME这一句话**；进入这个路径可以看到各种文件的位置就如下面那三句话说的那样。  

我们通过Wrapper来构建的时候，是可以直接指定版本的，如果不指定的话，默认是当前版本，这个版本值会影响配置文件中**distributionUrl的值https\://services.gradle.org/distributions/gradle-${version}-all.zip**;手动控制指定版本的方法是在命令行中添加**--gradle-version**参数：  

	gradle wrapper --gradle-version 4.1  
##### 自定义Wrapper task  
如果想一次自定义所有的配置文件里的值，也可以自定义一个wrapper task  

	task wrapper(type:Wrapper){
		gradleVersion= '4.0'  
		distributionBase='GRADLE_USER_HOME'  
		...
	}  
这里的**type:Wrapper**是创建task一种形式，**type关键字的意思类似于Java中的继承**  
#### 1.2.2 Gradle日志  
上文举得例子中用到了**gradle -q hello**这种命令，**-q**就是一种级别的日志；Gradle的日志有：  

* ERROR：错误消息  
* QUIET：重要消息
* WARNING：警告消息
* LIFECYCLE：进度消息
* INFO：信息消息
* DEBUG：调试消息

消息的**级别依次递减**，(*println方法打印的内容属于QUIET级别*)。  
虽然有这么多的消息级别，但是我们能够控制开关的只有四个：  

* 无选项：LIFECYCLE及其更高级别  
* -q或者--quiet：QUEIT及其更高级别  
* -i或者--info：INFO及其更高级别  
* -d或者--debug：DEBUG及其更高级别，**这一般会输出所有日志**  

#### 1.2.3 输出错误堆栈信息  
当出现错误的时候，错误堆栈的信息在分析原因的时候尤为重要，**默认情况下，堆栈信息的输出是关闭的**。有两个开关可以控制打开：  

* -s或者--stacktrace：输出关键性的堆栈信息  
* -S或者--full-stacktrace：输出全部的堆栈信息  

## 2. Groovy语言基础  
## 3. Android Gradle   
先看看一个**app模块build.gradle**的文件：  

	apply plugin: 'com.android.application'
	apply from:'version.gradle'
	android {
    compileSdkVersion 26
    useLibrary 'org.apache.http.legacy',false

    signingConfigs {
        release {
            storeFile file("myreleasekey.keystore")
            storePassword "password"
            keyAlias "MyReleaseKey"
            keyPassword "password"
        }
        debug {
            storeFile file("mydebugkey.keystore")
            storePassword "password"
            keyAlias "MyDebugKey"
            keyPassword "password"
        }
    }
    defaultConfig {
        applicationId "com.example.as.activityoptions"
        minSdkVersion 23
        targetSdkVersion 26
        versionCode appVersionCode
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        proguardFile getDefaultProguardFile('proguard-android.txt')
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    buildTypes {
        release {
            applicationIdSuffix ".debug"
            debuggable false
            minifyEnabled false
            multiDexEnabled true
            shrinkResources true
            signingConfig signingConfigs.release
            zipAlignEnabled true
            proguardFile getDefaultProguardFile('proguard-android.txt')
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        debug {

        }
    }
	}

	dependencies {
    	implementation fileTree(dir: 'libs', include: ['*.jar'])
    	compile 'com.android.support:appcompat-v7:26.1.0'
    	implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    	testImplementation 'junit:junit:4.12'
    	androidTestImplementation 'com.android.support.test:runner:1.0.1'
    	androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
	}  

再看看**Project的build.gradle**文件： 

	buildscript {
    
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
        

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
	}

	allprojects {
    	repositories {
       		google()
        	jcenter()
    	}
	}

	task clean(type: Delete) {
    	delete rootProject.buildDir
	}  
### 3.1 Gradle插件  
插件的作用：  

	* 可以添加任务到你的项目中去，帮我们自动完成一些事情；比如打包apk，测试，编译等  
	* 可以添加依赖配置到项目中去，可以让我们配置一些在项目构建过程中添加的依赖；比如使用第三方库  
	* 可以向项目中现有的对象类型添加新的扩展属性、方法等。比如android{}就是。  
	* 可以对项目进行一些约定，比如应用Java插件之后，比如src/main/java目录下使我们的源代码存放位置。  

**插件的应用都是通过Project.apply()**  
#### 3.1.1 应用二进制插件  
二进制插件就是实现了**org.gradle.api.Plugin接口的插件**，他们可以有plugin id，比如：  

	apply plugin:'java'  
上面的**'java'**就是Java插件的plugin id（**对于Gradle自带的核心插件都有一个容易记的短名，成为plugin id**），比如这里的**'java'**，其实他对应的类型是**org.gradle.api.plugins.JavaPlugin**,所以我们也可以这样用：  

	apply plugin:org.gradle.api.plugins.JavaPlugin  

*又因为org.gradle.api.plugins包*是默认导入的，所以也可以这样写：  

	apply plugin:JavaPlugin  

>第二种方法一般适用于我们**自定义二进制插件**的时候导入  

二进制插件一般是被打包在一个**jar包**里发布的。  

#### 3.1.2 应用脚本插件  
在**app模块的build.gradle**文件中，我们需要在defaultConfig中指定一些构建信息：  

	versionCode 1
    versionName "1.0"
比如这两个，但是如果该build文件的内容太多的时候，我们修改这些简单的信息的时候，找的时候可能太麻烦了，解决方案就是**将这些信息单独拿出来，作为一个单独的gradle脚本文件的扩展属性；然后引用这个脚本文件，继而引用这些属性；这样一来，我们修改这个被引用的脚本文件的内容就可以了**。比如，这里我把**versionCode**单独放到一个**version.gradle**文件中，在**appd的build中引用这个脚本文件，将versionCode指定为version.gradle中的一个属性即可。**引用脚本插件（实际上是脚本文件）的方法是**apply from:**    
**version.bulid**  

	ext {
    appVersionCode =1
	}    
在build.gradle中引用它：  

	apply from:'version.gradle'
	android {
    compileSdkVersion 26
    useLibrary 'org.apache.http.legacy',false
    ...
    defaultConfig {
        ...
        versionCode appVersionCode
        versionName "1.0"
        ...
    }
    
	}

	dependencies {
        ...
	}  

这样就可以了。  
>实际上就像我们平时写代码的时候，将一些特殊的方法或者变量定义到一个Util类里面，当使用到的时候我们导入这个类，然后调用这些方法或者变量  

#### 3.1.3 应用第三方插件  
第三方发布的**jar**包的二进制插件，我们在应用的时候必须在**buildscript{}**里配置其**classpath**才能使用，**这不想Gradle为我们内置的插件**。比如我们的**Android Gradle**插件：  

	apply plugin:'com.android.application'  

就需要我们在**project的build.gradle**里面进行配置：  

	buildscript {
    
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
    }
	}  
  
**buildscript{}**块是一个在构建项目之前，为项目进行**前期准备和初始化相关配置依赖**的地方。  

#### 3.1.4 apply方法  
我们上面讲的应用插件的方法都是  

	apply plugin:'com.android.application'   
这种形式的，实际上**Project.apply()**方法有三种形式：  

	void apply(Map<String,?> options):接受一个Map对象为参数  
	void apply(Closure closure):接受一个闭包为参数   
	void apply(Action<? super ObjectConfigurationAction> action):接受一个Action  

我们最常用的是第一种方法：接受一个Map对象，我们来看看第二种方法：  

	apply {
		plugin 'java'  
	}  
**显然，第二种方法当应用的插件较多时可以一次性应用，更加方便。**  
第三种方法就不说了，有兴趣自己下去看。  

#### 3.1.5 自定义插件  
自定义插件必须实现Plugin接口，现在我还没到那个地步，就不说了。  

### 4. Java Gradle插件  
为什么要说Java Gradle插件呢？因为Android Gradle插件就是基于Java Gradle插件的，有些特性实际上是Java插件的东西。  
还记得怎么应用java插件吗？  

	apply plugin:'java'  
因为Java插件是Gradle为我们内置的插件，所以不用添加classpath。  
#### 4.1 源代码集合SourceSet  
SourceSet--源代码集合，是Java插件用来描述和管理源代码以及资源的一个抽象概念，是一个Java源代码和资源文件的集合。  
Java插件默认的两个分组是：**main和test**   
我们很熟悉的Java代码存放的目录结构是：  

* src  
	* main
		* java
		* resource
	* test
		* java
		* resource

这种对代码的分开存放，方便了我们的管理，**这就是Java插件为我们添加的功能，这就是SourceSet的作用。**  
一般来说，我们不推荐去修改这两个Java插件为我们提供的SourceSet源代码集合，因为大家都习惯了，约定俗成了。当然，如果有必要，你也可以添加一个自定义的目录：如添加一个vip源代码集合，用来存放vip版本的源代码.  
在**build脚本**里这么写：  

	sourceSets{
		vip{
			java{
				java.srcDirs 'src/vip/java'
			}
		} 
	} 

常用的属性有：
  
* java：该源集的Java源文件
* java.srcDirs:该源集的Java源文件所在目录
* resource:该源集的资源文件
* resource.srcDirs:该源集的资源文件所在目录  

#### 4.2 配置第三方依赖库  
我们经常会用到第三方的jar，有很多优秀的开源工具和框架可以帮助我们快速开发，要想使用这些第三方依赖，你要告诉Gradle如何找到这些依赖，也就是我们要讲的依赖配置；  
首先，一般情况下我们都是从仓库中查找我们需要的Jar包，也就是说，在Gradle中要配置一个仓库的Jar依赖，首先我们得告诉Gradle我们要使用什么类型的仓库，这些仓库的位置在哪里，这样Gradle才知道从哪里去搜寻我们依赖的Jar：  

	repositories{
		jecenter()
	}  

以上脚本我们配置了一个jcenter()库，告诉Gradle可以在这个库里面搜寻我们依赖的Jar。  
然后，有了仓库，就需要通过配置告诉Gradle我们依赖的是什么：  

	dependencies{
		compile 'com.squareup.okhttp3:okhttp:3.9.0'  
	}  

>注意：如果还记得我们是怎么引用第三方插件的话，可千万别搞混了。  
>在引用第三方插件的时候，我们是在buildscript下的dependencies里添加了classpath 'com.android.tools.build:gradle:3.0.1'；这里是引用第三方库，也是在dependencies里添加了compile 'com.squareup.okhttp3:okhttp:3.9.0'。这两个是不一样的。  
>首先：第三方插件的dependencies是在**Project的build脚本文件里的buildscript下**，而第三方库的dependencies是在**Moudle的build脚本的dependencies下**，二者所处的位置不同；然后：前者是**classpath**后者是**compile**  

第三方库的dependencies不只有compile这一种依赖配置，还有其他的：  

* compile：编译时依赖  
* runtime：运行时依赖  
* testCompile：编译测试用例时依赖
* testRuntime：仅仅在测试用例运行时依赖
* archives：该项目发布构件依赖
* default：默认依赖配置  

上面所说的是外部模块的依赖，一般需要配置一个仓库；我们还可以依赖一个项目或者文件：  

	dependencies{  
		compile project(':example63') 
		compile files('libs/ex63_1.jar',...)
		compile fileTree(dir:'libs',include: '*.jar') 
	}

### 5. Android Gradle插件  
Android其实是Gradle的一个第三方插件,所以在使用的时候别忘了添加classpath。  
#### 5.1 Android Gradle插件的分类  
Android Gradle插件有三种：  

* App插件id:com.android.applicaion  
* Library插件id:com.android.library  
* Test插件id:com.android.test  

**build.gradle:**

	apply plugin: 'com.android.application'
	apply from:'version.gradle'
	android {
    compileSdkVersion 26
    useLibrary 'org.apache.http.legacy',false

    signingConfigs {
        release {
            storeFile file("myreleasekey.keystore")
            storePassword "password"
            keyAlias "MyReleaseKey"
            keyPassword "password"
        }
        debug {
            storeFile file("mydebugkey.keystore")
            storePassword "password"
            keyAlias "MyDebugKey"
            keyPassword "password"
        }
    }
    defaultConfig {
        applicationId "com.example.as.activityoptions"
        minSdkVersion 23
        targetSdkVersion 26
        versionCode appVersionCode
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        proguardFile getDefaultProguardFile('proguard-android.txt')
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    buildTypes {
        release {
            applicationIdSuffix ".debug"
            debuggable false
            minifyEnabled false
            multiDexEnabled true
            shrinkResources true
            signingConfig signingConfigs.release
            zipAlignEnabled true
            proguardFile getDefaultProguardFile('proguard-android.txt')
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        debug {

        }
    }
	}

	dependencies {
    	implementation fileTree(dir: 'libs', include: ['*.jar'])
    	compile 'com.android.support:appcompat-v7:26.1.0'
    	implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    	testImplementation 'junit:junit:4.12'
    	androidTestImplementation 'com.android.support.test:runner:1.0.1'
    	androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
	}  
这里的**android{}**配置块儿就是Android Gradle插件为我们提供的一个扩展类型，可以让我们自定义Android Gradle工程。  
我们就通过上面的**build.gradle**例子来学习学习Android Gradle的配置。  

#### 5.1.1 compileSdkVersion  
配置的是我们编译Android工程的SDK  
等价写法是：  

	compileSdkVersion 'android-26'  

#### 5.1.2 buildToolsVersion  
表示我们使用的Android构建工具的版本，我们可以在Android SDK目录里看到，他是一个工具包，包括**appt、dex**等工具。  
#### 5.1.3 defaultConfig  
defaultConfig从名字就可以看出，这是个默认配置，**他是一个ProductFlavor。**ProductFlavor允许我们根据不同的情况同时生成多个不同的APK包，比如我们后面介绍的多渠道打包。  
**如果没有自定义的ProductFlavor，会使用这个默认的defaultConfig的配置**  

* applicationId:配置我们的包名  
* minSdkVersion:最低支持的Android版本
* targetSdkVersion:表名我们是基于哪个Android版本开发的  
* versionCode:表名我们的App应用内部版本号
* versionName:表名我们的App应用的版本名称，用户可以看到，就是我们发布的版本  
* testApplicationId:用于配置测试App的包名，默认情况下是applicationId+“.test”  
* **signingConfig**:配置默认的**签名信息**  
* **proguardFile**:用于配置App ProGuard混淆所使用的ProGuard配置文件。  
* proguardFiles:配置多个混淆文件  

#### 5.1.4 buildType  
这个和之前的SourceSet一样，是一个**域对象**。这个代表了**构建类型**，默认提供的有两个：**release和debug**  
针对不同的构建类型，可以进行的配置有  

1. applicationIdSuffix:配置applicationId的后缀  
2. debuggable:是否生成一个可供调试的apk，默认为false  
3. minifyEnable:配置该BuildType是否使用混淆  
4. multiDexEnable:配置该BuildType是否启动自动拆分成多个**Dex**  
5. proguradFile:配置该BuildType的混淆文件  
6. proguardFiles  
7. shrinkResource:配置该BuildType是否自动清理未使用的资源，默认为false  
8. signingConfig:配置该BuildType的签名配置   
9. **zipAlignEnabled:**是否开启zipalign优化，默认为false。**(PS:开启优化可以提高系统和应用的运行效率，更快的读写资源，降低内存使用)**

#### 5.1.5 SigningConfigs  
该属性可以根据不同的**构建类型**，进行不同的签名配置：  

	signingConfigs {
        release {
            storeFile file("myreleasekey.keystore")
            storePassword "password"
            keyAlias "MyReleaseKey"
            keyPassword "password"
        }
        debug {
            storeFile file("mydebugkey.keystore")
            storePassword "password"
            keyAlias "MyDebugKey"
            keyPassword "password"
        }
    }  

**默认情况下，debug模式的签名已经被配置好了，使用的就是Android SDK自动生成的debug证书，他一般位于$HOME/.android/debug.keystore目录下，其Key和密码都是已知的。**  
上面的配置中，我们生成了两个签名信息实例(release和debug)，但是还没有被使用；引用是在defaultConfig中  

	signingConfig signingConfigs.debug  
>注意：定义实例的属性是**signingConfigs**，使用时属性是**signingConfig**  

### 6. Android Gradle高级自定义  
#### 6.1 使用共享库  
**Android的包(如android.aoo,android.view等)是默认就包含在Android SDK库里的，所有的应用都可以直接使用他们，系统会帮我们自动链接他们。但是另一些库(如com.google.android.maps等)是独立的，并不会被系统自动链接，要单独进行生成使用，我们称之为共享库。**  

在**清单文件中**，我们可以指定想要使用的库：  

	<use-library  
		android:name="com.google.android.map"
		android:required="ture"/>  

第二个属性**android:required="true"**的意思是，当我们在安装App的时候，如果手机里没有我们需要的共享库，将不能安装。  

除了标准的Android SDK库之外，还存在两种库:一种是**add-ons库**，这些库大多是第三方厂商或者公司开发的，让开发者使用却不想暴露具体标准实现；另一种是**optional**可选库，一般是为了兼容低版本的API。前者位于Android SDK的add-ons目录下，后者位于Android SDK的platforms/android-xx/optional目录下。  
**add-ons库只需要在清单文件里声明就可以了，Android Gradle会自动解析，帮我们添加到classpath里；optional库不仅要在清单文件里声明，还有自己添加到classpath里。**  
这里添加到classpath不是像使用第三方插件那样，而是在**android{}下的useLibrary属性**  

	android{
		useLibrary 'org.apache.http.legacy'
	}  
#### 6.2 BuildConfig类  
Android Gradle构建脚本在编译后会生成一个**BuildConfig类**，默认情况下是这样的：  

	public final class BuildConfig {
  	public static final boolean DEBUG = Boolean.parseBoolean("true");
  	public static final String APPLICATION_ID = "com.example.as.myapp";
  	public static final String BUILD_TYPE = "debug";
  	public static final String FLAVOR = "";
  	public static final int VERSION_CODE = 1;
  	public static final String VERSION_NAME = "1.0";
	}  
可以看到，这些常量都是我们在build.gradle文件中配置的。  
DEBUG用于标记是**debug模式(为true)**，还是**release模式(为false)**。
