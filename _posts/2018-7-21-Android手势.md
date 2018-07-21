---
layout: post

title: Android手势的正确使用姿势

tags: [Android]

---



Android 手势 — GestureDetector的使用攻略



GestureDetector这个类对外提供了两个接口和一个外部类 

- 接口：OnGestureListener，OnDoubleTapListener 
- 外部类:SimpleOnGestureListener

这个外部类，其实是两个接口中所有函数的集成，它包含了这两个接口里所有必须要实现的函数而且都已经重写，但所有方法体都是空的；不同点在于：该类是static class，程序员可以在外部继承这个类，重写里面的手势处理方法。



## GestureDetector.OnGestureListener

如果我们写一个类并implements OnGestureListener，会提示有几个必须重写的函数，加上之后是这个样子的：

```java
private class gesturelistener implements GestureDetector.OnGestureListener{
 
	public boolean onDown(MotionEvent e) {
		// TODO Auto-generated method stub
		return false;
	}
 
	public void onShowPress(MotionEvent e) {
		// TODO Auto-generated method stub
		
	}
 
	public boolean onSingleTapUp(MotionEvent e) {
		// TODO Auto-generated method stub
		return false;
	}
 
	public boolean onScroll(MotionEvent e1, MotionEvent e2,
			float distanceX, float distanceY) {
		// TODO Auto-generated method stub
		return false;
	}
 
	public void onLongPress(MotionEvent e) {
		// TODO Auto-generated method stub
		
	}
 
	public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
			float velocityY) {
		// TODO Auto-generated method stub
		return false;
	}
}
```

### 回调介绍

可见，这里总共重写了六个函数，这些函数都在什么情况下才会触发呢，下面讲一下：

**OnDown(MotionEvent e)：**用户按下屏幕就会触发；
**onShowPress(MotionEvent e)：**如果是按下的时间超过100ms，而且在按下的时候没有松开或者是拖动的，那么onShowPress就会执行。
**onLongPress(MotionEvent e)：**长按触摸屏，超过100ms+500ms，就会触发这个事件
    触发顺序：
    onDown->onShowPress->onLongPress
**onSingleTapUp(MotionEvent e)：**从名子也可以看出,一次单独的轻击抬起操作,也就是轻击一下屏幕，立刻抬起来，才会有这个触发，当然,如果除了Down以外还有其它操作,那就不再算是Single操作了,所以也就不会触发这个事件
    触发顺序：
    点击一下非常快的（不滑动）Touchup：
    onDown->onSingleTapUp->onSingleTapConfirmed 
    点击一下稍微慢点的（不滑动）Touchup：
    onDown->onShowPress->onSingleTapUp->onSingleTapConfirmed
**onFling(MotionEvent e1, MotionEvent e2, float velocityX,float velocityY) ：**滑屏，用户按下触摸屏、快速移动后松开，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE, 1个ACTION_UP触发   
     参数解释：
    e1：第1个ACTION_DOWN MotionEvent
    e2：最后一个ACTION_MOVE MotionEvent
    velocityX：X轴上的移动速度，像素/秒
    velocityY：Y轴上的移动速度，像素/秒   
**onScroll(MotionEvent e1, MotionEvent e2,float distanceX, float distanceY)：**在屏幕上拖动事件。无论是用手拖动view，或者是以抛的动作滚动，都会多次触发,这个方法       在ACTION_MOVE动作发生时就会触发
    滑屏：手指触动屏幕后，稍微滑动后立即松开
    onDown-----》onScroll----》onScroll----》onScroll----》………----->onFling
    拖动
    onDown------》onScroll----》onScroll------》onFiling

​    可见，无论是滑屏，还是拖动，影响的只是中间OnScroll触发的数量多少而已，最终都会触发onFling事件！

### 使用GestureDetector

#### 第一步：创建OnGestureListener监听函数：

可以使用构造实例：

```java
GestureDetector.OnGestureListener listener = new GestureDetector.OnGestureListener(){
};
```

也可以构造类：

```java
private class gestureListener implements GestureDetector.OnGestureListener{
}
```

#### 第二步： 创建GestureDetector实例mGestureDetector：

构造函数有下面三个，根据需要选择：

