# Android Service  
## 认识Service  
官方对服务的定义：  

>是一个可以在后台执行长时间运行操作而不提供用户界面的应用组件。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。 例如，服务可以处理网络事务、播放音乐，执行文件 I/O 或与内容提供程序交互，而所有这一切均可在后台进行。

---

>注意：**Service默认在主线程中运行，它既不创建自己的线程，也不在单独的进程中运行（除非另行指定）**。 所以建议大家在CPU 密集型工作(如:播放音乐)或阻止性操作(联网:需要等待)的操作,则应在服务内创建新线程来完成这项工作。这样可以降低发生“应用无响应”(ANR) 错误的风险。  

>我的理解：即使Service可以通过在其内部新开线程来进行耗时操作，但由于Activity和Service都是在主线程中运行，Activity也可以新开线程进行耗时操作啊。所以，Service中进行的耗时操作应该是不需要与用户进行交互的，而Activity中的是需要与用户进行交互的。比如一个是加载网络图片，一个是下载图片；由于前者是要给用户看的，所以不能再Service中进行；而后者不需要用户看，最好是在Service中完成。

我们从上面可以看到，Service和线程还是有些不一样的：  

* **Service默认在主线程运行**，我们不允许在UI线程进行耗时操作其中一个原因就是耗时操作会导致界面卡顿，影响用户的体验；如果我们直接在Service的实现类中进行耗时操作，那么由于是在主线程中进行的，所以还是会有卡顿的出现；我们可以在Service中新开一个线程进行耗时操作。
>在Activity和Service的onCreate方法中分别打印一下logd（"","ThreadId is "+Thread.currentThread().getName());可以看到都是main  

* **Android的后台指的是它的运行时完全不依赖UI的，即使Activity被销毁，或者程序被关闭，只要进程还在，Service就可以继续运行。**这是在Activity中新开一个线程无法实现的。  
* Activity很难对Thread进行控制，当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例。而且在一个Activity中创建的子线程，另一个Activity无法对其进行操作。但是Service就不同了，所有的Activity都可以与Service进行关联(通过绑定)，然后可以很方便地操作其中的方法，即使Activity被销毁了，之后只要重新与Service建立关联，就又能够获取到原有的Service中Binder的实例。因此，使用Service来处理后台任务，Activity就可以放心地finish，完全不需要担心无法对后台任务进行控制的情况。

## Service生命周期  
![](https://user-gold-cdn.xitu.io/2017/2/12/1f63b288a3c2df14f57f897537ee88c6?imageslim) 
### 使用  
由上图可以看到，Service有两种使用方式，一个还是通过startService()方式，另一个是通过bindService()方式。 
>Service在使用前要在清单文件中声明这个Service 
#### startService(Intent)方式  
这种方式启动的Service可以说有“无限的运行期”，这里的无限表示的意思是不在受其他控件的约束，除非调用stopService(Intent)；按照这个方式启动的话，只要重写onCreate(),onStartCommand(),onDestory()就可以了。
>注意：**假如我们是通过点击Button执行上面的代码,那么第一次点击的时候回执行其中的onCreate()跟onStartCommand()方法,但是当我们第二次点击的时候就只会执行onStartCommand()方法了**,同Activity如果没销毁实例，onCreate方法只会被调用一次.

使用方式：  

	Intent startIntent=new Intent(this,MyService.class);
	startService(startIntent);

#### stopService(Intent)停止  
通过startService方式启动的Service想要主动停止的话通过stopService（Intent）方法就可以了  
#### bindService(Intent)方法  
在继承Service类的时候会发现**onBind(Intent)方法是必须重写的一个方法，这个方法返回的是一个IBinder类，这个类也需要我们继承IBinder类来自定义一个。**这个方法就是在bindService(Intent,ServiceConnection,int)方法启动Service后回调的，回调的结果是在第二个参数的onServiceConnected(ComponentName name,IBinder service)中的第二个参数。
下面就来看一个通过该方法实现Service和Activity通信的例子：  

	public class MyService extends Service {

    public static final String TAG = "MyService";

    private MyBinder mBinder = new MyBinder();

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "onCreate() executed");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.d(TAG, "onStartCommand() executed");
        return super.onStartCommand(intent, flags, startId);
    }
	@Override
    public void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy() executed");
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    class MyBinder extends Binder {

        public void startDownload() {
            Log.d("TAG", "startDownload() executed");
            // 执行具体的下载任务
        }

    }

	}

