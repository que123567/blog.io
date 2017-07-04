---
layout: post
title: Android自义定view之九宫格解锁
date: 2017-05-15
categories: blog
tags: [Android]
description:  

---
## android自定义view之九宫格解锁
更多细节请看源码
https://github.com/que123567/lockview


- **1. 定义一个类作为九宫格的格子**

   包含坐标和索引（用来记录密码）
   point含三种状态分别对应三种不同情况下点的图片样式
```
package lockview;

/**
 * Created by smaug on 2017/5/11.
 */

public class Point {
    public float x;
    public float y;
    private int index;

    public int getIndex() {
        return index;
    }

    public void setIndex(int index) {
        this.index = index;
    }

    public static final int STATU_NORNAL = 0;
    public static final int STATU_PRESSED = 1;
    public static final int STATU_ERROR = 2;
    public int state;


    public void setState(int state) {
        this.state = state;
    }

    public int getState() {
        return state;
    }

    public Point() {super();}

    public Point(float x, float y) {
        this.x = x;
        this.y = y;
    }

    public float getX() {
        return x;
    }

    public void setX(float x) {
        this.x = x;
    }

    public float getY() {
        return y;
    }

    public void setY(float y) {
        this.y = y;
    }
}

```

- **2.新建类LockView继承自View**
  重写onDraw()方法
  - 初始化点的时候根据获取当前屏幕的宽高，比对来确定当前状态是横屏还是竖屏
  -   从资源中获取Bitmap，将点设置成图像。

```
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
            initPoints();
            initPaint();    
    }

  private void drawPoints(Canvas canvas) {
        for (int i = 0; i < points.length; i++) {
            for (int j = 0; j < points[i].length; j++) {
                Point point = points[i][j];
                if (point.getState() == Point.STATU_NORNAL) {
                    //获取当前点的坐标,减去图片的半径得到left和top的坐标点
                    canvas.drawBitmap(point_normal, point.x - br, point.y - br, null);
                } else if (point.getState() == Point.STATU_PRESSED) {
                    canvas.drawBitmap(point_pressed, point.x - br, point.y - br, null);

                } else if (point.getState() == Point.STATU_ERROR) {
                    canvas.drawBitmap(point_error, point.x - br, point.y - br, null);
                }
            }
        }
    }

public class LockView extends View {

 private void initPoints() {
        width = getWidth();
        height = getHeight();
        if (height > width) {//竖屏
            offsetY = (height - width) / 2;
            height = width;
        } else { //横屏
            offsetX = (height - width) / 2;
            width = height;
        }

        /**
         *     定义9个点的坐标
         */
        points = new Point[row][colun];
        int index = 1;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < colun; j++) {
                points[i][j] = new Point(offsetX + (width / (row + 1)) * (i + 1), offsetY +
                        (width / (colun + 1)) * (j + 1));
                points[i][j].setIndex(index);
                index++;
            }
        }

        /**
         * 从资源中获取Bitmap
         */
        point_normal = BitmapFactory.decodeResource(getResources(), R.drawable.point_normal);
        point_pressed = BitmapFactory.decodeResource(getResources(), R.drawable.point_pressed);
        point_error = BitmapFactory.decodeResource(getResources(), R.drawable.point_error);

        br = point_normal.getWidth() / 2;
    }
}
```
显示结果如下图

![九宫格](http://img.blog.csdn.net/20170531185731034?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)




- **3.重写onTouchEvent**

```
  case MotionEvent.ACTION_DOWN:
                reset();//先清空之前绘制的状态
                checkedPoint = checkPoint(eventX, eventY, br);
                if (checkedPoint != null) {
                    isSelecte = true;
                    checkedPoint.setState(Point.STATU_PRESSED);
                }
                break;

```

checkPoint()通过计算坐标距离判断当前手指坐标是否在point图片上

```
  private Point checkPoint(float eventX, float eventY, float br) {
        for (int i = 0; i < points.length; i++) {
            for (int j = 0; j < points[i].length; j++) {
                Point point = points[i][j];
                double distance = getDistance(point.x, point.y, eventX, eventY);
                if (distance < br) {
                    return point;
                }
            }
        }
        return null;
    }
```
getDistance()利用勾股定理计算坐标距离
```
private double getDistance(float x, float y, float eventX, float eventY) {
        return Math.sqrt(Math.abs(x - eventX) * Math.abs(x - eventX) + Math.abs(y - eventY) *
                Math.abs(y - eventY));
    }
```
  这个时候手指接触都某个点，某个点的状态就会从normal转换为pressed,其对应的图片也会改变。



- **4.添加点与点之间的连线 **
LockView中定义一个Point类的List --->pointList 记录用户按下的点

- 当pointList为空，即用户未按下任一点的时候*不绘制线段*，其余时刻都绘制

- 绘制线段调用canvas的drawLine()方法,传入起始点x,y坐标与目标点x,y坐标与一个自定义的paint画笔绘制线段		

```
 private void drawLine(Canvas canvas) {
        if (pointList.size() > 0) {
            Point a = pointList.get(0);
            for (int i = 1; i < pointList.size(); i++) {
                Point b = pointList.get(i);
                canvas.drawLine(a.x, a.y, b.x, b.y, paint);
                a = b;
            }
            if (!moveOnPoint) { //
                canvas.drawLine(a.x, a.y, eventX, eventY, paint);
            }
        }
    }
```
效果如图所示

![九宫格连线](http://img.blog.csdn.net/20170531185909866?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdmlwOTUwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- **5.自动连接中间点 **

在九宫格中，如果用户跳开中间点只连接了头尾两个点，按照规则应该把中间点自动连上。

即在ACTION_MOVE过程中始终判断中间点是否被连入，而中间点的判断方式为首尾点坐标/2.

```
case MotionEvent.ACTION_MOVE:
    if (isSelecte) {
       checkedPoint = checkPoint(eventX, eventY, br);
        if (checkedPoint != null) {
             if (!pointList.contains(checkedPoint)) {
                   middlePoint = checkPoint((checkedPoint.x + lastPoint.x) / 2, (checkedPoint.y + lastPoint.y) / 2, br);
     if (middlePoint != null) {
                middlePoint.setState(Point.STATU_PRESSED);
                        }
                 }
            checkedPoint.setState(Point.STATU_PRESSED);
              moveOnPoint = true;
           } else {
            moveOnPoint = false;
        }
       }
     break;
```

- **6.此外，在绘制过程中会记录密码，在使用该LockView时可设置setOnLockSuccessed监听来判断，其中带有参数：正确密码-passward  **

 对应的，连接数少于5判定为连接失败，也有对应的监听事件。

```
 if (pointList != null) {
                if (pointList.size() == 1) {
                    reset();//重置
                } else if (pointList.size() < 5) {//个数不足
                    errorPoint();//绘制错误的图案
                    if (onLockChangeListener != null) { //失败
                        onLockChangeListener.setOnLockError();
                    }
                } else if (pointList.size() >= 5) { //九宫格连接数应大于等于5
                    StringBuilder passward = new StringBuilder();
                    for (int i = 0; i < pointList.size(); i++) {
                        int tmpIndex = pointList.get(i).getIndex();
                        passward.append(tmpIndex + "");
                    }
                    if (onLockChangeListener != null)//成功
                        onLockChangeListener.setOnLockSuccessed(passward);
                }
            }
```