```java
GestureDetector gestureDetector=new GestureDetector(GestureDetector.OnGestureListener listener);

GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.OnGestureListener listener);

GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.SimpleOnGestureListener listener);
```

#### 第三步：onTouch(View v, MotionEvent event)中拦截：

```
public boolean onTouch(View v, MotionEvent event) {
	return mGestureDetector.onTouchEvent(event);   
}
```

详细代码如下：

```java
public class MainActivity extends Activity implements OnTouchListener{
 
	private GestureDetector mGestureDetector;   
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
      mGestureDetector = new GestureDetector(new gestureListener()); //使用派生自OnGestureListener
        
      TextView tv = (TextView)findViewById(R.id.tv);
      tv.setOnTouchListener(this);
      tv.setFocusable(true);   
      tv.setClickable(true);   
      tv.setLongClickable(true); 
	}
	
	
	/* 
     * 在onTouch()方法中，我们调用GestureDetector的onTouchEvent()方法，将捕捉到的MotionEvent交给GestureDetector 
     * 来分析是否有合适的callback函数来处理用户的手势 
     */  
	public boolean onTouch(View v, MotionEvent event) {
		return mGestureDetector.onTouchEvent(event);   
	}
	
	private class gestureListener implements GestureDetector.OnGestureListener{
 
		// 用户轻触触摸屏，由1个MotionEvent ACTION_DOWN触发   
		public boolean onDown(MotionEvent e) {
			Log.i("MyGesture", "onDown");   
	        Toast.makeText(MainActivity.this, "onDown", Toast.LENGTH_SHORT).show();   
			return false;
		}
 
		/*  
	     * 用户轻触触摸屏，尚未松开或拖动，由一个1个MotionEvent ACTION_DOWN触发  
	     * 注意和onDown()的区别，强调的是没有松开或者拖动的状态  
	     * 
	     * 而onDown也是由一个MotionEventACTION_DOWN触发的，但是他没有任何限制，
	     * 也就是说当用户点击的时候，首先MotionEventACTION_DOWN，onDown就会执行，
	     * 如果在按下的瞬间没有松开或者是拖动的时候onShowPress就会执行，如果是按下的时间超过瞬间
	     * （这块我也不太清楚瞬间的时间差是多少，一般情况下都会执行onShowPress），拖动了，就不执行onShowPress。
	     */
		public void onShowPress(MotionEvent e) {
			Log.i("MyGesture", "onShowPress");   
	        Toast.makeText(MainActivity.this, "onShowPress", Toast.LENGTH_SHORT).show();   
		}
 
		// 用户（轻触触摸屏后）松开，由一个1个MotionEvent ACTION_UP触发   
		///轻击一下屏幕，立刻抬起来，才会有这个触发
		//从名子也可以看出,一次单独的轻击抬起操作,当然,如果除了Down以外还有其它操作,那就不再算是Single操作了,所以这个事件 就不再响应
		public boolean onSingleTapUp(MotionEvent e) {
			Log.i("MyGesture", "onSingleTapUp");   
	        Toast.makeText(MainActivity.this, "onSingleTapUp", Toast.LENGTH_SHORT).show();   
	        return true;   
		}
 
		// 用户按下触摸屏，并拖动，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE触发   
		public boolean onScroll(MotionEvent e1, MotionEvent e2,
				float distanceX, float distanceY) {
			Log.i("MyGesture22", "onScroll:"+(e2.getX()-e1.getX()) +"   "+distanceX);   
	        Toast.makeText(MainActivity.this, "onScroll", Toast.LENGTH_LONG).show();   
	        
	        return true;   
		}
 
		// 用户长按触摸屏，由多个MotionEvent ACTION_DOWN触发   
		public void onLongPress(MotionEvent e) {
			 Log.i("MyGesture", "onLongPress");   
		     Toast.makeText(MainActivity.this, "onLongPress", Toast.LENGTH_LONG).show();   
		}
 
		// 用户按下触摸屏、快速移动后松开，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE, 1个ACTION_UP触发   
		public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
				float velocityY) {
			Log.i("MyGesture", "onFling");   
	        Toast.makeText(MainActivity.this, "onFling", Toast.LENGTH_LONG).show();   
			return true;
		}
	};
	
 
}

 
```



