# Metrial Design--SwipeRefreshLayout上拉刷新  
我们先来看看API文档中对这个控件的介绍：  

>The SwipeRefreshLayout should be used whenever the user can refresh the contents of a view **via(经由) a vertical swipe gesture**. The activity that instantiates(示例) this view should add an **OnRefreshListener** to be notified whenever the swipe to refresh gesture is completed. The SwipeRefreshLayout will notify the listener each and every time the gesture is completed again; **the listener is responsible for correctly determining when to actually initiate(开始) a refresh of its content.** If the listener determines there should not be a refresh, it must call setRefreshing(false) to cancel any visual indication of a refresh. If an activity wishes to show just the progress animation, it should call setRefreshing(true). To disable the gesture and progress animation, call setEnabled(false) on the view.  
This layout should be made the parent of the view that will be refreshed as a result of the gesture and can **only support one direct child**. This view will also be made the target of the gesture and will be forced to match both the width and the height supplied in this layout. The SwipeRefreshLayout does not provide accessibility events; instead, a menu item must be provided to allow refresh of the content wherever this gesture is used.  

通过官方文档的介绍，我们可以get几个点：  

* SwipeRefreshLayout支持的只有竖直方向(vertical)方向上的swipe动作  
* 对swipe的监听需要添加一个**onRefreshListener**监听器  
* setRefreshing(false)、setRefreshing(true)和setEnable(false)的意义分别是：关闭刷新的动画，开启动画，屏蔽刷新动作  
* SwipeRefreshLayout必须作为目标控件的父容器，且**只能有一个子控件(only support one direct child)**  

## 使用  
### 1. 首先要包住控件  

	<?xml version="1.0" encoding="utf-8"?>
	<android.support.v4.widget.SwipeRefreshLayout
    android:id="@+id/srl"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
	>

    <android.support.v7.widget.RecyclerView
        android:id="@+id/rv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

	</android.support.v4.widget.SwipeRefreshLayout>  

### 2. 添加监听器  

	mSrlRoot = (SwipeRefreshLayout) findViewById(R.id.srl);
	mSrlRoot.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
        @Override
        public void onRefresh() {
            
        }
    });
  
更新子控件中的数据在**onRefresh**中完成，一般在结束后会加上  

	mSrlRoot.setRefreshing(false)  
来关闭刷新时显示的动画。  
到此，基本的功能就是这样。  
### 3. 其他  
SwipeRefreshLayout为我们提供了  

	void	setColorSchemeColors(int... colors)
	//Set the colors used in the progress animation.

	void	setColorSchemeResources(int... colorResIds)
	//Set the color resources used in the progress animation from color resources.  
两个方法为刷新动画指定颜色，转一圈颜色就变一个。