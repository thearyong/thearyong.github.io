---
title: "Android自定义dialog显示问题"
date: 2015-08-15 19:30:08
tags: [android,自定义dialog,Window,WindowManager]
categories: android
---

之前项目中，自定义 dialog 时，不管怎么调整布局边距等属性，都没有效果。今天就以简单的弹窗显示问题，来继续深入了解 Window 实现原理与应用。
<!-- more -->

## 目的
1. 弹窗显示位置不对问题
2. 了解 Window

## 分析
先从一个案例开始吧。
### 0001 需求
**长按控件，弹出选择菜单。**
### 0010 实现
实现长按弹出框类似效果，首先想到的是用 ContextMenu 来实现，但是不够灵活，界面上和 app 不够协调。然后想到的是 Popwindow 和 Dialog ，最后选择 dialog 是因为它自带黑色半透明阴影效果。

**显示代码：**
```java
public void update(List<String> items, final PopDialogListener listener) {
    LinearLayout linearLayout = new LinearLayout(getContext());
    linearLayout.setOrientation(LinearLayout.VERTICAL);
    FrameLayout.LayoutParams layoutParams=new FrameLayout.LayoutParams(-1,-2);
    layoutParams.gravity = Gravity.Bottom | Gravity.CENTER_HORIZONTAL;    linearLayout.setLayoutParams(layoutParams); setContentView(linearLayout);
    for (int i = 0; i < items.size(); i++) {
        View itemView = LayoutInflater.from(getContext()).inflate(R.layout.pop_view, null);
        linearLayout.addView(itemView, new LinearLayout.LayoutParams(-1, -2));
        TextView tvInfo = (TextView) itemView.findViewById(R.id.tv_info);
        tvInfo.setText(items.get(i));
        final int pos = i;
        itemView.setClickable(true);
        itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                dismiss();
                if (listener != null)
                    listener.onItemClick(pos);
            }
        });
        if (i == 0){//特殊处理第一行，兼容标题（是否可点击、文字样式）
            if(firstItemTextColor != -1)
                tvInfo.setTextColor(firstItemTextColor);
            if(firstItemTextSize > 0)
                tvInfo.setTextSize(firstItemTextSize);
            if(!firstItemClickAble)
                itemView.setClickable(false);
        }
    }
    setCancelable(true);
    setCanceledOnTouchOutside(true);
    show();
}
```

