# Android Realm数据库  
## 一、简单的连接到Realm Cloud  
#### 1. 获取许可证实例  

	SyncCredentials credentials = SyncCredentials.nickname(nickname, true);   

获取许可证的方式有很多这里的只是通过nickName(有点类似于登录时的用户名)。  
#### 2. 连接远程服务器  

	SyncUser.loginAsync(credentials, AUTH_URL, new SyncUser.Callback<SyncUser>()  
需要重写Callback里的方法  

#### 3. 进行一些配置  

	SyncConfiguration configuration = new SyncConfiguration.Builder(
                SyncUser.currentUser(),
                REALM_BASE_URL + "/items")
                .build();  
	Realm.setDefaultConfiguration(configuration); //应用配置  

#### 4. 获取Realm实例  

	Realm realm=Realm.getRealmInstance()    

#### 5. build.gradle  

	realm {
    syncEnabled = true
	}   //该属性是在android{}外面的

## 二、部分同步  
默认情况下通过Realm的where语句获取的数据时所有曾经连接过同一个Realm Cloud的app的同一个class；比如：1. 有两个App(a、b)，这两个App都是连接的同一个Realm Cloud Server  2. a和b中RealmObject的实现类都一样，都是Project.class  那么如果我在a中直接：  

	RealmResults<Project> items=realm.where(Project.class).findAllAsync();  

得到的结果中不光有a以前写入的数据，还有b写入的数据；如果我只想得到a的数据怎么办？--**部分同步**  

1. 首先，我们要通过**SyncConfiguration**开启部分同步的功能：   

	    SyncConfiguration.Builder(
                SyncUser.currentUser(),
                REALM_BASE_URL + "/items")
				.partialRealm()    //开启部分同步
                .build();  
		Realm.setDefaultConfiguration(configuration); //应用配置

2. 其次，在**Project.class**中要有一个属性能够用来作为一个唯一标识来标记你的数据，而去其他用户创建的应用到**Project.class**的数据库区分开来，**一般都是用当前用户的identity来作为该属性的值的**:  

		RealmResults<Project> results=realm
                .where(Item.class)
                .equalTo("owener",SyncUser.current().getIdentity())   //通过当前用户的identity来获取自己的数据
                .findAllAsync();

> 插入数据的时候别忘了给这个唯一标识**赋值为当前用户的identity**  

## 三、 添加权限  
默认情况下，还是举上面的a、b的例子，上面所讲的**部分同步只是a过滤掉了它不关心的数据，是一种在自己设备上的操作**，假如b并没有使用部分同步，那么b将能够看到a中的数据，更要命的是，**b可以修改它所看到的a的任意数据。**所以，这时候**Permission类(权限)**就显得至关重要了。   
**Permission类**为我们定义了我们想要**授予的权限以及授予的角色(Role)**  

>默认情况下，每个登录用户都有为其创建的私人角色。此角色可以在PermissionUser.getPrivateRole（）中访问。我们将在创建新项目时使用此角色。  
>将权限对象与创建的项目相关联
在我们创建一个新的数据时，我们现在将它与一个新的权限实例关联起来，以保护它。  

	Project project = realm.createObject（Project.class);  

**创建权限**  

	Role role = realm.where(PermissionUser.class)
        .equalTo("id", SyncUser.current().getIdentity())     //Role得根据id找到对应用户的Role
        .findFirst()
        .getPrivateRole();
	Permission permission = new Permission.Builder(role).allPrivileges().build();  

好了，之后就需要将权限和新创建的数据库数据实例关联起来了，为此，我们在定义数据库的数据类型的时候(如Proje.class)，其要有一个能够存放Permission类实例的属性，因为权限可能有很多，所以一般选择一个集合:  

	//Project.class
	private RealmList<Permission> permissions;   //这里是RealmList

	project.getPermissions().add(permission);  

