---
title: DataBinding双向绑定
date: 2016-02-23 17:33:36
tags: 
- Android 
- DataBinding
categories:
- Android
aliases:
- /Android/databinding-two-way/
summary: DataBinding使我们远离了findViewById和各种的setValue，大大大简化了开发工作，也是的Android从官方层面上支持MVVM模式的体现。但是只有单向绑定,没有双向绑定
---
# DataBinding简介
 > [DataBinding](http://developer.android.com/intl/zh-cn/tools/data-binding/guide.html)是由谷歌公司于2015年发布的Android数据绑定库，已更新为正式版。
 
 
 DataBinding使我们远离了findViewById和各种的setValue，大大大简化了开发工作，也是的Android从官方层面上支持MVVM模式的体现。

 # 问题来源
 用过此框架的童鞋们知道，DataBinding库虽然已经实现了单向绑定，即数据的改变直接影响视图显示，但是没有实现双向绑定，即视图的改变影响数据。这离MVVM模式还差了一步。是否有解决的方案呢？答案是肯定的。
![MVVM示意图](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blogMVVM.png)
# 实现Data Binding双向绑定
废话说完，步入正题，我们实现原则是，尽量少写代码，尽量用原有的框架实现。（谁叫我懒呢）实现的原理便是绑定事件监听。在Data Binding中官方集成了很多实用的事件绑定，他们的声明藏在`android.databinding.adapters`包下以`BindingAdapter`为结尾的类中，下图为包中的部分截图。（全部的太长了，手懒）

![BindingAdapter](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blogBindingAdapter.png)

# 举个栗子
接下来我们就以EditText为例实现数据的双向绑定。 对于EditText控件我们需要绑定一个事件监听内容的实时变化，好在Data Binding已经在**android.databinding.adapters**包下的**TextViewBindingAdapter**为我们定义好了监听，我们可以通过在布局文件中用`android:afterTextChanged＝function(Editable s)`实现，如果把它和元素的setter函数绑定，就可以不用增加单独的监听函数。 下面是实例代码
## ViewModel层
```java
public class ViewModel extends BaseObservable {
    private ObservableField<String> userName; 
    public ViewModel() { 
        userName=new ObservableField<>(); 
    } 
    public String getUserName() {
        return userName.get(); 
    } 
    public void setUserName(Editable userName) { 
        this.userName.set(String.valueOf(userName)); 
    } 
}
```

## View层－XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        tools:context="com.xiezt.test.MainActivity">

    <data>

        <variable
            name="viewModel"
            type="com.example.databinding.ViewModel"/>
    </data>

    <LinearLayout
        android:id="@+id/mainLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <EditText
            android:id="@+id/edit"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:afterTextChanged="@{viewModel.setUserName}"
            android:text="@{viewModel.userName}"/>

        <Button
            android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="onclick"
            android:text="OK"/>
    </LinearLayout>
</layout>
```

## View层 Activity

```java
public class MainActivity extends AppCompatActivity {
    private ActivityMainBinding binding;
    private ViewModel viewModel;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        viewModel = new ViewModel();
        binding.setViewModel(viewModel);
    }

    public void onclick(View view) {
        Toast.makeText(getApplicationContext(), viewModel.getUserName(), Toast.LENGTH_LONG).show();
    }
}
```

## 效果图
![效果图](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blogdatabinding_two_way.gif)

# 总结
在官方还没有进一步完善此框架时，用现有的事件绑定来实现双向绑定算作是较为简单的解决方案。其他控件的双向绑定的解决思路，由于EditText事件的特殊性，我们不用单独写函数，但是其他控件不一定，例如CheckBox，它的绑定函数需要需要传递两个值，需要我们自己单独写。

---
相关链接
- [MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)
- [Data Binding（数据绑定）用户指南](http://www.jianshu.com/p/b1df61a4df77)