> **特别需要注意的一点是：一定要设置setLongClickable(true)，否则GestureDetector无法正常运行，只会走onDown，onShowPress，onLongPress方法**



## GestureDetector.OnDoubleTapListener

### 回调介绍

**onSingleTapConfirmed(MotionEvent e)**：单击事件。用来判定该次点击是SingleTap而不是DoubleTap，如果连续点击两次就是DoubleTap手势，如果只点击一次，系统等待一段时间后没有收到第二次点击则判定该次点击为SingleTap而不是DoubleTap，然后触发SingleTapConfirmed事件。触发顺序是：OnDown->OnsingleTapUp->OnsingleTapConfirmed

关于onSingleTapConfirmed和onSingleTapUp的一点区别：onSingleTapUp，只要手抬起就会执行，而对于onSingleTapConfirmed来说，如果双击的话，则onSingleTapConfirmed不会执行。

**onDoubleTap(MotionEvent e)：**双击事件

**onDoubleTapEvent(MotionEvent e)：**双击间隔中发生的动作。指触发onDoubleTap以后，在双击之间发生的其它动作，包含down、up和move事件；下图是双击一下的Log输出：

###使用GestureDetector.OnDoubleTapListener

**方法一：新建一个类同时派生自OnGestureListener和OnDoubleTapListener：**

```java
private class gestureListener implements GestureDetector.OnGestureListener,GestureDetector.OnDoubleTapListener{
	}
```

**方法二：使用GestureDetector::setOnDoubleTapListener();函数设置监听：**

```java
//构建GestureDetector实例	
mGestureDetector = new GestureDetector(new gestureListener()); //使用派生自OnGestureListener
private class gestureListener implements GestureDetector.OnGestureListener{
	
}
 
//设置双击监听器
mGestureDetector.setOnDoubleTapListener(new doubleTapListener());
private class doubleTapListener implements GestureDetector.OnDoubleTapListener{
	
}
```



> **注意：大家可以看到无论在方法一还是在方法二中，都需要派生自GestureDetector.OnGestureListener，前面我们说过GestureDetector 的构造函数，如下：**



```java
GestureDetector gestureDetector=new GestureDetector(GestureDetector.OnGestureListener listener);

GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.OnGestureListener listener);

GestureDetector gestureDetector=new GestureDetector(Context context,GestureDetector.SimpleOnGestureListener listener);
```

> **可以看到，在构造函数中，除了SimpleOnGestureListener 以外的其它两个构造函数都必须是OnGestureListener的实例。所以要想使用OnDoubleTapListener的几个函数，就必须先实现OnGestureListener。**



详细代码如下：