接下来就是在Activity中通过Button绑定和解绑：

	public class MainActivity extends Activity implements OnClickListener {

    private Button bindService;

    private Button unbindService;

    private MyService.MyBinder myBinder;

    private ServiceConnection connection = new ServiceConnection() {

        @Override
        public void onServiceDisconnected(ComponentName name) {
        }

        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            myBinder = (MyService.MyBinder) service;
            myBinder.startDownload();
        }
    };
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bindService = (Button) findViewById(R.id.bind_service);
        unbindService = (Button) findViewById(R.id.unbind_service);
        bindService.setOnClickListener(this);
        unbindService.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
        case R.id.bind_service:
            Intent bindIntent = new Intent(this, MyService.class);
            bindService(bindIntent, connection, BIND_AUTO_CREATE);
            break;
        case R.id.unbind_service:
            unbindService(connection);
            break;
        default:
            break;
        }
    }

	}
#### unBindService(ServiceConnection)解除绑定  
如果通过bindService()来启动的Service，解绑的话要调用unBindService(ServiceConnection)方法来进行解绑，**该方法的参数是ServiceConnection实例，该实例就是bindService（）方法中的第二个参数。**  

>两种方法都能够启动一个Service，但是可以看到，通过绑定的方式启动一个Service可以通过IBinder这个返回值来和Activity进行通信；而通过starService的方式就不可以。但是通过绑定形式启动Service与Activity进行通信是非常有限的，因为IBinder类是在Service绑定之前就写死了的，Activity无法回传给Service，这更像一种单方面的通信，比如Service的IBinder就无法控制View控件的行为，即使他们都是在主线程中
### 销毁Service  
如果只是单一的通过startService或者bindService方法启动的Service，那么销毁Service就可以通过stopService或者unBindService方法进行销毁；但是如果两个方法都调用了一遍呢？那么这时候stopService和unBindService也都得调用一下才行，只调用其中的一个方法是不行的；**如果绑定Service的Context销毁了，比如Activity调用了finish方法，unBindService方法可以不必调用了，调用stopService就可以了**  
>**一个Service必须要在既没有和任何Activity关联又处理停止状态的时候才会被销毁。**  
### 前后台Service  
Service默认创建的都是后台Service，**前台Service和普通Service最大的区别就在于，它会一直有一个正在运行的图标在系统的状态栏显示，下拉状态栏后可以看到更加详细的信息，非常类似于通知的效果。**后台运行的Service一直以来都是默默地做着辛苦的工作，系统优先级还是比较低的，很容易被杀死，这时候就可以考虑使用前台Service。  
来看看实现一个前台Service：  

	public class MyService extends Service {  

    public static final String TAG = "MyService";  

    private MyBinder mBinder = new MyBinder();  

    @Override  
    public void onCreate() {  
        super.onCreate();  
        Notification notification = new Notification(R.drawable.ic_launcher,  
                "有通知到来", System.currentTimeMillis());  
        Intent notificationIntent = new Intent(this, MainActivity.class);  
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 0,  
                notificationIntent, 0);  
        notification.setLatestEventInfo(this, "这是通知的标题", "这是通知的内容",  
                pendingIntent);  
        startForeground(1, notification);  
        Log.d(TAG, "onCreate() executed");  
    }  

    .........  
	}
这里只是修改了MyService中onCreate()方法的代码。可以看到，我们首先创建了一个Notification对象，然后调用了它的setLatestEventInfo()方法来为通知初始化布局和数据，并在这里设置了点击通知后就打开MainActivity。然后调用startForeground()方法就可以让MyService变成一个前台Service，并会将通知的图片显示出来。
如果我们要从前台移除服务，请调用stopForeground()方法，这个方法接受个布尔参数，表示是否同时移除状态栏通知。此方法不会终止服务。不过，如果服务在前台运行时被你终止了，那么通知也会同时被移除。

