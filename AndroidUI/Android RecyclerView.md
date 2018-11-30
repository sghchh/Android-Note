# Android RecyclerView  
## 一、 RecyclerView分割线--ItemDecortaion  
想要给RecyclerView的每一个item之间添加分割线，得继承**RecyclerView.ItemDecoration**实现自己的效果：  
	
	public class ID extends RecyclerView.ItemDecoration {
    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
        super.getItemOffsets(outRect, view, parent, state);
    }

    @Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDraw(c, parent, state);
    }

    @Override
    public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDrawOver(c, parent, state);
    }
	}  
要实现效果就是这三个方法共同实现的。  

### 1.1 getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state)方法  
首先看看这个方法在API文档中的描述：  

>Retrieve any offsets for the given item. Each field of outRect specifies the number of pixels that the item view should be inset by, similar to padding or margin. The default implementation sets the bounds of outRect to 0 and returns.  
If this ItemDecoration does not affect the positioning of item views, it should set all four fields of outRect (left, top, right, bottom) to zero before returning.  
If you need to access Adapter for additional data, you can call **getChildAdapterPosition(View)** to get the adapter position of the View  

从这段描述中我们可以get到几个点：  

* 这个方法的参数**outRect**的作用是**retrieve**每一个item的**offects**的，即改参数保存了item四个边的开端的  
* 默认情况下outRect的四个参数都是零  

![](https://user-gold-cdn.xitu.io/2017/5/3/0648d136fb6b45563986e78b4ac8f838?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  

就像这个图中展示的那样，**getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state)方法  **中的第一个参数**outRect**就代表了外面那层蓝色的区域；**outRect的top,left,bottom,right**就是蓝色边距离item的像素值。  

>注意：设置outRect的top和bottom后，item的高度不会因此被占用，也就是说，top和bottom不是从item的那里取得区域；但是如果设置left和right，如果item设置的是match_parent的话，item的区域会被占用，看起来并没有沾满整个容器，而是有一部分作为了outRect的left和right。（PS：如果item的height设置成了match_parent也会有如此情况）  

### 1.2 onDraw(Canvas c, RecyclerView parent, RecyclerView.State state)方法  
同样我们首先看看这个方法的API文档：  

>Draw any appropriate decorations into the **Canvas supplied to the RecyclerView.** Any content drawn by this method will be drawn before the item views are drawn, and will thus appear underneath the views.  

注意：该方法的Canvas并不是上面的outRect，**Canvas是这个RecyclerView的Canvas**，这个方法只会调用一次（并不会在每一个Item处都调用），所以要遍历所有item为他们绘制分割线。如果这里直接简单的Canvas.drawColor()，那么整个RecyclerView都会变成你绘制的那个颜色。  

	@Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        super.onDraw(c, parent, state);
        //c.drawColor(Color.BLUE);
        int count=parent.getChildCount();
        for(int i=0;i<count;i++){
            float top=parent.getChildAt(i).getBottom();
            float left=parent.getChildAt(i).getLeft();
            float right=parent.getChildAt(i).getRight()-RIGHT;
            float bottom=parent.getChildAt(i).getBottom()+TOP+BOTTOM;
            c.drawRect(left,top,right,bottom,paint);
            //c.drawRect();
            //paint.setColor(Color.BLACK);
            //c.drawText(parent.getChildAt(i).getMeasuredHeight()+"",0,3,0,0,paint);
        }
    }
	//这里的RIGHT等，都是outRect对应的参数
### 1.3 onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state)  
>Draw any appropriate decorations into the Canvas supplied to the RecyclerView. Any content drawn by this method will be drawn after the item views are drawn and will thus appear over the views.  

只要看这句话就够了：**Any content drawn by this method will be drawn after the item views are drawn and will thus appear over the views.**  
这个方法绘制在item绘制后，绘制的内容会显示在item的上方。

