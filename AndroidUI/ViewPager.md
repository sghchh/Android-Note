#Android控件—ViewPager学习  
ViewPager在使用上和ListView很像，都需要提供一个适配器，这个适配器中封装了ViewPager要显示的页的内容。viewpager只能完整的显示一页，但是，viewpager自带一个滑动切换的功能，而且切换的动画可以自定义，并且viewpager提供了一个可以添加Fragment元素的Adapter，这使得viewpager适用于一些轮播等视觉上较好的场景。  
###使用  
首先要在布局文件中加入ViewPager这个元素  

	<android.support.v4.view.ViewPager
        android:id="@+id/pager"
        android:clipChildren="false"
        android:layout_width="240dp"
        android:layout_height="189dp">    
###添加数据  
ViewPager为我们提供了一个adapter：PagerAdapter，我们创建一个ViewPager对象至少需要重写四个方法：  

* int getCount():返回显示多少个页面  
* boolean isViewFromObject(View view,Object object):这个方法的作用是看一个页面是否关联了一个key  
* Object instantiateltem(ViewGroup container,int position):初始化显示的页面，其作用相当于ListView的BaseAdapter中的getView方法。  
* void destoryItem(ViewGroup container,int position,Object object):根据Object这个key销毁一个页面  

上面提到了key这个东西，那么这是个什么东西呢？看一看官方文档里出现这个词是在isViewFromObject(View view, Object object) 方法的说明中：Determines whether a page View is associated with a specific key object as returned by **instantiateItem(ViewGroup, int).**所以之后的所有的key都是指instantiateitem方法的返回值。我们再来看看instantiateItem方法返回值的说明：Returns an Object representing the new page. This does not need to be a View, but can be some other container of the page.也就是说这个Object必须能够代表这个页面才行，具体是什么无所谓。  
综上可以看出，PagerAdapter的使用和listview的BaseAdapter几乎是一个套路，所以直接看示例就可以了：  

	final PagerAdapter adapter=new PagerAdapter() {
            @Override
            public int getCount() {
                return list.size();
            }

            @Override
            public boolean isViewFromObject(View view, Object object) {
                return view==object;
            }

            @Override
            public void destroyItem(ViewGroup container, int position, Object object) {
                container.removeView(list.get(position));
            }

            @Override
            public Object instantiateItem(ViewGroup container, int position) {
                container.addView(list.get(position));
                return list.get(position);
            }
        };
我的这个实例中，list集合中的元素是view，所以在instantiateItem中直接返回list.get(position)就可以了，当然也可以像BaseAdapter那样，在这个方法中现场初始化一个view然后返回，那样的话，list中封装的就是数据了。  
###添加切换动画  
ViewPager还支持通过自定义实现个性的切换页面的动画，viewpager有一个普通的public方法setPageTransformer（Boolean，ViewPager.PageTransformer transformer）方法来设置切换页面的动画，自定义动画就要继承ViewPager.PageTransformer这个类，并且重写其中的一个方法 public void transformPage(View page, float position)，根据方法的形参就可以知道，page就是当前的页面，重点就是position的含义。
position的可能性的值有，其实从官方示例的注释就能看出：
[-Infinity,-1)  已经看不到了
(1,+Infinity] 已经看不到了
 [-1,1] 
重点看[-1,1]这个区间 ， 其他两个的View都已经看不到了~~

假设现在ViewPager在A页现在滑出B页，则:
A页的position变化就是( 0, -1]
B页的position变化就是[ 1 , 0 ]  

	public class MyTransformation implements ViewPager.PageTransformer {

    private static final float MIN_SCALE=0.85f;
    private static final float MIN_ALPHA=0.5f;
    private static final float MAX_ROTATE=30;
    private Camera camera=new Camera();
    @Override
    public void transformPage(View page, float position) {
        float centerX=page.getWidth()/2;
        float centerY=page.getHeight()/2;
        float scaleFactor=Math.max(MIN_SCALE,1-Math.abs(position));
        float rotate=20*Math.abs(position);
        if (position<-1){

        }else if (position<0){
            page.setScaleX(scaleFactor);
            page.setScaleY(scaleFactor);
            page.setRotationY(rotate);
        }else if (position>=0&&position<1){
            page.setScaleX(scaleFactor);
            page.setScaleY(scaleFactor);
            page.setRotationY(-rotate);
        }
        else if (position>=1) {
            page.setScaleX(scaleFactor);
            page.setScaleY(scaleFactor);
            page.setRotationY(-rotate);
        }
    }
	}   
这个动画的效果就是实现一个3d画廊的效果  
###一些用的着的属性或者方法  
boolean clipChildren属性  
>Defines whether a child is limited to draw inside of its bounds or not. This is useful with animations that scale the size of the children to more than 100% for instance. In such a case, this property should be set to false to allow the children to draw outside of their bounds. The default value of this property is true.

这是官方文档对该属性的解释，上面的大意是说，ViewGroup的子View默认是不会绘制边界意外的部分的，倘若将clipChildren属性设置为false，那么子View会把自身边界之外的部分绘制出来。当然如果只是设为false但是当前的页面设置为了match_parent的宽，还是没有效果，所以，使用的时候要控制一下每一个页面的宽。  
**setOffscreenPageLimit(int)方法**，OffscreenPageLimit属性用于设置预加载的数量，比如说这里设置了2，那么就会预加载中心item左边两个Item和右边两个Item。

 
