# DI框架--Dagger 2  

## 实现原理  
先来看看我们的一个简单的列子：  

	/*User.java*/
	public class User {
	    public String name;
	    public int age;
	    public User(String name,int age){
	        this.name=name;
	        this.age=age;
	    }
	
	    public String toString(){
	        return "name"+this.name+",age:"+this.age;
	    }
	}  

	/*UserModule.java*/
	@Module
	public class UserModule{
	    public UserModule(){}
	
	    @Provides
	    public User provideUser(){
	        return new User("李明",20);
	    }
	}  

	/*MainActivity.java*/  
	@Inject lateinit var user: User
	
	DaggerMyCommponent.builder().userModule(UserModule()).build().inject(this)
        Log.d("",user.toString()+"+++++++++++++++++++++++++");  

上面是一个最简单的例子，我们来看看Dagger为我们的注释生成了神么？生成的类在**app/build/generated/apt/debug/**目录下：  

	/*UserModule_ProvideUserFactory.java*/  
	public final class UserModule_ProvideUserFactory implements Factory<User> {
	  private final UserModule module;
	
	  public UserModule_ProvideUserFactory(UserModule module) {
	    assert module != null;
	    this.module = module;
	  }
	
	  @Override
	  public User get() {
	    return Preconditions.checkNotNull(
	        module.provideUser(), "Cannot return null from a non-@Nullable @Provides method");
	  }
	
	  public static Factory<User> create(UserModule module) {
	    return new UserModule_ProvideUserFactory(module);
	  }
	}   
  
这个是为我们添加了**@Module**的**UserModule.java**生成的一个类。重点是这里生成了一个**get**方法，返回的是User，而该方法的具体实现就是调用了**我们定义在UserModuel的实例方法provideUser**；而得到**USerModule_ProviedUserFactory**实例的该类的静态方法**create**方法，但是这个方法的调用场景我们还不得而知。  

接下来我们来看看，**目标类(这里是Activity)**对应的生成类是什么：  

	public final class MainActivity_MembersInjector implements MembersInjector<MainActivity> {
	  private final Provider<User> userProvider;
	
	  public MainActivity_MembersInjector(Provider<User> userProvider) {
	    assert userProvider != null;
	    this.userProvider = userProvider;
	  }
	
	  public static MembersInjector<MainActivity> create(Provider<User> userProvider) {
	    return new MainActivity_MembersInjector(userProvider);
	  }
	
	  @Override
	  public void injectMembers(MainActivity instance) {
	    if (instance == null) {
	      throw new NullPointerException("Cannot inject members into a null reference");
	    }
	    instance.user = userProvider.get();
	  }
	}

回想我们在Activity中使用注入类实例的时候，并没有一处地方对注入类的实例变量进行过赋值操作，只是用了如下代码：**DaggerMyCommponent.builder().userModule(UserModule()).build().inject(this)**，之后便可以随意的访问我们的注入类的实例了。这要看看**MainActivity_MembersInjector**类中的**injectMembers**方法中有一段代码**instance.user = userProvider.get()**，没错，这里就为我们自动赋值了。同样，获取该类的实例的方法也是**create**方法，但是我们也不知道它具体的调用场合；还有一个问题就是这个**Provider<User>是如何传过来的？其get方法是怎么实现的？**
  
都说Commponent是目标类和注入类的桥梁，接下来我们就看一看是如何连接的：

	/*DaggerMyCommponent*/
	public final class DaggerMyCommponent implements MyCommponent {
	  private Provider<User> provideUserProvider;
	
	  private MembersInjector<MainActivity> mainActivityMembersInjector;
	
	  private DaggerMyCommponent(Builder builder) {
	    assert builder != null;
	    initialize(builder);
	  }
	
	  public static Builder builder() {
	    return new Builder();
	  }
	
	  public static MyCommponent create() {
	    return new Builder().build();
	  }
	
	  @SuppressWarnings("unchecked")
	  private void initialize(final Builder builder) {
	
	    this.provideUserProvider = UserModule_ProvideUserFactory.create(builder.userModule);
	
	    this.mainActivityMembersInjector = MainActivity_MembersInjector.create(provideUserProvider);
	  }
	
	  @Override
	  public void inject(MainActivity activity) {
	    mainActivityMembersInjector.injectMembers(activity);
	  }
	
	  public static final class Builder {
	    private UserModule userModule;
	
	    private Builder() {}
	
	    public MyCommponent build() {
	      if (userModule == null) {
	        this.userModule = new UserModule();
	      }
	      return new DaggerMyCommponent(this);
	    }
	
	    public Builder userModule(UserModule userModule) {
	      this.userModule = Preconditions.checkNotNull(userModule);
	      return this;
	    }
	  }
	}
	  
结合具体的使用**DaggerMyCommponent.builder().userModule(UserModule()).build().inject(this)**我们来看看原理到底是什么？首先，**DaggerMyCommponent**中有一个静态方法**builder()**返回一个内部接口**Builder**的匿名对象，而**Builder**对象有一个方法**userModule(UserModule userModule)**用来初始化UserModule对象；而**build()**方法就直接调用**DaggerMyCommponent**的构造器返回一个该类的对象，重点就是构造器，构造器传入Builder对象调用了**initialize(builder)**方法，从该方法的实现我们可以看到之前两个类的**create**方法都在这里被调用了，而且**目的类**的create方法传入的参数就是提供注入对象的**Moudle类(也可以是Inject修饰构造器的类)对应的create方法生成的Factory类对象(需要说明的是，Factory继承了Provider)**；而我们使用的最后一步**inject**方法的实现就是调用了目的类的injectMember方法来为我们目的类中的注入类自动赋了值。  

> 流程总结：**使用的时候@Commponent注释的类生成的xxxModule方法传入的xxxModule对象是一个开始；之后build方法内部的DaggerxxxCommponent构造器中的initialize方法通过多个Factory的create方法的调用将注入类和目标类连接起来；最后的inject方法内部直接调用目标类的injectMember方法将自己内部的注入类实例赋值**