```java
public class MainActivity extends Activity implements OnTouchListener{
 
	private GestureDetector mGestureDetector;   
	
 
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
 
      mGestureDetector = new GestureDetector(new gestureListener()); //使用派生自OnGestureListener
      mGestureDetector.setOnDoubleTapListener(new doubleTapListener());
        
      TextView tv = (TextView)findViewById(R.id.tv);
      tv.setOnTouchListener(this);
      tv.setFocusable(true);   
      tv.setClickable(true);   
      tv.setLongClickable(true); 
	}
	
	
	/* 
     * 在onTouch()方法中，我们调用GestureDetector的onTouchEvent()方法，将捕捉到的MotionEvent交给GestureDetector 
     * 来分析是否有合适的callback函数来处理用户的手势 
     */  
	public boolean onTouch(View v, MotionEvent event) {
		return mGestureDetector.onTouchEvent(event);   
	}
	
	//OnGestureListener监听
	private class gestureListener implements GestureDetector.OnGestureListener{
 
		public boolean onDown(MotionEvent e) {
			Log.i("MyGesture", "onDown");   
	        Toast.makeText(MainActivity.this, "onDown", Toast.LENGTH_SHORT).show();   
			return false;
		}
 
		public void onShowPress(MotionEvent e) {
			Log.i("MyGesture", "onShowPress");   
	        Toast.makeText(MainActivity.this, "onShowPress", Toast.LENGTH_SHORT).show();   
		}
 
		public boolean onSingleTapUp(MotionEvent e) {
			Log.i("MyGesture", "onSingleTapUp");   
	        Toast.makeText(MainActivity.this, "onSingleTapUp", Toast.LENGTH_SHORT).show();   
	        return true;   
		}
 
		public boolean onScroll(MotionEvent e1, MotionEvent e2,
				float distanceX, float distanceY) {
			Log.i("MyGesture22", "onScroll:"+(e2.getX()-e1.getX()) +"   "+distanceX);   
	        Toast.makeText(MainActivity.this, "onScroll", Toast.LENGTH_LONG).show();   
	        
	        return true;   
		}
 
		public void onLongPress(MotionEvent e) {
			 Log.i("MyGesture", "onLongPress");   
		     Toast.makeText(MainActivity.this, "onLongPress", Toast.LENGTH_LONG).show();   
		}
 
		public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
				float velocityY) {
			Log.i("MyGesture", "onFling");   
	        Toast.makeText(MainActivity.this, "onFling", Toast.LENGTH_LONG).show();   
			return true;
		}
	};
	
	//OnDoubleTapListener监听
	private class doubleTapListener implements GestureDetector.OnDoubleTapListener{
 
		public boolean onSingleTapConfirmed(MotionEvent e) {
			Log.i("MyGesture", "onSingleTapConfirmed");   
	        Toast.makeText(MainActivity.this, "onSingleTapConfirmed", Toast.LENGTH_LONG).show();  
			return true;
		}
 
		public boolean onDoubleTap(MotionEvent e) {
			Log.i("MyGesture", "onDoubleTap");   
	        Toast.makeText(MainActivity.this, "onDoubleTap", Toast.LENGTH_LONG).show();  
			return true;
		}
 
		public boolean onDoubleTapEvent(MotionEvent e) {
			Log.i("MyGesture", "onDoubleTapEvent");   
	        Toast.makeText(MainActivity.this, "onDoubleTapEvent", Toast.LENGTH_LONG).show();  
			return true;
		}
	};
}
```



## GestureDetector.SimpleOnGestureListener

它与前两个不同的是：

1、这是一个类，在它基础上新建类的话，要用extends派生而不是用implements继承！

2、OnGestureListener和OnDoubleTapListener接口里的函数都是强制必须重写的，即使用不到也要重写出来一个空函数但在SimpleOnGestureListener类的实例或派生类中不必如此，可以根据情况，用到哪个函数就重写哪个函数，因为SimpleOnGestureListener类本身已经实现了这两个接口的所有函数，只是里面全是空的而已。

下面利用SimpleOnGestureListener类来重新实现上面的几个效果，代码如下：

```java
public class MainActivity extends Activity implements OnTouchListener {
 
	private GestureDetector mGestureDetector;   
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		mGestureDetector = new GestureDetector(new simpleGestureListener());
		
		TextView tv = (TextView)findViewById(R.id.tv);
	    tv.setOnTouchListener(this);
	    tv.setFocusable(true);   
	    tv.setClickable(true);   
	    tv.setLongClickable(true); 
	}
	
	public boolean onTouch(View v, MotionEvent event) {
		// TODO Auto-generated method stub
		return mGestureDetector.onTouchEvent(event);   
	}
 
	private class simpleGestureListener extends
			GestureDetector.SimpleOnGestureListener {
		
		/*****OnGestureListener的函数*****/
		public boolean onDown(MotionEvent e) {
			Log.i("MyGesture", "onDown");
			Toast.makeText(MainActivity.this, "onDown", Toast.LENGTH_SHORT)
					.show();
			return false;
		}
 
		public void onShowPress(MotionEvent e) {
			Log.i("MyGesture", "onShowPress");
			Toast.makeText(MainActivity.this, "onShowPress", Toast.LENGTH_SHORT)
					.show();
		}
 
		public boolean onSingleTapUp(MotionEvent e) {
			Log.i("MyGesture", "onSingleTapUp");
			Toast.makeText(MainActivity.this, "onSingleTapUp",
					Toast.LENGTH_SHORT).show();
			return true;
		}
 
		public boolean onScroll(MotionEvent e1, MotionEvent e2,
				float distanceX, float distanceY) {
			Log.i("MyGesture", "onScroll:" + (e2.getX() - e1.getX()) + "   "
					+ distanceX);
			Toast.makeText(MainActivity.this, "onScroll", Toast.LENGTH_LONG)
					.show();
 
			return true;
		}
 
		public void onLongPress(MotionEvent e) {
			Log.i("MyGesture", "onLongPress");
			Toast.makeText(MainActivity.this, "onLongPress", Toast.LENGTH_LONG)
					.show();
		}
 
		public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
				float velocityY) {
			Log.i("MyGesture", "onFling");
			Toast.makeText(MainActivity.this, "onFling", Toast.LENGTH_LONG)
					.show();
			return true;
		}
		
		/*****OnDoubleTapListener的函数*****/
		public boolean onSingleTapConfirmed(MotionEvent e) {
			Log.i("MyGesture", "onSingleTapConfirmed");
			Toast.makeText(MainActivity.this, "onSingleTapConfirmed",
					Toast.LENGTH_LONG).show();
			return true;
		}
 
		public boolean onDoubleTap(MotionEvent e) {
			Log.i("MyGesture", "onDoubleTap");
			Toast.makeText(MainActivity.this, "onDoubleTap", Toast.LENGTH_LONG)
					.show();
			return true;
		}
 
		public boolean onDoubleTapEvent(MotionEvent e) {
			Log.i("MyGesture", "onDoubleTapEvent");
			Toast.makeText(MainActivity.this, "onDoubleTapEvent",
					Toast.LENGTH_LONG).show();
			return true;
		}
 
	}
}
```



