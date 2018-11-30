# Android Okhttp 自学习  
之前都是看别人的教程，自然是来的快去的也快，这次打算结合着[官方的API文档](http://square.github.io/okhttp/3.x/okhttp/)来学一学。  
## 一、相关的类  
### 1. Request相关  
#### 1.1 Request  
Request这个类代表了一次请求，官方文档对这个类的描述为：   

> An HTTP request. Instances of this class are immutable if their body is null or itself immutable.  
> 一个HTTP请求。 如果它们的主体为null或者它本身是不可变的，那么这个类的实例是不可变的。  

这个类很简单，其提供的方法也不多：  

* RequestBody body():
* CacheControl cacheControl()
* String header(String name):
* Headers headers():
* List<String> headers(String name)
* boolean isHttps()
* String method()
* Request.Builder newBuilder()
* Object tag():
* String toString()
* HttpUrl url():

#### 1.2 Request.Builder  
Request的创建一般采用的是创造者模式，通过Request.Builder这个辅助类来完成的，Request.Builder类中有很多方法来对一次request进行配置，比如设置缓存、配置请求头等。  

该类具有的方法有：  

* Request.Builder addHeader(String name,String value):为这次的Request添加一个请求头  
* **Request build():**Request.Builder在一系列的配置方法被流式调用完后，通过这个方法来**创建一个Request实例**   
* Request.Builder cacheControl(CacheControl cacheControl):设置这次Request的**Cache-Control**请求头，这个方法会**覆盖掉之前的调用**  
* Request.Builder header(String name,String value):这个方法和addHeader不同，该方法是将**已经存在的、名字为name参数的请求头的值设置为value参数**
* Request.Builder headers(Headers headers):这个方法通过传入的一个**Headers实例**，覆盖之前添加过的所有的请求头，只用该方法传入的参数封装的请求头  
* Request.Builder removeHeader(String name):移除**所有**name为参数的值的请求头  
* Request.Builder url(HttpUrl url)
* Request.Builder url(URL url)
* Request.Builder url(String url)  

#### 1.3 RequestBody  
在常见的Post请求的时候，我们需要上传一个请求体，该类(**是一个抽象类**)就封装了这个请求体。  

* long contentLength():返回调用**writeTo(okio.BufferedSink sink)**时写入Sink的**字节数**，如果数目未知，则返回**-1**
* abstract MediaType contentType():返回这个请求体的**Content-Type头部**
* static RequestBody create(MediaType contentType,byte[] content):将传入的字节数组封装成一个请求体
* static RequestBody create(MediaType contentType,byte[] cibtebt,int offset,int byteCount):同上一个方法，只不过指定了开始点和内容的长度
* static RequestBody create(MediaType contentType,okio.ByteString content)
* **static RequestBody create(MediaType contentType,File file):**该方法较为常用，我们在上传文件的时候就是这么干的，将一个文件内容封装到请求体中
* **static RequestBody create(MediaType contentType,String content):**这个也较为常见，当post的参数为一些String时，我们就是这么干的
* abstract void writeTo(okio.BufferedSink sink)  

#### 1.4 FormBody  
上面的RequestBody是一个抽象类，而这里的FormBody就是它的一个**实现类**，该类用于Okhttp的**表单上传**，POST要求的参数，被一个个以**key-value**的形式封装到了FormBody中，一次性提交到后台。  

* long contentLength():同RequestBody的同名方法  
* MediaType contentType():同RequestBody的同名方法
* String encodeName(int index)
* String encodeValue(int index)
* String name(int index)
* int size():返回**key-value对的数目**
* String value(int index)
* void writeTo(okio.BufferedSink sink)  

#### 1.5 FormBody.Builder  
我们创建一个FormBody的实例也是通过构建者模式来完成的，同Request.Builder类一样，这个类为提供了配置FormBody的一些方法。  

* **FormBody.Builder add(String name,String value):**为该FormBody添加对**key-value**，我们平常添加后台要求的参数就是这么干的  
* FormBody.Builder addEncode(String name,String value):  
* FormBody build()  

> *QUESTION:第二个方法是干啥的？和第一个方法的区别是什么？*  

#### 1.6 MultipartBody  
上面的FormBody是在表单提交的时候用的，但是却没法提交文件，**MultipartBody**不仅可以像FormBody一样提交普通的表单，还可以提交文件，同样也是**RequestBody的实现类**   

提供的成员：  

* static MediaType:ALTERNATIVE  
* static MediaType:DIGEST
* static MediaType:FOEM
* static MediaType:MIXED:默认的媒体类型
* static MediaType:PARALLED

提供的方法:

* String bundary()
* long contentLength():同父类一样
* MediaType contentType():同父类一样
* MultipartBody.Part part(int index)
* List<MultipartBody.Part> parts()
* int size():返回parts的数目
* MediaType type()
* void writeTo(okio.BufferedSink sink):同父类一样  

#### 1.7 MultipartBody.Part  
对比MultiPartBody和FormBody提供的方法就可以明白，在FormBody中表单的参数是以**key-value**的形式来封装的，那么在MultipartBody中就是通过**MultipartBody.Part**实现的  

* static MultipartBody.Part create(Headers headers,RequestBody body)  
* static MultipartBody.Part create(RequestBody body)
* **static MultipartBody.Part createFormData(String name,String value):**这种方法用来实现上传普通的字符串参数  
* **static MultipartBody.Part createFormData(String name,String filename,RequestBody body):**这种方式用来提交参数类型为一个**文件**的情况；第一个参数的意思是后台约定的该文件参数的**key**；第二个参数的意思是文件上传到后台的名字，该参数随意赋值就行；第三个参数就是RequestBody通过文件构建的对象了  
* Headers headers()  
* RequestBody body()  
 
#### 1.8 MultipartBody.Builder  
同样，MultipartBody也是通过构建者模式来创建实例的  

* MultipartBody.Builder addFormDataPart(String name,String value):类似于FormBody的形式，添加一个字符串参数  
* MultipartBody.Builder addFormDataPart(String name,String filename,RequestBody body):具体的参数意义和MultipartBody.Part的对应方法一样  
* MultipartBody.Builder addPart(Headers headers,RequestBody body)  
* MultipartBody.Builder addPart(MultipartBody.Part part)  
* MultipartBody.Builder addPart(RequestBody body)  
* **MultipartBody build():**返回最后得到的MultipartBody
* MultipartBody.Builder setType(MediaType type)  

可见，MultipartBody.Builder的这些方法，都能在MultipartBody.Part中找到对应的方法，所以，猜测本质还是调用的后者的相应的方法实现的  

#### 1.9 MediaType  
首先看看文档对这个类的描述：  

> An RFC 2045 Media Type, appropriate to describe the content type of an HTTP request or response body.  

说白了，这个类就是对**请求体/响应体**内容的一个描述  

* Charset charset():返回这个媒体类型采用的字符集，如果没有指定的话返回null  
* Charset charset(Charset defaultValue):同上，只不过当没用指定的时候返回参数指定的默认值  
* static MediaType parse(String string):创建一个MediaType实例,如"text/plain"，如果字符串对应的不合法的话，返回null。
* String type():返回媒体类型的**high-level的媒体类型**，比如"text","image"
* String subtype():返回一个**具体的媒体类型**，比如"plain","png"等  

#### 1.10 Cache  
关于缓存的知识：[点击这里跳转](https://blog.csdn.net/briblue/article/details/52920531)  
Cache用来设置缓存，当没有网络的时候，可以读取缓存的数据、同时也可以跳过网络，直接从缓存中获取数据  

构造器：  
* **Cache(File directory,long maxSize)**  

方法：  

* void initialize():初始化缓存。 这将包括从存储中读取日志文件并建立必要的内存中缓存信息。
初始化时间可能会因日志文件大小和当前的实际高速缓存大小而异.**应用程序需要知道在初始化阶段调用此函数，最好是在后台工作线程中。**
请注意，如果应用程序选择不调用此方法来初始化缓存。 默认情况下，okhttp会在第一次使用缓存时执行延迟初始化。
* void delete():关闭缓存，并且删除其中保存的所有值，这将删除缓存路径下的所有文件  
* void evictAll():删除存储在缓存中的所有值。 对缓存的正在进行的写入将正常完成，但相应的响应将不会被存储。  
* int writeAbortCount()
* int writeSuccessCount()
* long size()
* long maxSize()
* void flush()
* void close()
* File directory()
* boolean isClosed()
* int networkCount()
* int hitCount()
* int requestCount()

#### 1.11 CacheControl  
官方文档对其的描述是：  

> A Cache-Control header with cache directives from a server or client. These directives set policy on what responses can be stored, and which requests can be satisfied by those stored responses.
> 带有来自服务器或客户端的缓存指令的Cache-Control标头。 这些指令设置了可以存储什么响应的策略，以及那些存储的响应可以满足哪些请求。  

这个类提供有两个常量：  

* static CacheControl:FORCE_CACHE:缓存控制请求指令仅使用缓存，即使缓存的响应过时也是如此。
* static CacheControl:FORCE_NETWORK:缓存控制请求指令需要对响应进行网络验证。  

提供的方法有：  

* boolean immutable()
* boolean isPrivate()
* boolean isPublic()
* int maxAgeSeconds():
* int maxStaleSeconds()
* int minFreshSeconds()
* boolean mustRevalidate()
* boolean noCache():
* boolean noStore():
* boolean noTransform()
* boolean onlyIfCached():
* static CacheControl parse(Headers headers)
* int sMaxAgeSeconds():  

看到这里，我们要分清的一点是，**CacheControl才是控制Cache策略的类，而之前的Cache只不过是指明了缓存的路径和大小，根据两个类提供的方法就可以看出；同时，两个类在Okhttp的使用时机不同，Cache是在OkhttpClient创建的时候添加的，而CacheControl是在Request创建的时候添加的。**而且，**Cache-control是由服务器返回的Response中添加的头信息，它的目的是告诉客户端是要从本地读取缓存还是直接从服务器摘取消息**

#### 1.12 CacheControl.Builder  

* **CacheControl build()**  
* CacheControl.Builder immutable()
* CacheControl.Builder maxAge(int maxAge,TimeUnit timeUnit):设置缓存响应的最大时间
* CacheControl.Builder maxStale(int maxStale,TimeUnit timeUnit):
* CacheControl.Builder minFresh(int minFresh,TimeUnit timeUnit):
* CacheControl.Builder noCache()
* CacheControl.Builder noStore()
* CacheControl.Builder noTransform()
* CacheControl.Builder onlyIfCached()


