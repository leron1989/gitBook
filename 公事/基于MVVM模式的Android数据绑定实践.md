# 基于MVVM模式的Android数据绑定实践
[TOC]

为什么要在项目中用架构或者模式？
1. 使程序模块化，做到模块内部的高聚合和模块之间的低耦合
2. 在逻辑上更简单，很好的定义了单一原则，提供了更好的扩展性，方便定位问题

## 一、MVC简介

•视图层(View)
 对应于xml布局文件，

•控制层(Controller)
 Android的控制层是由Activity来承担的，Activity本来主要是作为初始化页面，展示数据的操作，但是因为XML视图功能太弱，所以Activity既要负责视图的显示又要加入控制逻辑，承担的功能过多，在复杂一点的页面Activity代码量达到1000+也就不足为奇了。

•模型层(Model)
 我们针对业务模型，建立的数据结构和相关的类，它主要负责网络请求，数据库处理，I/O的操作。

•

 弊端：在Android开发中，Activity并不是一个标准的MVC模式中的Controller，本来它的首要职责是加载应用的布局和初始化用户界面，接受并处理来自用户的操作请求，进而作出响应。在MVC模式下随着界面及其逻辑的复杂度不断提升，Activity类的职责不断增加，以致变得庞大臃肿。





## 二、MVP简介

### 什么是MVP架构

•视图层(View)
 负责绘制UI元素、与用户进行交互，对应于xml、Activity、Fragment、Adapter

•模型层(Model)
 负责存储、检索、操纵数据，一般包含网络请求，数据库处理，I/O流。

•控制层(Presenter)
 Presenter是整个MVP体系的控制中心，作为View与Model交互的中间纽带，

•处理View于Model间的交互和业务逻辑。



具体实现就是接收到View的请求，从Model层获取数据，将数据进行处理，通过View层的接口回调给Activity或者Fragment。MVP能够让Activity成为真正的View，只做UI相关的事。

### 优缺点

1. 优点

- 模型与视图完全分离，我们可以修改视图而不影响模型；
- 项目代码结构（文件夹）清晰，一看就知道什么类干什么事情；
- 我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁
- 协同工作（例如在设计师没出图之前可以先写一些业务逻辑代码或者其他人接手代码改起来比较容易）

2. 缺点

- Presente层与View层是通过接口进行交互的，View层可能会有大量的接口，因为有可能好几个Activity都是去实现同一个View接口，那么所有用到的Activity都要去实现所有的方法(不管你是否用到)，而且如果后面有些方法要删改，Presenter和Activity都要改动，比较麻烦；
- MVP把Activity相当的一部分责任放到了Presenter来处理，复杂的业务同时也可能会导致P层太大，一旦业务逻辑越来越多，View定义的方法越来越多，会造成Activity和Fragment实现的方法越来越多，依然臃肿。



## 三、MVVP介绍

### 什么是MVVM模式

Model ：负责数据实现和逻辑处理，类似MVP。
View ：
对应于Activity和XML，负责View的绘制以及与用户交互，类似MVP。
ViewModel ：
创建关联，将model和view绑定起来,如此之后，我们model的更改，通过viewmodel反馈给view,从而自动刷新界面。



### 双向数据绑定

![gs-双向数据绑定](.\img\gs-双向数据绑定.png)

通常情况下，数据的流向是单方面的，只能从代码流向UI，也就是单向绑定；而双向绑定的数据流向是双向的，当业务代码中的数据改变时，UI上的数据能够得到刷新；当用户通过UI交互编辑了数据时，数据的变化也能自动的更新到业务代码中的数据上

### MVVM 工作流

![gs-工作流](.\img\gs-工作流.png)

•职责和协同工作的原理。

•Model
 Model层就是职责数据的存储、读取网络数据、操作数据库数据以及I/O，一般会有一个ViewModel对象来调用获取这一部分的数据。

•View
 我感觉这里的View才是真正的View，为什么这么说？View层做的仅仅和UI相关的工作，我们只在XML、Activity、Fragment写View层的代码，View层不做和业务相关的事，也就是我们的Activity 不写和业务逻辑相关代码，一般Activity不写更新UI的代码，如果非得要写，那更新的UI必须和业务逻辑和数据是没有关系的，只是单纯UI逻辑来更新UI，比如：滑动时头部颜色渐变、editttext根据输入内容显示隐藏等，简单的说：View层不做任何业务逻辑、不涉及操作数据、不处理数据、UI和数据严格的分开。

•ViewModel
 ViewModel 只做和业务逻辑和业务数据相关的事，不做任何和UI、控件相关的事，ViewModel 层不会持有任何控件的引用，更不会在ViewModel中通过UI控件的引用去做更新UI的事情。ViewModel就是专注于业务的逻辑处理，操作的也都是对数据进行操作，这些个数据源绑定在相应的控件上会自动去更改UI，开发者不需要关心更新UI的事情。

## 四、DataBinding介绍

### 什么是DataBinding

原文：

> The Data Binding Library is a support library that allows you to bind UI components
> in your layouts to data sources in your app using a declarative format rather than programmatically.

谷歌翻译：

> DataBinding是一个库，允许你使用声明式语法而不是以编程方式将布局中的UI组件绑定到应用程序中的数据源。

白话翻译：

> DataBinding是一个用于UI与数据源进行双向数据绑定的框架。

### 简单使用
1. 在Module的build.gradle android模块中添加如下配置

```xml
android {
 dataBinding {
    enabled = true
 }
}
```

2. 创建一个简单的JavaBean对象

```java
public class UserBean {
    private String name; //姓名
    private int age; //年龄

    public UserBean(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

3. 使用了`DataBinding`之后的Activity的布局文件`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="user"
            type="com.leron.databindingdemo.bean.UserBean" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}" />

        <!--注意：这里age是int类型，必须转化为String，否则会运行时异常-->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf(user.age)}" />
    </LinearLayout>
</layout>
```

> - 根节点变成了layout，里面包括了`data`节点和传统的布局。
> - data节点作用是连接 View 和 Modle 的桥梁。
> - 布局文件可以通过`data`节点中声明的`variable`变量获取指，如：`android:text="@{String.valueOf(user.age)}"`。

4. MainActivity

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        UserBean userBean = new UserBean ("张三", "25");
        binding.setUser(userBean );
    }
}
```

> - 通过`DataBindingUtil.setContentView`获取`ActivityMainBinding` ，ActivityMainBinding 继承ViewDataBinding，ActivityMainBinding 类根据布局文件名自动生成，比如：activity_main.xml --> ActivityMainBinding 、fragment --> FragmentBinding，即：第一个单词首字母大写，第二个单词首字母大写，最后都会拼上Binding就是生成的Binding类。
> - 通过 `binding.setUser(userBean)` 与 variable 进行绑定。



### 













## 五、MVVP实例讲解