## OnFling应用——识别向左滑还是向右滑

这部分就有点意思了，可以说是上面知识的一个小应用，我们利用OnFling函数来识别当前用户是在向左滑还是向右滑，从而打出日志。先看下OnFling的参数：

```java
boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,float velocityY)
参数解释：   
e1：第1个ACTION_DOWN MotionEvent   
e2：最后一个ACTION_MOVE MotionEvent   
velocityX：X轴上的移动速度，像素/秒   
velocityY：Y轴上的移动速度，像素/秒 
```

首先，先说一下实现的功能：当用户向左滑动距离超过100px，且滑动速度超过100 px/s时,即判断为向左滑动;向右同理.代码如下:

```java
public class MainActivity extends Activity implements OnTouchListener {
 
	private GestureDetector mGestureDetector;   
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		mGestureDetector = new GestureDetector(new simpleGestureListener());
		
		TextView tv = (TextView)findViewById(R.id.tv);
	    tv.setOnTouchListener(this);
	    tv.setFocusable(true);   
	    tv.setClickable(true);   
	    tv.setLongClickable(true); 
	}
	
	public boolean onTouch(View v, MotionEvent event) {
		// TODO Auto-generated method stub
		return mGestureDetector.onTouchEvent(event);   
	}
 
	private class simpleGestureListener extends
			GestureDetector.SimpleOnGestureListener {
		
		/*****OnGestureListener的函数*****/
 
		final int FLING_MIN_DISTANCE = 100, FLING_MIN_VELOCITY = 200;  
		
		// 触发条件 ：   
        // X轴的坐标位移大于FLING_MIN_DISTANCE，且移动速度大于FLING_MIN_VELOCITY个像素/秒   
       
		// 参数解释：   
        // e1：第1个ACTION_DOWN MotionEvent   
        // e2：最后一个ACTION_MOVE MotionEvent   
        // velocityX：X轴上的移动速度，像素/秒   
        // velocityY：Y轴上的移动速度，像素/秒   
		public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
				float velocityY) {
			
	        
	        if (e1.getX() - e2.getX() > FLING_MIN_DISTANCE  
	                && Math.abs(velocityX) > FLING_MIN_VELOCITY) {  
	            // Fling left   
	            Log.i("MyGesture", "Fling left");  
	            Toast.makeText(MainActivity.this, "Fling Left", Toast.LENGTH_SHORT).show();  
	        } else if (e2.getX() - e1.getX() > FLING_MIN_DISTANCE  
	                && Math.abs(velocityX) > FLING_MIN_VELOCITY) {  
	            // Fling right   
	            Log.i("MyGesture", "Fling right");  
	            Toast.makeText(MainActivity.this, "Fling Right", Toast.LENGTH_SHORT).show();  
	        }  
			return true;
		}
 
	}
}
```

