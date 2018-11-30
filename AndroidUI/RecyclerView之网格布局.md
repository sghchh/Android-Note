#RecyclerView之基本的网格布局的实现  
首先，要在gradle配置文件里根据版本添加compile语句  

	compile 'com.android.support:recyclerview-v7:25.2.0@aar'
使用RecyclerView有如下操作  

* setLayoutManager(LayoutManager a):网格布局使用的是GirdLayoutManager  
* setAdapter(Adapter a):这个Adapter一般都要重写继承的，很像ListView的adapter，决定了每一项的控件和数据  
当然更多的功能如添加分割线，添加动画等高级的用法这里暂时不做说明。下面介绍一重写Adapter的一些实现：  
需要重写的方法主要有三个：  

* public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType)
* public void onBindViewHolder(MyViewHolder holder, int position)  
* public int getItemCount()
当然，ViewHolder也是重写继承的。在GirdLayoutManager的构造器中，有一个int类型参数是决定有多少列的。这个参数在自定义的Adapter中也有用。接下来想像一下这种情形，根据需求有的一行有两列，有的有三列，有的有一列；并且有的是个ImageView有的是一个Button，这该怎么实现呢？针对于前一种情况，GirdLayoutManaget有一个方法可以实现类似于合并单元格的效果，即**GridLayoutManager.setSpanSizeLookup(GridLayoutManager.SpanSizeLookup a);这是LayoutManager实例的方法，不是Adapter的方法**；针对后一种情况，可以通过第一个方法参数中的viewType来加载不同的布局来显示。下面就根据一个代码实例来讲解。  

		public class MyAdapter extends RecyclerView.Adapter<MyAdapter.MyViewHolder> {
		//这有两种布局想要显示
		//第一个是界面只有一个ImageView
		//第二个是界面只有一个TextView
   		 static final int IMAGE=1;
   		 static final int TEXT=2;
    	@Override
    	public MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
      		  if(viewType==0)
       		 {
       	     	View view= LayoutInflater.from(parent.getContext()).inflate(R.layout.text_recyclerview,parent,false);
            	return new MyViewHolder(view,MyAdapter.TEXT);
        	}
        	else
        	{
        	    View view=LayoutInflater.from(parent.getContext()).inflate(R.layout.image_recyclerview,parent,false);
        	    return new MyViewHolder(view,MyAdapter.IMAGE);
        	}
    	}

    	@Override
    	public void onBindViewHolder(MyViewHolder holder, int position) {

    	}

    	@Override
    	public int getItemCount() {
        	return 17;
    	}
   		@Override
   		 public int getItemViewType(int position) {
       		 if(position==0||position==6||position==10||position==14)
        	    return 0;
       		 else
       	 	    return 1;
    	}
        //自定义ViewHolder
   		 class MyViewHolder extends RecyclerView.ViewHolder{
      		  public ImageView image;
      		  public TextView text;
      		  public MyViewHolder(View view,int type)
      		  {
        		    super(view);
        		    if(type==MyAdapter.IMAGE)
         		   {
         		       image=(ImageView)view.findViewById(R.id.image);
          		  }
          		  else
          		  {
          		      text=(TextView)view.findViewById(R.id.text);
          		  }
      		  }
   		 }
		}
这是自定义Adapter的方法，首先看第一个重写的方法。根据viewType这个参数进行判断，来达到特定的位置显示不同的界面的效果，设置viewType的方法是getItemViewType(int position)，这个方法实例中也有重写。  
这里的onBindViewHolder()方法并没有实现，这个方法的意思是为每一个要显示的控件添加内容，比如为一个文本框添加文字，为一个ImageView添加图片等。  
getItemCount()方法就是返回一个item项的数目，这是是确定的17项。  

		gridLayoutManager=new GridLayoutManager(this,2);
        gridLayoutManager.setSpanSizeLookup(new GridLayoutManager.SpanSizeLookup() {
            @Override
            public int getSpanSize(int position) {
                if(position==0||position==6||position==10||position==14)
                    return 2;
                else
                    return 1;
            }
        });
这里的代码就是实现有的2列，有的1列的效果的。getSpanSize()返回的int类型数据可以理解为weight，即控件占的比重，最大比重就是LayoutManager构造器中的那个指定列数的参数。
