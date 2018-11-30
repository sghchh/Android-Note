# CoordinatorLayout, AppBarLayout, CollapsingToolbarLayout使用  
## 介绍  
这三个布局都是**Metrial Design**新引进的控件：  
#### CoordinatorLayout  
先看看官方文档对它的描述：  

>CoordinatorLayout is a super-powered FrameLayout.
CoordinatorLayout is intended for two primary use cases:  
**As a top-level application decor or chrome layout**  
**As a container for a specific interaction with one or more child views**  
By specifying **Behaviors** for child views of a CoordinatorLayout you can provide many different interactions within a single parent and those views can also interact with one another.**View classes can specify a default behavior when used as a child of a CoordinatorLayout using the DefaultBehavior annotation.**  
首先我们可以知道，这个布局是一个加强版的**FrameLayout**；其次通过**Behavior**与子View进行交流；最后，其子控件可以通过**behavior**实现一些**animation**  
#### AppBarLayout  
官方文档：  

>AppBarLayout is a **vertical LinearLayout** which implements many of the features of material designs app bar concept, namely scrolling gestures.  
Children should provide their desired scrolling behavior through **setScrollFlags(int)** and the associated layout xml attribute: **app:layout_scrollFlags.**  
This view depends heavily on being used as a direct child within a CoordinatorLayout. **If you use AppBarLayout within a different ViewGroup, most of it's functionality will not work.**  
我们也可以知道：  

* AppBarLayout是一个Vertical的LinearLayout  
* 子控件可以通过设置**scrollFlags**来拥有动画效果  
* 其父容器必须是上面说到的**CoordinatorLayout**  

#### CollapsingToolbarLayout  
官方文档介绍：  

>CollapsingToolbarLayout is a **wrapper for Toolbar** which implements a collapsing app bar. It is designed to be used **as a direct child of a AppBarLayout**.   

看到该控件是对**Toolbar**的一个管理，父容器必须是**AppBarLayout**  

### 1. CoordinatorLayout-协调布局  
### 2. AppBarLayout 使用  
在该控件的API文档中还有如下几句话：  

>AppBarLayout also requires a separate scrolling sibling in order to know when to scroll. The binding is done through the **AppBarLayout.ScrollingViewBehavior** behavior class, meaning that you should set your scrolling view's behavior to be an instance of **AppBarLayout.ScrollingViewBehavior.**  

也就是说，被依赖的**dependency(View)**的**layout_behavior**属性必须是**AppBarLayout.ScrollingViewBehavior实例才行。**  
要实现联动的滚动，还需要为**Toolbar添加scrollFlags属性才行**,可选的属性有：  

* **scroll(必选)**：子View如果设置layout_scrollFlags属性的值为scroll，这个View会随着Scrolling View一起滚动，当向上滚动的时候，Toolbar会滚出屏幕外，如果不设置，那么Toolbar会固定不动。**如果想有联动滚动的效果，这个是必加的。**  
* **enterAlways：**使用这个flag，当Scrolling View 向下滑动时，子View 将直接向下滑动，而不管Scrolling View 是否在滑动。注意：要与scroll 搭配使用，否则是不能滑动的。  
* **enterAlwaysCollapsed:**enterAlwaysCollapsed 是对enterAlways 的补充，当Scrolling View 向下滑动的时候，滑动View（也就是设置了enterAlwaysCollapsed 的View）下滑至折叠的高度，当Scrolling View 到达滑动范围的结束值的时候，滑动View剩下的部分开始滑动。这个折叠的高度是通过View的minimum height （最小高度）指定的。（要配合scroll｜enterAlways 一起使用）  
* **exitUntilCollapsed:**当Scrolling View 滑出屏幕时（也就时向上滑动时），滑动View先响应滑动事件，滑动至折叠高度，也就是通过minimum height 设置的最小高度后，就固定不动了，再把滑动事件交给 Scrolling view 继续滑动。  
* **snap:**在滚动结束后，如果view只是部分可见，它将滑动到最近的边界。比如，如果view的底部只有25%可见，它将滚动离开屏幕，而如果底部有75%可见，它将滚动到完全显示。

### 3. CollapsingToolbarLayout 使用  
该控件有如下特征： 

* **Collasping title（可折叠标题):**当布局完全可见时，这个标题比较大；当折叠起来时，标题也会变小。标题的外观可以通过**expandedTextAppearance和collapsedTextAppearance属性(这两个属性建议引用一个style文件，里面设置字体的大小和颜色等)**来调整。  
如：  

	//展开时的文字风格
	<item name="android:textSize" 30sp item>
    <item name="android:textColor" #fff item>

