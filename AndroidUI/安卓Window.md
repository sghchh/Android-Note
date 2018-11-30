# Android窗口--Window学习  
Window是一个抽象的类，安卓中的所有View都是放在Window中的。我这么说可能有点不太明显，首先Window是一个抽象类，它的实现类是PhoneWindow，就是这个PhoneWindow有一个内部类DecoderView，这下子就一目了然了，这个DecoderView是一个FrameLayout有上下两个部分，下面的部分就是我们在Activity中的onCreate方法中的setContentView方法将View所添加的地方了。  
## Window的描述  
既然Window是一个抽象类，没有自己的一个视图，我们怎么去描述他呢？  
  
	width：描述窗口的宽度
	height：描述窗口的高度
	type：这是哪一种类型的Window  
其中type分为应用程序Window（1-99），子Window（1000-1999），系统Window（2000-2999）他们的优先级依次增加  
## Window的创建  
下面就简单说一下Window的创建过程：  
1. 首先是启动Activity  
在ActivityThread的performLaunchActivity（该方法的返回值就是一个activity）方法中会进行判断，如果activity不为空则调用activity的attach方法，  

	private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
        ...
        Activity activity = null;
        try {
          //通过反射机制创建一个Activity 
            java.lang.ClassLoader cl = r.packageInfo.getClassLoader();
            activity = mInstrumentation.newActivity( cl, component.getClassName(), r.intent);
            StrictMode.incrementExpectedActivityCount(activity.getClass());
            r.intent.setExtrasClassLoader(cl);
            r.intent.prepareToEnterProcess();
            if (r.state != null) {
                r.state.setClassLoader(cl);
            }
        } catch (Exception e) {
          ...
        }

        try {
            Application app = r.packageInfo.makeApplication(false, mInstrumentation);
            if (activity != null) {
                Context appContext = createBaseContextForActivity(r, activity);
                CharSequence title = r.activityInfo.loadLabel(appContext.getPackageManager());
                Configuration config = new Configuration(mCompatConfiguration);
                //这个里面创建了Window对象
                activity.attach(appContext, this, getInstrumentation(), r.token,
                        r.ident, app, r.intent, r.activityInfo, title, r.parent,
                        r.embeddedID, r.lastNonConfigurationInstances, config,
                        r.referrer, r.voiceInteractor);

               }

              activity.mCalled = false;
               if (r.isPersistable()) {
                    //调用Activity的onCreate方法
                   mInstrumentation.callActivityOnCreate(activity, r.state, r.persistentState);
               } else {
                   mInstrumentation.callActivityOnCreate(activity, r.state);
               }

            mActivities.put(r.token, r);
            ...
        }  catch (Exception e) {
         ...
        }
        return activity;
    }
在attach方法中会通过PolicyManager创建Window对象，并且为Window设置回调接口。  

	mWindow = PolicyManager.makeNewWindow(this);
	mWindow.setCallback(this);/设置回调函数，使得Activity可以处理一些事件  
所以是**先有视图，后有Window，但是视图依赖Window存在**，来看一看Activity为Window设置了哪些回调的接口：  
	
	public interface Callback {

        public boolean dispatchKeyEvent(KeyEvent event);

        public boolean dispatchKeyShortcutEvent(KeyEvent event);

        public boolean dispatchTouchEvent(MotionEvent event);

        public boolean dispatchTrackballEvent(MotionEvent event);

        public boolean dispatchGenericMotionEvent(MotionEvent event);

        public boolean dispatchPopulateAccessibilityEvent(AccessibilityEvent event);

        public View onCreatePanelView(int featureId);

        public boolean onCreatePanelMenu(int featureId, Menu menu);

        public boolean onPreparePanel(int featureId, View view, Menu menu);

        public boolean onMenuOpened(int featureId, Menu menu);

        public boolean onMenuItemSelected(int featureId, MenuItem item);

        public void onWindowAttributesChanged(WindowManager.LayoutParams attrs);

        public void onContentChanged();

        public void onWindowFocusChanged(boolean hasFocus);

        public void onAttachedToWindow();

        public void onDetachedFromWindow();

        public void onPanelClosed(int featureId, Menu menu);

        public boolean onSearchRequested();

        public ActionMode onWindowStartingActionMode(ActionMode.Callback callback);

        public void onActionModeStarted(ActionMode mode);

        public void onActionModeFinished(ActionMode mode);
    }  
**Activity实现了这个回调接口，当Window的状态发生变化的时候，就会回调Activity中实现的这些接口**  
2. View是如何附属到Window上的  
上面的performLaunchActivity方法中在执行完atach方法后，会执行callActivityOnCreate方法，这样一来就进入了Activity的onCreate方法中，前面说过PhoneWindow有一个静态变量是DecoderView的一部分，没错，将View附属到Window的方法就是这个onCreate方法中的setContentView（int layoutResID）方法：  

	public void setContentView(int layoutResID) {  
        getWindow().setContentView(layoutResID);  
        initWindowDecorActionBar();  
    }    
接下来就看一看Window的setContentView方法：  

	 public void setContentView(int layoutResID) {
        // Note: FEATURE_CONTENT_TRANSITIONS may be set in the process of installing the window
        // decor, when theme attributes and the like are crystalized. Do not check the feature
        // before this happens.
        if (mContentParent == null) {
          //第一步，构建DecroView
            installDecor();
        } else if (!hasFeature(FEATURE_CONTENT_TRANSITIONS)) {
            mContentParent.removeAllViews();
        }

        if (hasFeature(FEATURE_CONTENT_TRANSITIONS)) {
            final Scene newScene = Scene.getSceneForLayout(mContentParent, layoutResID,
                    getContext());
            transitionTo(newScene);
        } else {
          //第二步，将View添加到mContentParent中
            mLayoutInflater.inflate(layoutResID, mContentParent);
        }
        final Callback cb = getCallback();
        if (cb != null && !isDestroyed()) {
          //第三步，回调Activity的onContentChanged方法，通知视图发生了改变
            cb.onContentChanged();
        }
    }  
