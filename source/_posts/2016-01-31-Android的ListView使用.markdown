---
title: Android的ListView使用
date: 2016-01-31 11:20:21
categories: Android
---


本文为Android的ListView相关知识整理，具体参考了
  
* 《Android群英传》第四章
* 《第一行代码》第三章

ListView是Android中最常用的控件，也是最难用的空间之一。ListView用来展示大量数据列表。

1. ListView的用法流程
--------------------
1.1 新建activity_main.xml
======
此页面作为主页布局页面，添加了一个ListView标签，显示全部数据。

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent" >
		<ListView
			android:id="@+id/list_view"
			android:layout_width="match_parent"
			android:layout_height="match_parent" >
		</ListView>
	</LinearLayout>


<!--more-->

1.2  新建fruit_item.xml
===========
此页面作为item单项的布局文件，显示单项的内容。

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
			android:layout_width="match_parent"
			android:layout_height="match_parent" >
				<ImageView
					android:id="@+id/fruit_image"
					android:layout_width="wrap_content"
					android:layout_height="wrap_content" />
				<TextView
					android:id="@+id/fruit_name"
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:layout_gravity="center"
					android:layout_marginLeft="10dip" />
		</LinearLayout>

1.3 定义一个数据类文件Fruit.java
===============
    
    public class Fruit {
		private String name;
		private int imageId;
		public Fruit(String name, int imageId) {
			this.name = name;
			this.imageId = imageId;
		}

		public String getName() {
			return name;
		}
		public int getImageId() {
			return imageId;
		}
	}

1.4 定义一个适配器类文件FruitAdapter.java
============

	public class FruitAdapter extends ArrayAdapter<Fruit> {
		private int resourceId;
		public FruitAdapter(Context context, int textViewResourceId,
			List<Fruit> objects) {
			super(context, textViewResourceId, objects);
			resourceId = textViewResourceId;
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			Fruit fruit = getItem(position); // 获取当前项的Fruit实例
			View view = LayoutInflater.from(getContext()).inflate(resourceId, null);
			ImageView fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
			TextView fruitName = (TextView) view.findViewById(R.id.fruit_name);
			fruitImage.setImageResource(fruit.getImageId());
			fruitName.setText(fruit.getName());
			return view;
		}
	}

1.5 创建活动MainActivity.java
===========


    public class MainActivity extends Activity {
		private List<Fruit> fruitList = new ArrayList<Fruit>();
			@Override
			protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			initFruits(); //  初始化水果数据
			FruitAdapter adapter = new FruitAdapter(MainActivity.this,
			R.layout.fruit_item, fruitList);
			ListView listView = (ListView) findViewById(R.id.list_view);

			listView.setAdapter(adapter);
		}
		private void initFruits() {
			Fruit apple = new Fruit("Apple", R.drawable.apple_pic);
			fruitList.add(apple);
			Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
			fruitList.add(banana);
			Fruit orange = new Fruit("Orange", R.drawable.orange_pic);
			fruitList.add(orange);
		}
	}


2. 使用ViewHolder模式优化ListView
-----------------
这里主要修改了getView()方法。
在子项被滚动到屏幕内的时候，会调用getView()。原始的getView()方法如下：
      
        @Override
		public View getView(int position, View convertView, ViewGroup parent) {
			Fruit fruit = getItem(position); // 获取当前项的Fruit实例
			View view = LayoutInflater.from(getContext()).inflate(resourceId, null);
			ImageView fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
			TextView fruitName = (TextView) view.findViewById(R.id.fruit_name);
			fruitImage.setImageResource(fruit.getImageId());
			fruitName.setText(fruit.getName());
			return view;
		}
    
通过`getItem(position)` 获取当前项的Fruit实例，然后通过`LayoutInflater`来为这个子项加载我们传入的布局。接着调用View的findViewById()方法获得控件实例。
> **缺点：每次在调用getView()时都通过findViewById()实例化控件。**

ViewHolder模式充分利用了ListView的视图缓存机制，避免了每次在调用getView()时都去通过findViewById()实例化控件。**提高效率50%以上！！**

