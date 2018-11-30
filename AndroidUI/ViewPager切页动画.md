# ViewPager切页动画  
>参考文章：[http://blog.csdn.net/silentweek/article/details/72676374](http://blog.csdn.net/silentweek/article/details/72676374)  

ViewPager的切页动画需要用户自定义一个类**实现ViewPager.PageTransformer接口，在其public void transformPage(View view, float position)方法中实现动画效果**  
本文开头的链接中有讲解position参数的意义：  
>在没有任何动作的时候，当前的页所对应的position为0；此时上一页对应的position为-1；下一页对应的position为1.   

### 官方示例  

	public class DepthPageTransformer implements ViewPager.PageTransformer {
    private static final float MIN_SCALE = 0.75f;  
  
    public void transformPage(View view, float position) {
        int pageWidth = view.getWidth();  
  
        if (position < -1) { // [-Infinity,-1)  
            // This page is way off-screen to the left.  
            view.setAlpha(0);  
  
        } else if (position <= 0) { // [-1,0]  
            // Use the default slide transition when moving to the left page  
            view.setAlpha(1);  
            view.setTranslationX(0);  
            view.setScaleX(1);  
            view.setScaleY(1);  
  
        } else if (position <= 1) { // (0,1]  
            // Fade the page out.  
            view.setAlpha(1 - position);  
  
            // Counteract the default slide transition  
            view.setTranslationX(pageWidth * -position);  
  
            // Scale the page down (between MIN_SCALE and 1)  
            float scaleFactor = MIN_SCALE  
                    + (1 - MIN_SCALE) * (1 - Math.abs(position));  
            view.setScaleX(scaleFactor);  
            view.setScaleY(scaleFactor);  
  
        } else { // (1,+Infinity]  
            // This page is way off-screen to the right.  
            view.setAlpha(0);  
        }  
    }  
	}    
根据官方示例类的效果和部分代码，我们来深入理解一下position以及切换动画。  

	else if (position <= 0) { // [-1,0]  
            // Use the default slide transition when moving to the left page  
            view.setAlpha(1);  
            view.setTranslationX(0);  
            view.setScaleX(1);  
            view.setScaleY(1);  
  
        }  
看看这段代码，判断中表示这是上一页在退出或者进入时的动画；*view.setTranslation(0)*这句代码表示不对当前页进行平移，结合动画效果，我们可以知道，每一个position都定格了一个瞬间，这个瞬间的page模样就是在动画的实现类中写的；比如这个官方示例的动画当下一页进入的动画，*view.setTranslationX(pageWidth*-position)*这句话的效果就是不管要进入的页的position是位于[1,0]中的哪一个瞬间，都将它平移到屏幕中间，而这种从后进入的效果是setScaleX和setScaleY共同实现的。