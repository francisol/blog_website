---
title: JavaJs连接桥的实现
date: 2016-09-17 22:54:47
categories:
  - Android
tags:
- Java
aliases:
- /Android/java_js_bridge/
---

# Java与Js调用
在Android开发中常用Java代码调用js的情况.这种比较简单
```java
webView..getSettings()..setJavaScriptEnabled(true);
webView.loadUrl("javascript:function()");
```
Js调用Java的方法如下
```java
//Java部分
webView.addJavascriptInterface(new Functions(), 'name');//API 17以上



public class Functions{

  //此函数被Js调用
  @JavascriptInterface
  public void function(){
    ///TODO
  }
}

```
```javascript
//Js部分
window.name.function()
```
# 关于连接桥
在Js调用Java部分不算是麻烦,但是在Java调用Js时候要把函数名参数等转换为字符串,比较麻烦.因为用过retrofit,对他的简洁很喜欢.于是仿照retrofit,利用Java的动态代理重新封装了一个JavaJs连接桥.
思路如下:
利用接口中函数的注解和参数列表保存Js方法的信息,方法名和参数.利用接口中函数的对象进行序列化请求字符串,例如"javascript:showMsg('Hello')"

## 使用方法
TestFunction
```java
//TestFunction
    @Js(method = "show")
    CallResult<Void> showMsg(String msg);
    @Js(method = "add")
    CallResult<Integer> add(int a,int b);
```

TestReceiver
```java
public class TestReceiver implements Receiver {
    private Context mContext;
    public TestReceiver(Context context) {
        mContext=context;
    }

    @Override
    public String getTag() {
        return "test";//js调用其中方法时为window.test
    }
    @JavascriptInterface
    public void showMsg(String msg){
        Toast.makeText(mContext,msg,Toast.LENGTH_LONG).show();
    }


}
```
TestActivity
```java
///TestActivity

    private JavaJsBridge mJavaJsBridge;
    private TestFunction mFunction;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test);
        WebView webView=(WebView) findViewById(R.id.web_view);
        webView.loadUrl("file:///android_asset/html/index.html");
        mJavaJsBridge= new JavaJsBridge.Builder()
          .adapter(new CallAdapter())//adapter 定义了如何实现接口方法的返回对象,实现 IAdapter
          .receive(new TestReceiver(this))//Js调用Java时,会调用TestReceiver里面的函数,此类实现Receiver接口.可以调用
          .converter(new ConverterFactory.JsonConverter())//定义了当Java传对象给Js的时候如何解析
          .inject(webView)
          .build();//构造连接桥
        mFunction=mJavaJsBridge.create(TestFunction.class);//获取实例
    }
      public void addClick(View view){
        //调用Js,没有有参数仅限API 19以上
         mFunction.add(1,1).ifSuccess(new Success<Integer>() {
             @Override
             public void success(Integer value) {
                 Toast.makeText(getBaseContext(),value.toString(),Toast.LENGTH_LONG).show();
             }
         }).ifError(new Failure() {
             @Override
             public void failure(Throwable value) {

             }
         }).call();
    }

    public void show(View v){
      //调用Js,没有返回参数
        mFunction.showMsg("Hello").call();
    }
```
Html

```html
<button  onclick="android()">显示Toast</button>
<script>
    function show(msg){
        alert(msg)
    }
    function add(a,b){
        return a+b
    }
    function android(){
        window.test.showMsg("Hello")
    }
</script>
```
## 效果
![效果](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blogJavaJsBridge.gif)

# 项目地址
[JavaJs连接桥的实现](https://github.com/francisCN/JsBridge)