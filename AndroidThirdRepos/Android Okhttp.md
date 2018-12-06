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

## 二、Okhttp源码学习  
> [参考文章](http://liuwangshu.cn/application/network/7-okhttp3-sourcecode.html)  

### 1. Okhttp异步请求中onFailure和onResponse方法被回调时机  

事件传递到Dispatcher的过程是：newCall方法返回一个RealCall对象(Call接口的实现类)，RealCall方法的enqueue方法内部先获取Dispatcher对象，然后调用Dispatcher的enqueue(AsyncCall)方法最终执行enqueue：  

**Dispatcher的enqueue(AsyncCall)方法：**  

	synchronized void enqueue(AsyncCall call) {
	  if (runningAsyncCalls.size() < maxRequests && runningCallsForHost(call) < maxRequestsPerHost) {
	    runningAsyncCalls.add(call);
	    executorService().execute(call);
	  } else {
	    readyAsyncCalls.add(call);
	  }
	}  

可见该方法的思想是：**如果当前正在运行的异步请求队列中的数量小于64并且正在运行的请求主机数小于5时则把请求加载到runningAsyncCalls中并在线程池中执行，否则就再入到readyAsyncCalls中进行缓存等待。**    

到此，事件流执行到了**executorService().execute(call)**，而这里的call参数是一个AsyncCall对象，其内部也实现了execute方法：  

	@Override protected void execute() {
	  boolean signalledCallback = false;
	  try {
	    Response response = getResponseWithInterceptorChain(forWebSocket);
	    if (canceled) {
	      signalledCallback = true;
	      responseCallback.onFailure(RealCall.this, new IOException("Canceled"));
	    } else {
	      signalledCallback = true;
	      responseCallback.onResponse(RealCall.this, response);
	    }
	  } catch (IOException e) {
	    if (signalledCallback) {
	      // Do not signal the callback twice!
	      logger.log(Level.INFO, "Callback failure for " + toLoggableString(), e);
	    } else {
	      responseCallback.onFailure(RealCall.this, e);
	    }
	  } finally {
	    client.dispatcher().finished(this);
	  }
	}  

可见**我们重写的onResponse和onFailure方法终于在这里被回调了**。  

但是有一个问题来了，这里方法被回调完了，代表着网络请求完成了，可是我们的线程池好像并没有发挥应有的效果啊？别慌，finally块中的代码是一定会执行的：  

	synchronized void finished(AsyncCall call) {
	   if (!runningAsyncCalls.remove(call)) throw new AssertionError("AsyncCall wasn't running!");
	   promoteCalls();
	 }  

	private void promoteCalls() {
	  if (runningAsyncCalls.size() >= maxRequests) return; // Already running max capacity.
	  if (readyAsyncCalls.isEmpty()) return; // No ready calls to promote.
	  for (Iterator<AsyncCall> i = readyAsyncCalls.iterator(); i.hasNext(); ) {
	    AsyncCall call = i.next();
	
	    if (runningCallsForHost(call) < maxRequestsPerHost) {
	      i.remove();
	      runningAsyncCalls.add(call);
	      executorService().execute(call);
	    }
	    if (runningAsyncCalls.size() >= maxRequests) return; // Reached max capacity.
	  }
	}  

思想就是：**当一个网络请求完成了以后，就将其从正在执行的队列中取出，从等待队列中取出一个放入正在执行的队列中，达到复用的效果**  

### 2. Interceptor之后的动作  

这时候网络请求的流程就传递到了**Response response = getResponseWithInterceptorChain(forWebSocket)**中：  

	private Response getResponseWithInterceptorChain(boolean forWebSocket) throws IOException {
	  Interceptor.Chain chain = new ApplicationInterceptorChain(0, originalRequest, forWebSocket);
	  return chain.proceed(originalRequest);
	}  

	@Override public Response proceed(Request request) throws IOException {
	  // If there's another interceptor in the chain, call that.
	  if (index < client.interceptors().size()) {
	    Interceptor.Chain chain = new ApplicationInterceptorChain(index + 1, request, forWebSocket);
	    //从拦截器列表取出拦截器
	    Interceptor interceptor = client.interceptors().get(index);
	    Response interceptedResponse = interceptor.intercept(chain);
	
	    if (interceptedResponse == null) {
	      throw new NullPointerException("application interceptor " + interceptor
	          + " returned null");
	    }
	
	    return interceptedResponse;
	  }
	
	  // No more interceptors. Do HTTP.
	  return getResponse(request, forWebSocket);
	}  

一个重要的概念是，拦截器如果不只有一个的话(**也就是index < client.interceptors().size()当前拦截器不是最后一个**)，下面三行代码会被执行：  

	Interceptor.Chain chain = new ApplicationInterceptorChain(index + 1, request, forWebSocket);
    //从拦截器列表取出拦截器
    Interceptor interceptor = client.interceptors().get(index);
    Response interceptedResponse = interceptor.intercept(chain);  

其中，**不同的拦截器的intercept方法实现不同，但是里面还是会回调Interceptor.Chain的proceed方法，以此来达到一种递归，直到所有的拦截器都执行过**  

> 我们知道，**拦截器的作用是拦截Response从而对其进行一些加工，所以中间的拦截器返回的response都不是最终的结果，最终的结果一定是最后一个拦截器调用Chain的proceed方法，proceed方法中的return getResponse(request, forWebSocket)方法返回的**  


	Response getResponse(Request request, boolean forWebSocket) throws IOException {
	 ...省略
	    // Create the initial HTTP engine. Retries and redirects need new engine for each attempt.
	    engine = new HttpEngine(client, request, false, false, forWebSocket, null, null, null);
	
	    int followUpCount = 0;
	    while (true) {
	      if (canceled) {
	        engine.releaseStreamAllocation();
	        throw new IOException("Canceled");
	      }
	
	      boolean releaseConnection = true;
	      try {
	        engine.sendRequest();
	        engine.readResponse();
	        releaseConnection = false;
	      } catch (RequestException e) {
	        // The attempt to interpret the request failed. Give up.
	        throw e.getCause();
	      } catch (RouteException e) {
	        // The attempt to connect via a route failed. The request will not have been sent.
	  ...省略     
	    }
	  }  

可见，最终的请求的发起是HttpEngine.sendRequest方法执行的，返回体是HttpEngine.readResponse方法得到的。    

**sendRequest缓存策略：**  

	public void sendRequest() throws RequestException, RouteException, IOException {
	   if (cacheStrategy != null) return; // Already sent.
	   if (httpStream != null) throw new IllegalStateException();
	   //请求头部添加
	   Request request = networkRequest(userRequest);
	   //获取client中的Cache,同时Cache在初始化的时候会去读取缓存目录中关于曾经请求过的所有信息。
	   InternalCache responseCache = Internal.instance.internalCache(client);
	   //cacheCandidate为上次与服务器交互缓存的Response
	   Response cacheCandidate = responseCache != null
	       ? responseCache.get(request)
	       : null;
	
	   long now = System.currentTimeMillis();
	   //创建CacheStrategy.Factory对象，进行缓存配置
	   cacheStrategy = new CacheStrategy.Factory(now, request, cacheCandidate).get();
	   //网络请求
	   networkRequest = cacheStrategy.networkRequest;
	   //缓存的响应
	   cacheResponse = cacheStrategy.cacheResponse;
	
	   if (responseCache != null) {
	    //记录当前请求是网络发起还是缓存发起
	     responseCache.trackResponse(cacheStrategy);
	   }
	   if (cacheCandidate != null && cacheResponse == null) {
	     closeQuietly(cacheCandidate.body()); // The cache candidate wasn't applicable. Close it.
	   }
	   //不进行网络请求并且缓存不存在或者过期则返回504错误
	   if (networkRequest == null && cacheResponse == null) {
	     userResponse = new Response.Builder()
	         .request(userRequest)
	         .priorResponse(stripBody(priorResponse))
	         .protocol(Protocol.HTTP_1_1)
	         .code(504)
	         .message("Unsatisfiable Request (only-if-cached)")
	         .body(EMPTY_BODY)
	         .build();
	     return;
	   }
	   // 不进行网络请求，而且缓存可以使用，直接返回缓存
	   if (networkRequest == null) {
	     userResponse = cacheResponse.newBuilder()
	         .request(userRequest)
	         .priorResponse(stripBody(priorResponse))
	         .cacheResponse(stripBody(cacheResponse))
	         .build();
	     userResponse = unzip(userResponse);
	     return;
	   }
	   //需要访问网络时
	   boolean success = false;
	   try {
	     httpStream = connect();
	     httpStream.setHttpEngine(this);
	
	     if (writeRequestHeadersEagerly()) {
	       long contentLength = OkHeaders.contentLength(request);
	       if (bufferRequestBody) {
	         if (contentLength > Integer.MAX_VALUE) {
	           throw new IllegalStateException("Use setFixedLengthStreamingMode() or "
	               + "setChunkedStreamingMode() for requests larger than 2 GiB.");
	         }
	
	         if (contentLength != -1) {
	           // Buffer a request body of a known length.
	           httpStream.writeRequestHeaders(networkRequest);
	           requestBodyOut = new RetryableSink((int) contentLength);
	         } else {
	           // Buffer a request body of an unknown length. Don't write request headers until the
	           // entire body is ready; otherwise we can't set the Content-Length header correctly.
	           requestBodyOut = new RetryableSink();
	         }
	       } else {
	         httpStream.writeRequestHeaders(networkRequest);
	         requestBodyOut = httpStream.createRequestBody(networkRequest, contentLength);
	       }
	     }
	     success = true;
	   } finally {
	     // If we're crashing on I/O or otherwise, don't leak the cache body.
	     if (!success && cacheCandidate != null) {
	       closeQuietly(cacheCandidate.body());
	     }
	   }
	 }



