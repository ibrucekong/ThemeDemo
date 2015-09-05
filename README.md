# ThemeDemo
日夜间模式切换
效果图:

![ThemeDemo.gif](http://upload-images.jianshu.io/upload_images/166866-f4a26bbeebb3fff9.gif?imageMogr2/auto-orient/strip)


[Github](https://github.com/zzz40500/ThemeDemo)
前面:
实现的原理像我微博之前的说的那样.
>关于多主题实现的,我这里的做法是继承AppCompatActivity,置换了AppCompatDelegate中AppCompatViewInflater中的createView 方法.实现了对 xml 控件的控制.


解决的问题:
1. 实现了日夜模式的切换.(不重启 Acitivity )
* 解决了因为快速点击 View 导致的多次响应点击事件.
* 内部实现了 Android 5.0 的CircularReveal效果.

优点:
布局中直接使用 Android 默认的控件就可以.在解析以后会根据控件转换成支持主题切换的控件.
缺点:
暂时不支持 Menu 级的切换.

支持属性:
View 级:  
`nightBackground`  
TextView 级:  
`nightTextColor`  
`nightTextColorHighlight`  
`nightTextAppearance`  
`nightTextColorLink`  
`nightTextColorHint`  
ListView 级:  
`nightLVDivider`  
LinearLayout 级别:  
`nightDivider`  
第三方控件支持:
`nightBackground`  
`nightTextColor`  



使用方法:
 Activity 继承MAppCompatActivity  
布局上
~~~
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:id="@+id/rl"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:background="@color/light_bg"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:orientation="vertical"
              tools:ignore="MissingPrefix"
              app:nightBackground="@color/night_bg"
              tools:context=".MainActivity">
    <android.support.v7.widget.Toolbar
        android:id="@+id/toolBar"
        android:layout_height="?attr/actionBarSize"
        android:layout_width="fill_parent"
        />
    <TextView
        android:layout_width="fill_parent"
        android:id="@+id/tv"
        android:layout_below="@+id/toolBar"
        android:gravity="center"
        android:background="@color/red"
        android:text="TextView"
        android:textColor="@color/normal_black"
        android:textAppearance="@style/TextAppearance.AppCompat.Display1"
        android:layout_height="100dp"
        app:nightTextColor="@color/night_tv_color"
        app:nightBackground="@color/night_bg"
        />

    <Button
        android:layout_below="@+id/tv"
        android:layout_width="fill_parent"
        android:text="Click"
        android:id="@+id/button"
        android:background="@color/button_bg"
        android:textColor="@color/normal_black"
        android:layout_height="50dp"
        app:nightBackground="@color/night_bg"
        />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fb"
        android:layout_width="48dp"
        android:layout_alignParentRight="true"
        android:layout_alignParentBottom="true"
        android:layout_margin="16dp"
        android:layout_height="48dp"
        app:backgroundTint="#ff87ffeb"
        app:rippleColor="#33728dff"
        android:src="@mipmap/ic_launcher"
        android:orientation="vertical" />

</RelativeLayout>
~~~
处女座在根控件加入`tools:ignore="MissingPrefix"`


切换主题方法:
~~~


/**
 * 
 * @param activity 当前 Activity
 * @param skinStyle Dark(夜间),Light(日间)
 * @param skinStyleChangeListener (转换监听器)
 */
SkinCompat.setSkinStyle(Activity activity, SkinStyle skinStyle,SkinStyleChangeListener skinStyleChangeListener) 
~~~

使用CircularReveal 效果:
~~~
 

CRAnimation crA =
        new CircularRevealCompat(mRl).circularReveal(
                mFloatingActionButton.getLeft() + mFloatingActionButton.getWidth() / 2, mFloatingActionButton.getTop() + mFloatingActionButton.getHeight() / 2, 0, mRl.getHeight());

if (crA != null)
    crA.start();
~~~


扩展:
第三方控件支持CircularReveal效果:
实现CircleRevealEnable这个接口:
模板:
~~~
/**
 * Created by zzz40500 on 15/8/26.
 */
public class Button extends AppCompatButton implements CircleRevealEnable{

    private CircleRevealHelper  mCircleRevealHelper ;
   
    public Button(Context context) {
        super(context);
        init(null);

    }

    public Button(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(attrs);
    }

    private void init(AttributeSet attrs) {

        mCircleRevealHelper=new CircleRevealHelper(this);
    }

    public Button(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(attrs);
    }

    @Override
    public void setOnClickListener(OnClickListener l) {
        super.setOnClickListener(new SingleClickListener(l));
    }

    @Override
    public void draw(Canvas canvas) {
        mCircleRevealHelper.draw(canvas);
    }

    @Override
    public void superDraw(Canvas canvas) {
        super.draw(canvas);
    }

    @Override
    public CRAnimation circularReveal(int centerX, int centerY, float startRadius, float endRadius) {
       return mCircleRevealHelper.circularReveal(centerX,centerY,startRadius,endRadius);
    }

}
~~~






