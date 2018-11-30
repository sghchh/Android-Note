# Android 跨进程通信--Binder(AIDL)  
## 一、Parcelable序列化  
**Serializable**接口是Java提供给我们的一个序列化的接口，而今天要说的**Parcelable**是Android为我们提供的一个序列化的接口，后者更加高效，但是实现的时候要复杂一些，需要重写一些方法。  
下面就来看看例子：  

	public class Book implements Parcelable{
    	public String bookName;

    	public Book(String name){
        	this.bookName=name;
    	}

    	public Book(Parcel in){
        	this.bookName=in.readString();
    	}

    	public static final Parcelable.Creator<Book> CREATOR=new Parcelable.Creator<Book>(){
        	@Override
        	public Book createFromParcel(Parcel source) {
            	return new Book(source);
        	}

        	@Override
        	public Book[] newArray(int size) {
            	return new Book[size];
        	}
    	};

    	@Override
    	public int describeContents() {
        	return 0;
    	}

    	@Override
    	public void writeToParcel(Parcel dest, int flags) {
        	dest.writeString(bookName);
    	}
	}   
暂时我知道的都是这么写的，比较套路，说麻烦也不麻烦。  

## 二、AIDL  
**AIDL**(Application Interface Define Language)应用接口定义语言，先看看如何通过AIDL实现Binder的跨进程通信：  

### 2.1 Entity的AIDL声明  
对于你将要实现Parcelable的一个Entity类，需要在AIDL文件中声明一下：  

	// Book.aidl
	package com.example.as.aidl;
	parcelable Book;  

就是这么简单。  
>注：如果先写好了Entity的Java类，此时再创建这个AIDL的文件的时候，名字是不能和那个Entity类的类名一样的，所以，我是先创建的AIDL文件，然后创建的Entity的类。
>有人说如果先创建了Entity的话，可以先给AIDL文件随便先起个名，然后在rename一下，可以试一试  

### 2.2 Entity的Java定义  
如果按照我的做法先声明的AIDL，那么第二步就是写Entity的Java定义了：  

	public class Book implements Parcelable{
    	public String bookName;

    	public Book(String name){
        	this.bookName=name;
    	}

    	public Book(Parcel in){
        	this.bookName=in.readString();
    	}

    	public static final Parcelable.Creator<Book> CREATOR=new Parcelable.Creator<Book>(){
        	@Override
        	public Book createFromParcel(Parcel source) {
            	return new Book(source);
        	}

        	@Override
        	public Book[] newArray(int size) {
            	return new Book[size];
        	}
    	};

    	@Override
    	public int describeContents() {
        	return 0;
    	}

    	@Override
    	public void writeToParcel(Parcel dest, int flags) {
        	dest.writeString(bookName);
    	}
	}     

### 2.3 真正的AIDL  
然后针对你想要实现怎样的跨进程的功能，将这些方法声明到一个或者多个AIDL文件中，在用到上面第一次创建的AIDL文件中声明的类的时候，要导入那个类，即使是在同一个包下也得导入：  

	// IBookManager.aidl
 	package com.example.as.aidl;
	//即使在同一个包下，也得导入
 	import com.example.as.aidl.Book;
 
 	interface IBookManager {
     List<Book> getBookList();
     void addBook(in Book book);
 	}  