* **Content scrim（内容纱布):**根据CollapsingToolbarLayout是否滚动到一个临界点，内容纱布会显示或隐藏。可以通过setContentScrim(Drawable)来设置内容纱布。
* **Status bar scrim（状态栏纱布):**也是根据是否滚动到临界点，来决定是否显示。可以通过setStatusBarScrim(Drawable)方法来设置。这个特性只有在Android5.0及其以上版本，我们设置fitSystemWindows为ture时才能生效。  
* **Parallax scrolling children（视差滚动子View):**子View可以选择以“视差”的方式来进行滚动。（视觉效果上就是子View滚动的比其他View稍微慢些）  
* **Pinned position children：**子View可以选择固定在某一位置上。  

接下来我们来看一个例子：  
![](http://img.blog.csdn.net/20161223112504573?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdHlrMDkxMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  

代码：  

	<?xml version="1.0" encoding="utf-8"?>
	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="250dp">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleMarginEnd="60dp"
            app:expandedTitleMarginStart="50dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">

            <ImageView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="fitXY"
                android:src="@drawable/bg"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.7" />


            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />

        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

	</android.support.design.widget.CoordinatorLayout>  
分析：  

1. 看到**layout_scrollFlags**这个属性是在添加到了**CollapsingToolbarLayout**，这是因为它是**AppBarLayout**的子View  
2. 管理toolbar字体随滚动进行放缩的属性也是添加到**CollapsingToolbarLayout**上  
3. **CollapsingToolbarLayout**的子View必须添加**layout_collapseMode (折叠模式)属性**，有两种可选值：  
	* **parallax：**设置为这个模式时，在内容滚动时，CollapsingToolbarLayout中的View（比如ImageView)也可以同时滚动，实现视差滚动效果，通常和视差因子搭配使用；  
		* **layout_collapseParallaxMultiplier：**设置视差滚动因子，值为0~1，值越大视察越大。  
	* **pin：设置为这个模式时，当CollapsingToolbarLayout完全收缩后，Toolbar还可以保留在屏幕上**  
4. 被依赖的**dependency(view,这里是RecyclerView):**必须添加**layout_behavior**而且是**AppBarLayout.ScrollingViewBehavior实例才行。**  
5. 根容器必须是**CoordinatorLayout**  

### 4. CoordinatorLayout与CoordinatorLayout.Behavior<T extends View>    
CoordinatorLayout的使用核心是Behavior，**Behavior就是执行你定制的动作**。在讲Behavior之前必须先理解两个概念：Child和Dependency，什么意思呢？Child当然是子View的意思了，是谁的子View呢，当然是CoordinatorLayout的子View；其实Child是指要执行动作的CoordinatorLayout的子View。而Dependency是指Child依赖的View。  
简而言之，就是如过Dependency这个View发生了变化，那么Child这个View就要相应发生变化。发生变化是具体发生什么变化呢？这里就要引入Behavior，Child发生变化的具体执行的代码都是放在Behavior这个类里面。

怎么使用Behavior呢，首先，我们定义一个类，继承CoordinatorLayout.Behavior<T>,其中，泛型参数T是我们要执行动作的View类，也就是Child。然后就是去实现Behavior的两个方法：  

	/**
	* 判断child的布局是否依赖dependency
	*/
   		@Override
 		public boolean layoutDependsOn(CoordinatorLayout parent, T child, View dependency) {
    		boolean rs;
    		//根据逻辑判断rs的取值
    		//返回false表示child不依赖dependency，ture表示依赖
   			return rs;  
		}

	/**
	* 当dependency发生改变时（位置、宽高等），执行这个函数
	* 返回true表示child的位置或者是宽高要发生改变，否则就返回false
	*/
		@Override
		public boolean onDependentViewChanged(CoordinatorLayout parent, T child, View dependency) {
     	//child要执行的具体动作
        	return true;
	}  

#### 实例  
![](http://img.blog.csdn.net/20161223154247986?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdHlrMDkxMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  

	
	<?xml version="1.0" encoding="utf-8"?>
	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_behavior=".FollowBehavior" />

    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="被观察者" />


	</android.support.design.widget.CoordinatorLayout>


        btn.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                if (event.getAction() == MotionEvent.ACTION_MOVE) {
                    v.setX(event.getRawX() - v.getWidth() / 2);
                    v.setY(event.getRawY() - v.getHeight() / 2 - getStatusBarHeight(getApplicationContext()));
                }
                return true;
            }
        });

