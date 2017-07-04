---
layout: post
title: Android自义定view之加载动画
date: 2017-05-15
categories: blog
tags: [Android]
description: 安卓数据库可视化工具推荐

---

更多细节请看源码
https://github.com/que123567/CradleBallView
用途：可充当加载动画

效果如图
![这里写图片描述](http://img.blog.csdn.net/20170601123523478?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### **1.自定义类CradleBall继承自View**

即：画一个小球

```
public class CradleBall extends View {
    private int width;
    private int height;

    private Paint paint;

    private int loadingColor = Color.WHITE;

    public CradleBall(Context context) {
        super(context);
        initView(null);
    }

    public CradleBall(Context context, AttributeSet attrs) {
        super(context, attrs);
        initView(attrs);
    }

    public CradleBall(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initView(attrs);
    }

    private void initView(AttributeSet attrs) {
        if (null != attrs) {
            TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable
                    .CradleBall);
            loadingColor = typedArray.getColor(R.styleable.CradleBall_cradle_ball_color, Color
                    .BLACK);
            typedArray.recycle();
        }
        paint = new Paint();
        paint.setColor(loadingColor);
        paint.setStyle(Paint.Style.FILL);
        paint.setAntiAlias(true);
    }

    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        width = w;
        height = h;
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        canvas.drawCircle(width / 2, height / 2, width / 2, paint);
    }

    public void setLoadingColor(int color) {
        loadingColor = color;
        paint.setColor(color);
        postInvalidate();
    }

    public int getLoadingColor() {
        return loadingColor;
    }

}

```
其中带有2个参数的第二个构造函数，在当xml中传入自定义属性时会被调用。根据传入的这个参数，自定义小球的颜色。

关于view传入自定义属性通常需要以下几个步骤:

- 1.自定义一个extends View的类

- 2.编写values/attrs.xml，在其中编写styleable和item等标签元素,如
```
 <declare-styleable name="CradleBall">
        <attr name="cradle_ball_color" format="color"/>
 </declare-styleable>
```

- 3.在布局文件中XMl中使用自定义的属性（注意名空间）
 一般默认res-auto
```
xmlns:smaug="http://schemas.android.com/apk/res-auto"
```
- 4.在自定义view的构造方法中通过TypedArray获取(如上代码所示)


### **2.定义一个持彩球引用的布局**

 通过ScaleAnimation实现小球的大小变化。
 延迟效果的实现原理很简单，设置不同的动画起始时间即可
```
 mScaleAnimation5.setStartOffset();
```

小球布局完整代码如下
```
public class ColorBallLoading extends LinearLayout {
    private CradleBall cradleBallOne;
    private CradleBall cradleBallTwo;
    private CradleBall cradleBallThree;
    private CradleBall cradleBallFour;
    private CradleBall cradleBallFive;

    ScaleAnimation mScaleAnimation1;//大小
    ScaleAnimation mScaleAnimation2;
    ScaleAnimation mScaleAnimation3;
    ScaleAnimation mScaleAnimation4;
    ScaleAnimation mScaleAnimation5;

    private boolean isStart = false;

    public ColorBallLoading(Context context) {
        super(context);
        initView(context);
    }

    public ColorBallLoading(Context context, AttributeSet attrs) {
        super(context, attrs);
        initView(context);
    }

    public ColorBallLoading(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initView(context);
    }

    private void initView(Context context) {
        LayoutInflater.from(context).inflate(R.layout.animation_color_ball, this, true);
    }

    @Override
    protected void onFinishInflate() {
        super.onFinishInflate();
        cradleBallOne = (CradleBall) findViewById(R.id.ball_one);
        cradleBallTwo = (CradleBall) findViewById(R.id.ball_two);
        cradleBallThree = (CradleBall) findViewById(R.id.ball_three);
        cradleBallFour = (CradleBall) findViewById(R.id.ball_four);
        cradleBallFive = (CradleBall) findViewById(R.id.ball_five);

    }


    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }


    @Override
    protected void onAttachedToWindow() {
        super.onAttachedToWindow();
        // 设置颜色
        cradleBallOne.setLoadingColor(getResources().getColor(R.color.red));
        cradleBallTwo.setLoadingColor(getResources().getColor(R.color.orange));
        cradleBallThree.setLoadingColor(getResources().getColor(R.color.yellow));
        cradleBallFour.setLoadingColor(getResources().getColor(R.color.lightgreen));
        cradleBallFive.setLoadingColor(getResources().getColor(R.color.purple));
        //变大变小
        startAni();

    }

    @Override
    protected void onDetachedFromWindow() {
        super.onDetachedFromWindow();
        stop();
    }


    public void start() {
        if (!isStart) {
            isStart = true;
            startAni();//开始动画
        }
    }

    public void stop() {
        isStart = false;
        cradleBallOne.clearAnimation();
        cradleBallTwo.clearAnimation();
        cradleBallThree.clearAnimation();
        cradleBallFour.clearAnimation();
        cradleBallFive.clearAnimation();

    }

    public boolean isStart() {
        return isStart;
    }


    public void setLoadingColor(int color) {
        cradleBallOne.setLoadingColor(color);
        cradleBallTwo.setLoadingColor(color);
        cradleBallThree.setLoadingColor(color);
        cradleBallFour.setLoadingColor(color);
        cradleBallFive.setLoadingColor(color);
    }

    public void startAni() {
        mScaleAnimation1 = new ScaleAnimation(1, 0, 1, 0, 1, 0.5f, 1, 0.5f);
        mScaleAnimation1.setDuration(700);
        mScaleAnimation1.setStartOffset(20);
        mScaleAnimation1.setInterpolator(new LinearInterpolator());
        mScaleAnimation1.setRepeatCount(-1);
        mScaleAnimation1.setRepeatMode(Animation.REVERSE);


        mScaleAnimation2 = new ScaleAnimation(1, 0, 1, 0, 1, 0.5f, 1, 0.5f);
        mScaleAnimation2.setDuration(700);
        mScaleAnimation2.setStartOffset(40);
        mScaleAnimation2.setInterpolator(new LinearInterpolator());
        mScaleAnimation2.setRepeatCount(-1);
        mScaleAnimation2.setRepeatMode(Animation.REVERSE);

        mScaleAnimation3 = new ScaleAnimation(1, 0, 1, 0, 1, 0.5f, 1, 0.5f);
        mScaleAnimation3.setDuration(700);
        mScaleAnimation3.setStartOffset(60);
        mScaleAnimation3.setInterpolator(new LinearInterpolator());
        mScaleAnimation3.setRepeatCount(-1);
        mScaleAnimation3.setRepeatMode(Animation.REVERSE);


        mScaleAnimation4 = new ScaleAnimation(1, 0, 1, 0, 1, 0.5f, 1, 0.5f);
        mScaleAnimation4.setDuration(700);
        mScaleAnimation4.setStartOffset(80);
        mScaleAnimation4.setInterpolator(new LinearInterpolator());
        mScaleAnimation4.setRepeatCount(-1);
        mScaleAnimation4.setRepeatMode(Animation.REVERSE);

        mScaleAnimation5 = new ScaleAnimation(1, 0, 1, 0, 1, 0.5f, 1, 0.5f);
        mScaleAnimation5.setDuration(700);
        mScaleAnimation5.setStartOffset(100);
        mScaleAnimation5.setInterpolator(new LinearInterpolator());
        mScaleAnimation5.setRepeatCount(-1);
        mScaleAnimation5.setRepeatMode(Animation.REVERSE);

        cradleBallOne.startAnimation(mScaleAnimation1);
        cradleBallTwo.startAnimation(mScaleAnimation2);
        cradleBallThree.startAnimation(mScaleAnimation3);
        cradleBallFour.startAnimation(mScaleAnimation4);
        cradleBallFive.startAnimation(mScaleAnimation5);
    }
}
```