这样就做完了接口的声明，点击Build->Make Project后，将当前的模式调成Project，就可以在**app->build->generated->source->aidl->debug->包名->**下看到这个真正的AIDL对应的**java**文件：  

	package com.example.as.aidl;
	public interface IBookManager extends android.os.IInterface{
		/** Local-side IPC implementation stub class. */
		public static abstract class Stub extends android.os.Binder implements com.example.as.aidl.IBookManager{
		private static final java.lang.String DESCRIPTOR = "com.example.as.aidl.IBookManager";
		/** Construct the stub at attach it to the interface. */
		public Stub(){
			this.attachInterface(this, DESCRIPTOR);
		}
		/**
 		* Cast an IBinder object into an com.example.as.aidl.IBookManager interface,
 		* generating a proxy if needed.
 		*/
		public static com.example.as.aidl.IBookManager asInterface(android.os.IBinder obj){
			if ((obj==null)) {
				return null;
			}
			android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
			if (((iin!=null)&&(iin instanceof com.example.as.aidl.IBookManager))) {
				return ((com.example.as.aidl.IBookManager)iin);
			}
			return new com.example.as.aidl.IBookManager.Stub.Proxy(obj);
		}
		@Override 
		public android.os.IBinder asBinder(){
			return this;
		}
		@Override 
		public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException{
			switch (code){
				case INTERFACE_TRANSACTION:{
					reply.writeString(DESCRIPTOR);
					return true;
				}
				case TRANSACTION_getBookList:{
					data.enforceInterface(DESCRIPTOR);
					java.util.List<com.example.as.aidl.Book> _result = this.getBookList();
					reply.writeNoException();
					reply.writeTypedList(_result);
					return true;
				}
				case TRANSACTION_addBook:{
					data.enforceInterface(DESCRIPTOR);
					com.example.as.aidl.Book _arg0;
					if ((0!=data.readInt())) {
						_arg0 = com.example.as.aidl.Book.CREATOR.createFromParcel(data);
					}
					else {
						_arg0 = null;
					}
					this.addBook(_arg0);
					reply.writeNoException();
					return true;
				}
			}
			return super.onTransact(code, data, reply, flags);
		}
		private static class Proxy implements com.example.as.aidl.IBookManager{
			private android.os.IBinder mRemote;
			Proxy(android.os.IBinder remote){
				mRemote = remote;
			}
			@Override 
			public android.os.IBinder asBinder(){
				return mRemote;
			}
			public java.lang.String getInterfaceDescriptor(){
				return DESCRIPTOR;
			}
			@Override 
			public java.util.List<com.example.as.aidl.Book> getBookList() throws android.os.RemoteException{
				android.os.Parcel _data = android.os.Parcel.obtain();
				android.os.Parcel _reply = android.os.Parcel.obtain();
				java.util.List<com.example.as.aidl.Book> _result;
				try {
					_data.writeInterfaceToken(DESCRIPTOR);
					mRemote.transact(Stub.TRANSACTION_getBookList, _data, _reply, 0);
					_reply.readException();
					_result = _reply.createTypedArrayList(com.example.as.aidl.Book.CREATOR);
				}
				finally {
					_reply.recycle();
					_data.recycle();
				}
				return _result;
			}
			@Override 
			public void addBook(com.example.as.aidl.Book book) throws android.os.RemoteException{
				android.os.Parcel _data = android.os.Parcel.obtain();
				android.os.Parcel _reply = android.os.Parcel.obtain();
				try {
						_data.writeInterfaceToken(DESCRIPTOR);
						if ((book!=null)) {
							_data.writeInt(1);
							book.writeToParcel(_data, 0);
							}
						else {
							_data.writeInt(0);
							}
						mRemote.transact(Stub.TRANSACTION_addBook, _data, _reply, 0);
						_reply.readException();
						}
						finally {
							_reply.recycle();
							_data.recycle();
						}
					}	
				}
				static final int TRANSACTION_getBookList = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
				static final int TRANSACTION_addBook = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
			}
			public java.util.List<com.example.as.aidl.Book> getBookList() throws android.os.RemoteException;
			public void addBook(com.example.as.aidl.Book book) throws android.os.RemoteException;
	}  

详细的知识有空再说。  

## 三、使用  
### 3.1 服务器端  
常见的方式是创建一个Service来监听客户的链接请求，且在该Service中实现AIDL中的使用到的方法：  

	public class BookService extends Service {

    	private CopyOnWriteArrayList<Book> list=new CopyOnWriteArrayList<Book>();
    	private Binder mBinder=new IBookManager.Stub() {
        	@Override
        	public List<Book> getBookList() throws RemoteException {
            	return list;
        	}

        	@Override
        	public void addBook(Book book) throws RemoteException {
            	list.add(book);
        	}
    	};

    	@Override
    	public void onCreate() {
        	super.onCreate();
        	list.add(new Book("Android 讲义"));
    	}

    	@Nullable
    	@Override
    	public IBinder onBind(Intent intent) {
        	return mBinder;
    	}
	}  

### 3.2 远程客户端的实现  
客户端首先要做的就是绑定远程服务，绑定成功后将服务端返回的Binder对象转化为AIDL接口，然后就可以通过这个接口去调用服务器端的远程方法了，实际上就是一个**代理**：  

	public class MainActivity extends AppCompatActivity {

    	private ServiceConnection connection=new ServiceConnection() {
        	@Override
        	public void onServiceConnected(ComponentName name, IBinder service) {
            	IBookManager manager=IBookManager.Stub.asInterface(service);
            	try{
                	List<Book> list=manager.getBookList();
                	Log.d("", "onServiceConnected: +++++++"+list.get(0).bookName);
                	Book book=new Book("JAVA讲义");
                	manager.addBook(book);
                	List<Book> list1=manager.getBookList();
                	Log.d("", "onServiceConnected: +++++++++++++"+list1.toString());
            	}
            	catch (RemoteException r){
                	r.printStackTrace();
            	}
        	}

        	@Override
        	public void onServiceDisconnected(ComponentName name) {

        	}
    	};

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);
        	Intent intent=new Intent(this,BookService.class);
        	bindService(intent,connection, Context.BIND_AUTO_CREATE);
    	}

    	@Override
    	protected void onDestroy() {
        	unbindService(connection);
        	super.onDestroy();
    	}
	}  


