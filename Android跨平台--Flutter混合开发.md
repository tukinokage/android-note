主要来源[参考链接](https://book.flutterchina.club/chapter1/mobile_development_intro.html#_1-1-1-%E5%8E%9F%E7%94%9F%E5%BC%80%E5%8F%91%E4%B8%8E%E8%B7%A8%E5%B9%B3%E5%8F%B0%E6%8A%80%E6%9C%AF)

## 一. 跨平台基础说明

### 1. 跨平台前世今生：

1. 原生开发有以下主要优势：

- 可访问平台全部功能（GPS、摄像头）；
- 速度快、性能高、可以实现复杂动画及绘制，整体用户体验好；

主要缺点：

- 平台特定，开发成本高；不同平台必须维护不同代码，人力成本随之变大；
- 内容固定，动态化弱，大多数情况下，有新功能更新时只能发版；

##### 1. 目前存在三大类跨平台：
针对原生开发面临的问题，业界一直都在努力寻找好的解决方案，而时至今日，已经有很多跨平台框架（注意，本书中所指的“跨平台”若无特殊说明，即特指 Android 和 iOS 两个平台），根据其原理，主要分为三类：

- H5 + 原生（Cordova、Ionic、微信小程序）
- JavaScript 开发 + 原生渲染 （React Native、Weex）
- 自绘UI + 原生 (Qt for mobile、Flutter)

#### 2. Hybird技术：
 将H5+原生混合开发的方式称之为**混合开发或者** 或者**HTMLybrid App**，如果大多数功能都是web实现那么我们称其为webapp.
 
首先来说Native端调用Web端，这个比较简单，JavaScript作为解释性语言，最大的一个特性就是可以随时随地地通过解释器执行一段JS代码，所以可以将拼接的JavaScript代码字符串，传入JS解析器执行就可以，JS解析器在这里就是webView。

 我们把依赖于 WebView 的用于在 JavaScript 与原生之间通信并实现了某种消息传输协议的工具称之为 **WebView JavaScript Bridge** , 简称 **JsBridge**，它也是混合开发框架的核心。
 
 我们可以通过第三方 **JsBridge**框架进行对原生的调用。
[WebViewJavascriptBridge](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fmarcuswestin%2FWebViewJavascriptBridge)

交互体验和性能明显没有原生的好，图片多的时候也不行。

##### Android调用js
Android 4.4之前只能用 **loadUrl** 来实现，并且无法执行回调：

```java
String jsCode = String.format("window.showWebDialog('%s')", text);
webView.loadUrl("javascript: " + jsCode);
```

Android 4.4之后提供了 **evaluateJavascript** 来执行JS代码，**并且可以获取返回值执行回调：**

```java
String jsCode = String.format("window.showWebDialog('%s')", text);
webView.evaluateJavascript(jsCode, new ValueCallback<String>() {
  @Override
  public void onReceiveValue(String value) {
  
  }
});
```

##### Web->Native

Web调用Native端主要有两种方式

##### 拦截Webview请求的URL Schema

URL Schema是类URL的一种请求格式，格式如下：

`<protocol>://<host>/<path>?<qeury>#fragment`

我们可以自定义JSBridge通信的URL Schema，比如：`jsbridge://showToast?text=hello`

Native加载WebView之后，Web发送的所有请求都会经过WebView组件，所以Native可以重写WebView里的方法，从来拦截Web发起的请求，我们对请求的格式进行判断：

- 如果符合我们自定义的URL Schema，对URL进行解析，拿到相关操作、操作，进而调用原生Native的方法
- 如果不符合我们自定义的URL Schema，我们直接转发，请求真正的服务
#### 注入Api（注入对象）
上面已经说到了Native、Web间双向通信的两种方法，但站在一端而言还是一个单向通信的过程 ，比如站在Web的角度：Web调用Native的方法，Native直接相关操作但无法将结果返回给Web，但实际使用中会经常需要将操作的结果返回，也就是JS回调。**考虑SPI设计，通过命令调用相应的方法。**

所以在对端操作并返回结果，有输入有输出才是完整的调用，那如何实现呢？

其实基于之前的单向通信就可以实现，我们在一端调用的时候在参数中加一个**callbackId**标记对应的回调，对端接收到调用请求后，进行实际操作，如果带有callbackId，对端再进行一次调用，将结果、callbackId回传回来，这端根据callbackId匹配相应的回调，将结果传入执行就可以了。

说白了就是webView.addJavascriptInterface(new 注入对象CLASS, "**对应注入对象***"); 让js通过命令----命令模式调用注入的对象嗲用native方法，JSevaluateJavascript回调到JS。
```html
// Web端代码：
<body>
  <div>
    <button id="showBtn">获取Native输入，以Web弹窗展现</button>
  </div>
</body>
<script>
  let id = 1;
  // 根据id保存callback
  const callbackMap = {};
  // 使用JSSDK封装调用与Native通信的事件，避免过多的污染全局环境
  window.JSSDK = {
    // 获取Native端输入框value，带有回调
    getNativeEditTextValue(callback) {
      const callbackId = id++;
      callbackMap[callbackId] = callback;
      
      //...............核心
      // 调用JSB方法，并将callbackId传入
      window.NativeBridge.getNativeEditTextValue(callbackId);
     //..........
     
    },
    // 接收Native端传来的callbackId
    receiveMessage(callbackId, value) {
      if (callbackMap[callbackId]) {
        // 根据ID匹配callback，并执行
        callbackMap[callbackId](value);
      }
    }
  };

    const showBtn = document.querySelector('#showBtn');
  // 绑定按钮事件
  showBtn.addEventListener('click', e => {
    // 通过JSSDK调用，将回调函数传入
    window.JSSDK.getNativeEditTextValue(value => window.alert('Natvie输入值：' + value));
  });
</script>
```

```java
// Android端代码,注入对象，让js通过window.NativeBridge.去调用
webView.addJavascriptInterface(new NativeBridge(this), "NativeBridge");

class NativeBridge {
  private Context ctx;
  NativeBridge(Context ctx) {
    this.ctx = ctx;
  }

  // 获取Native端输入值
  @JavascriptInterface
  public void getNativeEditTextValue(int callbackId) {
    MainActivity mainActivity = (MainActivity)ctx;
    // 获取Native端输入框的value
    String value = mainActivity.editText.getText().toString();
    // 需要注入在Web执行的JS代码
    String jsCode = String.format("window.JSSDK.receiveMessage(%s, '%s')", callbackId, value);
    // 在UI线程中执行
    mainActivity.runOnUiThread(new Runnable() {
      @Override
      public void run() {
        mainActivity.webView.evaluateJavascript(jsCode, null);
      }
    });
  }
}
```
#### 开源的JSBridge

可以看到，实现一个完整的JSBridge还是挺麻烦的，还需要考虑低端机型的兼容问题、同步异步调用问题，好在已经有开源的JSBridge供我们直接使用了：

- DSBridge，主要通过注入API的形式，[DSBridge for Android](https://link.zhihu.com/?target=https%3A//github.com/wendux/DSBridge-Android)、[DSBridge for IOS](https://link.zhihu.com/?target=https%3A//github.com/wendux/DSBridge-IOS)
- JsBridge，主要通过拦截URL Schema，[JsBridge](https://link.zhihu.com/?target=https%3A//github.com/lzyzsd/JsBridge)


#### 3.  React Native

**JavaScript开发 + 原生渲染** 的跨平台框架。
React Native （简称 RN ）是 Facebook 于 2015 年开源的跨平台移动应用开发框架，是 Facebook 早先开源的 Web 框架 React 在原生移动应用平台的衍生产物，目前支持 iOS 和 Android 两个平台。

由于React Native是 原生控件渲染，所以性能会比混合应用中 H5 好一些，同时 React Native 提供了很多原生组件对应的 Web 组件，大多数情况下开发者只需要使用 Web 技术栈 就能开发出 App

JavaScript 开发 + 原生渲染 的方式主要优点如下：

1. 采用 Web 开发技术栈，社区庞大、上手快、开发成本相对较低。
2. 原生渲染，性能相比 H5 提高很多。
3. 动态化较好，支持热更新。

不足：

1. 渲染时需要 JavaScript 和原生之间通信，**在有些场景如拖动可能会因为通信频繁导致卡顿。
2. JavaScript 为脚本语言，**执行时需要解释执行** （这种执行方式通常称为 JIT，即 Just In Time，指在执行时实时生成机器码），执行效率和编译类语言（编译类语言的执行方式为 AOT ，即 Ahead Of Time，指在代码执行前已经将源码进行了预处理，这种预处理通常情况下是将源码编译为机器码或某种中间码）仍有差距。
3. 由于渲染依赖原生控件，不同平台的控件需要单独维护，并且当**系统更新时，社区控件可能会滞后**；除此之外，其控件系统也会受到原生UI系统限制，例如，在 Android 中，手势冲突消歧规则是固定的，这在使用不同人写的控件嵌套时，手势冲突问题将会变得非常棘手。这就会导致，如果需要自定义原生渲染组件时，开发和维护成本过高。
4. 
#### 4. Qt Mobile

 “自绘UI + 原生” 跨平台技术。
 1. 性能高；由于自绘引擎是直接调用系统API来绘制UI，所以性能和原生控件接近。
    
2. 灵活、组件库易维护、UI外观保真度和一致性高；由于UI渲染不依赖原生控件，也就不需要根据不同平台的控件单独维护一套组件库，所以代码容易维护。由于组件库是同一套代码、同一个渲染引擎，所以在不同平台，组件显示外观可以做到高保真和高一致性；另外，由于不依赖原生控件，也就不会受原生布局系统的限制，这样布局系统会非常灵活。
    
不足：

1. 动态性不足；为了保证UI绘制性能，自绘UI系统一般都会采用 AOT 模式编译其发布包，所以应用发布后，不能像 Hybrid 和 RN 那些使用 JavaScript（JIT）作为开发语言的框架那样动态下发代码。
2. 应用开发效率低：Qt 使用 C++ 作为其开发语言，而编程效率是直接会影响 App 开发效率的，C++ 作为一门静态语言，在 UI 开发方面灵活性不及 JavaScript 这样的动态语言，另外，C++需要开发者手动去管理内存分配，没有 JavaScript 及Java中垃圾回收（GC）的机制。

#### 5. Flutter
“自绘UI + 原生” 跨平台技术。

1. Flutter 底层使用 Skia 作为其 2D 渲染引擎，Skia 是 Google的一个 2D 图形处理函数库，包含字型、坐标转换，以及点阵图，它们都有高效能且简洁的表现。Skia 是跨平台的，并提供了非常友好的 API，目前 Google Chrome浏览器和 Android 均采用 Skia 作为其 2D 绘图引擎。

2. Flutter App 采用 Dart 语言开发。Dart 在 JIT（即时编译）模式下，执行速度与 JavaScript 基本持平。但是 Dart 支持 AOT，当以 AOT模式运行时，JavaScript 便远远追不上了。

3. Dart 运行时和编译器支持 Flutter 的两个关键特性的组合：

- **基于 JIT 的快速开发周期**：Flutter 在开发阶段采用 JIT 模式，这样就避免了每次改动都要进行编译，极大地节省了开发时间；
    
- **基于 AOT 的发布包**: Flutter 在发布时可以通过 AOT 生成高效的机器码以保证应用性能。而 JavaScript 则不具有这个能力。

|技术类型|UI渲染方式|性能|开发效率|动态化|框架代表|
|---|---|---|---|---|---|
|H5 + 原生|WebView渲染|一般|高|支持|Cordova、Ionic|
|JavaScript + 原生渲染|原生控件渲染|好|中|支持|RN、Weex|
|自绘UI + 原生|调用系统API渲染|好|Flutter高, Qt低|默认不支持|Qt、Flutter|

## 2. Flutter结构
![](pic/82c25693.png)

### 1. 框架层
Flutter Framework，即框架层。这是一个纯 Dart实现的 SDK，它实现了一套基础库，自底向上，我们来简单介绍一下：

- 底下两层（Foundation 和 Animation、Painting、Gestures）在 Google 的一些视频中被合并为一个dart UI层，对应的是Flutter中的`dart:ui`包，它是 Flutter Engine 暴露的底层UI库，提供动画、手势及绘制能力。
- Rendering 层，即渲染层，这一层是一个抽象的布局层，它依赖于 Dart UI 层，渲染层会构建一棵由可渲染对象组成的**渲染树**，当动态更新这些对象时，渲染树会找出变化的部分，然后更新渲染。渲染层可以说是Flutter 框架层中最核心的部分，它除了确定每个渲染对象的位置、大小之外还要进行坐标变换、绘制（调用底层 dart:ui ）。
- Widgets 层是 Flutter 提供的一套基础组件库，在基础组件库之上，Flutter 还提供了 Material 和 Cupertino 两种视觉风格的组件库，它们分别实现了 Material 和 iOS 设计规范。

Flutter 框架相对较小，因为一些开发者可能会使用到的更高层级的功能已经被拆分到不同的软件包中，使用 Dart 和 Flutter 的核心库实现，其中包括平台插件，例如 [camera](https://pub.flutter-io.cn/packages/camera) 和 [webview](https://pub.flutter-io.cn/packages/webview_flutter) ，以及和平台无关的功能，例如 [animations](https://pub.flutter-io.cn/packages/animations)。

我们进行Flutter 开发时，大多数时候都是和 Flutter Framework 打交道。

### 2. 引擎层
Engine，即引擎层。毫无疑问是 Flutter 的核心， 该层主要是 C++ 实现，其中包括了 Skia 引擎、Dart 运行时（Dart runtime）、文字排版引擎等。在代码调用 `dart:ui`库时，调用最终会走到引擎层，然后实现真正的绘制和显示。

### 3. 嵌入层
Embedder，即嵌入层。Flutter 最终渲染、交互是要依赖其所在平台的操作系统 API，嵌入层主要是将 Flutter 引擎 ”安装“ 到特定平台上。嵌入层采用了当前平台的语言编写，例如 Android 使用的是 Java 和 C++， iOS 和 macOS 使用的是 Objective-C 和 Objective-C++，Windows 和 Linux 使用的是 C++。 Flutter 代码可以通过嵌入层，以模块方式集成到现有的应用中，也可以作为应用的主体。Flutter 本身包含了各个常见平台的嵌入层，假如以后 Flutter 要支持新的平台，则需要针对该新的平台编写一个嵌入层。

## 3. Flutter基础
Flutter在Android平台上的运行
![[5275b2f9e9942f752d8f04b032270783.png]]
#### 1.widget与控件视图
与原生开发中“控件”不同的是，Flutter 中的 widget 的概念更广泛，它不仅可以表示UI元素，也可以表示一些功能性的组件如：用于手势检测的 `GestureDetector` 、用于APP主题数据传递的 `Theme` 等等，而原生开发中的控件通常只是指UI元素。在后面的内容中，我们在描述UI元素时可能会用到“控件”、“组件”这样的概念，读者心里需要知道他们就是 widget ，只是在不同场景的不同表述而已。

一个 StatefulWidget类会对应一个 State 类，State表示与其对应的 StatefulWidget 要维护的状态，State 中的保存的状态信息可以：

1. 在 widget 构建时可以被同步读取。
2. 在 widget 生命周期中可以被改变，当State被改变时，可以手动调用其`setState()`方法通知Flutter 框架状态发生改变，Flutter 框架在收到消息后，会重新调用其`build`方法重新构建 widget 树，从而达到更新UI的目的。

State的生命周期有四种状态：

    created：当State对象被创建时候，State.initState方法会被调用；
    initialized：当State对象被创建，但还没有准备构建时，State.didChangeDependencies在这个时候会被调用；
    ready：State对象已经准备好了构建，State.dispose没有被调用的时候；
    defunct：State.dispose被调用后，State对象不能够被构建。

Widget ：仅用于存储渲染所需要的信息。  
RenderObject ：负责管理布局、绘制等操作。  
Element ：才是这颗巨大的控件树上的实体。  
Widget会被inflate（填充）到Element，并由Element管理底层渲染树。Widget并不会直接管理状态及渲染,而是通过State这个对象来管理状态。Flutter创建Element的可见树，相对于Widget来说，是可变的，通常界面开发中，我们不用直接操作Element, 而是由框架层实现内部逻辑。就如一个UI视图树中，可能包含有多个TextWidget(Widget被使用多次)，但是放在内部视图树的视角，这些TextWidget都是填充到一个个独立的Element中。Element会持有renderObject和widget的实例。记住，Widget 只是一个配置，RenderObject 负责管理布局、绘制等操作。

在第一次创建 Widget 的时候，会对应创建一个 Element， 然后将该元素插入树中。如果之后 Widget 发生了变化，则将其与旧的 Widget 进行比较，并且相应地更新 Element。重要的是，Element 不会被重建，只是更新而已。

链接：https://www.jianshu.com/p/e0f2c9778514  
#### 2. 页面/界面 Route
路由（Route）在移动开发中通常指页面（Page），这跟 Web 开发中单页应用的 Route 概念意义是相同的，Route 在 Android中 通常指一个 Activity，在 iOS 中指一个 ViewController。
### 3.Dart
#### 1. 异步
通过创建Furture类来控制链式切换，跟kotlin flow类似。
```dart
Future<void> loadData() async {
  var dataURL = Uri.parse('https://jsonplaceholder.typicode.com/posts');
  http.Response response = await http.get(dataURL);
  setState(() {
    widgets = jsonDecode(response.body);
  });
}
```

```dart
Future<Map?> getFilePath(String methodName, [Map? params]) async {  
  var reslut = await methodChannel.invokeMapMethod(methodName, params);  
  return reslut;  
}
```
使用async和await关键字来处理future

作用：

    async：在方法体前面是使用，定义该方法为一个异步方法。
    await：等待并获得异步表达式的执行结果，并且给关键字只能在async修饰的方法中。

futrue异步通过then和onError获取futrue的返回值或者异常
```dart
var future = getNetworkData();
  future.then((value) {
    print(value);
  }, onError: (e) {
    print(e);
  });
```

处理async方法中的异常

对于async中的方法的异常，我们按一下方式进行处理
```dart
Future<String> fetchUserOrder(){
  return Future.delayed(const Duration(seconds: 2),(){
    return throw Exception('handle order error');
  });
}

Future<void> printOrderMessage() async{
  print('Awaiting user order...');
  try{
    var order = await fetchUserOrder();
    print('You order is:$order');
  }catch (e){
    print(e);
  }
}

```

对于async方法中的异常，需要通过try/catch进行处理。

小结

    Future是什么：将要完成的结果未来。
    Future如何获取异步的值：通过then()方法
    Future如何处理异常：通过catchError()或者在then()中传入命名参数onError
    async和wait的关键的作用是什么：async声明一个异步方法，wait等待异步任务完成；简单来讲就是同步的方式编写异步代码。
    如何捕获和处理async中的异常：需要使用try/catch来捕获以及处理程序运行中的异常。


#### 2. stream
#### 3. **mixin**
解决多继承问题，
### 4.混合开发
一般的开发方式有两种，一种是创建一个flutter应用，一种是创建flutter模块引入。

在 Java 中调用 Flutter 模块有两种方式：

- 使用 Flutter.createView API的方式；
- 使用 FlutterFragment 的方式；

Flutter类提供了一个createView()方法。通过该方法，可以将flutter页面构建成一个View，接下来就可以通过addView()，将flutter页面添加到相应的地方。实现代码如下：

```
public void onLoadFlutter(View view) {
    View flutterView = Flutter.createView(this, getLifecycle(), "route1");
    FrameLayout.LayoutParams layoutParams = new FrameLayout.LayoutParams(600, 600);
    layoutParams.topMargin = 100;
    addContentView(flutterView, layoutParams);
}
```

Flutter还提供了方法createFragment()，通过该方法会将flutter页面构建成一个fragment，然后根据fragment的操作将flutter页面添加到相应的地方。实现代码如下：

```
public void onLoadFlutter(View view) {
    FragmentTransaction transaction= getSupportFragmentManager().beginTransaction();
    transaction.replace(R.id.someContainer,Flutter.createFragment("这里是flutter页面"));
    transaction.commit();
}
```
作者：GT9456  (原文链接)[https://www.imooc.com/article/334116  ]