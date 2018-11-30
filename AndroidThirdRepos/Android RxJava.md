# Android RxJava再学习  
>文章参考：[http://gank.io/post/560e15be2dca930e00da1083](http://gank.io/post/560e15be2dca930e00da1083)  

## 一、RxJava中的观察者模式  
RxJava 有四个基本概念：**Observable (可观察者，即被观察者)、 Observer (观察者)、 subscribe (订阅)、事件。**Observable 和 Observer 通过 **subscribe() 方法实现订阅关系**，从而 Observable 可以在需要的时候发出事件来通知 Observer。  

与传统观察者模式不同， RxJava 的事件回调方法除了普通事件 onNext() （相当于 onClick() / onEvent()）之外，还定义了两个特殊的事件：onCompleted() 和 onError()。  

* **onCompleted():** 事件队列完结。RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。RxJava 规定，当不会再有新的 onNext() 发出时，需要触发 onCompleted() 方法作为标志。  
* **onError()**: 事件队列异常。在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出。  
* 在一个正确运行的事件序列中, onCompleted() 和 onError() 有且只有一个，并且是事件序列中的最后一个。需要注意的是，onCompleted() 和 onError() **二者也是互斥的**，即在队列中调用了其中一个，就不应该再调用另一个。  

>RxJava作为一种观察者模式的设计，通过subscribe()方法将观察者和被观察者进行*关联*，这时候，被观察者**隐式地持有了一个观察者的引用**，而这可能导致**内存泄漏**，后面我们会学到，在必要的地方进行二者绑定的取消，从而避免这种情况的发生。 

## 二、基本实现  
### 2.1 观察者--Observer  
Observer即观察者，它决定事件触发的时候进行怎样的处理，RxJava中的Observer接口的实现方式：  

	Observer<String> observer = new Observer<String>() {
    	@Override
    	public void onNext(String s) {
        	Log.d(tag, "Item: " + s);
    	}

    	@Override
    	public void onCompleted() {
        	Log.d(tag, "Completed!");
    	}

    	@Override
    	public void onError(Throwable e) {
        	Log.d(tag, "Error!");
    	}
	};  

除了 Observer 接口之外，RxJava 还内置了一个实现了 Observer 的**抽象类：Subscriber**。 Subscriber 对 Observer 接口进行了一些扩展，但他们的基本使用方式是完全一样的：  

	Subscriber<String> subscriber = new 	Subscriber<String>() {
    	@Override
    	public void onNext(String s) {
        	Log.d(tag, "Item: " + s);
    	}

    	@Override
    	public void onCompleted() {
        	Log.d(tag, "Completed!");
    	}

    	@Override
    	public void onError(Throwable e) {
        	Log.d(tag, "Error!");
    	}
	};  
实际上，RxJava在subscribe过程中，**observer也总是会先被转换成一个Subscriber**再使用，二者的区别主要有两点：  

1. **onStart()**: 这是 Subscriber 增加的方法。它会在 subscribe 刚开始，而**事件还未发送之前被调用**，可以用于做一些准备工作，例如数据的清零或重置。这是一个可选方法，默认情况下它的实现为空。需要注意的是，如果对准备工作的线程有要求（例如弹出一个显示进度的对话框，这必须在主线程执行）， onStart() 就不适用了，因为它**总是在 subscribe 所发生的线程被调用，而不能指定线程**。要在指定的线程来做准备工作，可以使用 **doOnSubscribe()** 方法，具体可以在后面的文中看到。  
2. **unsubscribe()**: 这是 Subscriber 所实现的另一个接口 Subscription 的方法，**用于取消订阅**。在这个方法被调用后，Subscriber 将不再接收事件。一般在这个方法调用前，可以使用 isUnsubscribed() 先判断一下状态。 unsubscribe() 这个方法很重要，因为在 subscribe() 之后， Observable 会持有 Subscriber 的引用，这个引用如果不能及时被释放，将有内存泄露的风险。所以最好保持一个原则：要在不再使用的时候尽快在合适的地方（例如 onPause() onStop() 等方法中）调用 unsubscribe() 来解除引用关系，以避免内存泄露的发生。  

>个人感觉Subscriber功能更强大，直接用这个不就好了？为什么还要提供Observer

### 2.2 被观察者--Observable  
Observable 即被观察者，它决定什么时候触发事件以及触发怎样的事件。 RxJava 使用 **create()** 方法来创建一个 Observable ，并为它定义事件触发规则：  

	Observable observable = Observable.create(new Observable.OnSubscribe<String>() {
    	@Override
    	public void call(Subscriber<? super String> 	subscriber) {
        	subscriber.onNext("Hello");
        	subscriber.onNext("Hi");
        	subscriber.onNext("Aloha");
        	subscriber.onCompleted();
    	}
	});  

可以看到，这里传入了一个 OnSubscribe 对象作为参数。OnSubscribe 会被存储在返回的 Observable 对象中，它的作用相当于一个计划表，当 Observable 被订阅的时候，OnSubscribe 的 call() 方法会自动被调用，事件序列就会依照设定依次触发（对于上面的代码，就是观察者Subscriber 将会被调用三次 onNext() 和一次 onCompleted()）。这样，由被观察者调用了观察者的回调方法，就实现了由被观察者向观察者的事件传递，即观察者模式。  

>刚复习到这里的时候，我有点茫然，根本不知道这么个创建被观察者的方式。因为我使用RxJava都是和Retrofit一起使用的，而Retrofit中创建Observable是通过定义的接口的返回值得到的，并没有这么麻烦。  

### 2.3 订阅--subscribe()  
创建了 Observable 和 Observer 之后，再用 subscribe() 方法将它们联结起来，整条链子就可以工作了。代码形式很简单：  

	observable.subscribe(observer);
	// 或者：
	observable.subscribe(subscriber);  

Observable.subscribe(Subscriber) 的内部实现是这样的（仅核心代码）：  

	// 注意：这不是 subscribe() 的源码，而是将源码中与性能、兼容性、扩展性有关的代码剔除后的核心代码。
	// 如果需要看源码，可以去 RxJava 的 GitHub 仓库下载。
	public Subscription subscribe(Subscriber subscriber) {
    	subscriber.onStart();
    	onSubscribe.call(subscriber);
    	return subscriber;
	}  

可以看到，subscriber() 做了3件事：  

* 调用 Subscriber.onStart() 。这个方法在前面已经介绍过，是一个可选的准备方法。  
* 调用 Observable 中的 OnSubscribe.call(Subscriber) 。在这里，事件发送的逻辑开始运行。从这也可以看出，在 RxJava 中， Observable 并不是在创建的时候就立即开始发送事件，而是在它被订阅的时候，即当 subscribe() 方法执行的时候。  
* 将传入的 Subscriber 作为 Subscription 返回。这是为了方便 unsubscribe().  

## 三、线程控制--Scheduler  
在不指定线程的情况下， RxJava 遵循的是线程不变的原则，即：在哪个线程调用 subscribe()，就在哪个线程生产事件；在哪个线程生产事件，就在哪个线程消费事件。如果需要切换线程，就需要用到 Scheduler （调度器）。  

### 3.1 Scheduler的API  
在RxJava 中，Scheduler ——调度器，相当于线程控制器，RxJava 通过它来指定每一段代码应该运行在什么样的线程。RxJava 已经内置了几个 Scheduler ，它们已经适合大多数的使用场景：  

* **Schedulers.immediate()**: 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
* **Schedulers.newThread()**: 总是启用新线程，并在新线程执行操作。
* **Schedulers.io()**: I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler。行为模式和 newThread() 差不多，**区别在于 io() 的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程**，因此多数情况下 io() 比 newThread() 更有效率。不要把计算工作放在 io() 中，可以避免创建不必要的线程。  
* Schedulers.computation(): 计算所使用的 Scheduler。这个**计算指的是 CPU 密集型计算**，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。  
* 另外， Android 还有一个专用的 AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。

### 3.2 线程的控制  
有了上面几个Scheduler后，就可以通过相应的方法来进行线程的控制了。  

* **subscribeOn(Schedulers)**:指定subscribe方法发生的线程，即**事件发生的线程**  
* **observeOn(Schedulers)**:指定observer方法发生的线程，即**事件处理的线程**，也即观察者的onNext、onCompleted、onError方法发生的线程  

>注意：事件发生的线程是subscribe方法发生的线程，并不是Observable实例被初始化的线程。  

*问题：使用Retrofit时，Observable的获取是通过Retrofit的接口获取的，怎么感觉是得到Observable时就是已经进行了网络访问啊？*
  
## 四、变换
**所谓变换，就是将事件序列中的对象或整个序列进行加工处理，转换成不同的事件或事件序列。**  

### 4.1 一对一的变换--map()
  
	Observable.just("images/logo.png") // 输入类型 String
    	.map(new Func1<String, Bitmap>() {
        	@Override
        	public Bitmap call(String filePath) { // 参数类型 String
            	return getBitmapFromPath(filePath); // 返回类型 Bitmap
        	}
    	})
    	.subscribe(new Action1<Bitmap>() {
        	@Override
        	public void call(Bitmap bitmap) { // 参数类型 Bitmap
            	showBitmap(bitmap);
        	}
    });

这里出现了一个叫做 **Func1** 的类。它和 **Action1** 非常相似，也是 RxJava 的一个接口，用于包装含有一个参数的方法。 **Func1** 和 **Action** 的区别在于， **Func1** 包装的是有返回值的方法。另外，和 **ActionX** 一样， **FuncX** 也有多个，用于不同参数个数的方法。**FuncX** 和 **ActionX** 的区别在 **FuncX** 包装的是有返回值的方法。

可以看到，**map()** 方法将参数中的 **String** 对象转换成一个 **Bitmap** 对象后返回，而在经过 **map()** 方法后，事件的参数类型也由 **String** 转为了 **Bitmap**。这种直接变换对象并返回的，是最常见的也最容易理解的变换。不过 RxJava 的变换远不止这样，它不仅可以针对事件对象，还可以针对整个事件队列，这使得 RxJava 变得非常灵活。  

### 4.2 一对多的变换--flatMap()  
那么再假设：如果要打印出每个学生所需要修的所有课程的名称呢？（需求的区别在于，每个学生只有一个名字，但却有多个课程。）首先可以这样实现：  

	Student[] students = ...;
	Subscriber<Student> subscriber = new Subscriber<Student>() {
    	@Override
    	public void onNext(Student student) {
        	List<Course> courses = student.getCourses();
        	for (int i = 0; i < courses.size(); i++) {
            	Course course = courses.get(i);
            	Log.d(tag, course.getName());
        	}
    	}
    	...
	};
	Observable.from(students)
    .subscribe(subscriber);  

依然很简单。那么如果我不想在 Subscriber 中使用 for 循环，而是希望 Subscriber 中直接传入单个的 Course 对象呢（这对于代码复用很重要）？用 map() 显然是不行的，因为 map() 是一对一的转化，而我现在的要求是一对多的转化。那怎么才能把一个 Student 转化成多个 Course 呢？  

这个时候，就需要用 flatMap() 了：  

	Student[] students = ...;
		Subscriber<Course> subscriber = new Subscriber<Course>() {
    	@Override
    	public void onNext(Course course) {
        	Log.d(tag, course.getName());
    	}
    	...
	};
	Observable.from(students)
    	.flatMap(new Func1<Student, Observable<Course>>() {
        	@Override
        	public Observable<Course> call(Student student) {
            	return Observable.from(student.getCourses());
        	}
    	})
    	.subscribe(subscriber);  

和 map() 不同的是， **flatMap()** 中返回的是个 **Observable** 对象，并且这个 Observable 对象**并不是被直接发送到了 Subscriber 的回调方法中**。 flatMap() 的原理是这样的：1. 使用传入的事件对象创建一个 Observable 对象；2. 并不发送这个 Observable, 而是将它激活，于是它开始发送事件；3. 每一个创建出来的 Observable 发送的事件，都**被汇入同一个 Observable** ，而这个 Observable 负责将这些事件**统一**交给 **Subscriber** 的回调方法。  

### 4.3 变换的原理--lift()  
这些变换虽然功能各有不同，但实质上都是**针对事件序列的处理和再发送**。而在 RxJava 的内部，它们是基于同一个**基础的变换方法： lift(Operator)**。首先看一下 **lift()** 的内部实现（仅核心代码）：  

	// 注意：这不是 lift() 的源码，而是将源码中与性能、兼容性、扩展性有关的代码剔除后的核心代码。
	// 如果需要看源码，可以去 RxJava 的 GitHub 仓库下载。
	public <R> Observable<R> lift(Operator<? extends R, ? super T> operator) {
    	return Observable.create(new OnSubscribe<R>() {
        	@Override
        	public void call(Subscriber subscriber) {
            	Subscriber newSubscriber = operator.call(subscriber);
            	newSubscriber.onStart();
            	onSubscribe.call(newSubscriber);
        	}
    	});
	}  

这段代码很有意思：它生成了一个新的 **Observable** 并返回，而且创建新 **Observable** 所用的参数 **OnSubscribe** 的回调方法 **call()** 中的实现竟然看起来和前面讲过的 **Observable.subscribe()** 一样！然而它们并不一样哟~不一样的地方关键就在于第二行 **onSubscribe.call(subscriber)** 中的 **onSubscribe** 所指代的对象不同（高能预警：接下来的几句话可能会导致身体的严重不适）——  

* **subscribe()** 中这句话的 **onSubscribe** 指的是 **Observable** 中的 **onSubscribe** 对象，这个没有问题，但是 **lift()** 之后的情况就复杂了点。  
* 当含有 **lift()** 时：   
	* lift() 创建了一个 Observable 后，加上之前的原始 Observable，已经有**两个 Observable 了**；而同样地，新 Observable 里的新 OnSubscribe 加上之前的原始 Observable 中的原始 OnSubscribe，也就有了**两个 OnSubscribe**； 
	* 当用户调用经过 lift() 后的 Observable 的 subscribe() 的时候，**使用的是 lift() 所返回的新的 Observable **，于是它所触发的 onSubscribe.call(subscriber)，也是用的**新 Observable 中的新 OnSubscribe**，即**在 lift() 中生成的那个 OnSubscribe**；
	* 而这个新 OnSubscribe 的 call() 方法中的 onSubscribe ，就是**指的原始 Observable 中的原始 OnSubscribe** ，在这个 call() 方法里，新 OnSubscribe 利用 operator.call(subscriber) 生成了一个新的 Subscriber（Operator 就是在这里，通过自己的 call() 方法将新 Subscriber 和原始 Subscriber 进行关联，并插入自己的『变换』代码以实现变换），然后利用这个新 Subscriber 向原始 Observable 进行订阅。  

> 总结一下lift()变换的原理：经过lift后返回了一个新的Observable(ObNew),之后再调用subscribe()方法的时候，其实是这个obNew去订阅，所以obNew内部的OnSubscribe(onSubNew)就决定了事件发生后如何进行处理，也即**onSubNew的call方法**，可是这个call方法的最终实现确实调用了**onSubscribe.call(newSubscriber)**来实现，这里的OnSubscribe则是**老的Observable的OnSubscribe**；
> 所以flatMap中所说的*被汇入同一个 Observable** ，而这个 Observable 负责将这些事件**统一**交给 **Subscriber** 的回调方法。*就是指老的那个Observable了