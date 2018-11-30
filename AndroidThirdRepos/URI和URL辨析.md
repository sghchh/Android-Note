# URI和URL  
## 一、URI详解  

>JDK官方文档：[http://tool.oschina.net/apidocs/apidoc?api=jdk-zh](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)  

### 1.1 URI语法组成  

在最高级别上，字符串形式的URI引用语法如下：  
> **[scheme:]scheme-specific-part[# fragment]**  
> 其中方括号表示内容是可选的，字符':'和'#'代表他们自身。  

#### 分类  

1. 根据是否指定了**方案（scheme）**分为绝对URI和非绝对URI  
2. 可以分为**不透明URI和分层URI**  

**不透明URI为绝对URI**：其**特定于方案的部分(即高级别语法上的*scheme-specifit-part*部分)不是以斜线字符('/')开始**。不透明URI无法进行进一步的解析。  
	
	实例：
	mailto:java-net@java.sun.com	
	news:comp.lang.java	
	urn:isbn:096139210x    

**分层URI**：要么是**绝对URI（其特定于方案的部分以斜线字符开始）**，要么是相对URI  

	http://java.sun.com/j2se/1.3/ 
	docs/guide/collections/designfaq.html#28 
	../../../demo/jfc/SwingSet2/src/SwingSet2.java 
	file:///~/calendar  

#### 进一步分类  

上面说的分层URI还可以按照下面的语法进行进一步解析：  
>**[ scheme :][ // authority][ path][ ? query][ # fragment]**  
>即把相对于模式的部分进一步细分了  
> 其中 :、 /、 ? 和 # 代表它们自身  

而其中的**授权部分(authority)**为*基于服务器的*或者*基于注册表的*。  
基于服务器的授权按照如下语法进行解析：  
> **[ user-info @] host[ : port]**  
> 几乎当前使用的所有 URI 方案都是基于服务器的。**不能采用这种方式解析的授权组成部分被视为基于注册表的。**  

## 二、URL  

URL的语法格式为：  
>**protocol://host[:port]/path**  

其中，端口号是可选的，如果不写，则**默认为协议的端口号**  

host和端口号代表链接的远程主机以及端口号，而path则指明了主机上的文件。  

URL 后面可能还跟有一个“片段”，也称为“引用”。该片段由井字符 "#" 指示，后面跟有更多的字符。例如，  

	http://java.sun.com/index.html#chapter1  

从技术角度来讲，URL 并不需要包含此片段。但是，使用此片段的目的在于表明，在获取到指定的资源后，应用程序需要使用文档中附加有 chapter1 标记的部分。标记的含义特定于资源。  

上面所说的是绝对URL，相对URL则不需要协议、主机、端口号。  

## 三、辨析  

URI名为统一资源标识符，URL为统一资源定位符，前者的范围更大，包括了后者。