只需要修改适配器类FruitAdapter.java中的getView()方法就可以。

        @Override
		public View getView(int position, View convertView, ViewGroup parent) {
			Fruit fruit = getItem(position);
			ViewHolder holder = null;
			View view;
			if(convertView == null){
				view = LayoutInflater.from(getContext()).inflate(resourceId, null);
				viewHolder = new ViewHolder();
				viewHolder.fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
				viewHolder.fruitName = (TextView) view.findViewById(R.id.fruit_name);
				view.setTag(viewHolder); //  将ViewHolder 存储在View 
			}else{
				view = convertView;
				viewHolder = (ViewHolder)view.getTag();
			}
			
			viewHolder.fruitImage.setImageResource(fruit.getImageId());
			viewHolder.fruitName.setText(fruit.getName());
			return view;
		}

		public final class ViewHolder{
			public ImageView fruitImage;
			public TextView fruitName;
		}

3. ListView小技巧
----------------
* 分隔线，在ListView标签中设置如下就会在ListView中得到一个颜色为灰色，高为10dp的分隔线。
      
      android:divider="@android:color/darker_gray"
      android:dividerHeight="10dp"

* 滚动条
    
      android:scrollbars="none"

* 取消点击后的回馈效果

      android:listSelector="#00000000"
      或者 android:listSelector="@android:color/transparent"

* 设置显示在第N项

      listView.setSelection(N); //瞬间完成移动
      //以下为平滑移动
      listView.smoothScrollBy(offset);
      listView.smoothScrollBy(index);
      listView.smoothScrollBy(distance, duration);

* 设置空ListView，当数据为空时，可以显示的一个View
  * 在ListView标签的同级添加一个id为`empty_view`的View

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent" >
		<ListView
			android:id="@+id/list_view"
			android:layout_width="match_parent"
			android:layout_height="match_parent" >
		</ListView>
		<TextView
		        android:layout_width="match_parent"
			android:layout_height="match_parent"
			android:text="这是无数据时显示的信息"
		/>
	    </LinearLayout>
       
  *  然后在MainActivity.java中设置
        
              listView.setEmptyView(findViewById(R.id.empty_view));



4. 动态修改ListView
-----------------
笨方法是重新设置adapter，但这样效率特别低。所以，要用notifyDataSetChanged()方法。

      Fruit mango = new Fruit("Mango", R.drawable.mango_pic);
      fruitList.add(mango);
      adapter.notifyDataSetChanged();

> **注意：此fruitList必须和之前是同一个对象！！！**如果新建了一个对象fruitList2，然后fruitList = fruitList2，这样是行不通的！！！因为并没有改变原始的对象。


5. 滑动监听
---------

* [View.OnTouchListener](http://developer.android.com/reference/android/view/View.OnTouchListener.html)

      listView.setOnTouchListener(new View.OnTouchListener(){
            @Override
            public boolean onTouch(View v, MotionEvent event){
                      switch(event.getAction()){
                            case MotionEvent.ACTION_DOWN:
                                    //触摸时
                                    break;
                            case MotionEvent.ACTION_MOVE:
                                    //移动时
                                    break;
                            case MotionEvent.ACTION_UP:
                                    //离开时
                                    break;
                      }     
           }
      });   

* [AbsListView.OnScrollListener](http://developer.android.com/reference/android/widget/AbsListView.OnScrollListener.html)   

      listView.setOnScrollListener(new OnScrollListener(){
            @Override
            public boolean onScrollStateChanged(AbsListView view, int scrollState){
                      switch(scrollState){
                             case OnScrollListener.SCROLL_STATE_IDLE:
                                    //滑动停止时
                                    break;
                             case OnScrollListener.SCROLL_STATE_IDLE:
                                    //正在滚动时
                                    break;
                             case OnScrollListener.SCROLL_STATE_IDLE:
                                    //手指抛动时，手指离开后随着惯性继续滑动的状态
                                    break;
                      }
           }   
           @Override
            public boolean onScroll(AbsListView view,
                                int firstVisibleItem,  //第一个能看见的item的ID
                                int visibleItemCount,  //当前能看见的item总数
                                int totalItemCount){  //整个ListView的item总数
                    //滚动时一直调用
                    //do something
            }
                                
      }