### 0011 运行结果
![0x2-3](http://img.thearyong.com/2016-09-18-01-08-38-0x2-3.png!post)

### 0100 提出问题

**疑问：上面红色部分代码在setContentView时已经设置了填充屏幕宽和居下显示，为什么不起作用？**

打开Andriod Device Monitor,如下图：

![0x3-1](http://img.thearyong.com/2016-09-18-01-11-36-0x3-1.png!post)

之前一直认为是在 activity 的根布局上盖了一层，共用一个 window。结果当然大相庭径，发现 Dialog 布局是独立于 activity 所在的 window。所以多翻翻源码才是实际，所谓大胆假设，小心求证，正式如此！
```java
Dialog(Context context, int theme, boolean createContextThemeWrapper) {
        if (createContextThemeWrapper) {
            if (theme == 0) {
                TypedValue outValue = new TypedValue();
 context.getTheme().resolveAttribute(com.android.internal.R.attr.dialogTheme,outValue, true);
                theme = outValue.resourceId;
            }
            mContext = new ContextThemeWrapper(context, theme);
        } else {
            mContext = context;
        }
        mWindowManager = (WindowManager)context.getSystemService(Context.WINDOW_SERVICE);
        Window w = PolicyManager.makeNewWindow(mContext);
        mWindow = w;
        w.setCallback(this);
        w.setOnWindowDismissedCallback(this);
        w.setWindowManager(mWindowManager, null, null);
        w.setGravity(Gravity.CENTER);
        mListenersHandler = new ListenersHandler(this);
    }
```

从上述代码可见，调用PolicyManager.makeNewWindow(mContext);重新new了个window,添加到WindowManager中.

>注：这里引用的 mContext 为当前 activity 的引用，突然想到经常遇到的一个问题：当 Dialog 显示的时候，finish 掉 activity 会出现的内存泄漏打印，因为还被 dialog 引用着。

在调用 show() 时，才真正把Dialog添加到window中：
```java
public void show() {
        if (mShowing) {
            if (mDecor != null) {
                if (mWindow.hasFeature(Window.FEATURE_ACTION_BAR)) {
                    mWindow.invalidatePanelMenu(Window.FEATURE_ACTION_BAR);
                }
                mDecor.setVisibility(View.VISIBLE);
            }
            return;
        }
        mCanceled = false;

        if (!mCreated) {
            dispatchOnCreate(null);
        }
        onStart();
        mDecor = mWindow.getDecorView();

        if (mActionBar == null && mWindow.hasFeature(Window.FEATURE_ACTION_BAR)) {
            final ApplicationInfo info = mContext.getApplicationInfo();
            mWindow.setDefaultIcon(info.icon);
            mWindow.setDefaultLogo(info.logo);
            mActionBar = new WindowDecorActionBar(this);
        }
        WindowManager.LayoutParams l = mWindow.getAttributes();
        if ((l.softInputMode
                & WindowManager.LayoutParams.SOFT_INPUT_IS_FORWARD_NAVIGATION) == 0) {
            WindowManager.LayoutParams nl = new WindowManager.LayoutParams();
            nl.copyFrom(l);
            nl.softInputMode |=
               WindowManager.LayoutParams.SOFT_INPUT_IS_FORWARD_NAVIGATION;
            l = nl;
        }

        try {
            mWindowManager.addView(mDecor, l);
            mShowing = true;
            sendShowMessage();
        } finally {
        }
    }
```
看到这里，值得注意的是 WindowManger 有个内部类 LayoutParams 被设置了参数，这里才是真正设置视图大小的地方。

### 0101 解决方法

找到症结所在，上面显示代码添加重新设置显示位置：
```java
public void update(List<String> items, final PopDialogListener listener) {

    WindowManager.LayoutParams lp1 = getWindow().getAttributes();
    lp1.width = WindowManager.LayoutParams.MATCH_PARENT;
    lp1.height = WindowManager.LayoutParams.WRAP_CONTENT;
    lp1.gravity = Gravity.BOTTOM | Gravity.CENTER_HORIZONTAL;
    getWindow().setAttributes(lp1);

}
```
运行一遍，显示正常。
![0x3-2](http://img.thearyong.com/2016-09-18-01-15-35-0x3-2.png!post)

好了，问题虽然解决，但是蹦出来的 window 具体原理熟悉又陌生的样子，挺恼人的，就继续磕下去！

## 探索 window
相信大多数人之前有对视图布局层级好奇，一个 activity 布局到底有多少层？有用 DDMS 工具看过的同学，大概都会发现外面套了一层又一层？

### window、WindowManager、Activity 关系
首先，在 Activity.java，可以发现，包含2个很重要的成员：
- **mWindow**
- **mWindowManager**

window，顾名思义，窗口。一个设备，一块屏幕，window 就是显示内容的容器，屏幕可以包含多个 window 层叠；mWindowManager 管理显示内容大小、位置等。就暂时先这样理解吧。

在 android 源码中，Window是一个抽象类，其中包含了一个 WindowManager 引用，上面 activity 中的 mWindowManager 就是该引用。

回到上面的 WindowManager，该类是一个继承 ViewManager 的接口，具体实现于 WindowManagerImpl。

``` java
public interface ViewManager
{
    public void addView(View view, ViewGroup.LayoutParams params);
    public void updateViewLayout(View view, ViewGroup.LayoutParams params);
    public void removeView(View view);
}
```
看到上面3个方法，有没有一种似曾相识的感觉？
嗯，没感觉错，在我们实现悬浮窗效果的时候，显示、更新位置，调用的就是这3个方法。

WindowManager 中，有定义很多常量：
- TYPE_BASE_APPLICATION = 1
- TYPE_APPLICATION_PANEL = 1000
- FIRST_SYSTEM_WINDOW = 2000
- TYPE_PHONE = FIRST_SYSTEM_WINDOW +2
- TYPE_TOAST = FIRST_SYSTEM_WINDOW +5
- TYPE_SYSTEM_DIALOG = FIRST_SYSTEM_WINDOW + 8
  ...

由于篇幅，不全部列出来。大致可以分为3类：
1. 普通窗口
    activity、dialog 都是基于普通窗口
2. 子窗口
    需要依赖其他窗口，比如 PopWindow
3. 系统窗口
    toast、系统弹窗等，部分需要权限，更多相关信息，可以参考网上实现悬浮球

大致了解后，继续看如何创建 Window 的。

跟踪 Activity 源码 attach 时，初始化 window:
```java
        ...
        mWindow = new PhoneWindow(this, window, activityConfigCallback);
        ...
        mWindow.setWindowManager(
               (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
               mToken, mComponent.flattenToString(),
               (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
        ...
```
PhoneWindow 是 Window 的子类，并且设置 WindowManager。
这里可以得出，**每个 Activity 都有对应的一个 window**。

接下来继续跟进 PhoneWindow，可以看到 DecorView：
``` java
// This is the top-level view of the window, containing the window decor.
    private DecorView mDecor;
```
DecorView 就是我们平时经常在 activity 中 getWindow().getDecorView()的实例，它是继承 FrameLayout 的顶级视图。它包含 contentView、actionBar、statusBar 等视图。所以可以得出如下结构：
![0x3-3](http://img.thearyong.com/2017-10-17-22-31-14-3.png!post)

从上图中，我们回答之前的疑问，**Activity 至少有3层以上的布局**。

接下来继续跟进 Activity 中 attach 调用 Window 的setWindowManager 方法：
Manager：
``` java
public void setWindowManager(WindowManager wm, IBinder appToken, String appName,
            boolean hardwareAccelerated) {
        mAppToken = appToken;
        mAppName = appName;
        mHardwareAccelerated = hardwareAccelerated
                || SystemProperties.getBoolean(PROPERTY_HARDWARE_UI, false);
        if (wm == null) {
            wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
        }
        mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);
    }
```
通过 mContext.getSystemService 获取设备 wm ，然后重新创建 mWindowManager 引用。我们知道所有的窗口管理都是由一个 WindowManagerService，简称 WMS。最终的添加删除都在此类进行，它在开机启动时，就已经 SystemServer 中注册运行。所以 wm 是一个全局的引用。

接下来继续看 createLocalWindowManager：


这里可能会有疑问，**不是已经得到了 wm 么？为什么要重新创建，wm 和 mWindowManager 有什么区别么？**
关于这个问题，目前我也不是特别明确，看到 WindowManagerImpl 描述：

> Provides low-level communication with the system window manager for
> operations that are bound to a particular context, display or parent window.
> Instances of this object are sensitive to the compatibility info associated
> with the running application.
>
> Applications will not normally use WindowManager directly, instead relying
> on the higher-level facilities in {@link android.app.Activity} and
> {@link android.app.Dialog}.

WindowManager 通常不会直接使用，实例是非常敏感的。要是用同一个引用，如果后台有多个程序，会不会影响到其他程序呢，所以才重新创建。
这仅是个人的理解，猜测，如有不正，欢迎斧正。

WindowManagerImpl 是 WindowManager 的实现类，包含一个 WindowManagerGlobal 单例：
``` java
private final WindowManagerGlobal mGlobal = WindowManagerGlobal.getInstance();
```
window 的 add,remove,update 都是调用 mGlobal 的实现。因此最后我们可知：
>**一个应用中，每个 Activity 都对应一个 window， 每个的 window 都有一个 WindowManagerImpl 实例，但其最终指向同一个 WindowManagerGlobal 单例。**


## 总结
1. 理解 dialog 和 activity 的区别，布局上、实现上的不同。
2. 明白为什么 dialog 要在 activity onDestory() 之前 dismiss，防止内存泄露。
3. 清楚 window 、WindowManager、Activity 之间的关系。
4. 知道 Activity 至少有多少层布局。

## 脑洞
延伸思考下还有哪些应用场景：
    - 悬浮球
    - 子线程、service 全局弹窗
    - 应用内 loading、数据为空展示页
    ...
