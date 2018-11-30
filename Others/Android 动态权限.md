# Android 权限  
杠杆在学习SmsManager发短信的用法的时候，明明在清单文件中申请了短信权限，但是在运行之后，依然提示没有权限，于是上网搜索了一下原因，发现是动态权限的问题，于是这篇笔记应运而生：  

## Android 权限分级  
从**Android 6.0 （API为23）**之后，谷歌对系统权限做了很大的改变，之前用户安装app的时候，只是把需要使用的权限列出来告知用户一下，app安装之后就可以访问这些权限。从6.0开始，一些**敏感的权限**需要使用时是**动态申请**的，并且用户可以选择拒绝授权。  
### 分级  
权限主要分为normal、dangerous、signature和signatureOrSystem四个等级，常规情况下我们只需要了解前两种，即正常权限和危险权限。  
#### 1. 正常权限  
正常权限涵盖应用需要访问其外部数据或资源，但对用户隐私或其他应用操作风险很小的区域。应用声明其需要正常权限，系统会自动授予该权限。例如设置时区，只要应用声明过权限，系统就直接授予应用此权限。 

> 普通权限列表：  
> ACCESS_LOCATION_EXTRA_COMMANDS  
ACCESS_NETWORK_STATE  
ACCESS_NOTIFICATION_POLICY  
ACCESS_WIFI_STATE  
BLUETOOTH  
BLUETOOTH_ADMIN  
BROADCAST_STICKY  
CHANGE_NETWORK_STATE  
CHANGE_WIFI_MULTICAST_STATE  
CHANGE_WIFI_STATE  
DISABLE_KEYGUARD  
EXPAND_STATUS_BAR  
GET_PACKAGE_SIZE  
INTERNET  
KILL_BACKGROUND_PROCESSES  
MODIFY_AUDIO_SETTINGS  
NFC  
READ_SYNC_SETTINGS  
READ_SYNC_STATS  
RECEIVE_BOOT_COMPLETED  
REORDER_TASKS  
REQUEST_INSTALL_PACKAGES  
SET_TIME_ZONE  
SET_WALLPAPER  
SET_WALLPAPER_HINTS  
TRANSMIT_IR  
USE_FINGERPRINT  
VIBRATE  
WAKE_LOCK  
WRITE_SYNC_SETTINGS  
SET_ALARM  
INSTALL_SHORTCUT  
UNINSTALL_SHORTCUT  

以上权限只需要在清单文件中声明即可，**系统会直接授权，不需要用户同意**。  

#### 2. 危险权限  
危险权限涵盖应用需要涉及用户隐私信息的数据或资源，或者可能对用户存储的数据或其他应用的操作产生影响的区域。例如读取用户联系人，在6.0以上系统中，需要在**运行时(动态申请)**明确向用户申请权限。  
谷歌还做了一个权限组，以分组的形式来呈现(**对于同一个组的权限，只要申请一个权限，如果用户同意，同组的其他权限自动被授予**)：  
![](https://upload-images.jianshu.io/upload_images/2189443-81b82d37bb9b39e0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/639)  
以上的权限**不仅要在清单文件中声明(如果不声明，则不会弹出申请的提示)**，还需要我们在代码中**主动申请**：  
### 如何申请  
管理权限很简单，大部分情况一下三个方法就够用了：  

*  **int checkSelfPermission(String permission)：**用来检测应用是否已经具有权限,返回值有两种  
	*  **PERMISSION_GRANTED(为0):**表示具有权限  
	*  **PERMISSION_DENIED(为1):**表示无权限  
* **void requestPermissions(String[] permissions, int requestCode)：** 进行请求单个或多个权限，该方法执行后会弹出一个对话框，来供用户决定是否申请 
* **void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)：** 请求权限结果回调；当执行**requestPermissions**放发返回后回调(requestPermissions弹出的对话框点击“同意"则返回PERMISSION_GRANTED，并保存在该回调方法的第三个参数数组里；在申请多个权限的时候，权限的数组和其是否同意的结果数组下标识对应的)；第一个参数和**requestPermissions方法的第二个参数是对应的**，这两个方法的关系和**starActivityForResult方法与onActivityResult方法的关系一样**  
  
我们在进行动态申请的时候，实际上只需要重写最后一个方法就行了。
#### 实例  
 我们在申请权限之前可以先判断一下是不是Android 6.0以上的，如果不是，就不用申请了：  

	//版本判断  
	if (Build.VERSION.SDK_INT >= 23) {  
    //检查是否拥有权限  
    int checkCallPhonePermission = ContextCompat.checkSelfPermission(getApplicationContext(), permission);  
    if (checkCallPhonePermission != PackageManager.PERMISSION_GRANTED) {  
        //弹出对话框接收权限  
        ActivityCompat.requestPermissions(BaseActivity.this, new String[]{permission}, id);  
        return;  
	}  

	@Override  
	public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {  
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);  
  		if(requestCode==id){
			if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {  
        		//TODO:已授权  
    		} else {  
       			//TODO:用户拒绝  
    		}  
		}
    
	}    



