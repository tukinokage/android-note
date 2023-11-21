其他细分领域：
[[音视频开发--基于Android平台]]
[[Android跨平台--Flutter混合开发]]
[[Android逆向开发与安全]]
[[Android--Framework详解与开发]](待写)
[[Android--HAL与驱动开发]](待写)
# 0.基础

## 0.1 android体系结构

Andrid系统的体系结构设计为多层结构，这种结构在给用户提供安全保护的同时还保持了开放平台的灵活性。如下图所示：

官方图
![](7048342-ee25f19c86daa94a%201.webp)

Google官方提供的Android系统的四层架构图
应用层，framework，第三方c++库 + 安卓虚拟机 + java核心库，linux内核层。

正常来说在native层应该还需要加上一层HAL硬件抽象层，硬件厂商驱动可以不开源的情况下提供接口给android系统调用
![[截图20230814163045.png]]
### 一、应用层 Applications

应用层由运行在Android设备上的所有应用构成，包括预装的系统应用和自己安装的第三方应用。**大部分是由Java语言编写并运行在Dalvik虚拟机中，另一部分应用是通过c++/c语言编写的本地应用**。但无论采用何种编程语言，**两类应用运行的安全环境相同，都在应用沙箱中运行**。而程序员正是在这层中，通过Android提供的组件和API进行开发，从而编写出形形色色的app。

### 二、应用框架层 Application Framework

集中体现Android系统系统的组件设计思想，是Android应用开发的核心，为开发者开发应用时提供基础的API框架。框架层由多个系统服务组成。我们知道Android应用是由若干个组件构成，组件与组件之间的通信是通过框架层提供的服务集中调度和传递消息实现的，而不是组件之间直接进行的。

- **View System  主要用于UI设计，包括List、Grid、Text、Button、Webview等。**
- **Activity Manager Service -AMS 负责管理应用程序中的activity的生命周几以及提供activity之间切换功能等 Intent相关。**
- **Windows Manager Service-WMS 用于管理所有的窗口程序，如Dialog、Toast等。**
- Recource Manager 提供非代码资源的管理 如布局文件、图形、字符串资源文件等。
- Location Manager 负责与定位功能相关功能
- Content Providers 提供了一组通用的数据访问接口，可用于应用程序间的内容交互，比如获取手机联系人数据等。
- Notification Manager 用户管理手机状态栏中的自定义信息等。
- Telephony Manager  手机底层功能管理模块，可用于获取手机串号或者调用短信功能
- Pacakage Manager Android系统内的包管理模块，负责管理安装的应用程序。
- XMPP Service 用于主持XMPP协议的服务，比如与Google Talk通信等

### 三、类库层：

主要由类库 Libraries 和Android运行时 Android Runtime 两部分组成：

##### 1.类库 Libraries

 由一系列的二进制**动态库**构成，大部分来源于优秀的第三方类库，另一部分是系统原生类库，通常使用c/c++语言开发。（因为java代码无法直接调用c/c++驱动代码,所以在这一层,系统通过封装了一系列的函数库供上层使用.）以下列举一些比较重要的类库的功能，以供了解：

- Surface Manager: 负责管理显示与存取操作间的互动，另外也负责将2D绘图与3D绘图进行显示上的合成
- Media Framework: 一个开源的多媒体框架,允许我们创造出更高质量与全新的播放器效果
- SQLite: 安卓自带的数据库，是一个嵌入式的数据库
- OpenGL ES: 是 OpenGL 三维图形 API 的子集，针对手机、PDA和游戏主机等嵌入式设备而设计。3D效果库
- FreeType: 一个完全免费（开源）的、高质量的且可移植的字体引擎。支持位图、矢量、字体等
- SGL: 2D图形引擎库
- SSL: 位于TCP/IP协议与各种应用层协议之间，为数据通信提供支持。是安全数据通信的支持。
- WebKit: 是一个开源的浏览器引擎。
- Libc: c层中最基本的函数库

##### 2.Android运行时 Android Runtime 

是由**Java核心类库（Core Libraries）**和**Android虚拟机（Dalvik）**共同构成。

- Java核心类库包括框架层和应用层所用到的基本Java库。
- Dalvik是为Android量身打造的Java虚拟机，它与标准Java虚拟机JVM的差别在于Dalvik是基于寄存器设计的，而JVM是基于栈结构设计的；JVM通过解码class文件（java编译生成的的:.java---.class 的class文件）中的内容来运行程序；而Dalvik运行时是由java字节码文件进一步转化而来的文件，，并被打包成一个DEX可执行文件，Dalvik虚拟机通过解释DEX文件来执行这些字节码 ，即android的class 文件实际上只是编译过程中的中间目标文件，需要链接成dex 文件后才能在dalvik 上运行；Dalvik能够更快的编译较大的应用程序，允许在有限的内存空间中同时运行多个虚拟机的实例，每一个Dalvik应用作为一个独立的Linux进程执行，这样可以防止某一虚拟机崩溃时所有的应用都被关闭。

### 四、系统内核层 Linux Kernel

Android内核 具有和标准的Linux内核 一样的功能，主要实现 
内存管理、
进程调度、
进程间通信（Android增加了一种进程间的通信机制IPC Binder）、
设备驱动（Display Driver: 显示驱动；Camera Driver: 照相机驱动；
Flash Memory Driver: 闪存驱动；
Binder Driver: IPC通讯驱动；
KeyPad Driver: 键映射驱动；
Wifi Driver:Wifi驱动；
Audio Driver:音频驱动；P
ower Management:电量管理驱动）等


###  五,系统启动流程

相关扩展：[[#2.2.1 app启动流程]]
相关扩展：[[#2.2.2 activity启动流程]]

![[20230814063644.png]]
![[android-boot.jpg]]
boot Rom(ROM中写死的程序)
→ bootloader驱动引导操作系统启动(类似bios）（（bootloader初始化各种硬件，检验和加载 启动镜像，然后触发linux内核启动）

 - →linux内核启动idle （pid=0）进程（到此启动了linux, 启动各类驱动包括binder，注意此处并不是驱动抽象层）
补充
	- 内核态（Kernel Mode）：运行操作系统程序，操作硬件
	- 用户态（User Mode）：运行用户程序
补充2
	- idle进程，或者也称为swapper进程,0号进程。：
	- 该进程是Linux中的第一个进程（线程），PID为0；
	- idle进程是init进程和k thread d进程（叫 内核线程，用于控制内核相关）的父进程；


- →(navite层) 启动 `init `进程（pid=1），执行Main.cpp ，挂载各种文件（ 用户空间在此初始化
	```cpp
	...
	CHECKCALL(mount("tmpfs", "/dev", "tmpfs", MS_NOSUID, "mode=0755"));
	
	CHECKCALL(mkdir("/dev/pts", 0755));
	
	CHECKCALL(mkdir("/dev/socket", 0755));
	
	CHECKCALL(mkdir("/dev/dm-user", 0755));
	
	CHECKCALL(mount("devpts", "/dev/pts", "devpts", 0, NULL));
	...
	```

- ->(navite层)`init` 进程解析并处理 init.rc
	- init.rc，执行脚本用于后续启动进程（比如zygote以及其启动参数， serviceManager以及其启动参数）和构建文件[init.rc文件概述](#^4cae1a)

- -> 启动serviceManager进程（保存各种binder），早期启动serviceManager为了进程通信 
	- **1）初始化binder驱动**
	- **2）将自身以“manager” 添加到 servicemanager 中的map集合中**
	- **3）注册成为binder驱动的上下问管理者**
	- **4)  给Looper设置callback,进入无限循环，处理client端发来的请求**

- -> 启动zygote（通过init.rc各种参数启动的）（即java进程的鼻祖）并且==初始化了一个binder。==
	- 在这个过程中，Zygote 进程会加载和初始化 Android 的核心库和类库，包括 Binder。

- -> zygote启动dvm 虚拟机( ==AndroidRuntime.startVm()方法==，该方法中设置了很多虚拟机参数，比如堆大小等等 )
- -> zygote初始化注册jni（完成虚拟机中的JNI方法注册。动态注册各种常用jni，例如messagequeue的nativePollOnce，Bitmap的处理）
	```cpp
	//frameworks/base/core/jni/AndroidRuntime.cpp
	JNIEnv* env;
	if (startVm(&mJavaVM, &env, zygote, primary_zygote) != 0) {//启动虚拟机
	return;
	}
	onVmCreated(env);
	if (startReg(env) < 0) {//注册jni，内部有很多
	ALOGE("Unable to register all android natives\n");
	return;
	}
	```

- -> 反射执行 ZygoteInit.main() 进入java层，執行java代碼
	//zygoteInit.java zygote在java层的代理

- -> java zygoteInit  **perload()加载系统类（各种应用层组件例如Textview），resources资源（例如 字体）
	- peload是一个耗时操作，优化系统启动可以从这里出手
		预加载类和资源
		ZygoteInit#preload()
		
		static void preload() {
		beginIcuCachePinning();
		preloadClasses(); //加载所需的各种class文件
		preloadResources(); //加载资源文件
		preloadOpenGL(); //初始化OpenGL
		preloadSharedLibraries(); //加载系统Libraries
		preloadTextResources(); //加载文字、字體资源


- -> **zygoteInit 创建zygoteService（zygoyeService用于 socket通信）*

- -> zygoteInit 通過jni調用  ==forkSystemServer()进程== (创建第一个system server进程，之后的==ams， atms(API29新增)，pks，wms都是由它创建)==
	- **SystemSever负责启动系统的各项服务,Android系统中Java世界的核心 Service都在这里启动**

- -**> 执行systemServer main()
- -> 调用zygoteServer.loop, zygote 进入死循环监听  socket事件（比如frok app進程）（此处开始产生一些进程lancher，app等）

![](QQ截图20220506193542.png)



#### 补充点
- （1）新进程fork zygote，能fork zygote中的资源的，dvm，jni等，每个进程就一个binder（指进程的binder，不是说app的binder，要分清）

- （2）app请求zygote fork进程使用socket而不是用binder，因为binder支持异步调用（是有綫程池的），binder只有一个，调用的是zygote的binder，这个时候可能会等待binder的锁，**這時fork一個新进程，那麽他的的binder也可能在锁，但是无人通知，就会造成新进程binder死锁。
fork子线程时候为了避免死锁，需要单线程环境。而binder是多线程，在fork子线程时候容易出线程死锁的问题。


##### ServiceManager
由init.rc配置，调用源文件service_manager.cpp或者frameworks/native/cmds/servicemanager/main.cpp启动。（不同android版本）

一个独立的`守护进程`，管理了所有binder，即binder服务管家
	- **1）初始化binder驱动**
	- **2）将自身以“manager” 添加到 servicemanager 中的map集合中**
	- **3）注册成为binder驱动的上下问管理者**
	- **4) 给Looper设置callback,进入无限循环，处理client端发来的请求**

```cpp
int main(...){
	...
	ProcessState::initwithDriver()
	...
}
```
相关[[#BINDER]]

补充（_守护进程_,在linux或者unix操作系统中在系统引导的时候会开启很多服务,这些服务就叫做守护进程）
##### Systemserver
 由zygoteInit 通過jni調用  **forkSystemServer()

- 系统服务进程，启动了systemService
- systemServerManager管理SysytemService
- **各类service由`systemServerManager`管理，创建就添加到systemservicemanger中，获取也是通过systemservicemanger的binder获取service**
- 所有服务继承SysytemService
- 各类service创建后 publish 他的 binder 到`ServiceManager`进程
- 问题就存在了，各类service都是在`Systemserver`进程中的，通过Binder通信，binder由`ServiceManager`进程的`servicemanager`管理。

![](app启动基本流程.png)

####（4 init.rc概述 ^4cae1a

init是由Android的Linux内核启动的第一个第一个进程，这个进程非常特殊，**它的PID永远是1**，并且这个进程是不会死亡的，如果它死亡，内核就会崩溃，
init.rc是一个规定init进程行为和动作的配置文件。init进程可以做哪些事情，都由它规定。
init.rc 文件并不是普通的配置文件，而是由一种被称为“Android初始化语言”（Android Init Language，这里简称为AIL）的脚本写成的文件。
该文件在ROM中是只读的，即使有了root权限，可以修改该文件也没有。
因为我们在根目录看到的文件只是内存文件的镜像。也就是说，android启动后，会将init.rc文件装载到内存。而修改init.rc文件的内容实际上只是修改内存中的init.rc文件的内容。
一旦重启android，init.rc文件的内容又会恢复到最初的装载。想彻底修改init.rc文件内容的唯一方式是**修改Android的ROM中的内核镜像。init.rc只是语法文件，并不是程序，真正的入口则是上面提到的system/core/init/init.c 。**
#### 为什么init进程也要死循环？
init是一个守护进程，为了防止init的子进程（zoygote进程）成为僵尸进程(zombie process)，**需要==init在子进程在结束时获取子进程的结束码，通过结束码将程序表中的子进程移除==，** 防止成为僵尸进程的子进程占用程序表的空间，**当程序表的空间达到上限时，则系统就不能再启动新的进程了，** 那么就会引起很严重的系统问题。
## 1.Android 四大组件,五大存储，六大布局

### 1.总体简介：
https://www.cnblogs.com/tsingke/p/9007563.html

### 2.Content Provider
在Android技术持久化技术中，**包含着文件存储，SharedPreferences存储以及SQLite，在这些持久化技术中保存到数据都只能在当前应用程序中访问**，那么如何将数据共享给其他程序呢？那就要说到contentprovider

由于Android系统中，数据基本都是私有的，处于一个沙箱环境，都是存放于“data/data/程序包名”目录下，所以要实现数据共享contentprovider是正确操作。
创建ContentProvider对外提供接口，其他应用通过ContentResolver和scheme uri访问对应的方法
底层也是通过binder进行app进程间通信

[content Provide](https://www.jianshu.com/p/5e13d1fec9c9)
声明：
```xml
  <provider
	        android:authorities="com.jrmf360.studentProvider"//主机地址
        android:name=".MyContentProvider"
        android:exported="true"/>
```

```java
//服务端
public class MyContentProvider extends ContentProvider {
    public MyContentProvider() {
    }

    @Override
    public boolean onCreate() {
        // TODO: Implement this to initialize your content provider on startup.
        return false;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {
        // TODO: Implement this to handle query requests from clients.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public String getType(Uri uri) {
        // TODO: Implement this to handle requests for the MIME type of the data
        // at the given URI.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        // TODO: Implement this to handle requests to insert a new row.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        // Implement this to handle requests to delete one or more rows.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection,
                      String[] selectionArgs) {
        // TODO: Implement this to handle requests to update one or more rows.
        throw new UnsupportedOperationException("Not yet implemented");
    }
}
```

客户端访问:通过ContentResolver和uri访问对应的方法

```java
//根据号码获取联系人的姓名
public void getContactNameByNumber() throws Exception {
    ContentResolver resolver = getContentResolver();
    
    Cursor cursor = resolver.query(ContactsContract.Contacts.CONTENT_URI, new String[]{ContactsContract.Data.DISPLAY_NAME}, null, null, null);
    
    if(cursor.moveToFirst()){
        String name = cursor.getString(0);
        Log.i(TAG, name);
    }
    cursor.close();
}
```

### FileProvider
ContentProvider 的一个特殊子类，用于==进程间文件共享，为应用内的文件创建 content:// URI，允许对文件的安全共享==，
```xml 
manifest.xml
<provider                
   android:name="androidx.core.content.FileProvider" 
   android:authorities="${applicationId}.fileprovider"        android:grantUriPermissions="true"  
   android:exported="false">               
    <meta-data              
    android:name="android.support.FILE_PROVIDER_PATHS" 
    android:resource="@xml/filepaths" />           
</provider>           

filepaths.xml
<paths>       
   <files-path path="images/" name="myimages" />  
</paths>
```

如果您根据本课程中的代码段定义，并请求文件 `default_image.jpg` 的内容 URI，将返回以下 
	假如applicationId =   com.example.myapp
URI：
    content://com.example.myapp.fileprovider/myimages/default_image.jpg
    
`<files-path>` 标记共享了应用内部存储空间的 `files/` 目录中的目录。`path` 属性共享了 `files/` 的 `images/` 子目录。`name` 属性指示 `[FileProvider] 将路径段 `myimages` 添加到 `files/images/` 子目录中文件的内容 URI 中。
    <paths>        <files-path path="images/" name="myimages" />    </paths>
官方教程：https://developer.android.google.cn/training/secure-file-sharing/setup-sharing?hl=zh-cn
在您需要共享文件的地方（例如，通过 Intent），调用 `FileProvider.getUriForFile()` 方法来获取文件的 content:// URI：
```java
//在这里替换域和文件名
String authority = BuildConfig.APPLICATION_ID + ".fileprovider";
File fileToShare = new File(filePath);//filepath就传入我们app的文件目录就行了
Uri contentUri = FileProvider.getUriForFile(context, authority, fileToShare);

```

actionSend `Intent.ACTION_SEND` 是Android系统中的一个通用动作，用于指代在应用之间发送数据或分享内容的行为。当使用 `Intent` 的 `ACTION_SEND` 动作时，开发人员可以通过代码将一些数据传递给其他兼容的应用，如文本、图片或其他文件格式。这样一来，用户可以在不同应用之间共享数据。
```java
//contentUri 发送给对应的app

Intent shareIntent = new Intent(Intent.ACTION_SEND);
shareIntent.setType("image/jpeg"); //设置分享的文件类型

shareIntent.putExtra(Intent.EXTRA_STREAM, contentUri);

shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); //为接收者授予权限
startActivity(Intent.createChooser(shareIntent, "分享图片"));

```


## 创建文件选择 Activity

如需设置文件选择 Activity，请先在清单中指定以下内容：Activity、与操作 ACTION_PICK匹配的 intent 过滤器以及类别 CATEGORY_DEFAULT 和 CATEGORY_OPENABLE。另外，还需为您的应用向其他应用提供的文件添加 MIME 类型过滤器。以下代码段展示了如何指定新的 Activity和 intent 过滤器：
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">        
<application>            
	<activity android:name=".FileSelectActivity">                   
	<intent-filter>                        
		<action android:name="android.intent.action.PICK"/> 
			<category android:name="android.intent.category.DEFAULT"/>  
			<category  android:name="android.intent.category.OPENABLE"/>
			<data android:mimeType="text/plain"/>   
			<data android:mimeType="image/*"/>    
			</intent-filter>               
	</activity>
	</application> 
</manifest>

```

### sharedPrefenrence:(简介， 详细对比见39)

 SP 的底层是由Xml来实现的，**操作SP的过程就是Xml的序列化和解析的过程，并且存在了Map中。Xml是存储在磁盘上的，因此当我们频繁进行SP操作时，就是频繁进行序列化与解析，这就频繁进行I/O的操作，所以肯定会导致性能消耗。**
 同时**序列化Xml是就是将内存中的数据写到Xml文件中**，由于DVM 的内存是很有限的，因此单个SP文件不建议太大，具体多大是没有一个具体的要求的，但是我们知道DVM 堆内存也就是**16M**，因此数据大小肯定不能超过这个数字的。其实 SP 设置的目的就是为了保存用户的偏好和配置信息的,
 
他会有缓存，并不是每次都去文件中读写，有一个以sharedPreference的名称为key(通过名称缓存一个file，以这个file为key)，对应这个sharedPerference的内容为value的静态的map来缓存整个应用中的sp，所以我们最好不要创建过多的小的sp，尽量合并，不然这个静态的map会很大。
 
```java
val sharedPreferences = getSharedPreferences("FIRST", MODE_PRIVATE)  
val edit = sharedPreferences.edit()  
edit.putInt("1123", 12323)  
edit.apply()  //异步提交
edit.commit()//同步提交
```
applay，commit，同步和异步，都会阻塞
为什么呢？
因为

【相关联系】
[[#17.SharedPreferences]]
[[#39.MMKV 与SP]]


apply源码
```java
public void apply() {
            finallongstartTime= System.currentTimeMillis();
            finalMemoryCommitResultmcr= commitToMemory();
            finalRunnableawaitCommit=newRunnable() {
                    publicvoidrun() {
                        try {
                            mcr.writtenToDiskLatch.await();
                        } catch (InterruptedException ignored) {
                        }
 
                        if (DEBUG && mcr.wasWritten) {
                            Log.d(TAG, mFile.getName() + ":" + mcr.memoryStateGeneration
                                    + " applied after " + (System.currentTimeMillis() - startTime)
                                    + " ms");
                        }
                    }
                };
			// 将 awaitCommit 添加到队列 QueuedWork 中
            QueuedWork.addFinisher(awaitCommit);
 
            RunnablepostWriteRunnable=newRunnable() {
                    publicvoidrun() {
                        awaitCommit.run();
                        QueuedWork.removeFinisher(awaitCommit);// 将 awaitCommit 从队列 QueuedWork 中移除
                    }
                };
 
            SharedPreferencesImpl.this.enqueueDiskWrite(mcr, postWriteRunnable);
 
            // Okay to notify the listeners before it's hit disk// because the listeners should always get the same// SharedPreferences instance back, which has the// changes reflected in memory.
            notifyListeners(mcr);
        }
```

从上面我们可以看到apply是把任务添加到了queuework中，
**Android系统为了保障在页面切换，也就是在多进程中sp文件能够存储成功，在ActivityThread的handlePauseActivity和handleStopActivity时会通过QueuedWork.waitToFinish保证这些异步任务都已经被执行完成**。如果这个时候==过度使用apply方法，则可能导致onpause，onStop执行时间较长，从而导致ANR==。
Queuework：这是个内部 工具 类，用于==跟踪那些未完成的或尚未结束的全局任务（onPause，onStop检查）==，新任务通过方法 queue
加入。添加 finisher
的runnables，由 waitToFinish
方法保证执行，用于保证任务已被处理完成。

```java
    private void handlePauseActivity(IBinder token, boolean finished,
                boolean userLeaving, int configChanges, boolean dontReport, int seq) {
           ......
                r.activity.mConfigChangeFlags |= configChanges;
                performPauseActivity(token, finished, r.isPreHoneycomb(), "handlePauseActivity");
     
                // Make sure any pending writes are now committed.if (r.isPreHoneycomb()) {
                    QueuedWork.waitToFinish();
                }
     
               ......
        }
```



你肯定要问，为什么过渡使用apply方法，就有可能导致ANR？那我们只能看QueuedWork.waitToFinish();当任务未完成的时候会阻塞。

### SQLite
SQLite是一个轻量级的数据库，支持基本SQL语法，是常被采用的一种数据存储方式。Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的API.

索引是一种数据结构，顾名思义，跟我们看书的某一页的索引一样用于快速寻找到某一页，按照索引字段进行排序。
[[数据库基础与原理]]

了解即可：
	1. 单列索引：基于一个表的单个列创建的索引。
	2. 多列索引（复合索引）：基于表的多个列创建的索引，通常用于多条件查询的优化。
	3. 唯一索引：这种索引要求索引列的所有值都是唯一的，因此可以防止重复数据。主键索引就是一种特殊的唯一索引。
	4. 非唯一索引：此类型的索引允许索引列出现重复的值。
	5. 聚簇索引：这种索引将数据行与索引结构紧密结合在一起，因此数据行按照索引顺序存储。在查询时，如果使用聚簇索引的列进行查询，数据行本身可以在索引结构中找到。
	6. 非聚簇索引：这种索引与聚簇索引相反，保持了数据行和索引结构的独立性。非聚簇索引通常为数据行提供一个“指针”，指示数据行在数据表中的实际位置。

### 3. 布局面试
[布局对比和常见面试问题](https://www.dandelioncloud.cn/article/details/1546584614565527554)
- xmlns：android是XML中的命名空间，为了防止属性冲突。类似于Java中的package。xmlns：android的值是不允许任意设置的。xmlns：android的是必须是以“http://schemas.android.com/apk/res”开始，后面的部分表示定义属性R.java文件所在的包名。在R.java文件中包含了属性名的定义。例如，如果使用系统属性，需要指定系统R.java文件的位置。该文件位于res \\ android目录中，因此，xmlns：android值的最后是android。

### 广播BroadCastReciever: 

- 静态注册方式是在AndroidManifest.xml文件中进行注册，这种方式会在APP装上设备后，就会一直处于接收状态。
- 动态注册方式则是在运行时进行注册，只有当注册后，才会处于接收状态，当解除注册后，就不会接收广播了。

7.0 开始移除了三项隐式广播，其中包括了网络状态的广播

==Android8.0开始，Android将**不支持大部分隐式广播**，包括自定义的广播和系统的。==
	1.改为带签名权限的广播。==因为这些广播只会发送到使用**相同证书签名的应用**，而不是发送到设备上的所有应用。==
	2. 动态注册而==不是在清单文件中注册==Reciever

**需要通过context.regeister的方式注册。**

## 1.1 存储路径

### 一.getCacheDir、getCacheDir

> getCacheDir()方法用于获取/data/data//cache目录 
>  getFilesDir()方法用于获取/data/data//files目录

### 二. getExternalFilesDir、getExternalCacheDir(android10之后的方式)


应用程序在运行的过程中如果需要向手机上保存数据，一般是把数据保存在SDcard中的。大部分应用是直接在SDCard的根目录下创建一个文件夹，然后把数据保存在该文件夹中。这样当该应用被卸载后，这些数据还保留在SDCard中，留下了垃圾数据。如果你想让你的应用被卸后，与该应用相关的数据也清除掉，该怎么办呢？

- 通过Context.getExternalFilesDir()方法可以获取到 SDCard/Android/data/你的应用的包名/files/ 目录，一般放一些长时间保存的数据
通过Context.getExternalCacheDir()方法可以获取到  SDCard/Android/data/你的应用包名/cache/目录，一般存放临时缓存数据.如果使用上面的方法，当你的应用在被用户卸载后，SDCard/Android/data/你的应用的包名/ 这个目录下的所有文件都会被删除，不会留下垃圾信息。


而且上面二个目录分别对应 设置->应用->应用详情里面的”清除数据“与”清除缓存“选项 
如果要保存下载的内容，就不要放在以上目录下

#### filePath放在哪个文件夹

```java
Environment.getDataDirectory() = /data


Environment.getDownloadCacheDirectory() = /cache(android10 api29之前)
Environment.getExternalStoragePublicDirectory() = /mnt/sdcard(android10 api29之前)
Environment.getExternalStoragePublicDirectory(“test”) = /mnt/sdcard/test(android10 api29之前)

/**
在使用Android SDK 版本超过29编译的时候，Android Studio会提示Environment.getExternalStorageDirectory()过时了，要用Context#getExternalFilesDir代替，Android Q以后Environment.getExternalStorageDirectory()返回的路径可能无法直接访问，所以改成了Context#getExternalFilesDir
**/

Environment.getRootDirectory() = /system

getPackageCodePath() = /data/app/com.my.app-1.apk
getPackageResourcePath() = /data/app/com.my.app-1.apk
getCacheDir() = /data/data/com.my.app/cache
getDatabasePath(“test”) = /data/data/com.my.app/databases/test
getDir(“test”, Context.MODE_PRIVATE) = /data/data/com.my.app/app_test
getExternalCacheDir() = /mnt/sdcard/Android/data/com.my.app/cache(android10 api29之后)
getExternalFilesDir(“test”) = /mnt/sdcard/Android/data/com.my.app/files/test(android10 api29之后)
getExternalFilesDir(null) = /mnt/sdcard/Android/data/com.my.app/files(android10 api29之后)
getFilesDir() = /data/data/com.my.app/files
```

应用包下的存储都不需要存储权限申请

### 1.1.3 分区存储


Android 10 /11之后，Google首次引入了分区存储，将公共区域划分成了不同的集合，并且在媒体文件和其他文档之间建立了清楚的分割。**经过划分之后应用不可以随意访问外部存储区中的文件，而只能访问媒体文件**。
 * 1.应用私有目录：存储应用私有数据，外部存储应用私有目录对应Android/data/packagename，内部存储应用私有目录对应data/data/packagename；
 * 2.共享目录：**存储其他应用可访问文件**， 包含媒体文件、文档文件以及其他文件，对应设备DCIM、Pictures、Alarms, Music, Notifications,Podcasts, Ringtones、Movies、Download等目录。
 * Android 11 (API 级别 30) 进一步增强了平台功能，为外部存储中的应用和用户数据提供了更好的保护。
 * 从 Android 11 开始，使用 分区存储模式 的应用即使拥有 READ_EXTERNAL_STORAGE 权限，**也无法再访问 外部存储 中的任何其他应用的 专属目录（Android\data） 中的文件**，受此影响应用间分享就需要使用通过应用间共享文件**适配的方式（ FileProvider进行分享），通过FileProvider，就允许第三方应用读取你的应用所分享的文件，而不会受到分区存储的限制**。

## 2.java和android的字节

Java的数据类型：

1. 整型

byte     1个字节
short   2个字节
int        4个字节
**long    8个字节**

2. 实型

float      4个字节
double 8个字节

3.字符型

**char      2个字节**

3.对象型

Java中数组和String类型的都被解释为对象

4.其它

Boolean 可以认为boolean型占八分之一个字节,即1bit，但是它的大小是不明确的，视编译器而定

 

android数据类型所占字节数：

 **1.char             1个字节**

 2.int                 4个字节  （默认有符号）

 3. **long            4个字节** 

 4.long long    8个字节

 5.short            2个字节

 6.int 64_t        8个字节

 7.uint 8_t        1个字节

原文链接：https://blog.csdn.net/ludongdong/article/details/20839531

## 3. Android版本特性简述

- ### **5.0 **
	-使用ART虚拟机
	-禁止隐式启动service，要先androidmanifest声明再显式调用
	```java
	//禁止隐式
	Intent intent = new Intent();  
	intent.setAction("com.example.user.firstapp.FIRST_SERVICE");  
	bindService(intent,coon,Service.BIND_AUTO_CREATE);  
	或者
	startService(intent);
	```

- ### 6.0

动态权限 ，引入了Doze机制和应用程序待机。当屏幕关闭且设备静止时, 打盹模式会限制应用程序的行为。应用程序待机将未使用的应用程序置于限制其网络访问、作业和同步的特殊状态。

- ### 7.0

分屏 分享私有要使用fileprovider

- ### 8.0
- 多显示器

- **系统不允许==后台应用==创建后台服务（service）。因此Android 8.0引入了一种全新的方法，即 Context.startForegroundService()，以在前台启动新服务。**想要在后台运行服务只能是前台应用。

- workmanager和schedulerjob进行轮询。
	在系统创建服务后，应用有五秒的时间来调用该服务的 startForeground()方法以显示新服务的用户可见通知。如果应用在此时间限制内未调用startForeground()，则系统将停止服务并声明此应用为ANR。
	原文链接：https://blog.csdn.net/haoyuegongzi/article/details/112000275

- 不允许隐式广播

- ### 9.0支持刘海屏


10.0  android10以上禁止获取deviceId，可以通过获取AndroidID(无需权限，缺点时重置系统会重置)

```
private fun getAndroidID() {
    val androidID = Settings.System.getString(
        contentResolver, Settings.Secure.ANDROID_ID
    )
    Log.i(TAG, "AndroidID为:$androidID")
}

```

5G和可折叠 

分区存储

- ### 10.0 

10.0  android10以上禁止获取deviceId，可以通过获取AndroidID(无需权限，缺点时重置系统会重置)

```
private fun getAndroidID() {
    val androidID = Settings.System.getString(
        contentResolver, Settings.Secure.ANDROID_ID
    )
    Log.i(TAG, "AndroidID为:$androidID")
}

```


 5G和可折叠

- 对储存的更新：分区储存

	**旧版本：**
	**获取外部存储目录**

	**函数使用：**getExternalStorageDirectory()

	**获取外部存储公共目录**
	**函数使用：**getExternalStoragePublicDirectory()

	**新版本**
	在使用Android SDK 版本超过29编译的时候，Android Studio会提示[Environment](https://so.csdn.net/so/search?q=Environment&spm=1001.2101.3001.7020).getExternalStorageDirectory()过时了，要用Context#getExternalFilesDir代替，Android Q以后Environment.getExternalStorageDirectory()返回的路径可能无法直接访问，所以改成了Context#getExternalFilesDir

Android 10 之前的文件系统 , 内存分为两块 , 应用私有目录 , 和 [共享存储](https://cloud.tencent.com/product/cfs?from_column=20421&from=20421)空间 ;

**私有目录 :** 只能保存本应用的数据 , 其它应用无法读写该目录中的数据 , 应用卸载时 , 该目录自动删除 ;

**共享存储空间 :** 所有的应用都可以随意访问 , 随意读写该区域内的数据 , 数据可以任意放置 , 删除 , 修改 ; 应用卸载时写出到该区域的数据不会被删除 ;

任何目录可以存储任何类型文件 , 可以将图片存储到 Movies , 视频存储到 Music 目录中 ;

共享存储空间 绝对是个 天坑 , 能不用尽量不用

**Android** 10 **及以后的文件系统 :**

**私有目录 :** 与之前保持一致 , 只能保存本应用的数据 , 其它应用无法读写该目录中的数据 , 应用卸载时 , 该目录自动删除 ; 即沙盒目录。/data/data/#apppackname#下的目录

**共享存储空间 :** Google 官方对该存储区域进行了统一规划 , Android 11 中 , 每个目录存储什么类型的文件 , 都有相应规范 , 文件不能乱放置 ;

[**文件存储**](https://cloud.tencent.com/product/cfs?from_column=20421&from=20421)**类型限制 :** 文件一旦放错位置 , 就会抛异常 ;

	- 专门存储 图片文件 的目录 Pictures , 只能存放图片 , 不能存放其它类型文件 ,
	- 专门存储 视频文件 的目录 Movies , 只能存视频文件 ;
	- 专门存储音频文件的目录 Music , 只能存储音频文件 ;
	- 专门存储文档文件的目录 Document , 只能存储文档相关文件 ;
	- Download 目录可以放置任何类型的文件 ;

- ### 11 
	-支持一次性权限

强制分区存储

https://www.jianshu.com/p/7875ac6139a3

https://www.jianshu.com/p/7155b224ddfc

Android 11之后，储存权限更加严格，添加了一条叫做`MANAGE_EXTERNAL_STORAGE`的权限，用来管理整个文件系统，才能访问文件。
我们不难发现当我们申请 ==MANAGE_EXTERNAL_STORAGE 管理外部存储权限== 后，完全可以当做之前外部存储的方式去读取，完美解决沙盒模式对拿取外部的非APP创建的文件的困扰。

代码中用 ==Environment.isExternalStorageManager()== 来判断APP是否拥有该权限，若无此权限，则将页面手动跳转至设置页面，让用户手动打开该权限，

==PS.若小伙伴们需要把APP发布到谷歌商店中，慎重使用该权限，因为该权限本为浏览器或本地文件操作器类型的APP设计，所以一般APP申请该权限，谷歌可能不会同意。==

**Android** 11**（API 级别** 30**） 之后的存储机制 :
** Android 中所有文件存储 , 都会将文件的索引存储在[数据库](https://cloud.tencent.com/solution/database?from_column=20421&from=20421)中 , 在 /data/data/com.android.providers.media **目录下的文件就是专门用于管理该数据库的 ;需要使用 MediaStore 进行文件操作,无法直接使用new file()，但是可以复制到沙盒空间中（data下)进行new file**


- 12
	禁止==后台启动前台服务==
- 13.0

## 4.apk打包流程

![[61ba2c433839491ea9256f81bf4dabd3.png]]

dex生成、instant run等看 [[#45.热修复]]


![[dcc282fd0bcc45f686473d2c23a9232f~tplv-k3u1fbpfcp-zoom-in-crop-mark 4536 0 0 0.webp]]

### 1.打包资源文件，生成R.java文件

aapt（打包工具Android Asset Packaging Tool）来打包res资源文件，生成==R.java、resources.arsc和res文件。==

**AAPT2**（Android 资源打包工具）是一种构建工具，Android Studio 和 Android Gradle 插件使用它来编译和打包应用的[资源](https://link.zhihu.com/?target=https%3A//developer.android.com/guide/topics/resources/providing-resources)。AAPT2 会解析资源、为资源编制索引，并将资源编译为针对 Android 平台进行过优化的二进制格式。
**1、编译：将资源文件编译为二进制格式。**
把所有的Android资源文件进行解析，生成扩展名为.flat的二进制文件。比如是png图片，那么就会被压缩处理，采用.png.flat的扩展名。可以在build/intermediates/merged_res/文件下查看生成的中间产物。

**2、链接：合并所有已编译的文件并将它们打包到一个软件包中。**
首先，这一步会生成辅助文件，比如R.java与resources.arsc，R文件大家应该都比较熟悉，就是一个资源索引文件，我们平时引用也都是通过R.的方式引用资源id。而resources.arsc则是资源索引表，供在程序运行时根据id索引到具体的资源。最后会将R文件，ressources.arsc文件和之前的二进制文件进行打包，打包到一个软件包中。  

这种拆分方式有助于提高增量编译的性能。例如，如果某个文件中有更改，您只需要重新编译该文件

Android Gradle 插件 3.0.0 及更高版本默认情况下会启用 AAPT2，因此您通常不需要自行调用 aapt2。不过，如果您更愿意使用自己的终端和构建系统而不是 Android Studio，则可以从命令行使用 AAPT2。


### 2.处理aidl文件，生成相应的Java文件

aidl（Android Interface Definition Language，Android接口描述语言），位于android-sdk/platform-tools目录下。aidl工具解析接口定义文件然后生成相应的Java代码接口供程序调用。如果项目没用到aidl则跳过这一步。
==AIDL的Binder是匿名的。==这意味着它没有在ServiceManager中注册名称，==而是通过匿名Binder进行通信。匿名Binder必须建立在实名Binder之上==，也就是必须有一个在ServiceManager中注册过的实名Binder。通过AIDL创建的Binder可以与ServiceManager通信，并获取其他进程的服务引用。
### 3.编译项目源代码，生成class文件

Java Compiler阶段。项目中所有的Java代码，包括R.java和.aidl文件，都会变Java编译器（javac）编译成.class文件，生成的class文件位于工程中的bin/classes目录下。

### 4.转换所有的class文件，生成classes.dex文件

dex阶段。通过dx工具，将.class文件和第三方库中的.class文件处理生成classes.dex文件。该工具位于android-sdk/platform-tools 目录下。dx工具的主要工作是将Java字节码转成成Dalvik字节码、压缩常量池、消除冗余信息等。

### 5.打包生成APK文件

apkbuilder阶段。通过apkbuilder工具，将aapt生成的resources.arsc和res文件、assets文件和classes.dex一起打包生成apk。打包的工具apkbuilder位于 android-sdk/tools目录下。

### 6.对APK文件进行签名

Jarsigner阶段。通过Jarsigner工具，对上面的apk进行debug或release签名。



### 7. 对签名后的APK文件进行对齐处理

通过zipalign工具，将签名后的apk进行对齐处理。工具位于android-sdk/tools目录下。对齐的主要过程是将APK包中所有的资源文件距离文件起始偏移为4字节整数倍，这样通过内存映射访问apk文件时的速度会更快。对齐的作用就是减少运行时内存的使用。


## 5.apk文件结构

![apk结构](https://img-blog.csdnimg.cn/20190428215401792.png) 

apk是一个压缩包，里面有lib，META-INF，classes.dex，res，resources.arsc文件夹和文件 

|文件名|解释|
|---|---|
|**lib**	|放的是so动态链接库	apk打包不需要处理的动态库|
|**META-INF**	|签名文件夹	三个签名证书（MANIFEST.MF、CERT.SF、CERT.RSA）。MANIFEST.MF文件是对每个文件的SHA-256-Digest；CERT.SF是对每个文件的头3行进行SHA-256-Digest；CERT.RSA这个文件保存了签名和公钥证书。|
|**classes.dex**|	执行文件	java编译后的Android可执行的dex文件|
|**AndroidManifest.xml**	|声明文件	记录应用的名字、版本、权限、引用的库文件等信息|
|**res**|	资源文件	有**animator,anim,color,drawable,layout,menu，raw等文件夹**|
|**resources.arsc**|	编译后的二进制资源文件	记录了所有的**应用程序资源目录的信息**，包括每一个资源名称、类型、值、ID以及所配置的维度信息。 这是一个索引文件。|

## 6.build优化加速

https://developer.android.com/studio/build/optimize-your-build?hl=zh-cn
#### 避免编译不必要的资源

避免编译和打包不测试的资源（例如，其他语言本地化和屏幕密度资源）。您可以仅为“dev”变种的版本指定一个语言资源和屏幕密度，如下面的示例中所示：比如某个版本不需要某些语言，那么配置对应productFlavors，debug下可能很多都不需要都可以去除

```java
android {    ...  
productFlavors {        
		dev {      
			// The following configuration limits the "dev" flavor to using            
			// English stringresources and xxhdpi screen-density resources.            
			resourceConfigurations "en", "xxhdpi"        }     
	...    }  
}
```

### 代码仓库
优先使用国内仓库，依赖的版本也写成固定的版本号，而不是动态的版本号，这样不用每次都打包都会去检查更新，而且最新版可能会导致一些报错或者bug。
### 将图片转换为 WebP 格式
[WebP](https://developers.google.com/speed/webp/?hl=zh-cn) 是一种既可以提供有损压缩（像 JPEG 一样）也可以提供透明度（像 PNG 一样）的图片文件格式，不过与 JPEG 或 PNG 相比，WebP 格式可以提供更好的压缩。

==减小图片文件大小可以加快构建速度（无需在构建时进行压缩），尤其是当应用使用大量图片资源时，还能减小apk大小。不过，在解压缩 WebP 图片时，cpu使用率会有所上升。

### 开启并行编译
开启后可以多个编译task并行运行·
只需要在gradle.properties中添加：

> org.gradle.parallel=true

### 增量式编译
Gradle的增量式构建特性紧紧的与生命周期相结合。Gradle的增量式构建支持自动鉴别不需要被运行的任务。这些任务会被标记为 UP-TO-DATE。特别是在大型的企业级项目，这个特性是节约时间的好帮手。

**Gradle通过比较两次构建task的inputs和outputs来决定task是否是最新的。自从最后一次task执行以来，如果inputs和outputs没有发生变化，则认为task是最新的。**
### 启用构建缓存

默认情况下，构建缓存是没有启用的，有两种方法可以启用构建缓存：

- 构建的命令行下增加`--build-cache`选项,但要注意只是本次生效。
- `gradle.properties`文件中设置`org.gradle.caching=true`，这样就可以每次生效，除非命令行下带了`--no-build-cache`选项
### native编译缓存

native编译工具链也可以缓存，将org.gradle.caching.native设置为true就可启用，比如CCompile和CppCompile
### **增大编译内存**

由于大家的电脑配置都不一样，因此具体设置多大内存需要根据个人情况进行合理配置，一般在**gradle.properties**里已经有相关配置，可以对该配置进行修改，例如

```java
org.gradle.jvmargs=-Xmx4096m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8

```

同时在主工程模块的build.gradle中进行修改：

```java
    dexOptions {
        javaMaxHeapSize "4g"
    }

```
### 组件化
可以采用组件化的形式，只打包对应的模块。这样debug的时候就不用整个项目都运行。
### **开启按需构建**  
对没有更改的模块不再进行编译，非常适合已经组件化的项目，在gradle.properties中添加：

```java
org.gradle.configureondemand=true
```
### **关闭R文件传递**  
在 apk 打包的过程中，module 中的 R 文件采用对依赖库的R进行累计叠加的方式生成。如果我们的 app 架构如下：

编译打包时每个模块生成的R文件如下：

```java
1. R_lib1 = R_lib1;
2. R_lib2 = R_lib2;
3. R_lib3 = R_lib3;
4. R_biz1 = R_lib1 + R_lib2 + R_lib3 + R_biz1(biz1本身的R)
5. R_biz2 = R_lib2 + R_lib3 + R_biz2(biz2本身的R)
6. R_app = R_lib1 + R_lib2 + R_lib3 + R_biz1 + R_biz2 + R_app(app本身R)

```

** 1.关闭R文件传递可以通过编译避免的方式获得更快的编译速度  **
2.关闭R文件传递有助于**确保每个模块的R类仅包含对其自身资源的引用**，避免无意中引用其他模块资源，**明确模块边界**。  
3.关闭R文件传递也可以**减少很大一部分包体积与dex数量**

从 Android Studio Bumblebee 开始，新项目的非传递 R 类默认处于开启状态。即
gradle.properties
文件中都开启了如下标记

```java
android.nonTransitiveRClass=true
```
### **开启Kotlin跨模块增量编译**  
使用组件化多模块开发的同学都有经验，当我们修改底层模块(比如util模块)时，所有依赖于这个模块的上层模块都需要重新编译，Kotlin的增量编译在这种情况往往是不生效的，这种时候的编译往往非常耗时。

在Kotlin 1.7.0中，Kotlin编译器对于跨模块增量编译也做了支持，并且与Gradle构建缓存兼容，对编译避免的支持也得到了改进。这些改进减少了模块和文件重新编译的次数，让整体编译更加迅速。

在 gradle.properties 文件中设置以下选项即可使用新方式进行增量编译：

```java
kotlin.incremental.useClasspathSnapshot=true // 开启跨模块增量编译
kotlin.build.report.output=file // 可选，启用构建报告

```


### **Module源码转aar**  
随着业务量的增大，module的引入也会增多，每个module在编译的时候都需要花费一定的时间。把module转化成aar后就不再需要每次都进行编译或者取缓存，可以减少一部分时间。
### KAPT升级到KSP
如果用了很多注解框架，ARouter，butterknife，retrofit等等，==可以将kapt升级到ksp==，生成代码的过程中少了生产java stub的中间类的过程，节省了时间空间。
### transform 优化

#### 背景

transform 作为 Android gradle plugin 提供给开发者的 API，用于在 apk 构建过程中，对 class 字节码，resources 等文件内容进行插桩修改，例如官方的 dex, proguard 等功能均由此 api 实现。

对于今日头条这种大型工程来说，==有很多诸如性能插桩、自动埋点插桩等相关需求，因此基于此 api 开发了大量 transform，用于实现特定功能，但是这些 transform 基本上都是不支持增量编译的==，即使只改动了一行代码，这 些 transform 都会遍历所有 class 文件，解析字节码中的方法字段信息，关键是这类 transform 数量有十几个，将这些遍历耗时乘以 10 累加之后，增量编译耗时自然居高不下。

根据分析，==**其中性能插桩等相关 transform 做的一些面向线上的插桩方案是完全可以只在 release 打包时打开的，因此可以直接在 debug 编译时禁用这些功能，用于提升开发期间的编译速度==。**而剩下的 9 个 transform 特征比较相似，可能在一些插桩细节上有所不同，它们大致的处理逻辑为：

  
1. 在各个模块中使用 apt processor 收集模块 xx 注解的 class 信息然后生成一个 xxCollect 类，该类的作用是收集好 apt 阶段解析到的本模块的类信息
    
2. 将所有模块收集到的信息进行汇总，利用 transform 阶段扫描出所有的 xxCollect 信息，通过 javaassit 或者 asm 往一个 xxCollectMgr 的某个 collectXxx 方法插桩注入之前收到的信息
    

3. 业务代码可通过 xxCollectMgr 的 collectXxx 方法获取到在各个模块动态生成的所有 xxCollect 信息。（例: 页面路由相关框架便是通过该逻辑收集到所有子模块的路由注册信息)

由于这 9 个自定义 transform 的功能如此类似，便决定将这些 transform 合并成一个，这样同一个文件的读写操作只执行一次，并且可以做定制化的增量编译优化。虽然公司内有类似的 transform 合并优化方案 byteX ( 已在 github 开源），但是由于今日头条项目在 debug 阶段未开启该功能，且 ByteX 做了一些诸如 ClassGrapth 的构建，对类文件做两次遍历等操作，对于实现类信息收集和信息注入 这个功能来说，byteX 显得比较重 ，于是仍然针对类信息收集注入功能这个细分场景开发了一个收敛框架。
## 7.ADB常用命令

——查看ADB版本：adb version

——查看手机设备：adb devices

——查看设备型号：adb shell getprop ro.product.model

——查看电池信息：adb shell dumpsys battery

——查看设备ID：adb shell settings get secure android_id

——查看设备IMEI：adb shell dumpsys iphonesubinfo

——查看Android版本：adb shell getprop ro.build.version.release

——查看手机网络信息：adb shell ifconfig

——查看设备日志：adb logcat

——重启手机设备：adb reboot

——安装一个apk：adb install /path/demo.apk

——卸载一个apk：adb uninstall

——查看系统运行进程：adb shell ps

——查看系统磁盘情况：adb shell ls /path/

——手机设备截屏：adb shell screencap -p /sdcard/aa.png
https://blog.51cto.com/u_15296378/3087781

## 8. 唯一标识符

- AndroidID：根据硬件信息系统信息和操作系统版本号等等。Android ID是在设备首次启动时生成的。获取无需权限。

- IMEI,MEID（deviceid一般是）: 和手机支持的网络制式相关，注意：Android10以上禁止获取IMEI，因为需要READ_PRIVILEGED_PHONE_STATE权限，而该权限只能是系统应用才可以获取到。

- OAID(推荐):移动安全联盟（中国通讯院）和各个国内厂商推出的设备识别字段。不需要权限，引入lib支持，仅安卓10以上。

# 1. Context

上下文，可以这么理解，context是存储了当前activity/application中的各种配置信息，资源读取等等，是与参与app内容和资源管理的代理者。
详细读取与加载资源看context的ceateAppContext和createActivityContext方法。
Contextmpl
![[5c86b30f1705676b57702455c42ce48b.jpg]]
# 2. Activity

## 2.1 app启动方式

**冷启动**：当启动应用时，**后台没有该应用的进程**，这时系统会重新创建一个新的进程分配给该应用，这个启动方式就是冷启动。冷启动因为系统会重新创建一个新的进程分配给它，所以会先创建和初始化Application类，再创建和初始化MainActivity类（包括一系列的测量、布局、绘制），最后显示在界面上。 

**热启动：**当启动应用时，后台**已有该应用的进程（例：==按back键、home键，应用虽然会退出，但是该应用的进程是依然会保留在后台，可进入任务列表查看）**，所以在已有进程的情况下，这种启动会从已有的进程中来启动应用，这个方式叫热启动。==

热启动因为会从已有的进程中来启动，所以热启动就不会走Application这步了，而是直接走MainActivity（包括一系列的测量、布局、绘制），所以热启动的过程只需要**创建和初始化一个MainActivity就行了**，而不必创建和初始化Application，因为一个应用从新进程的创建到进程的销毁，Application只会初始化一次。 

**冷启动**
系统不存在App进程（APP首次启动或APP被完全杀死）时启动APP，此时，APP的启动将经历两个阶段：

第一阶段：

    加载并启动app;
    app启动后，第一时间为app显示一个空白的window；
    创建app进程

第二阶段：

    系统一旦创建了app进程，app进程就要负责做以下的任务：

创建app对象

    启动主进程ActivityThread；
    创建MainActivity；
    渲染视图；
    执行onLayout；
    执行onDraw
    完成第一次绘制后，把mainActivity替换已经展示的BackgroundWindow，即空白window。

**热启动

    当我们按了Home键或其它情况app被切换到后台，再次启动app的过程。
    热启动时，系统将activity带回前台。如果应用程序的所有activity存在内存中，则应用程序可以避免重复对象初始化、渲染、绘制操作
    如果由于内存不足导致对象被回收，则需要在热启动时重建对象，此时与冷启动时将界面显示到手机屏幕上一样。

**温启动**

温启动包含了冷启动的一些操作，==由于app进程依然在==，温启动只执行冷启动的第二阶段，这代表着它比热启动有更多的开销。

温启动有很多场景，例如：

    用户按连续按返回退出了app，然后重新启动app；
    由于系统收回了app的内存，然后重新启动app。


## 启动模式总共有四种：

- standard : 标准模式，每次启动都会创建一个activity对象。无论堆栈中是否存在相应的activity.每次创建过后的[生命周期](https://so.csdn.net/so/search?q=%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F&spm=1001.2101.3001.7020) 会从oncreate 开始重新执行
- singTop ：栈顶复用模式，启动activity时，如果activity实例位于栈顶，那么就复用该activity对象。如果存在于栈顶的话，这时候就会直接复用栈顶的activity.
	这时候的生命周期,不会调用oncreate,和onstart, **而会调用 onNewIntent.**
- singTask ：栈内复用模式（单实例模式），启动activity时，如果activiy实例在栈内已经存在，那么将复用此activity。**该实例上面的activity全部弹出堆栈，保证当前的实例存在于栈顶，这时候的生命周期依然是调用onNewIntent.**
- singInstance ：单实例模式，拥有singTask所有的特性，同时具有此模式的Activity只能单独的位于一个任务栈中的特点。

onNewIntent ：这时候虽然传递过来新的Intent, 但是如果我们直接使用getIntent获取的数据依然是老数据，所以我们在onNewIntent中需要设置新的intent

建议扩展到ATMS里去查看实现。
## 2.2 Activity生命周期和启动流程
相关扩展：[[#五,系统启动流程]]

![[8c7a5783e7ed4376aa45fccf2fca26d4.webp]]

onCreate  onStar() onResume  ->开始绘制 -> 切换界面->onPasue

->onStop ->onDestory

onCreate（saveInstanceState）  参数里-> 恢复

锁屏 onPause()->onStop()

![[24b51461d53a4664a80927a9b523f67b.png]]

onSaveInstanceState()在生命周期结束前会调用该方法保存状态，当Activity非正常销毁之后，例如手机旋转，内存不足导致的后台自动销毁。
我们可以将状态数据以key-value的形式放入到savedInstanceState中 


- onCreat() 不可见不可交互 创建时调用. setContentView,反射解析xml布局。

- onStart()是activity界面被显示出来的时候执行的，用户可见，包括有一个activity在他上面，但没有将它完全覆盖，用户可以看到==部分activity==但不能与它交互    创建时或者从后台重新回到前台时调用(==此时没有实际绘制view==)

下面这三个状态是静态（static）的，意味着activity只有在这三个状态下能停留一段时间：

- onResume()是该activity与用户能进行交互时被执行，用户可以获得activity的焦点，能够与用户交互。创建或者从被覆盖、后台重新回到前台时被调用，此时解析的view被add到contentview中。

- onPause() 可见不可交互（部分可见），不能接收用户输入也不能执行代码，另一个半透明或者小的activity正挡在前面。被覆盖到下面或者锁屏时被调用 

- onStop() 不可见不可交互失去焦点，activity完全被遮挡，不能被用户看到，activity被认为在background，当Stopped的时候，activity实例的状态信息被保留，但是不能执行任何代码。
退出当前Activity或者跳转到新Activity时被调用 



- onRestart() 从不可见到可见  ，
	1. **用户按下后退按钮**: 如果当前 Activity 处于栈顶，当用户按下返回（back）按钮，当前 Activity 会调用 `onPause` 和 `onStop`，当用户再通过某种方式返回到该 Activity 时，系统会先调用 `onRestart`，然后是 `onStart` 和 `onResume`。
	    
	2. **用户导航到其他 Activity**：如果您的 app 启动了另一个 Activity，并且该 Activity 完全覆盖了您的 Activity（即您的 Activity 是完全不可见的），那么您的 Activity 就会进入停止状态，并调用 `onStop`。然后，如果用户返回到您的 Activity，根据生命周期，系统会调用 `onRestart()`，然后才是 `onStart()` 和 `onResume()。
	    
	3. **用户按下主屏幕按钮**：如果用户按下主屏幕按钮将应用程序置于后台，Activity 将调用 `onPause` 和 `onStop`。当用户再次打开应用程序时，系统将首先调用 `onRestart`，然后是 `onStart` 和 `onResume`。

- onDestory() 销毁activity     退出当前Activity时被调用,调用之后Activity就结束了
### 启动新的Activity生命周期
当启动一个新的Activity时（比如说，通过调用startActivity(intent)方法），当前Activity会按照其生命周期的规则进行变化。

当前Activity生命周期的行为如下所示：

1. onPause()：当前Activity即将停止运行并进入Paused状态，因为新Activity会运行在前台。在这个方法中，您可以暂停与用户交互同相关的操作（例如动画，传感器数据收集等）以及释放共享或一次性资源。
    
2. onStop()：当新Activity全屏启动并完全遮挡当前Activity时，当前Activity的onStop()方法会被调用。此时，当前Activity进入Stopped状态。您可以在这个方法中进行一些资源释放、保存数据等操作，但请注意在此阶段不要执行太多操作，以免影响新Activity的启动速度。
	注意：==如果由于某些原因新Activity启动失败或退出，导致当前Activity重新回到前台，它会执行onRestart()==，然后再继续调用onStart()和onResume()方法逐级恢复。
	在一般情况下，当前Activity的生命周期状态将按照如下顺序：
		- onPause()
		- onStop()
	
	当用户返回到当前Activity时，它将执行：
		
		- onRestart()
		- onStart()
		- onResume()

![](QQ截图20230112185042.png)
## onRestoreInstanceState和onSaveInstanceState（Activity创建与恢复）
![[20191207134521432.png]]
### onCreate也有一个：
SaveInstanceState
1. 配置变更：当设备配置发生变更时，例如屏幕旋转或者键盘可用性发生改变，Activity 可能会被销毁并重建。在这种情况下，系统会调用 Activity 的 `onSaveInstanceState()` 方法来保存实例状态，然后在重建 Activity 时通过 `onCreate(Bundle savedInstanceState)` 方法将其传递回去。通过这种方式，您可以保存 Activity 的状态信息，在重建之后恢复到正确状态。
    
2. 内存回收：如果系统资源不足，系统可能会销毁后台中的 Activity 以释放内存。在这种情况下，系统通常会在销毁 Activity 之前调用 `onSaveInstanceState()` 方法来保存实例状态。当用户再次返回到该 Activity 时，系统会使用已保存的实例状态创建一个新的 Activity 实例，然后调用 `onCreate(Bundle savedInstanceState)` 方法将其传递回去。
    

`onCreate()` 方法的参数 `Bundle savedInstanceState` 主要==用于传递== onSaveInstanceState() 保存的状态信息，以便在重新创建Activity时实现之前Activity状态的恢复。

### onSaveInstanceState()方法的作用：

    给Bundle对象中保存相应的 instance state （指的是key-value pairs），这样当activity重新创建的时候，就可以通过获取bundle中存储的值，恢复到自己之前的状态
    在OnStop方法之后调用，通过lifeCycler的注册监听的。

### onRestoreInstanceState()方法的作用

获取bundle中存储的instance state ，通过存储的值将activity恢复到我们期望的状态
当系统将我们的activity回收，并重新恢复的时候，会在Created状态之后调用onStart()方法，然后调用onRestoreInstanceState()方法，比如屏幕旋转的时候会调用onRestoreInstanceState()。
	
但是activity进入后台再到可见的时候并不会调用onRestoreInstanceState()。
系统仅仅会在存在==需要恢复的状态信息的时候调用onRestoreInstanceState()==，因此在onRestoreInstanceState()方法中我们不需要判断bundle是否为null，这也意味着，调用了onRestoreInstanceState()那onSaveInstanceState()方法也必定调用过。但是如果是在onCreate()中恢复Bundle中的数据，那么我们就需要考虑Bundle是否为null。


### 2.2.1 app启动流程
相关扩展[[#五,系统启动流程]]
相关扩展[[#2.2.2 activity启动流程]]
#### 流程

**app启动流程** 

![[截图20230815033305.png]]

- > **. launcher调用startActivitySafely()通过 SERVICE_MANAGER 获取ams**
	```java
	default boolean startActivitySafely(
	View v, Intent intent, @Nullable ItemInfo item) {
	....
	intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);//启动一个新的activtiy栈
	....
	if (isShortcut) {
	// Shortcuts need some special checks due to legacy reasons.
		startShortcutIntentSafely(intent, optsBundle, item);
		} else if (user == null || user.equals(Process.myUserHandle())) {
		// Could be launching some bookkeeping activity
		context.startActivity(intent, optsBundle);
		} else {
		context.getSystemService(LauncherApps.class).startMainActivity(
		intent.getComponent(), user, intent.getSourceBounds(), optsBundle);
		}
	....
	} catch (NullPointerException | ActivityNotFoundException | SecurityException e) {
	.....
	}
	```
- >通知ams通知要启动一个activity ，通过pkms查询到对应启动Ap
- > ams通过binder通知**launcher进入** **pause状态**
- > launcher通过binder**通知ams准备就绪** 
- > **AMS创建一个通知zygote fork一个app进程
- > ams通知activityThread调用main方法 
	
- > acitvitythread创建返回一个==**applicationThread 类型的binder**== 用于activityThread与ams通信。
	- **applicationThread 是单例，main中new acitvitythread就跟随该类初始化了
- > 调用==acitvitythread.attch(   )与ams 进行绑定applicationThread==
	- 如果看过源码，可能会在该attach中的if else 疑惑，其实启动app的是走if内代码，也就是下方的代码，如果是意外重启的才会走else，其else内就直接创建了appcotnext
	```java
	 attach( .... ){
		RuntimeInit.setApplicationObject(mAppThread.asBinder());  
		final IActivityManager mgr = ActivityManager.getService();  
		try {  
		    mgr.attachApplication(mAppThread, startSeq);  
		} catch (RemoteException ex) {  
		    throw ex.rethrowFromSystemServer();  
		}
	 }                   

	```
-  > ams将入口 activity 信息==ActivityClientRecord==等信息通过binder传递给 app
	- > **binder调用`bindAppliction( )`->handleBindApplication（）方法根据ams发过来的包信息创建 applictioncontext，初始化资源信息
	- handleBindApplication内还初始化了contentProvider，这就是为什么contentProvider的onCreate比app的早的原因**
- >初始化 Instrumentation，==app通过performLanchActivity()方法,创建activtiycontext和activity, 启动对应入口的activity , 进入activtiy启动流程==
- [[#2.2.2 activity启动流程]]

注意：版本不同会代用不一致，比如创建application的方式或者是启动activity的方式

#### 常见问题：
### 什么是activtiyThread:
ActivityThread是应用进程的初始化类，它的main()方法就是应用的入口方法，也就是说应用进程被创建后会调用ActivityThread.main()方法, **创建一个ActivtyThread，并进MainLooper的循环。**
== 此时的prepareMainLooper把主线程的thread就设置好了==
```JAVA
static void main(String[] args){
....
	Looper.prepareMainLooper();  
	  
.....
//创建ActivityThread
	ActivityThread thread = new ActivityThread();  
	thread.attach(false, startSeq);  
	  
	if (sMainThreadHandler == null) {  
	    sMainThreadHandler = thread.getHandler();  
	}  
	  
	if (false) {  
	    Looper.myLooper().setMessageLogging(new  
	            LogPrinter(Log.DEBUG, "ActivityThread"));  
	}  
	  
	// End of event ActivityThreadMain.  
	Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);  
	Looper.loop();}
  
....
```

### 什么是ApplicationThread
他是一个binder，用于AMS通知ActivityThread
```java
private class [ApplicationThread] extends [IApplicationThread].[Stub]() {..

..}
```





### 2.2.2 activity启动流程

相关扩展[[#五,系统启动流程]]
相关扩展[[#2.2.1 app启动流程]]
相关[[#7.2 View绘制流程]]

-> strartctivtiy
-> Instrument.execStartActivities
->**通过servicemanager获取到ATMS--ActivityTaskManagerService.startActivity()**

->ams 通过 applicationThread 回调ActivityThread.startActivityNow ，并传回一个token
	- ams解析了activity信息，发送ActivityClientRecord到Activity
-》ActivtiyThread.handleLaunchActivity()
-》==ActivtiyThread.performLanchActivity()==
- 创建==activityContext==
- 调用==mInstrumentation.newActivtiy创建对象activtiy==
		- 》調用==activtiy.attach==（传入了大量参数，instrument等等
		- 》**在==Activtiy.attch(....)方法中創建phonewindow，传入windowsmanager**==
		
```java
//ActivityThread
private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
....
	ContextImpl appContext = createBaseContextForActivity(r);  
	Activity activity = null;  
	try {  
	    java.lang.ClassLoader cl = appContext.getClassLoader();  
	    
	    activity = mInstrumentation.newActivity(  
	            cl, component.getClassName(), r.intent);
	            ...}
	            
	    activity.attach(instrument... activtycontext..., window,..)
	    
	            ...
	            
	    mInstrumentation.callActivityOnCreate( )//回调OnCreate( )
....
}
```
- 》==mInstrumentation.callActivityOnCreate==
- 》==**setContentView解析  xml構建viewtree，==（mLayoutInflater.inflate（）解析xml结构， 解析的控件通过反射创建view ）**
- 调用phonewindow==创建decoreview。==


- 》  **ActivtiyThread.handlePerfromResume(), ==看下图可知调用onResume比addView早。==

```JAVA
public void handleResumeActivity(ActivityClientRecord r, boolean finalStateRequest,  
        boolean isForward, String reason) {
        ....
        if (!performResumeActivity(r, finalStateRequest, reason)) {  //回调Activity Resume
		    return;  
		}
        ....
        windowmanager.addView(decorview, l);
	    ....
        }
```
-> 进入ui绘制流程，此时==decodview才添加到**wm（windowMangerGlobal）中==
-> windowmanager是一个代理类，addview调用的其实是内部的windowmanagerGlobal单例的addview
-> **==创建`new ViewRootImpl`**，**调用`viewrootimpl addView`,  把`decoredview`放进去**,  调用viewrootview.requstLayout()==
- View 的交互，同时实在 ViewRootImpl 的 setView 时进行注册的，注册了一个 InputEventReceiver 接收事件。
	
->viewrootview.scheduleTraversals( )
->开始绘制，显示界面（看上图）

后续[[#7.2 View绘制流程]]

- （1）黑白屏原因在创建appliction + oncreate + setcontentview

- （2）子线程setTextview不一定报错，如果在onCreate中，不会报错，但是等到resume之后就会报错
- （3）生命周期回调函数都是在ActivtiyThread中performxxx之后才回调


![](QQ图片20220524183505.png)

![](4100513-11d4f617465558e7.webp)
### 2.2.3 **ActivityServiceManger/AMS**

Framework层非常重要的一个service。用于启动和管理四大组件以及他们的生命周期
存在于Sytemserver进程中.

在Android系统中，AMS（Activity Manager Service）和 ATMS（Activity Task Manager Service）都是系统服务的一部分，它们负责管理应用程序的界面和任务。都在systemserver中，它们之间不需要进程通信。

AMS是Android系统中的核心服务之一，它负责==控制应用程序的执行和生命周期==。它处理==应用程序的启动、停止、切换和回收等操作==，同时还负责==权限管理、安全检查和其他与应用程序相关的系统级功能==。

ATMS是另一个系统服务，它负责管理应用程序的任务和界面。它提供了一组API，允许应用程序在Android设备上创建、管理和切换应用程序界面。ATMS还负责==管理应用程序的任务，例如在设备上创建新的任务或切换到现有的任务==。

AMS和ATMS之间有紧密的关系。==ATMS依赖于AMS来获取关于应用程序的信息，例如应用程序的名称、标识符和权限等==。同时，AMS也依赖于==ATMS来管理应用程序的界面和任务==。在Android系统中，AMS和ATMS共同协作，确保应用程序的正确执行和任务的顺畅切换。

### PKMS/PackageManagerService
要先注意的是PMS是指PowerMangerService，不是指PKMS。

主要职责：==安装、卸载、AndroidManifest解析、扫描apk文件、权限获取==

核心源码路径
![[截图20230916191048.png]]


PKMS启动时候是比较费时的，因为在启动的时候需要解析系统根目录下的几个主要的apk路径（包括了data/data下的apk和system下的apk），==解析apk结构，package信息和四大组件==的信息，以及==权限==

###  PowerManagerService

1. 向上提供给应用程序接口，例如音频场景中保持系统唤醒、消息通知中唤醒手机屏幕场景；
2. 向下决策HAL层以及Kernel层来==控制设备待机状态，控制显示屏、背光灯、距离传感器、光线传感器==等硬件设备的状态；
PowerMananger 是PowerManangerService的代理类，PowerMananger向上层应用提供交互的接口。上层调用对应的接口后，具体的工作内容交予PowerManangerService完成。可以重点关注如下接口：

- wakeUp()  hide接口，不开放给应用。作用：强制系统从睡眠状态唤醒。
- gotoSleep() hide接口，不开放给应用。作用：强制系统进入睡眠状态
 - userActivity()   向PowerManagerService报告影响系统休眠的用户活动，重新计算灭屏时间，背光亮度，例如触屏、滑屏、power键等用户行为。
- Wakelock（特别常用）
    wakelock是PowerManager的一个内部类，提供了相关的接口来操作wakelock锁。应用层可以使用newWakeLock方法创建wakelock锁，使用acquire()和release()来申请和释放锁。例如如下demo的使用

- isDeviceIdleMode  ==查看是否进入了Doze低功耗模式==
## 2.3 APP重启

```JAVA
// android.os.Process.killProcess(android.os.Process.myPid());//清空所有资源，包括线程
//但会保留某些后进程,例如:Service,Notifications等
//如果使用system.exit()是重启java虚拟机
//如果在activity栈顶，则会直接退出，否则framework会重新创建进程

//ActivityManager manager = (ActivityManager)context.getSystemService(ACTIVITY_SERVICE); 
//获取应用程序管理器
//manager.killBackgroundProcesses(getPackageName()); //强制结束当前应用程序
//这种方式退出应用，会结束本应用程序的一切活动,因为本方法会根据应用程序的包名杀死所有进程包括
//Activity,Service,Notifications等
 Intent i =new Intent(getBaseContext(), StartActivity.class);
 i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
 startActivity(i);
```

### 

## 2.4 保持数据

**正常情况下Activity的创建和销毁不会调用onSaveInstanceState和onRestoreInstanceState方法。** 

**当横竖屏切换时，Activity会被销毁，生命周期方法onPause, onStop, onDestory等均会被调用**，此时Activity属于异常情况下终止的，所以系统会调用`onSaveInstanceState`方法对Activity的状态进行保存。该方法在`onStop`之前调用，与`onPause`没有既定的时序关系。

**当出现Crash时，除了其他的生命周期方法不会执行外，也会执行保存数据的操作。**


```
// 此方法用来保存当前异常退出的Activity的数据 ，bundle存入数据
 override fun onSaveInstanceState(outState: Bundle?, outPersistentState: PersistableBundle?) {
        super.onSaveInstanceState(outState, outPersistentState)
    }

	// 此方法用来重载当前异常退出的Activity的数据
override fun onRestoreInstanceState(savedInstanceState: Bundle?) {
        super.onRestoreInstanceState(savedInstanceState)
    }
```

## 2.5 Activtiy任务栈

## 2.6 启动另外APP的activtiy

### 第一种：通过applicationId与package+activityPath

applicationId告诉系统活动在那个App内，进入App内就需要类路径找具体的Activity。
一定要添加 android:exported="true"
1. `<!--project2中AndroidManifest.xml-->`
2. `<!--注意一定要添加 android:exported="true"-->`
3. `<activity android:name=".TargetActivity" android:exported="true"></activity>`

```java
 `//project1中MainActivity.java`
 `ComponentName component = new ComponentName("cn.rojao.robot", "cn.rojao.robot.TargetActivity");`
 `Intent intent = new Intent();`
 `intent.setComponent(component);`
 `startActivity(intent);`
```


### **第二种：通过自定义Action启动。**
```xml
 `<!--project2中AndroidManifest.xml-->`
 `<activity android:name=".TargetActivity" android:exported="true">`
     `<intent-filter>`
         `<action android:name="target" />`
         `<category android:name="android.intent.category.DEFAULT" />`
     `</intent-filter>`
 `</activity>`

 `//project1中MainActivity.java`
 `Intent intent = new Intent();`
 `intent.setAction("target");`
 `startActivity(intent);`
```



### **第三种：通过设置可以响应的指定数据类型。**
通过intent-filter设置scheme协议连接

> 添加不能通过第二种方式启动
```xml
1. `<!--project2中AndroidManifest.xml-->`
2. `<activity android:name=".TargetActivity" android:exported="true">`
3.     `<intent-filter>`
4.         `<data android:scheme="xl" android:host="goods" android:path="/goodsDetail" android:port="8888"/>`
5.         `<category android:name="android.intent.category.DEFAULT" />`
6.         `<action android:name="android.intent.action.VIEW"/>`
7.         `<category android:name="android.intent.category.BROWSABLE"/>`
8.     `</intent-filter>`
9. `</activity>`

11. `//project1中MainActivity.java`
12.  `Intent intent = new Intent();`
13. `intent.setData(Uri.parse("xl://goods:8888/goodsDetail?goodsId=10011002"));`
14. `startActivity(intent);`
15. `//网页上调用`
16. `<a href="xl://goods:8888/goodsDetail?goodsId=10011002">打开商品详情</a>`

```

# 3.Service

服务中的代码都默认运行在主线程中，如果直接在服务中执行耗时操作很容易出现ANR（Application not Responding） 所以这个时候需要用到Android多线程编程技术，我们应该在服务的每个具体的方法里启动一个子线程。

Service同时可以用于AIDL的IPC, 在Service的OnBind返回AIDL对象

## 1.服务类型

**Android10之后google推荐使用work，workmanager和JobIntentService(也已丢弃，傻逼谷歌，出没一会又是推荐的，就不用了)替代后台service任务**

### 1. 前台服务

用户所知道的，且内存不足的情况下的不被系统回收，前台服务必须给状态栏一个通知，被放到正在运行的状态栏下。==**该通知只能在服务被终止或者从前台主动移除后才能被解除。**==

以下是在自定义前台
当然==如果不需要自定义那么直接在activity中startForegroundService(intent);也可以==

```java
public class MyService extends Service{
	Intent intent = new Intent(this, MainActivity.class);
	PendingIntent pi = PendingIntent.getActivity(this, 0 , intent, 0);
	Notification notification  = new NotificationCompat.Builder(this)
		.setContentTitle(" this is content titile")
		.setContentText("this is content text")
		.setWhen(System.currentTimeMillis())
		.setSmallIcon(R.mipmap.ic_launcher);
		.setLargeIcon(BitmapFactory.decodeResource(getResource(),
			R.mipmap.ic_launcher))
		.setContentIntent(pi)
		.build();
	//启动前台
	startForeground(1,notification);
}

```



### 2. 后台服务

一般情况下，Service几乎都是在后台运行，一直默默地做着辛苦的工作。但这种情况下，**后台运行的Service系统优先级相对较低**，当系统内存不足时，在后台运行的Service就有可能被回收。 

8.0之后禁止==后台应用==启动后台service
 **什么是前台应用?系统可以区分前台和后台应用。如果满足以下任意条件，应用将被视为处于前台：
	1. 具有可见 Activity
	2. 具有前台 Service
	3. 另一个前台应用已关联到该应用（绑定 Service 或使用 content providers）

如果以上条件均不满足，应用将被视为处于后台。
	- Android 8.0 不再允许后台service直接通过startService方式去启动， 具体行为变更如下：
> 	如果针对 Android 8.0 的应用尝试在不允许其创建后台服务的情况下使用 startService() 函数，则该函数将引发一个 IllegalStateException。 新的 Context.startForegroundService() 函数将启动一个前台服务。现在，即使应用在后台运行， 系统也允许其调用 Context.startForegroundService()。不过，应用必须在创建服务后的五秒内调用该服务的 startForeground() 函数。
	 解决方法：
	 修改启动方式
```java
1. if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
     context.startForegroundService(intent);
 } else {
     context.startService(intent);
     }
```java
```

## 2.服务之间通信

可以通过handler message或者是广播

## 3.startService()与bindService()区别

(a)started service（启动服务）是由其他组件调用startService()方法启动的，这导致服务的onStartCommand()方法被调用。当服务是started状态时，其生命周期与启动它的组件无关，并且可以在后台无限期运行，即使启动服务的组件已经被销毁。**因此，服务需要在完成任务后调用stopSelf()方法停止，或者由其他组件调用stopService()方法停止。**

(b)使用bindService()方法启用服务，**调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同时生，必须同时死”的特点。**

通过bindService绑定Service相对startService方式要复杂一点。 由于bindService是异步执行的，所以需要额外构建一个ServiceConnection对象用与接收bindService的状态，同时还要指定bindService的类型

链接：https://juejin.cn/post/6844903781541347341

(3)开发人员需要在应用程序配置文件中声明全部的service，使用<service></service>标签。

(4)Service通常位于后台运行，它一般不需要与用户交互，因此Service组件没有图形用户界面。Service组件需要继承Service基类。Service组件通常用于为其他组件提供后台服务或监控其他组件的运行状态。

**(5)onStartCommand方法返回有4种** 
- START_STICKY （从Android 10（API级别29）开始，所有运行中的Service都会在==应用程序被系统杀死时停止==，而不会保持START_STICKY状态。）
- START_NOT_STICKY 
- START_REDELIVER_INTENT 
- START_STICKY_COMPATIBILITY 

**START_STICKY**：如果service进程被kill掉，保留service的状态为开始状态，但不保留递送的intent对象。随后系统会尝试重新创建service，由于服务状态为开始状态，所以创建服务后一定会调用onStartCommand(Intent,int,int)方法。如果在此期间没有任何启动命令被传递到service，那么参数Intent将为null。

**START_NOT_STICKY**：“非粘性的”。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统不会自动重启该服务。

**START_REDELIVER_INTENT**：重传Intent。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统会自动重启该服务，并将Intent的值传入。

**START_STICKY_COMPATIBILITY**：START_STICKY的兼容版本，但不保证服务被kill后一定能重启。



作者：proud2008
链接：https://www.jianshu.com/p/fd49a83bce8d



引出一下注意点：

1. 无论==多少次的 startService 又 bindService，Service 只被创建一次，Service 的 onCreate 的方法只会被调用一次。==

2. 多次调用==startService==的话，service 会多次调用  ==onStartCommand==  方法。多次调用 ==stopService 的话，service 只会调用一次 onDestroyed 方法。== 

3. 多次调用 ==bindService 的话，service 只会调用一次 onBind 方法==。一个 ==Service 可以被多个客户进行绑定，只有所有的绑定对象都执行了  unbindService （）方法后==（ 或者 Activity 被 finish 的时候绑定会自动解除 ）该 Service 才会销毁。一个客户多次调用 unbindService 的话会抛出异常。

4. 如果同时（多次）调用了 startService 和 bindService，要所有绑定都解除和调用一次 stopService () Service 才会销毁。
startService：onCreate onStartCommand onDestroy
bindsService: onCreate onBind unBind onDestroy

## 4.**IntentService** （新版本已丢弃）

但是，这种服务一旦启动之后，就会一直处于运行状态，必须调用stopService()或者stopSelf()方法才能让服务停止下来，所以，如果想要实现让一个服务在**执行完毕后自动停止**的功能，就可以这样写：

```java
public class MyService extends IntentService {

    private static final String TAG = MyService.class.getSimpleName();

    private int count = 0;

    public MyService() {
        super(TAG);
        // TODO Auto-generated constructor stub 
        //handle.sendMessage(intent);
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        // TODO Auto-generated method stub
        //在这里添加我们要执行的代码，Intent中可以保存我们所需的数据，
        //每一次通过Intent发送的命令将被顺序执行
        count ++;
        Log.w(TAG, "count::" + count);
    }
}

```



如此，==线程就会自动启动并执行逻辑，执行完毕后自动关闭==。这就是**IntentService** 的好处，能够自动开启和关闭； 

### IntentService 特征

会创建独立的 worker 线程来处理所有的 Intent 请求；
会==创建独立的 worker 线程来处理 onHandleIntent()方法实现的代码==，无需处理多线程问题；
==所有请求处理完成后，IntentService 会自动停止，无需调用 stopSelf()方法停止 Service；==
为 Service 的 onBind()提供默认实现，返回 null；
为 Service 的 onStartCommand 提供默认实现，将请求 Intent 添加到队列中； 

## 5.生命周期

![img](https://upload-images.jianshu.io/upload_images/4625401-756d89b600d55081.png?imageMogr2/auto-orient/strip|imageView2/2/w/404/format/webp)



onCreate():首次创建服务，服务已在运行则不再调用

onStartCommand（）：startService时调用

onDestory：不再使用且被销毁，会调用

onBind：

onUnbind：

onReBind：旧的组件解绑后与新组件绑定会调用

## *6*. JobService

**JobService是Android针对后台任务优化推出的新组件，和**Servie**有着细致的关系又有很多不同。在需要执行后台任务的时候，面对JobService和Service，应该如何选择？

## 使用
继承自JobService实现startJob和onStop。记得在清单中注册service和**android.permission.BIND_JOB_SERVICE** 权限。
```java
@RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
public class DemoJobService extends JobService {
    @Override
    public boolean onStartJob(JobParameters jobParameters) {
        Log.d("tag","onStartJob");
        return false;
    }

    @Override
    public boolean onStopJob(JobParameters jobParameters) {
        Log.d("tag","onStopJob");
        return false;
    }
}
```

JobService是由JobSchedulerService管理的。JobScheduler
```java

        int jobId = 1;
        JobScheduler jobScheduler = (JobScheduler)getSystemService(Context.JOB_SCHEDULER_SERVICE);
    
    //
        ComponentName jobService = new ComponentName(getPackageName(),
                DemoJobService.class.getName());
                
        JobInfo jobInfo = new JobInfo.Builder(jobId, jobService)
                .setPeriodic(15 * 60 * 1000)//重复执行周期
                .setPersisted(true)
                .setRequiredNetworkType(JobInfo.NETWORK_TYPE_ANY)
                .build();
                
        if(jobScheduler != null){
        
            jobScheduler.schedule(jobInfo);
        }
```


#### **原理上的对比**

***Service***   

App通过Context发出请求，AMS接收请求后进行调度，通知App侧进行创建，开始，停止(或绑定，解绑)和销毁Service。

***JobService***    

App通过**JobScheduler**接口发出创建JobService请求，JobSchedulerService接收到请求后解析任务执行的条件，通过AMS去依照任务的条件调度JobService的创建，绑定和解绑。不同于Service，JobService的开始，取消和停止等操作不是由App发出，由**JobSchedulerService**自行处理。

#### **执行条件的对比**

***Service***   

Service的启动并没有什么特定的条件设置，如果说非要有什么具体的执行条件的话，就是App根据业务逻辑在适当的时候调用startService()或者bindService()。

***JobService\***   

JobService的执行需要至少一个条件。没有条件的JobService是无法启动的，在创建JobInfo的时候会发生异常。

#### **启动时机上的对比**

***Service***   

App一旦通知Context去执行startService()，Service将得到运行。(使用bindService()的话，Service的运行取决于ServiceConnection的onServiceConnected()的回调)

***JobService***  

JobService必须等待执行条件满足了才能被创建和开始。

#### **执行时间上的对比**

***Service***   

onStartCommand()回调在UI线程，不宜执行耗时逻辑，否则可能造成ANR。

***JobService***   

onStartJob()的回调也执行在UI线程，亦不宜执行耗时逻辑，否则可能造成ANR或者在超过8s后Job被强制销毁。并且，JobService里即便新起了工作线程，处理的时间也不能超过10min，否则Job仍会被强制销毁。

#### **能否再启动的对比**

***Service***    

onStartCommand()返回START_STICKY可以告诉AMS在被停止后自动启动。

***JobService***   

**onStopJob()返回true，即可设置在被强制停止后可以再度启动。** 除了这些原理上的细微区别，那么你更应该关注实际应用上的不同。

#### **实际应用**

***Service***   

适合需要常驻后台，立即执行，进行数据获取，功能维持等场景，比如音乐播放，定位，邮件收发等。

***JobService***   

适合不需要常驻后台，不需要立即执行，在某种条件下触发，执行简单任务的场景。比如联系人信息变化后的快捷方式的更新，定期的更新电话程序的联系人信息，壁纸更改后去从壁纸提取颜色的后台任务。
## 7. WorkManager
WorkManager 是一个 Android Jetpack 扩展库，它可以让您轻松规划那些可延后、异步但又需要可靠运行的任务。对于绝大部分后台执行任务来说，使用 WorkManager 是目前 Android 平台上的最佳实践。

WorkManager的**定时任务最短也需要15分钟**
1. 创建自己的Worker类
新建一个TestWorker继承自Worker，里面只有一个重写的方法dowork()
```java
/**
 * 作者：Vaccae
 * 邮箱：3657447@qq.com
 * 创建时间： 10:43
 * 功能模块说明：WorkManager测试类
 */
class TestWorker(context: Context, parameters: WorkerParameters) : Worker(context,parameters) {
 
    private var TAG= "taskjob";
    private var times =0;
    override fun doWork(): Result {
        times++;
        var data=Data.Builder().putInt("times",times).build();
        if(times<10){
            Log.i(TAG, "我是Work的测试")
            return Result.success()
        }else{
            Log.i(TAG, "重新测试")
            return Result.failure()
        }
    }
}
```
从 doWork() 返回的 Result 会通知 WorkManager 服务工作是否成功，以及工作失败时是否应重试工作。
    Result.success()：工作成功完成。
    Result.failure()：工作失败。
    Result.retry()：工作失败，应根据其重试政策在其他时间尝试。


2.任务队列加入自己work
```java
//创建WorkManager任务
        val periodicwork =
            PeriodicWorkRequestBuilder< TestWorker >(5000, TimeUnit.MILLISECONDS).build()
            
        WorkManager.getInstance(this).enqueueUniquePeriodicWork(
            "test", ExistingPeriodicWorkPolicy.REPLACE,
            periodicwork
        )
```


### JobScheduler 和 WorkManager

- JobScheduler和WorkManager都**只能在APP存活的时候执行，但是定时器是一直工作的。**

- 关闭APP再启动，**JobScheduler并不能够直接继续运行，但是WorkManager可以。**

- 如果重启APP的时候，WorkManager任务的计时器应该已经执行了一次或多次，则会立即开始执行。

- 重启App之后WorkManager如果直接执行了一个任务，**则从这个时候开始算新的周期，不会按旧有周期走。**

# 4.Android 运行时权限要点

总结于：https://www.jianshu.com/p/30cb93984c04

android app默认**无任何权限**，必须在AndroidManifest中声明

例：

```jsx
<uses-permission android:name="android.permission.INTERNE"/>
```

标签<user-permission>, 位于application标签之外。

### 要点

**要点一**：如果授予权限是一般权限，则系统会授予该权限。

**要点二：**如果授予权限为危险权限，系统会明确用户授予该权限。 android发出权限请求方式取决于由系统版本(即targetSdkVersion) 

**要点三：**运行在 Android 6.0 及以上版本，App targetSdkVersion 大于23，**则需要在运行时向用户请求权限**，**并且需要在 App 使用相关的权限之前检查自身是否已被授予该权限。**

```java
 if (ContextCompat.checkSelfPermission(getApplication(), Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {    
     //拥有权限，做你想做的事情    
     doyourSelfSomething(); 
 } else{    //没有开启权限，向系统申请权限    
         ActivityCompat.requestPermissions(activity, new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, 114514); }



```

用户不同意开启权限有以下三种情况，用shouldShowRequestPermissionRationale方法判断。

```java
/*shouldShowRequestPermissionRationale方法返回值分几种情况：
1、第一次请求该权限，返回false。
2、请求过该权限并被用户拒绝，返回true。（例子是这种情况，一般都是该种情况，只要不勾选不再提醒，就会一直询问权限是否开通）
3、请求过该权限，但用户拒绝的时候勾选不再提醒，返回false。*/

@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        //通过requestCode来识别是否同一个请求
        if (requestCode == 114514){
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED){
                //用户同意开启权限，执行操作
                doyourSelfSomething();
                
            }else{//不同意

        }
```

链接：https://juejin.cn/post/6844904029156278279



**要点四：**运行在 Android 6.0 以下版本，或App targetSdkVersion 小于23（此时设备可以是Android 6.0 (API level 23)或者更高），则系统会在用户安装则系统会在用户安装App时要求用户授予权限，系统就告诉用户App需要什么权限组。如果App将新权限添加到更新的应用版本，系统会在用户更新应用时要求授予该权限。用户一旦安装应用，他们撤销权限的唯一方式是卸载应用。



## Android O运行时权限策略变更：



- **Android O 之前**：如果应用在运行时请求权限并且被授予该权限，系统会错误地将属于**同一权限组**并且在**清单中注册的**其他权限也一起授予应用。一旦用户为应用授予某个权限，则所有后续对该权限组中权限的请求都将被自动批准。



- **Android O之后**：同一权限组的某一权限被申请通过后，仅会允许该权限的获取，并不会影响同一权限组下其他权限。并且在以后再次申请该权限时不会提醒用户。
- 

例子：

- 假设某个应用在其清单中列出READ_EXTERNAL_STORAGE和WRITE_EXTERNAL_STORAGE。应用请READ_EXTERNAL_STORAGE，并且用户授予了该权限，如果该应用针对的是API级别24或更低级别，系统还会同时授予WRITE_EXTERNAL_STORAGE，因为该权限也属于STORAGE权限组并且也在清单中注册过。如果该应用针对的Android O，则系统此时仅会授予READ_EXTERNAL_STORAGE，不过在该应用以后申请WRITE_EXTERNAL_STORAGE权限时，系统会立即授予该权限，而不会提示用户。
- 我们申请了WRITE_EXTERNAL_STORAGE权限，在Android O之前，我们同时会得到READ_EXTERNAL_STORAGE权限，我们在其它地方涉及到读取存储卡的操作时只需要判断有WRITE_EXTERNAL_STORAGE权限就去读取了。此时应用如果安装在Android O的系统中我们会发现，判断了有WRITE_EXTERNAL_STORAGE权限后去读取存储卡内容时应用崩溃了，原因就是我们没有申请READ_EXTERNAL_STORAGE权限。

## 备注

注意在国产机的部分权限问题，以及国产机在低API版本下的问题



# 5.Android 事件分发机制
Framework层： 由**InpurServiceManager通过InputDispatcher分发到对应窗口**，对应窗口是在**wms中查询窗口集合**得到的。
- InputDispatcher会与我们的==应用进程建立连接，它是socket的服务端==；我们应用进程的==native层会有一个socket的客户端，客户端收到消息后，会通知我们应用进程里ViewRootImpl创建的WindowInputEventReceiver（继承自InputEventReceiver）来接收这个输入事件==。事件传递也就走通了，后面就是上层的View树事件分发了。
	-==为什么用socket？主要是在此处使用binder太过浪费性能，需要客户端和服务端两个线程并且binder自身还会占用线程，然而一般input是短时间且频繁的操作，binder需要反复操作2(N+1)个线程。==
下图为分发流程图：

图1：![](966283-b9cb65aceea9219b%201.png)

图2 视图三者一般结构：![](5bbb5665bb58f%201.png)

事件分为三层，从图1来看，从上往下为activity、viewgroup、view。事件由左上角传入，由activity的分发器dispatch做事件分发

## 5.1事件种类

被分发的事件主要是用户触摸屏幕触发的事件，被封装成MotionEvent，主要有：

| 事件                       | 触发场景     | 单次事件流中触发次数 |
| -------------------------- | ------------ | -------------------- |
| MotionEvent.ACTION_DOWN    | 屏幕按下     | 1                    |
| MontionEvent.ACTION_MOVE   | 屏幕滑动     | 0或多次              |
| MontionEvent.ACTION_UP     | 屏幕抬起     | 0或1                 |
| MontionEvent.ACTION_CANCEL | 滑动超出边界 | 0或1                 |
 |ACTION_POINTER_DOWN |双指及多指按下动作（仅在第二根手指或者大于第二根手指按下时有效） | 同ACTION_DOWN|
 |ACTION_POINTER_UP |双指及多指抬起动作（仅在第二根手指或者大于第二根的手指抬起时有效） | ACTION_UP|


## 5.2要点

### 要点一：

只有viewgroup拥有onIntecpetTouchEvent，拦截则viewgroup处理

### 要点二：

如果由onIntecpetTouchEvent不拦截，则传递到view处理

### 要点三：

一旦处理完毕，返回true，即单个事件只触发0或1次，不再传播。

例:

listview子部件的点击事件会屏蔽到listview本身item的点击事件，因此recycleriew不自带点击事件）

### 要点四：

第一个motionEvent会影响后续事件流消费的对象。在哪里消费了，后续事件流都在同一地方被消费。

例1：

按下button1，产生down事件，在button1内滑动，产生move事件，抬起产生up事件。

由viewgoroup分发到对应button1消费，后续move和up都消费在button1中了。

button listener不消费，dispatch返回false，则传到父级view（如viewgroup），后续事件流都被父级view拦截。



viewGorup的onIntecptTouchEvent：

```
// ViewGroup中该方法的核心部分伪代码
public boolean dispatchTouchEvent(MotionEvent ev) {
    if (!onInterceptTouchEvent(ev)) {
        return child.dispatchTouchEvent(ev);    //不拦截，则传给子View进行分发处理
    } else {
        return onTouchEvent(ev);    //拦截事件，交由自身对象的onTouchEvent方法处理
    }
}
```





# 6.Android屏幕适配——使用 dp 实现完美适配

### 基础概念 :

dp：最常用的长、宽、margin、padding等的单位（px = dp * density）（dp = px / density）
sp：字体的单位，和dp差不多，**区别是如果字体使用的sp为单位，那如果你手机字体调大了，那你app的字体会随之变大，如果用dp则不会变化**。
px：像素
dpi：dots per inch，即每英寸上有多少个像素,也就是屏幕密度
density：density = dpi / 160

Google规定的见下表：

|       | 一英寸有多少个点？ | 我们用来density（密度）来分辨 |
| :---: | :----------------: | :---------------------------: |
| ldpi  |       120dpi       |             0.75              |
| mdpi  |       160dpi       |               1               |
| hdpi  |       240dpi       |              1.5              |
| xhdpi |       320dpi       |               2               |



1、选用主要分辨率来适配：480X800,720X1280,1080X1920 



2、各个分辨率单独适配,res，dimens里设置各个对应的px，再统一调用，有系统筛选。 

*A.*建立多个dimens，这个建立方式有两种

![img](https://img-blog.csdn.net/20180731183934848?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwNzExMDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

![img](2018073118415054.png) 



B.建立不同drawable匹配

放置不同的尺寸资源

3、用weight和match等，少用dp在**位置**布局里  layout上的weight等等

当设备的屏幕 DPI 不是基准（mdpi，160 dpi）时，Android 系统会根据设备的实际屏幕密度尝试匹配最合适的资源目录。以下是一些常见的屏幕密度和对应的资源目录：

- ldpi（低密度，120 dpi）：res/drawable-ldpi
- mdpi（中密度，160 dpi）：res/drawable-mdpi
- hdpi（高密度，240 dpi）：res/drawable-hdpi
- xhdpi（超高密度，320 dpi）：res/drawable-xhdpi
- xxhdpi（超超高密度，480 dpi）：res/drawable-xxhdpi
- xxxhdpi（超超超高密度，640 dpi）：res/drawable-xxxhdpi

系统会尝试从离设备屏幕密度最近的资源目录中获取图像资源。例如，如果设备的屏幕密度介于 mdpi（160 dpi）和 hdpi（240 dpi）之间，系统会根据实际密度与这些密度之间的关系来确定资源目录。如果没有更接近设备密度的文件夹，系统通常会选择较高密度的资源文件夹，然后缩放图像以适应屏幕。

因此我们采用==矢量图svg替代位图==才是最好的选择，==容量小、无损缩放且缩放不怎么消耗性能==

# 7.Android视图及自定义view

Activity：一个Activity是一个应用程序组件,提供一个屏幕,用户可以用来交互为了完成某项任务,例如拨号、拍照、发送email……。
- View：作为所有图形的基类。
- ViewGroup: 对View继承扩展为视图容器类。繼承自view和ViewParent接口
- Window: 它概括了Android窗口的基本属性和基本功能。(抽象类)
- PhoneWindow:Window的子类。
- DecorView：界面的根View，PhoneWindow的内部类。
- ViewRootImpl: ViewRoot是GUI管理系统与GUI呈现系统之间的桥梁。
- **WindowManangerService**：简称WMS,它的作用是管理所有应用程序中的窗口，并用于管理用户与这些窗口发生的的各种交互。



## 7.1**Android视图层次结构简介**  

 在介绍View绘制流程之前，咱们先简单介绍一下Android视图层次结构以及**DecorView**，因为View的绘制流程的入口和DecorView有着密切的联系。

activity-》fragmentManager-》管理fragemnt

activtiy 》window -》**管理view**（实际上都是管理生命周期，如同ams管理activtiy）

 ![[%E7%AC%94%E8%AE%B0/472002-20190518164042396-2093768305.png])         ![img](https://img-blog.csdn.net/20180325115530683?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTEwMTE3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 


### 7.1.1 ViewRootImpl



```java
public final class ViewRootImpl implements ViewParent,
        View.AttachInfo.Callbacks, HardwareRenderer.HardwareDrawCallbacks {
    /***部分代码省略**/
}
```

ViewRootImpl是View中的最高层级，属于所有View的根（`但ViewRootImpl不是View，只是实现了ViewParent接口`），（实现了View和WindowManager之间的通信协议，实现的具体细节在WindowManagerGlobal这个类当中，windowsManagerGlobal创建viewroot。 ），通过AIDL

Activity中有Window对象，一个Window对象创建着一个`DecorView`，`ViewRootImpl`就是对这个View进行操作的
**包括各种刷新开始操作等等，==onResume之后才windowMangerGlobal才创建viewrootImpl==，并且添加decorview进去，这也是也是为什么onCreate创建decordview之后没有显示，onResume之后才显式的原因。**

viewImpl通过aidl binder （mWindowsSession）与wms通信，==setView之后通知wms==

**

### 7.1.2 DecorView

decorview中包含所有视图的具体逻辑。acitvity中的**setContentView()把布局文件传到decorview中**，

**decorView本身是一个framelayout**，收到setContentview的布局后，使用**inflater**（布局填充器），把布局转化为view填充在其中。属于Phonewindow

### 7.1.3 window

window是个抽象概念，其具体实现类是phonewindow**，activity和dialog都是phoneWindows。用于创建decorview和管理**



###  7.1.4 dialog和dialogFragmnet

[]: https://juejin.cn/post/6854573211854733320	"介绍和比对"



## 7.2 View绘制流程
相关[[#2.2.2 activity启动流程]]
[[#7.5 Android渲染机制--SurfaceFlinger]]



onResume中无法正常获取宽高，**此时还没viewroot.addview*, resume之后才会触发绘制流程，

-》**ActivityThread接受到resumre消息，执行handleResume**，
-> hanleResume中
- 先调用activtiy.PerformerResume，再调用 ==wm.addView创建viewRootImpl把decodview添加进去==了。
	- 注意，此時才創建一個ViewRootImpl，這時候才能通知绘制系统进行绘制
	- windowMangaerGlobal保存了 decordview和viewrootImpl
```java
//windowMangaerGlobal.java#addView( )
......
if (windowlessSession == null) {  
    root = new ViewRootImpl(view.getContext(), display);  
} else {  
    root = new ViewRootImpl(view.getContext(), display,  
            windowlessSession);  
}
view.setLayoutParams(wparams);  
  
mViews.add(view);  
mRoots.add(root);  
mParams.add(wparams);

try {  
    root.setView(view, wparams, panelParentView, userId);
    ......
```
- 继续调用 wm.updateViewLayout( )

-> 调用到viewrootImpl.`scheduleTraversals` 方法，`scheduleTraversals` 方法内部通过一个Choreographer 对象的 postCallback 执行一个 Runnable ，在这个 Runnable 中，调用 `doTraversal` 方法
```java
	void scheduleTraversals() {  
	    if (!mTraversalScheduled) {  
	        mTraversalScheduled = true;  
	        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();  
	        mChoreographer.postCallback(  
	                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);  
	        notifyRendererOfFramePending();  
	        pokeDrawLockIfNeeded();  
	    }  
	}
```

-》==viewrootImpl. **scheduleTravsals**（绘画的起点）==-> ==调用消息屏障== ->`mChoreographer`注册监听回调 [[#Choreographer]]
-》接收到vsync 回调（viewrootImpl）**doTraversal 移除消息屏障
//为什么需要消息屏障？请看消息分发机制
```java
		void doTraversal() {  
	    if (mTraversalScheduled) {  
	        mTraversalScheduled = false;  
	        mHandler.getLooper().getQueue().removeSyncBarrier(mTraversalBarrier);
	        performTraversals();
	        .....
	        }
```
-》viewrootImpl.perfromTraversal（）->内部还执行 relayoutWindow（）（window通过IWindowsSession通知wms创建surface）
-》==performMeasure，performDraw，performlAYOUT==，调用==decordview的各个绘制过程*==*
==并且該過程中執行了view.post的各種任務（post是在measure之后）和 TreeObserver 回調（三大perform之前）、dispatchAttachWidnow( 三大perform之前）==
![](繪製流程.webp)

![[截图20231017004246.png]]


**View的绘制基本由 measure()、layout()、draw() 这个三个函数完成**

| 函数      | 作用                             | 相关方法                                       |
| --------- | -------------------------------- | ---------------------------------------------- |
| measure() | **测量View的宽高**               | measure(), setMeasuredDimension(), onMeasure() |
| layout()  | **计算当前View以及子View的位置** | layout(),onLayout(), setFrame()                |
| draw()    | **视图的绘制工作**               | draw(),onDraw()                                |

通过如下方法可以获取View到其父控件的距离。

- getTop()；获取View到其父布局顶边的距离。
- getLeft()；获取View到其父布局左边的距离。
- getBottom()；获取View到其父布局顶边的距离。
- getRight()；获取View到其父布局左边的距离。

```控件大小 = （h = getBottom - getTop）（w = getRight - getLeft）```



系统内部会依次调用DecorView 开始

**measure() : 测量视图大小，从顶层父view到子view递归，测量view的大小，生成MeasureSpec对象，又回调OnMeasure。**



**layout()： 确定view的位置，进行页面布局。父view根据上一步measure子view所得的布局大小和布局参数将子view放在合适的位置上 **

**draw：**绘制视图。**viewroot创建一个canvas对象，绘制背景，保存图层，绘制view和绘制子view，绘制滚动条等**，然后回调onDraw
```java
//viewrootImpl.java
private boolean drawSoftware(Surface surface, AttachInfo attachInfo, int xoff, int yoff,  
        boolean scalingRequired, Rect dirty, Rect surfaceInsets) {  
  
    // Draw with software renderer.  
    final Canvas canvas;  
  
    // We already have the offset of surfaceInsets in xoff, yoff and dirty region,  
    // therefore we need to add it back when moving the dirty region.    int dirtyXOffset = xoff;  
    int dirtyYOffset = yoff;  
    if (surfaceInsets != null) {  
        dirtyXOffset += surfaceInsets.left;  
        dirtyYOffset += surfaceInsets.top;  
    }  
  
    try {  
        dirty.offset(-dirtyXOffset, -dirtyYOffset);  
        final int left = dirty.left;  
        final int top = dirty.top;  
        final int right = dirty.right;  
        final int bottom = dirty.bottom;  
        //看到没有
        canvas = mSurface.lockCanvas(dirty);
        .....
         //看到没
        mView.draw(canvas);
        .....
```


#### **invalidate及其流程**

viewTree的子view调用invalidate（），其实是调用viewGroup的invalidateChildInParent（该过程支持硬件加速。此过程==mPrivateFlags标记了view刷新类型（是否需要重绘）和 标记对应受影响的矩形区域dirty）==，不断自下而上回调到==Decoview==，再到==rootviewimpl，,调用perfromTravsals，==开始绘制流程

- view. invalidate()->invalidateInternal( 传入本身大小)-> 调用skipInvalidate()判断一下是否刷新，不符合条件直接return ,不更新。这里可以看出不可见并且没有播放动画时候才能变化，
	```java
		private boolean skipInvalidate() {  
	    return (mViewFlags & VISIBILITY_MASK) != VISIBLE && mCurrentAnimation == null &&  
            (!(mParent instanceof ViewGroup) ||  
                    !((ViewGroup) mParent).isViewTransitioning(this));  
	}
	```
- **往下继续看 `invalidateInternal()` 的代码，可以翻到：这里调用了`ViewParent`的`invalidateChild()`： 并且把刷新区域Rect传入

	```java
		final ViewParent p = mParent;  
		if (p != null && ai != null && l < r && t < b) {  
		    final Rect damage = ai.mTmpInvalRect;  
		    damage.set(l, t, r, b);  
		    p.invalidateChild(this, damage);  
		}
	```
- viewparent(也就是viewgroup， viewgroup extends viewparent) 继续运行invalidateChild(child, final Rect dirty )，重点来了，此处会不断do{ ...}while（）不断获取parent的parent，直到viewRootImpl
	```java
    @Override
    public final void invalidateChild(View child, final Rect dirty) {
        final AttachInfo attachInfo = mAttachInfo;
        if (attachInfo != null && attachInfo.mHardwareAccelerated) {
            // HW accelerated fast path
            onDescendantInvalidated(child, child);
            return;
        }

        ViewParent parent = this;
        if (attachInfo != null) {
            ...
            do {
                ...
                parent = parent.invalidateChildInParent(location, dirty);
                ...
            } while (parent != null);
        }
    }
	```

- 最终会调用到ViewRootImpl.invalidateChildInParent -> 
invalidateRectOnScreen(Rect dirty) {

-  ViewRootImpl.scheduleTraversals( )   开始重绘 ->进入view的绘制流程
	- 
 ```java
	void scheduleTraversals() {  
	    if (!mTraversalScheduled) {  
	        mTraversalScheduled = true;  
	        //发送一个消息屏障，绘制优先
	        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();  
	        mChoreographer.postCallback(  
	                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);  
	        notifyRendererOfFramePending();  
	        pokeDrawLockIfNeeded();  
	    }  
	}
 ```

Webvie建议使用硬件加速，不然又加载问题，有了也可能会出现其他问题

**仅用于重绘**
一般来说我们setLayoutParams，addview等操作都会触发重绘。
然而，以下情况下不会触发重绘，需要手动调用invalidate：

1. 通过绘图类直接修改不会触发重绘：例如，使用Canvas或者Paint等绘图类直接在View上进行绘制操作，不会触发该View的重绘。这是因为这些绘图类并不影响View的布局或者大小。
2. 不影响布局大小的变化不会触发重绘：例如，改变一个View的margin值或者padding值等属性，不会触发该View及其子View的重绘。这是因为这些属性的变化只影响了View的位置，并没有改变其布局或者大小。


postInvalidate是在非UI线程中调用，invalidate则是在UI线程中调用。
postInvalidate是直接调用`viewrootImpl `的 `mHandler.sendMessageDelay()`，viewrootImpl才调用目标`view的invalidate`
```java
Message msg = mHandler.obtainMessage(MSG_INVALIDATE, view);  
mHandler.sendMessageDelayed(msg, delayMilliseconds);
```

思考：postInvalidate会导致什么问题？提示：消息屏障

#### ***requestlayout：***
```java
//viewrootImpl的requestlayout，也就是view最终调用的最顶级绘制管理类
public void requestLayout() {  
//！！！是否已经在处理requestalyout
    if (!mHandlingLayoutInLayoutRequest) {  
        //检查线程！！！
        checkThread();  
        mLayoutRequested = true;  
        scheduleTraversals();  
    }  
}
```
会调用父view requestlayout

requestLayout ==会触发Measure、Layout过程，如果尺寸发生改变，则会调用invalidate==，基本是会重绘制的，但是不一定。

requestLayout会直接递归调用父窗口的requestLayout，
直到ViewRootImpl,
然后触发peformTraversals，
由于mLayoutRequested为true，**会导致onMeasure和onLayout被调用。不一定会触发OnDraw**。requestLayout触发onDraw可能是因为在在layout过程中发现l,t,r,b和以前不一样，那就会触发一次invalidate，所以触发了onDraw，也可能是因为别的原因导致mDirty非空（比如在跑动画）

当一个View的布局或者大小发生变化时，也会触发重新布局（reLayout）。重新布局会导致该View以及其子View重新测量和绘制。以下是一些触发重新布局的情况：

1. 手动调用requestLayout方法。
2. View的LayoutParams发生改变。
3. View的父视图的尺寸发生改变。
4. View的可见性发生变化（由INVISIBLE、VISIBLE转换为GONE或者反向变化）。
5. 添加或删除子View。
6. View被移出当前父视图，然后又重新加入。

链接：https://www.jianshu.com/p/5ec0f278e0a3

![](pic/16925694-c76eba5f99c1dfae.webp)

一般来说，只要刷新的时候就调用invalidate，需要重新measure就调用requestLayout，后面再跟个invalidate（为了保证重绘），这是我个人的理解。
### Measure
`measure`函数接下来的这一段主要是为了判断是否需要进行重新测量，毕竟每次测量也不容易。

```java
    //用于存储上次测量的结果
        long key = (long) widthMeasureSpec << 32 | (long) heightMeasureSpec & 0xffffffffL;
        if (mMeasureCache == null) mMeasureCache = new LongSparseLongArray(2);
    
        //view是否需要强行刷新，调用froceLayout
        final boolean forceLayout = (mPrivateFlags & PFLAG_FORCE_LAYOUT) == PFLAG_FORCE_LAYOUT;
    
        //判断此次的widthMeasureSpec与heightMeasureSpec是否与上次相等
        final boolean specChanged = widthMeasureSpec != mOldWidthMeasureSpec
                || heightMeasureSpec != mOldHeightMeasureSpec;
        
        //判断此次测量模式是否是精确模式，不是精确的可能需要重新测量
        final boolean isSpecExactly = MeasureSpec.getMode(widthMeasureSpec) == MeasureSpec.EXACTLY
                && MeasureSpec.getMode(heightMeasureSpec) == MeasureSpec.EXACTLY;
    
        //判断此次测量大小是否与已保存的大小一致，不是一致可能需要重新测量         
        final boolean matchesSpecSize = getMeasuredWidth() == MeasureSpec.getSize(widthMeasureSpec)
                && getMeasuredHeight() == MeasureSpec.getSize(heightMeasureSpec);
                
         //如果specChanged为false,即宽高measureSpec与上次都相等，不需要重新测量；true则进一步检查其他条件
         //sAlwaysRemeasureExactly主要用于判断LinearLayout在旧版本的不同测量模式都会返回不同的测量结果，小于Android 6.0为true,大于为false；所以但小于Android 6.0需要重新测量
         //如果isSpecExactly测量模式是非精确模式需要重新测量
          //如果matchesSpecSize与已保存大小不一致需要重新测量
        final boolean needsLayout = specChanged
                && (sAlwaysRemeasureExactly || !isSpecExactly || !matchesSpecSize);
​
```

`needsLayout`就是根据上面相关变量的值共同判断是否需要重新测量的最终结果。也可以通过下图一览上面的注释。
调用`forceLayout`或`requestLayout`函数，`mPrivalteFlags`就会添加`PFLAG_FORCE_LAYOUT`标记,那么`forceLayout`就是true,无论后面其他判断条件怎么样，一定会调用`onMeasure`函数进行测量。而`needsLayout`就在上文刚分析了。

ViewGroup测量自身或者测量子View只能重写`onMeasure`函数。但在ViewGroup类仔细寻找，却没有发现重写`onMeasure`函数的痕迹。具体原因是因为具体的ViewGroup，如LinearLayout和RelativeLayout它们各自的测量方式是不一样的，`onMeasure`需要它们具体去实现。


### 7.2.1 MeasureSpec

**measure（）时使用。**

**view内部类，封装了view尺寸。**

值保存在int值中，mode +size
int是32位的，其中高2位表示模式（Mode），低30位表示大小（Size）。

对于View来说，**`MeasureSpec`的mode**和Size有如下意义

| 模式        | 意义                                                         | 对应 |
| ----------- | ------------------------------------------------------------ | ---- |
| EXACTLY     | 精准模式，View需要一个精确值，这个值即为MeasureSpec当中的Size |      |
| AT_MOST     | 最大模式，View的尺寸有一个最大值，View不可以超过MeasureSpec当中的Size值 |      |
| UNSPECIFIED | 无限制，View对尺寸没有任何限制，View设置为多大就应当为多大。 这种情况一般用于系统内部，表示一种测量状态。 |      |

**子View的MeasureSpec是由自身的LayoutParams和父布局的MeasureSpec共同确定的**。

1. `MeasureSpec.EXACTLY`：准确模式，要求视图的大小必须符合给定的值。在xml布局中，对应与"match_parent"或者是一个明确的尺寸（比如20dp，300px等）。
    
2. `MeasureSpec.AT_MOST`：最大值模式，视图大小可以任意，但不得超过设定的值。在xml布局中，对应于"wrap_content"。
    
3. `MeasureSpec.UNSPECIFIED`：不指定大小模式，通常用在系统内部，在MeasureSpec中，允许视图大小为任何值。在xml布局中不存在直接对应，一般在ScrollView或者ListView这类控件内部使用。

###  7.2.2 Draw()



在当前控件canvas上绘制 背景，view， 子view

```java
    public void draw(Canvas canvas) {

       
        int saveCount;
        // 1. 如果需要，绘制背景
        if (!dirtyOpaque) {
            drawBackground(canvas);
        }

        // 2. 有过有必要，保存当前canvas。
        final int viewFlags = mViewFlags;
      
        if (!verticalEdges && !horizontalEdges) {
            // 3. 绘制View的内容。
            if (!dirtyOpaque) onDraw(canvas);

            // 4. 绘制子View。
            dispatchDraw(canvas);

            drawAutofilledHighlight(canvas);

            // Overlay is part of the content and draws beneath Foreground
            if (mOverlay != null && !mOverlay.isEmpty()) {
                mOverlay.getOverlayView().dispatchDraw(canvas);
            }

            // 6. 绘制装饰，如滚动条等等。
            onDrawForeground(canvas);

            // we're done...
            return;
        }
    }
    
    /**
    *  1.绘制View背景
    */
    private void drawBackground(Canvas canvas) {
        //获取背景
        final Drawable background = mBackground;
        if (background == null) {
            return;
        }

        setBackgroundBounds();

        //获取便宜值scrollX和scrollY，如果scrollX和scrollY都不等于0，则会在平移后的canvas上面绘制背景。
        final int scrollX = mScrollX;
        final int scrollY = mScrollY;
        if ((scrollX | scrollY) == 0) {
            background.draw(canvas);
        } else {
            canvas.translate(scrollX, scrollY);
            background.draw(canvas);
            canvas.translate(-scrollX, -scrollY);
        }
    }
    
    /**
    * 3.绘制View的内容,该方法是一个空的实现，在各个业务当中自行处理。
    */
    protected void onDraw(Canvas canvas) {
    }
    
    /**
    * 4. 绘制子View。该方法在View当中是一个空的实现，在各个业务当中自行处理。
    *  在ViewGroup当中对dispatchDraw方法做了实现，主要是遍历子View，并调用子类的draw方法，一般我们不需要自己重写该方法。
    */
    protected void dispatchDraw(Canvas canvas) {

    }
        
``` 

通过Surface类与SurfaceFlinger进程通信通知渲染，通过Gereograher同步绘制信息。
相关扩展[[#7.5 Android渲染机制--SurfaceFlinger]]

### 7.2.4 常见问题

#### 1）onResume中无法正常获取宽高
但postdelay之后可以（参考handleResume和addview创建过程和时机，或者在onWindowsFocusChange中（**得到焦点**或者**失去焦点**时会调用，绘制完毕时候会调用）

#### 2）onCreate和onResume中可以在子线程操作view（没有触发activtiy刷新，其余原理同上

在分析ViewRootImpl里requestLayout/invalidate过程中，发现其内部调用了checkThread()方法：
原因在于两点：
- ViewRootImpl此时还没创建，还没进行渲染，resume后才会requestLayout/invalidate，不会出发创建

- 其实判断的是创建viewRootImpl了ViewTree的线程才能操作里边的View，如果在子线程创建Dialog，那么该子线程也能刷新dialog的view。 mThread 是 创建viewRootImpl时传入的。
```java
	#ViewRootImpl.java
    void checkThread() {
        //当前调用线程与mThread不是同一线程则会抛出异常
        if (mThread != Thread.currentThread()) {
            //简单翻译来说：只有创建了ViewTree的线程才能操作里边的View
            throw new CalledFromWrongThreadException(
                    "Only the original thread that created a view hierarchy can touch its views.");
           }
```
#### 3）”黑科技“——View.post（）
我们看下源码，post后并没有马上执行，而是添加到一个任务队列 HandlerActionQueue 中，

知是 `dispatchAttachedToWindow` 的时候才会调用执行队列内的任务，就是
`performTraversals`之后才会调用dispatchAttachedToWindow。
**是在 View 绘制流程的开始阶段，通过mAttachInfo将所有任务重新发送到消息队列的尾部，此时相关任务的执行已经在 View 绘制任务之后，即 View 绘制流程已经结束，此时便可以正确获取到 View 的宽高了**。

getRunQueue().post(action)

```java
public boolean post(Runnable action) {  
    final AttachInfo attachInfo = mAttachInfo;  
    if (attachInfo != null) {  
        return attachInfo.mHandler.post(action);  
    }  
    // Postpone the runnable until we know on which thread it needs to run.  
    // Assume that the runnable will be successfully placed after attach.    
    getRunQueue().post(action);  
    return true;}
```

```java
void dispatchAttachedToWindow(AttachInfo info, int visibility) {  
    mAttachInfo = info;  
    if (mOverlay != null) {  
        mOverlay.getOverlayView().dispatchAttachedToWindow(info, visibility);  
    }  
    mWindowAttachCount++;  
    // We will need to evaluate the drawable state at least once.  
    mPrivateFlags |= PFLAG_DRAWABLE_STATE_DIRTY;  
    if (mFloatingTreeObserver != null) {  
        info.mTreeObserver.merge(mFloatingTreeObserver);  
        mFloatingTreeObserver = null;  
    }  
  
    registerPendingFrameMetricsObservers();  
  
    if ((mPrivateFlags&PFLAG_SCROLL_CONTAINER) != 0) {  
        mAttachInfo.mScrollContainers.add(this);  
        mPrivateFlags |= PFLAG_SCROLL_CONTAINER_ADDED;  
    }  
    // Transfer all pending runnables.  
    if (mRunQueue != null) {  
        mRunQueue.executeActions(info.mHandler);  
        mRunQueue = null;  
    }  
    performCollectViewAttributes(mAttachInfo, visibility);  
    onAttachedToWindow();

```
### 多次mesure（）原因

View的这个方法是被它的父控件调用的，也就是说widthMeasureSpec和heightMeasureSpec是通过父控件传递进来的，这两个参数确实在很大程度上是决定了一个View的尺寸的 。

**但最终了取决于view和子view测量模式。** 子view draw之后尺寸发生变化，重新测量和布局

下图是View的生命周期.

![http://img1.sycdn.imooc.com/5850b96600017b2d06920611.jpg](http://img1.sycdn.imooc.com/5850b96600017b2d05000442.jpg)



**父视图可能在它的子视图上调用一次以上的measure(int,int****)方法***。***例如，父视图可以使用unspecified dimensions来将它的每个子视图都测量一次来算出它们到底需要多大尺寸，如果所有这些子视图没被限制的尺寸的和太大或太小，那么它会用精确数值再次调用measure()（也就是说，如果子视图不满意它们获得的区域大小，那么父视图将会干涉并设置第二次测量规则）**。 

一般来说，需要重新测量布局，就调用requestLayout，然后在调用invalidate保证onDraw一定被调用。也就是说requestLayout不一定保证onDraw被调用，但会调用onMeasure和onLayout。

### 7.2.5 扩展内容
#### 优化view
**从内优化**

1. 减少 View 层级。这样会加快 View 的循环遍历过程。
2. 去除不必要的背景。由于 在 draw 的步骤中，会单独绘制背景。因此去除不必要的背景会加快 View 的绘制。
3. 尽可能少的使用 margin、padding。在测量和布局的过程中，对有 margin 和 padding 的 View 会进行单独的处理步骤，这样会花费时间。我们可以在父 View 中设置 margin 和 padding，从而避免在子 View 中每个单独设置和配置。
4. 去除不必要的 scrollbar。这样能减少 draw 的流程。
5. 慎用渐变。能减少 draw 的流程。

**从外优化**

1. 布局嵌套过于复杂。这会直接 View 的层级变多。
2. View 的过渡绘制。
3. View 的频繁重新渲染。
4. UI 线程中进行耗时操作。在 Android 4.0 之后，不允许在 UI 线程做网络操作。
5. 冗余资源及错误逻辑导致加载和执行缓慢。简单的说，就是代码写的烂。
6. 频繁触发 GC，导致渲染受阻。当系统在短时间内有大量对象销毁，会造成内存抖动，频繁触发 GC 线程，而 GC 线程的优先级高于 UI 线程，因而会造成渲染受阻。

外部因素最为致命！日常开发中更多的应该关心布局的嵌套层级和冗余资源。

比如，当需要将一个 TextView 和一张图片放在一起展示时，我们可以考虑使用 TextView 的 `drawableLeft`(drawableRight、drawableTop、drawableBottom) 属性来设置图片，而不是使用一个 LinearLayout 来将 TextView 和 ImageView 封装在一起，这样就能减少 View 的绘制层级。

又比如，子元素和父元素都是相同的背景时，就不必在每个子元素中都添加背景属性，等等。


扩展[[#56. 内存优化]] 和 [[#55. 界面卡顿优化]]
##  7.3自定义view

**创建新的控件，修改属性，增加监听器，增加内容，解决触摸冲突等。**



https://www.jianshu.com/p/705a6cb6bfee

### 7.3.1自定义view常见类型

|                       |                                                              |
| --------------------- | ------------------------------------------------------------ |
| 组合控件              | layout xml中组合                                             |
| 继承系统view控件      | 在原来的基础上修改。                                         |
| 继承view              | 继承view创建新的view（**需要重写onDraw，onMeasure**）        |
| 继承viewgroup（布局） | 继承linearlayout等控件，修改布局成为控件（**可以读取设置好的布局成为控件**） |
| 直接继承viewgroup     | 需要 对自身的大小和位置进行测量之外，还需要对子View的测量参数负责。 |

```java
//继承view控件
public class LineTextView extends TextView {

    //定义画笔，用来绘制中心曲线
    private Paint mPaint;
    
    /**
     * 创建构造方法
     * @param context
     */
    public LineTextView(Context context) {
        super(context);
        init();
    }

    public LineTextView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public LineTextView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    private void init() {
        mPaint = new Paint();
        mPaint.setColor(Color.BLACK);
    }

    //重写draw方法，绘制我们需要的中间线以及背景
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        int width = getWidth();
        int height = getHeight();
        mPaint.setColor(Color.BLUE);
        //绘制方形背景
        RectF rectF = new RectF(0,0,width,height);
        canvas.drawRect(rectF,mPaint);
        mPaint.setColor(Color.BLACK);
        //绘制中心曲线，起点坐标（0,height/2），终点坐标（width,height/2）
        canvas.drawLine(0,height/2,width,height/2,mPaint);
    }
}
```



### 7.3.2 触摸坐标

除了Android坐标系，还存在View坐标系，View坐标系内部关系如图所示。

 

![img](10294405-4ca426e6a92db696.webp)

getY，getX是view内部的xy。

rawY，rawX才是在屏幕中的xy。



### 7.3.4自定义属性

1，编写attrs.xml，（属性名，属性数值类型）

2，布局中使用

3，自定义view中使用TypeArray获取和设置对应属性

```java
public class MyTextView extends View {
    private static final String TAG = MyTextView.class.getSimpleName();

    //在View的构造方法中通过TypedArray获取
    public MyTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        
        TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.test);
        String text = ta.getString(R.styleable.test_testAttr);
        int textAttr = ta.getInteger(R.styleable.test_text, -1);
        
        Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);
        ta.recycle();
    }
}
```

## 7.4 WMS/WindowsMangerService (内容等待完善)
职责：
![[3884533-5c1e2b3c161851b2.webp]]

位于Framework层的窗口管理服务，属于Sysrtem_server进程，binder存在于serviceManger进程。

activtiy onResume之后才绑定

![](R01OZX29H8WT365.png)
![[7bac31f1a02d29163350542fccfd0a78.png]]
通常情况下，一个 `Activity` 的 `UI` 渲染本质是 系统提供一块内存，并创建一个图形缓冲区进行维护**；这块内存就是 `Surface`，最终页面所有 `View` 的 `UI` 状态数据，都会被填充到同一个 `Surface` 中


###  7.4.1 window分类

Window 有三种类型，分别是==应用 Window==、==子 Window==和==系统 Window==。
==应用类 Window 对应一个 Acitivity==，==子 Window 不能单独存在，需要依附在特定的父 Window 中，比如常见的一些 Dialog 就是一个子 Window==。

需要注意的是， ==popupwindow和toast都不是window，而是view==。
系统 Window是需要声明权限才能创建的 Window，比如 Toast 和系统状态栏都是系统 Window。
![[14919101-a34496946644357e.webp]]

==window主要用于管理decoview，与WindowManagerService通过AIDL IBinder --- windowSession==

#### Dialog
首先看看Dialog的构造方法
```java

    Dialog(@NonNull Context context, @StyleRes int themeResId, boolean createContextThemeWrapper) {
      	
      	//省略无关代码
      	
        mWindowManager = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);

        final Window w = new PhoneWindow(mContext);
        mWindow = w;	//Dialog中的Window其实是一个PhoneWindow
       
       //一些Window的设置
    }

```


从它的构造方法可以看出，Dialog中的Window其实是一个PhoneWindow

#### 特殊说明
如果你通过wm.addView添加的view，view会在decordview之上，但是你再启动一个dialog则会覆盖这个view。
`Dialog`可以被认为是一个子窗口（`Window`）。它是一个在已有`Activity`窗口之上，在用户界面层级中具有较高优先级的窗口。当一个`Dialog`实例被创建时，它会创建自己的窗口，与现有的`Activity`窗口分离，但同时与之关联。


#### WindowToken：

类声明：
```ruby
class WindowToken extends WindowContainer<WindowState>
```
**表明WindowToken也是子容器，其子容器是WindowState，所以WindowState也是一个容器。**

WindowToken在窗口体系中有**两个作用**：

- 1.**应用组件标识**：将属于同一个应用组件的窗口组织在一起，这里的应用组件可以是：Activity、InputMethod、Wallpaper以及Dream。 WMS在对窗口的管理过程中：用WindowToken来指代一个应用组件。例如在进行窗口的Z-Order排序时，属于同一个WindowToken的窗口会被安排在一起。
- 2.**令牌作用**：WindowToken由应用组件或其管理者负责向WMS声明并持有，应用组件在需要更新窗口时，需要向WMS提供令牌表明自己的身份， 并且窗口的类型必须与所持有的WindowToken的类型一致。

#### WindowState：

类声明：

```ruby
class WindowState extends WindowContainer<WindowState>
```

**表明WindowState也是一个WindowContainer容器，但是其子容器也是WindowState**，一般窗口有子窗口SUB_WINDOW的情况下，WindowState才有子容器节点。

==**WindowState在WMS中表示一个Window窗口状态属性，其内部保存了一个Window所有的属性信息。**==

  
![[3884533-3110238af14e7e3b.webp]]
  

### 7.4.2 WindowManger

WindowManager是Android中一个重要的Service,是全局且唯一的。WindowManager继承自ViewManager。  WindowManager主要用来管理窗口的一些状态、属性、view增加、删除、更新、窗口顺序、消息收集和处理等。Android中真正展示给用户的是window和view，activity所起的作用主要是处理一些逻辑问题，比如生命周期管理及建立窗口。
==通过IWindowSession （AIDL Binder）与WMS跨进程通信，
这里蛮特殊的，不是直接通过serviceManager.getService("WindowsManagerService")显式获取binder，而是隐式创建binder（wms作为service， wmGlobal作为客户端）==

wm = 接口viewManager，接口windowManager，windowsMangerImpl实现类，，《—
windowsMangerGlobal是wmImpl内部的单例，viewrootImpl操作view
![[20201214164951395.png]]

## 7.5 Android渲染机制--SurfaceFlinger
渲染流程
![[截图20231016222318.png]]

![[截图20231017004534.png]]
### SurfaceFlinger

Android 图形架构使用了生产者——消费者模型。Surface 表示缓冲队列中的生产方，图像流最常见的消耗方是 SurfaceFlinger，该系统服务接收来自于多个源的数据缓冲区，组合它们，并将它们发送给显示设备。

Android 应用程序为了能够将自己的 UI 绘制在系统的帧缓冲区上，它们就必须要与 SurfaceFlinger 服务进行通信。==**SurfaceFlinger 服务运行在 Android 系统的 System 进程中，它负责管理 Android 系统的帧缓冲区（Frame Buffer 一般是GPU显存上）。**== 缓存的具体体现是surfac队列
SurfaceFlinger 的职责

SurfaceFlinger 主要有以下几个职责：

    分配图形缓冲区
    合成图形缓冲区
    管理 VSYNC 事件

![[f3c207c30635fd3be72b2f2d76eba1ef.png]]


### VSync事件/信号
作为严重影响Android口碑问题之一的UI流畅性差的问题，首先在Android 4.1版本中得到了有效处理。
其解决方法就是本文要介绍的Project Butter。Project Butter对Android Display系统进行了重构，引入了三个核心元素，即`VSYNC`、`Triple Buffer`和`Choreographer`。其中，VSYNC是理解Project Buffer的核心。
VSYNC是Vertical Synchronization（垂直同步）的缩写，是一种在PC上已经很早就广泛使用的技术

手机的屏幕都是60Hz的刷新率，系统为了配合屏幕的刷新频率，每过16.6ms就会发出Vsync信号来通知应用进行绘制。如果每个Vsync周期应用都能完成渲染逻辑，那么应用的FPS就是60，给用户的感觉就是非常流畅。

附：Android Display完整架构图
![[0331a4df5fce4bf58a9a93af0c1650f1.png]]


## Choreographer

我们可以看下这个类的注释：**Coordinates the timing of animations, input and drawing**。  
我翻译为**控制输入、动画。绘制的时机。**  
可以看出这个类是告诉我们**何时**去执行**输入、动画和绘制**的。  

Choreographer 的引入，主要是配合 Vsync，给上层 App 的渲染提供一个稳定的 Message 处理的时机，也就是 Vsync 到来的时候 ，系统通过对 Vsync 信号周期的调整，来控制每一帧绘制操作的时机. 目前大部分手机都是 60Hz 的刷新率，也就是 16.6ms 刷新一次，系统为了配合屏幕的刷新频率，将 Vsync 的周期也设置为 16.6 ms，每个 16.6 ms，Vsync 信号唤醒 Choreographer 来做 App 的绘制操作 ，这就是引入 Choreographer 的主要作用

还有一个注释也很重要：  
**The choreographer receives timing pulses (such as vertical synchronization) from the display subsystem then schedules work to occur as part of rendering the next display frame.**  
下面是个人的翻译：  
**Choreographer从显示的子系统中接受到类似垂直同步的时间脉冲，然后安排在下一帧中要呈现的工作。**  
这个注释告诉我们Choreographer要干两件事:

- 接收垂直同步的时间脉冲，
- 安排在下一帧中要做的工作。



## 7.6 沉浸式

安卓的可以分为三个阶段

- Android4.4(API 19) - Android 5.0(API  21)：这个阶段可以实现沉浸式，但是表现得还不是很好，实现方式为:   通过FLAG_TRANSLUCENT_STATUS设置状态栏为透明并且为全屏模式，然后通过添加一个与StatusBar  一样大小的View，将View 的  background 设置为我们想要的颜色，从而来实现沉浸式;

- 获取decoview

- Android 5.0(API 21)以上版本：在Android 5.0的时候，加入了一个重要的属性和方法  android:statusBarColor  (对应方法为  setStatusBarColor)，通过这个方法我们就可以轻松实现沉浸式。也就是说，从Android5.0开始，系统才真正的支持沉浸式;

- ```
  getWindow().addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS); 
  //注意要清除 FLAG_TRANSLUCENT_STATUS flag 跟5.0不兼容
  getWindow().clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS); 
  getWindow().setStatusBarColor(getResources().getColor(android.R.color.holo_red_light)); 
  ```

- Android 6.0(API 23)以上版本：其实Android6.0以上的实现方式和Android 5.0   +是一样，为什么要将它归为一个单独重要的阶段呢?是因为从Android 6.0(API   23)开始，我们可以改状态栏的绘制模式，可以显示白色或浅黑色的内容和图标(除了魅族手机，魅族自家有做源码更改，6.0以下就能实现);


## 换肤框架
紧密关联 [[#52.插件化]]
### 换肤方式一：切换使用主题Theme

使用相同的资源id，但在不同的Theme下边自定义不同的资源。我们通过主动切换到不同的Theme从而切换界面元素创建时使用的资源。这种方案的代码量不多发，而且有个很明显的缺点不支持已经创建界面的换肤，必须重新加载界面元素。

### 加载资源包
核心思想是在setContentView的反射生成view的时候进行。

**AsserManager加载资源默认传入的资源路径是key.mResDir(app下面的res)，当我们把这个资源路径改成皮肤包资源路径，那不就加载我们皮肤包的资源了**(通过Hook实现)

这里放上AsserManager创建流程(有需要了解可根据下方的方法来看)

> ```java
> performLaunchActivity @ActivityThread.java
> --> ContextImpl appContext = createBaseContextForActivity(r);
> --> ContextImpl.createActivityContext
> --> context.setResources
> --> createResources
> --> ResourcesImpl resourcesImpl = findOrCreateResourcesImplForKeyLocked(key);
> --> impl = createResourcesImpl(key);
> --> final AssetManager assets = createAssetManager(key);
> --> builder.addApkAssets(loadApkAssets(key.mResDir, false /*sharedLib*/,false /*overlay*/));
> ```

自自定义AssetManager 更改资源加载路径为下载的apk包


```java
public abstract class LayoutInflater{
    /***部分代码省略****/
    public interface Factory{
        public View onCreateView(String name, Context context, AttributeSet attrs);
    }

    public interface Factory2extends Factory{
        public View onCreateView(View parent, String name, Context context, AttributeSet attrs);
    }
    /***部分代码省略****/
}
```

我们可以给当前的页面的Window对象在创建的时候设置Factory，那么在Window中的View进行创建的时候就会先通过自己设置的Factory进行创建。Factory使用方式和相关注意事项请移位到 遇见LayoutInflaterFactory ，关于Factory的相关知识点尽在其中。
通过自定义LayoutInflaterFactory，在解析xml的时候我们堆需要替换的view和属性筛选出来处理即可。

### 补充：AssetManger和Resources和assets目录和raw目录
- **assets目录** 的文件，被称为原生文件，在apk的编译打包流程不会生成资源ID，也就是说我们无法通过R.xxx.xxx的方式去访问，这个目录下的文件在被打包生成apk的时候不会进行压缩。
    
- **res目录** 的文件，这类文件在打包生成apk的时候，直接通过aapt（资源文件打包工具）打包res资源文件，生成R.java、resources.arsc和res文件，我们可以直接通过R.xxx.xxx的方式访问到资源文件。**（注意：raw下的文件会被原封不动的打包到apk中）**

- res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.xxx；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。

- res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹

Resource也是通过AssetManger来访问被编译过的应用程序资源（resources.arsc），访问之前是会先通过id来访问，==**AssetManger既可以通过文件名访问被编译过的也可以访问没被编译过的资源文件。**==

# 8. Fragment 与Framgment懒加载的实现

生命周期：

onAttach -> 刚添加到framgmentManger
onCreate -> 只是创建framgment
onCreatedView -> 真正解析frament中的view的开始，现在才能findView
onActivityCreated -> 
onStart -> 此时findView比较合适
onResume -> 
onPause -> 
onStop -> 
onDestroyView -> 
onDestroy -> 
onDetach

![[20210418225656166.webp]]

## **ViewPager+Fragment 模式下的老方案**

```java
setUserVisibleHint()  看过注释，大概意思是 给系统设置一个关于当前fragment的UI是否显示给用户的暗示,这个方法可能被fragment的生命周期的外部调用,所以不能确保它在生命周期的哪个阶段被调用

onHiddenChanged()  这个一般用于fragment被 FragmentManager hide或者show的时候
```

 

Fragment可见状态改变时会被调用**setUserVisibleHint()方法**，可以通过复写该方法实现Fragment的懒加载，但需要注意该方法可能在onviewCreated之前调用，需要确保界面已经初始化完成的情况下再去加载数据，避免空指针。 Fragment的懒加载 



![img](4879515-3e61af283ad24f21.webp)

## Androidx 下 Fragment 懒加载的新实现
使用==FragmentStatePagerAdapter==待状态的adapter
使用了 `BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT` 后，把framgment添加进去的时候只会执行create，确实只有**当前可见的 Fragment 调用了 onResume 方法**，也就是切换到对应framgment的时候就会调用onResume。而导致产生这种改变的原因，是因为其 `setPrimaryItem` 方法中调用了 `setMaxLifecycle` 方法

```java

public void setPrimaryItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        Fragment fragment = (Fragment)object;
        //如果当前的fragment不是当前选中并可见的Fragment,那么就会调用
        // setMaxLifecycle 设置其最大生命周期为 Lifecycle.State.STARTED
        if (fragment != mCurrentPrimaryItem) {
            if (mCurrentPrimaryItem != null) {
                mCurrentPrimaryItem.setMenuVisibility(false);
                if (mBehavior == BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT) {
                    if (mCurTransaction == null) {
                        mCurTransaction = mFragmentManager.beginTransaction();
                    }
                    mCurTransaction.setMaxLifecycle(mCurrentPrimaryItem, Lifecycle.State.STARTED);
                } else {
                    mCurrentPrimaryItem.setUserVisibleHint(false);
                }
            }
        //对于其他非可见的Fragment,则设置其最大生命周期为
        //Lifecycle.State.RESUMED
            fragment.setMenuVisibility(true);
            if (mBehavior == BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT) {
                if (mCurTransaction == null) {
                    mCurTransaction = mFragmentManager.beginTransaction();
                }
                mCurTransaction.setMaxLifecycle(fragment, Lifecycle.State.RESUMED);
            } else {
                fragment.setUserVisibleHint(true);
            }

            mCurrentPrimaryItem = fragment;
        }
    }

```



# 9.防止app/service被杀死（进程保活）
我們app的进程优先级：
- 系统的进程
- 前台进程
- 可见的进程
- 后台进程
- 空进程

#### 1.Android 8.0之前-常用的保活方案

> 1.开启一个前台Service
>  2.Android 6.0+ 忽略电池优化开关(`稍后会有代码`)
>  3.无障碍服务(只针对有用这个功能的app，如支付宝语音增强提醒用了它)
	//当启动无障碍服务时会自动启动前台服务，只要前台服务存在，无障碍就可以一直运行！如果没有加入前台服务，无障碍服务是运行在后台的，非常容易被系统杀死！！！（个人见解） 作者：填写昵称已被占用 https://www.bilibili.com/read/cv12200073/ 出处：bilibili
------


#### 2.Android 8.0之后-常用的保活方案

> 1.开启一个前台Service(可以加上,单独启用的话无法满足保活需求)
>  2.Android 6.0+ 忽略电池优化开关(`稍后会有代码`)
>  3.无障碍服务(只针对有用这个功能的app，如支付宝语音增强提醒用了它)
>  4.应用自启动权限(`最简单的方案是针对不同系统提供教程图片-让用户自己去打开`)
>  5.多任务列表窗口加锁(`提供GIF教程图片-让用户自己去打开`)
>  6.多任务列表窗口隐藏App(`仅针对有这方面需求的App`)
>  7.应用后台高耗电(`仅针对Vivo手机`)



作者：Halifax
链接：https://juejin.cn/post/7003992225575075876

### 1，在service中重写onstartCommand

START_STICK，service被kill后自动重写创建. android

```java
@Override
public void onStartCommand(Intent inent, int flags, int startId){
    return START_STICK;
}
```

### 2，Service的onDestroy中重启service(startService方式)

！**android8已无法从后台启动后台服务**

```java
@Override
public void onDestroy(){
    super.onDestroy();
    Intent intent = new Intent();
    localIntent.setClass(this, MyService.class);
    this.startService(intent);
}
```

### 3，在androidmanifest中设置application节点android:persistent="true" 

**这个方法, 必须要system app,所以这个基本没用** 

太多系统级别服务影响性能

### 4， 提高Service的优先级：

```xml
<!-- 为防止Service被系统回收，可以尝试通过提高服务的优先级解决，1000是最高优先级，数字越小，优先级越低 -->  
android:priority="1000" 
```

### 5，自动重启
#### 利用android系统广播检查（类似心跳检查）
系统广播是Intent.ACTION_TIME_TICK，这个广播每分钟发送一次，我们可以每分钟检查一次Service的运行状态，如果已经被结束了，就重新启动Service。 

####  多进程service
开启多个进程的service并且互相绑定，一旦断开就重启service。

### 6，设置服务为前台服务

创建Notification

在Service的onStartCommand中添加如下代码：

```java
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
　　Log.d(TAG, "onStartCommand()");
　　// 在API11之后构建Notification的方式
　　Notification.Builder builder = new Notification.Builder
　　　　(this.getApplicationContext()); //获取一个Notification构造器
　　Intent nfIntent = new Intent(this, MainActivity.class);
　　
　　builder.setContentIntent(PendingIntent.
　　　　getActivity(this, 0, nfIntent, 0)) // 设置PendingIntent
　　　　.setLargeIcon(BitmapFactory.decodeResource(this.getResources(),
　　　　　　R.mipmap.ic_large)) // 设置下拉列表中的图标(大图标)
　　　　.setContentTitle("下拉列表中的Title") // 设置下拉列表里的标题
　　　　.setSmallIcon(R.mipmap.ic_launcher) // 设置状态栏内的小图标
　　　　.setContentText("要显示的内容") // 设置上下文内容
　　　　.setWhen(System.currentTimeMillis()); // 设置该通知发生的时间
　　
　　Notification notification = builder.build(); // 获取构建好的Notification
　　notification.defaults = Notification.DEFAULT_SOUND; //设置为默认的声音
}
```

## Native保活
### 早期
通过native frok一个守护进程---子进程监听，如果被杀就通过**通过 am 命令調用app對應的intent.action进行拉活**。
被大杀器，forestop命令杀死是进程树都会被杀的。
### Tim的机制
通过多个进程互相监听native文件锁，通过文件锁的释放来判断进程是否被杀死。


## 全家桶互拉
## 7.android10 activity保活

优雅的手段：通过申请用户白名单权限，或者判断不品牌机型跳转设置白名单。

## 8.app自启动

注册静态广播，接受启动的系统广播。需先打开权限

### 9.忽略Android 6.0+ 忽略电池优化开关和无障碍服务

1.我们需要在AndroidManifest.xml中声明一下权限

```xml
<uses-permission android:name="android.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS" />
复制代码
```

2.通过**Intent**来请求忽略电池优化的权限(需要`引导用户`点击)

```kotlin
//在Activity的onCreate中注册ActivityResult，一定要在onCreate中注册
//监听onActivityForResult回调
mIgnoreBatteryResultContract = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { activityResult ->
            //查询是否开启成功
            if(queryBatteryOptimizeStatus()){
               //忽略电池优化开启成功
            }else{
               //开启失败
            }
        }
复制代码
```

通过Intent`打开`忽略电池优化弹框：

```kotlin
val intent = Intent(Settings.ACTION_REQUEST_IGNORE_BATTERY_OPTIMIZATIONS)
intent.data = Uri.parse("package:$packageName")
//启动忽略电池优化，会弹出一个系统的弹框，我们在上面的
launchActivityResult(intent)
复制代码
```

查询`是否成功`开启忽略电池优化开关：

```kotlin
fun Context.queryBatteryOptimizeStatus():Boolean{
    val powerManager = getSystemService(Context.POWER_SERVICE) as PowerManager?
    return if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        powerManager?.isIgnoringBatteryOptimizations(packageName)?:false
    } else {
        true
    }
}
```



作者：Halifax
链接：https://juejin.cn/post/7003992225575075876

# 10.AnsycTask分析（弃用）

已被**弃用**。官方推荐**协程**。

**AsyncTask整体的运行模式了：配置创建mwork （一个callable）-> 传入furturetask  ->Executor 传入FutureTask -> 内部维护一个线程池 -> 线程池处理异步任务 -> 异步任务承载Callable进行任务的执行 -> handle负责传输信息。** 

## 10.1 特点

1. 本质上是封装了 **线程池** 和 **handler** 的异步框架, 是一个**静态线程池**

2. 封装了两个线程池，**一个并行，一个串行。默认串行**

3. 串行的线程池SerialExecutor（用于任务排队），维护一个双端任务（runnable）队列，所以运行时是按顺序来的，**可以通过运行asyncTask.executeOnExecutor(asynctask内部的并行线程池,  参数),** 来并行执行。

4. 容易造成内存泄露，处理方法就是把asynctask写成静态内部类或者软引用（推荐），建议并在onDestroy()中CANCEL（true）

5. onPreExecute() --> doInBackground() --> publishProgress() --> onProgressUpdate() --> onPostExecute() 

6. 会经历**PENDING（未开始执行）->RUNNING->FINISHED三个状态** 

7. AsyncTask 里面使用的是InternalHandler ,里面绑定了主线程的Looper和消息队列 ，如果handler收到是**MESSAGE_POST_RESULT** ，就会执行finish(), 最后调用onPostExecute(); **MESSAGE_POST_RESULT**

   就会执行onProgressUpdate();

   8. asynctask会一直执行直到doInBackground()完成。 如果执行cancel，会执行onCancel（）。mFurture调用cancel方法。 但并不是能真正的取消，如果存在不可中断操作就无法中断。

# 11.Okhttp源码分析

## 主框架流程图

![](3631399-0626631d246373a4%201.png)

## 基本原理
基于socket的以及复用连接池，线程池，支持超时链接高并发的高性能高可扩展的多功能网络框架。
如果支持Http/2 则堆**同一host**复用socket链接，如果不是则从连接池中取出，默认Gzip压缩数据，支持缓存。


##  基本创建

在new OkHttpClient内部维护了使用**建造者模式**初始化了一些配置信息，包括：

1，**支持协议** —— http1.0、http2.0、https---QUIC

2，**任务分发器Dispatcher（维护请求队列，内部包含了线程池，用于执行异步请求）**

3，**连接池（包含线程池，用于维护connection）**

4，**连接/读/写/超时时长**

```java
//原builder
public Builder() {
    dispatcher = new Dispatcher(); //任务调度器
    
    protocols = DEFAULT_PROTOCOLS; //支持的协议
    connectionSpecs = DEFAULT_CONNECTION_SPECS;
    eventListenerFactory = EventListener.factory(EventListener.NONE);
    proxySelector = ProxySelector.getDefault();
    cookieJar = CookieJar.NO_COOKIES;
    socketFactory = SocketFactory.getDefault();
    hostnameVerifier = OkHostnameVerifier.INSTANCE;
    certificatePinner = CertificatePinner.DEFAULT;
    proxyAuthenticator = Authenticator.NONE;
    authenticator = Authenticator.NONE;
    
    connectionPool = new ConnectionPool(); //连接池
    
    dns = Dns.SYSTEM;
    followSslRedirects = true;
    followRedirects = true;
    retryOnConnectionFailure = true;
    connectTimeout = 10_000;//超时时间
    readTimeout = 10_000;
    writeTimeout = 10_000;
    pingInterval = 0;
}
```

## **OkHttp网络请求流程** 

### 1.首先会创建okhttpclient，然后创建request，再client.newCall(request)

```java
//创建
mOkHttpClient = OkhttpBuilder().readTimeout(10L, TimeUnit.SECONDS).build();
Request request = RequestBulider().url("http://www.baidu.com").get().build();
Call call = mOkHttpClient.newCall(request);//内部实际上是newRealCall()， call是一个接口

//RealCall其实就是封装了我们的 请求操作 和 拦截器 的执行方法类。


//源码
 static RealCall newRealCall(OkHttpClient client, Request originalRequest, boolean forWebSocket) {
    // Safely publish the Call instance to the EventListener.
    RealCall call = new RealCall(client, originalRequest, forWebSocket);
    call.eventListener = client.eventListenerFactory().create(call);
    return call;
  }

```

### 2.然后会执行call的enqueue方法 

该方法会先判断请求有没有执行，已执行，则抛出异常

```java
 @Override public void enqueue(Callback responseCallback) {
    synchronized (this) {
        //已执行，则抛出异常
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    captureCallStackTrace();
    eventListener.callStart(this);
    //3.调用调度器dispatcher中equeue，创建异步请求
    client.dispatcher().enqueue(new AsyncCall(responseCallback));
  }

```



### 3.执行调度器enqueue方法（重要）

```java
synchronized void enqueue(AsyncCall call) {
    if (
        //判断  正在运行的异步请求  是否小于 最大并发数
        runningAsyncCalls.size() < maxRequests 
    && 
        //判断 正在运行的客户端 是否小于 规定的每个主机最大请求数量
        runningCallsForHost(call) < maxRequestsPerHost) {
        
        //条件满足，添加到 正在运行的异步请求队列
      runningAsyncCalls.add(call);
      //添加到线程池执行
      executorService().execute(call);
    } else {
        //不满足，添加到 将要执行的异步请求队列中
      readyAsyncCalls.add(call);
    }
  }

```



## 调度器Dispatcher（重要）


客户端builder第一行创建了调度器，

包含：

三个**双向任务队列。**
用于保证请求先后顺序执行。

RealCall的enqueue就会创建asyncCall，Realcall继承于call，但是AsyncCall是实现Runnable接口的执行类，不要搞混了。
执行之前都会检查是否已有对应对应请求在执行了有会抛出异常。

两个异步队列：
[[#3.执行调度器enqueue方法]]
- 1.准备执行的请求队列  **readyAsyncCalls ** 。runningAsyncCalls的**数量大于maxRequests最大并发数**，就推入该队列。
- 2.正在执行的请求队列** runningAsyncCalls **。enqueue把AsyncCall推入请求队列并且同时推入线程池。[[JAVA基础--JAVA数据结构--JVM#2.1.4 创建线程池]]
	```kotlin
	if (executorServiceOrNull == null) {  
	//keepAliveTime = 60s
	  executorServiceOrNull = ThreadPoolExecutor(0, Int.MAX_VALUE, 60, TimeUnit.SECONDS,  
	      SynchronousQueue(), threadFactory("$okHttpName Dispatcher", false))  
	}
	```

一个同步队列：
- 一个正在运行的同步请求队列**runningSyncCalls**。直接调用execute就会推入该队列并且执行。


**队列采用ArrayDequeue双向队列，速度比LinkedList更快**一个内部是Array实现，一个是链表。
### 调度器源码（重要）：
maxRequestsPerHost 每个host上最大的并发请求
maxRequests 同时请求最大的执行


> ```java
> public final class Dispatcher {
>     private int maxRequests = 64; //最大请求数量
>     private int maxRequestsPerHost = 5; //每台主机最大的请求数量
>     private @Nullable Runnable idleCallback;
>     
>     /** Executes calls. Created lazily. */
>     private @Nullable ExecutorService executorService; //线程池
>     
>     /** Ready async calls in the order they'll be run.
>     准备请求的请求队列*/
>     private final Deque<AsyncCall> readyAsyncCalls = new ArrayDeque<>();
>     
>     /** Running asynchronous calls. Includes canceled calls that haven't finished yet.
>     正在执行的请求队列 */
>     private final Deque<AsyncCall> runningAsyncCalls = new ArrayDeque<>();
>     
>     /** Running synchronous calls. Includes canceled calls that haven't finished yet.
>     正在运行的同步请求队列 */
>     private final Deque<RealCall> runningSyncCalls = new ArrayDeque<>();
>     
>     /** 这个线程池没有核心线程，线程数量没有限制，空闲60s就会回收*/
>     public synchronized ExecutorService executorService() {
>         if (executorService == null) {
>           executorService = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60, TimeUnit.SECONDS,
>               new SynchronousQueue<Runnable>(), Util.threadFactory("OkHttp Dispatcher", false));
>         }
>         return executorService;
>     }
> }  
> ```

## realcall

==**一个realcall不能调用两次enqueue，会报错已执行。**==
**call的方法（.enqueue(request）)其实是执行Dispatcher中的enqueue方法**，把realccall传入。

```kotlin
override fun enqueue(responseCallback: Callback) {  
  check(executed.compareAndSet(false, true)) { "Already Executed" }  
  
  callStart()  
  client.dispatcher.enqueue(AsyncCall(responseCallback))  //
}
```

再根据条件调用realcall execute()。

同步realcall的execute（原始realcall类）：



异步realcall中的execute()方法（**包装过的runnable类**）：

```java
  @Override protected void execute() {
      boolean signalledCallback = false;
      try {
          
          //RealCall通过执行getResponseWithInterceptorChain()返回Response，
         // 如果请求被取消则在进行OnFailue回调，
          //如果请求成功则进行onResponse的回调。
        Response response = getResponseWithInterceptorChain();
          
        if (retryAndFollowUpInterceptor.isCanceled()) {
          signalledCallback = true;
          responseCallback.onFailure(RealCall.this, new IOException("Canceled"));
        } else {
          signalledCallback = true;
          responseCallback.onResponse(RealCall.this, response);
        }
      } catch (IOException e) {
       ...
      } finally {
        client.dispatcher().finished(this);
      }
    }
  }

```



队列中的RealCall创建chain，并调用chain.proceed

创建interceptorchain时候，把拦截器都添加进去。
```java
//创建interceptorChain
internal fun getResponseWithInterceptorChain(): Response {  
  // Build a full stack of interceptors.  
  val interceptors = mutableListOf<Interceptor>()  
  interceptors += client.interceptors  
  interceptors += RetryAndFollowUpInterceptor(client)  
  interceptors += BridgeInterceptor(client.cookieJar)  
  interceptors += CacheInterceptor(client.cache)  
  interceptors += ConnectInterceptor  
  if (!forWebSocket) {  
    interceptors += client.networkInterceptors  
  }  
  interceptors += CallServerInterceptor(forWebSocket)  
  
  val chain = RealInterceptorChain(  
      call = this,  
      interceptors = interceptors,  
      index = 0,  
      exchange = null,  
      request = originalRequest,  
      connectTimeoutMillis = client.connectTimeoutMillis,  
      readTimeoutMillis = client.readTimeoutMillis,  
      writeTimeoutMillis = client.writeTimeoutMillis  
  )
try {  
//开启链式调用
  val response = chain.proceed(originalRequest)
  .......

```


chain.proceed中调用**RealInterceptorChain**的**proceed**方法，
开启链式调用，源码如下：

```java
  public Response proceed(Request request, StreamAllocation streamAllocation, HttpCodec httpCodec, RealConnection connection) throws IOException {
      
    if (index >= interceptors.size()) throw new AssertionError();

    calls++;

    // If we already have a stream, confirm that the incoming request will use it.
    if (this.httpCodec != null && !this.connection.supportsUrl(request.url())) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must retain the same host and port");
    }

    // If we already have a stream, confirm that this is the only call to chain.proceed().
    if (this.httpCodec != null && calls > 1) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must call proceed() exactly once");
    }

     /***************************核心部分***************************/
      
    // Call the next interceptor in the chain.
    RealInterceptorChain next = new RealInterceptorChain(interceptors, streamAllocation, httpCodec,
        connection, index + 1, request, call, eventListener, connectTimeout, readTimeout,
        writeTimeout);
    Interceptor interceptor = interceptors.get(index);
      //调用对应拦截器进行操作
    Response response = interceptor.intercept(next);
      
     /*******************************************/

    // Confirm that the next interceptor made its required call to chain.proceed().
	    if (httpCodec != null && index + 1 小于 interceptors.size() && next.calls != 1) {
      throw new IllegalStateException("network interceptor " + interceptor
          + " must call proceed() exactly once");
    }

    // Confirm that the intercepted response isnt null.
    if (response == null) {
      throw new NullPointerException("interceptor " + interceptor + " returned null");
    }

    if (response.body() == null) {
      throw new IllegalStateException(
          "interceptor " + interceptor + " returned a response with no body");
    }

    return response;
  }
  ```



## 拦截器
拦截器要看源码的话主要是看 ==缓存拦截器== 和 ==链接拦截器==
realcall中依靠getResponseWithInterceptorChain()方法返回response。

### getResponseWithInterceptorChain()源码

```java
 Response getResponseWithInterceptorChain() throws IOException {
    // Build a full stack of interceptors.
    List<Interceptor> interceptors = new ArrayList<>();
     
     //在配置 OkHttpClient 时设置的 interceptors()
    interceptors.addAll(client.interceptors());
     
     //负责失败重试以及重定向的  RetryAndFollowUpInterceptor
    interceptors.add(retryAndFollowUpInterceptor);//2
     
     //负责把用户构造的请求转换为发送到服务器的请求、把服务器返回的响应转为用户友好的响应的 			   BridgeInterceptor
    interceptors.add(new BridgeInterceptor(client.cookieJar()));//3
     
     //负责读取缓存返回，更新缓存的  cacheIntercptor
    interceptors.add(new CacheInterceptor(client.internalCache()));//4
     
     //负责和服务器建立连接的 ConnectInterceptor
    interceptors.add(new ConnectInterceptor(client));//5
     
    if (!forWebSocket) {
        
       //配置 OkHttpClient 时设置的 networkInterceptors
      interceptors.addAll(client.networkInterceptors());//6 
    }
     
     //负责向服务器发送请求数据、从服务器读取响应数据的 CallServerInterceptor
    interceptors.add(new CallServerInterceptor(forWebSocket));//7

    Interceptor.Chain chain = new RealInterceptorChain(interceptors, null, null, null, 0,
        originalRequest, this, eventListener, client.connectTimeoutMillis(),
        client.readTimeoutMillis(), client.writeTimeoutMillis());

    //前面都是在配置chain的拦截器链
     
     //开启链式调用
    return chain.proceed(originalRequest);//8
  }

```

总共有**五个拦截器**

- RetryAndFollowUpInterceptor

- BridgeInterceptor

- cacheIntercptor

- ConnectInterceptor

- CallServerInterceptor




### 各个拦截器
注意先后顺序
1. `在配置 OkHttpClient 时用户设置的 interceptors ()`
2. `负责失败重试以及重定向的RetryAndFollowUpInterceptor`
3. `负责把用户构造的请求转换为发送到服务器的请求、把服务器返回的响应转为用户友好的响应的 BridgeInterceptor(修改请求头、响应头)`
4. `负责读取缓存直接返回、更新缓存的 CacheInterceptor`
5. `负责和服务器建立连接的 ConnectInterceptor`
6. `配置 OkHttpClient 时用户设置的 networkInterceptors`
7. `负责向服务器**发送请求数据、从服务器读取响应数据**的 CallServerInterceptor`

 #### 自定义interceptor
 继承interceptor，实现intercept(chain: Chain)方法
有两种：
- addIntecepteceptor---请求发起之前，适合 比如添加通用数据、自定义http缓存规则等等。
- addNetworkIntecetor---真正发起网络请求之前，callAdapter之前，connectIntecept之后
适合：如果你要修改请求头，cookie, 重试规则等等，就要用addNetwork，不然会被默认处理覆盖。

#### RetryAndFollowUpInterceptor

重試次數==不超過20（已經作限制了）==，不然呢抛出異常

#### BridgeInterceptor
添加各種請求頭, 包括cookie，cookie我們可以繼承cookieJar去實現實現對應的方法並在配置client的時候傳入。
```kotlin
.....
if (userRequest.header("Host") == null) {  
  requestBuilder.header("Host", userRequest.url.toHostHeader())  
}  
  
if (userRequest.header("Connection") == null) {  
  requestBuilder.header("Connection", "Keep-Alive")  
}  
  
// If we add an "Accept-Encoding: gzip" header field we're responsible for also decompressing  
// the transfer stream.  
var transparentGzip = false  
if (userRequest.header("Accept-Encoding") == null && userRequest.header("Range") == null) {  
  transparentGzip = true  
  //gzip压缩
  requestBuilder.header("Accept-Encoding", "gzip")  
}  
  
val cookies = cookieJar.loadForRequest(userRequest.url)  
if (cookies.isNotEmpty()) {  
//cookie头
  requestBuilder.header("Cookie", cookieHeader(cookies))  
}  
  
if (userRequest.header("User-Agent") == null) {  
  requestBuilder.header("User-Agent", userAgent)  
}  
  
val networkResponse = chain.proceed(requestBuilder.build())
```

#### CacheInteceptor
對數據進行緩存或者讀取緩存，判断缓存有没有命中，通过缓存请求头：

![[1824809-a462a44fb545063a.webp]]

####  **ConnectInterceptor**
与服务器进行连接
**OKHttp如何复用TCP连接？**
ConnectInterceptor的主要工作就是负责建立TCP连接，建立TCP连接需要经历三次握手四次挥手等操作，如果每个HTTP请求都要新建一个TCP消耗资源比较多。

在使用 HTTP/1.1 或 HTTP/2 协议时，==OkHttp 的连接池会将完成请求的 TCP 连接存放起来，以便后续请求可以复用这些连接。==

持久连接使得在多个请求和响应之间共享同一 TCP 连接成为可能。==在 HTTP/1.1 中，请求和响应在持久连接上顺序传输；而在 HTTP/2 中，多个请求和响应可以**并行地**在同一连接上进行传输，通过多路复用（Multiplexing）来提高性能。==
连接池的主要目的是在多个 HTTP 请求之间高效地重用和共享这些已经建立的持久连接。

而Http1.1已经支持keep-alive，即多个Http请求复用一个TCP连接，OKHttp也做了相应的优化，下面我们来看下OKHttp是怎么复用TCP连接的。

[[#**OkHttp的复用连接池**]]

  ```java
   @Override public Response intercept(Chain chain) throws IOException {
      RealInterceptorChain realChain = (RealInterceptorChain) chain;
      Request request = realChain.request();
      StreamAllocation streamAllocation = realChain.streamAllocation();
  
      // We need the network to satisfy this request. Possibly for validating a conditional GET.
      boolean doExtensiveHealthChecks = !request.method().equals("GET");
      HttpCodec httpCodec = streamAllocation.newStream(client, chain, doExtensiveHealthChecks);
      RealConnection connection = streamAllocation.connection();
  
      return realChain.proceed(request, streamAllocation, httpCodec, connection);
    }
  ```



  **实际上建立连接就是创建了一个 HttpCodec 对象，它将在后面的CallServerInterceptor用于发送和接收, 它是对 HTTP 协议操作的抽象，有两个实现：Http1Codec和 Http2Codec**，顾名思义，它们分别对应 HTTP/1.1 和 HTTP/2 版本的实现。

  **在 Http1Codec中，它利用 [Okio](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fsquare%2Fokio%2F) 对 Socket 的读写操作进行封装，它对 java.io和 java.nio 进行了封装**，让我们更便捷高效的进行 IO 操作 

#### CallServerInterceptor

	

### 结论

OkHttp就是通过定义许多拦截器**一步一步地对Request进行拦截处理**(从头至尾)，直到请求返回网络数据，后面又倒过来，**一步一步地对Response进行拦截处理，最后拦截的结果就是回调的最终Response**。(从尾至头)



RealInterceptorChain的proceed方法，通过顺序地传入一个拦截器的集合，创建一个RealInterceptorChain，然后拿到之前OkHttp创建的各种拦截器，并调用其interrupt方法，并返回Response对象。其调用顺序如下：

 

![](1728184-7ba0e2d734014b3b%201.webp)



 其实 Interceptor的设计也是一种分层的思想，每个 Interceptor 就是一层。为什么要套这么多层呢？分层的思想在 TCP/IP 协议中就体现得淋漓尽致，分层简化了每一层的逻辑，每层只需要关注自己的责任（单一原则思想也在此体现），而各层之间通过约定的接口/协议进行合作（面向接口编程思想），共同完成复杂的任务，这是典型的[责任链设计模式](https://link.jianshu.com?t=https%3A%2F%2Fwww.cnblogs.com%2Fjava-my-life%2Farchive%2F2012%2F05%2F28%2F2516865.html)

 ![](1728184-2e74efa5083f6134%201.png)





##  **OkHttp的复用连接池** 

Http有一种叫做keepalive connections的机制，而okHttp支持5个并发socket连接，默认keepalive时间为5分钟 

 在`timeout`空闲时间内，连接不会关闭，相同重复的request将复用原先的`connection`，减少握手的次数，大幅提高效率 。
源码在 ExchangeFinder.class 中
![[640.jpg]]

#  12.ButterKnife原理解析

utterknife的原理主要分为三个部分来介绍，主要为：

注解生成模板代码分析

butterknife.bind()方法分析

生成的模板类代码分析。 

## **注解扫描生成模板：**

**annotationProcessor** + javaPoet 技术生成



```java
@AutoService(Processor.class)
public final class ButterKnifeProcessor extends AbstractProcessor {
  ...
  @Override public boolean process(Set < ? extends TypeElement> elements, RoundEnvironment env) {
  //解析bindview注解
    Map<TypeElement, BindingSet> bindingMap = findAndParseTargets(env);//1
    for (Map.Entry<TypeElement, BindingSet> entry : bindingMap.entrySet()) {
      TypeElement typeElement = entry.getKey();
      
      BindingSet binding = entry.getValue();//8
        
        //生成binding类
      JavaFile javaFile = binding.brewJava(sdk, debuggable);
      try {
        javaFile.writeTo(filer);
      } catch (IOException e) {
        error(typeElement, "Unable to write binding for type %s: %s", typeElement, e.getMessage());
      }
    }
    return false;
  }
  ...
}
```

注解器：ButterKnifeProcessor



1，（建造者模式）findAndParseTargets（）获取或创建BindingBuilder,从builderMap中获取BindingSet.Builder如果有则return, 如果没有则创建并放入Map缓存中 ，使用该build解析bindview注解的信息

**（AnnanotationProcessor：（编译时注解处理器）项目编译时对源代码进行扫描检测找出存活时间为`RetentionPolicy.CLASS`的指定注解，然后对注解进行解析处理，进而得到要生成的类的必要信息，然后根据这些信息动态生成对应的 java 类 ）**



 2，BindingSet binding = entry.getValue(); 获取类信息

先将扫描得到的注解相关信息保存到`builderMap`和`erasedTargetNames`中，最后对这些信息进行重新整理返回一个以`TypeElement`为 key 、

`BindingSet`为 value 的 Map，其中`TypeElement`代表使用了 ButterKnife 的类，即 Activity、Fragment等，**`BindingSet`是butterknife-compiler中的一个自定义类，用来存储要生成类的基本信息以及注解元素的相关信息。**

3，生成binding类

##  butterknife.bind() ：

 我们应该从`ButterKnife.bind(this)`开始分析，因为它像是 ButterKnife 和 Activity 建立绑定关系的过程，看具体的代码：

```
@NonNull @UiThread
public static Unbinder bind(@NonNull Activity target) {
   View sourceView = target.getWindow().getDecorView();
   return createBinding( target , sourceView);
}
```

`sourceView`代表当前界面的顶级父 View，是一个`FrameLayout`，继续看`createBinding()`方法：

```java
private static Unbinder createBinding(@NonNull Object target, @NonNull View source) {
    Class< ?> targetClass = target.getClass();
    if (debug) Log.d(TAG, "Looking up binding for " + targetClass.getName());
    Constructor< ? extends Unbinder> constructor = findBindingConstructorForClass(targetClass);

    if (constructor == null) {
      return Unbinder.EMPTY;
    }

    //noinspection TryWithIdenticalCatches Resolves to API 19+ only type.
    try {
      return constructor.newInstance(target, source);
    } 
    // 省略了相关异常处理代码
  }
```

首先得到要绑定的 Activity 对应的 Class，然后用根据 Class 得到一个继承了`Unbinder`的`Constructor`，最后通过反射`constructor.newInstance(target, source)`得到`Unbinder`子类的一个实例，到此`ButterKnife.bind(this)`操作结束。

这里我们重点关注`findBindingConstructorForClass()`方法是如何得到`constructor`实例的：



**butterknife中会缓存constructo**r（view）于Building中（LinkedHashMap）。

```java
@Nullable
@CheckResult
@UiThread
  private static Constructor< ? extends Unbinder> findBindingConstructorForClass(Class<?> cls) {
      
      //从缓存中获取
    Constructor< ? extends Unbinder> bindingCtor = BINDINGS.get(cls);
    if (bindingCtor != null) {
      if (debug) Log.d(TAG, "HIT: Cached in binding map.");
      return bindingCtor;
    }
    String clsName = cls.getName();
    if (clsName.startsWith("android.") || clsName.startsWith("java.")) {
      if (debug) Log.d(TAG, "MISS: Reached framework class. Abandoning search.");
      return null;
    }
    try {
    
    //没有缓存
      Class<?> bindingClass = cls.getClassLoader().loadClass(clsName + "_ViewBinding");
      
      
      //noinspection unchecked
      bindingCtor = (Constructor< ? extends Unbinder>) bindingClass.getConstructor(cls, View.class);
      if (debug) Log.d(TAG, "HIT: Loaded binding class and constructor.");
    } catch (ClassNotFoundException e) {
      if (debug) Log.d(TAG, "Not found. Trying superclass " + cls.getSuperclass().getName());
      bindingCtor = findBindingConstructorForClass(cls.getSuperclass());
    } catch (NoSuchMethodException e) {
      throw new RuntimeException("Unable to find binding constructor for " + clsName, e);
    }
    BINDINGS.put(cls, bindingCtor);
    return bindingCtor;
  }
```



接下来看当不存在对应 Constructor 时如何构造一个新的，首先如果`clsName`是系统相关的，则直接返回 null，否则先创建一个新的 Class：

创建xx_ViewBinding模板类

```
//读取对应的viewbinding类，生成class对象
Class< ?> bindingClass = cls.getClassLoader().loadClass(clsName + "_ViewBinding");
```



这里新的 Class 的 name 是 `com.shh.sometest.MainActivity_ViewBinding`，最后用新的 Class 创建一个 继承了`Unbinder`的 Constructor，并添加到`BINDINGS`：

```java
bindingCtor = (Constructor< ? extends Unbinder>) bindingClass.getConstructor(cls, View.class);
BINDINGS.put(cls, bindingCtor);
```



`bind()`方法返回的是`MainActivity_ViewBinding`类的实例 。该类由AnnanotationProcessor（编译时注解处理器）生成。



## ViewBinding类：

构造函数就是传入activity（target）以及其view（source）。并且最终执行了findviewbyid（）获取。

unbinder的时候就是把所有传入参数置空

## 总结流程

butterknife注解器获取注解，生成或者获取类信息的建造者，获取activity下注解的控件类信息对象bindingse，放入map中，根据类信息生成对应viewbinding类。接下来执行bind(this)，获取当前activity类class，再获取其binding类的constructor，通过contructor.newInstance实例化viewbinding类，构建时最终通过findviewbyid实例化控件。

butterknife本身不是通过反射实现viewbinding，而是在编译阶段直接生成类，性能上比动态代理处理注解性能要好。

缺点会生成更多的类，工程大小会受到影响。





# 13.Android-APT

**APT**(Annotation Processing Tool)即**注解处理器**。用于编译时注解处理生成类文件。

## 组成

注解处理器（annotation processor）+ 代码处理（**javaPoet** ） + 处理器注册（AutoService） 

annotationProcessor：是APT工具中的一种，是google开发的内置框架 （已封装java注解工具）

javaPoet  ：一款可以自动生成Java文件的第三方依赖 ，配置类信息之类，然后生成



> ```java
> //annotationProcessor：注解autoservice以及继承自AbstractProessor
> @AutoService(Processor.class)//google的Auto库，自动注册解析器，不然要手动新建MEAT-INF下的注册 
> public xxxProcess extends AbstractProessor{
>     @Override
>     public Set<String> getSupportedAnnotationTypes(){
>         return Collection.singletion(XXX注解.class.getCanoicalName());
>     }
>     
>     
>      @Override
>     public boolean process(Set< ? extends TypeElement> annotations, RoundEnvironment roundEnv) {
>         //以下这些创建方法，类等，属于Javapoet的范畴
>         MethodSpec main = MethodSpec.methodBuilder("main")
>                 .addModifiers(Modifier.PUBLIC, Modifier.STATIC)
>                 .returns(void.class)
>                 .addParameter(String[].class, "args")
>                 .addStatement("$T.out.println($S)", System.class, "Hello, JavaPoet!")
>                 .build();
> 
>         //
>         TypeSpec helloWorld = TypeSpec.classBuilder("HelloWorld")
>                 .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
>                 .addMethod(main)
>                 .build();
> 
>         JavaFile javaFile = JavaFile.builder("com.xx.xx", helloWorld)
>                 .build();
> 
>         
>         try {
>             javaFile.writeTo(processingEnv.getFiler());
>         } catch (IOException e) {
>             e.printStackTrace();
>         }
>         return false;
>     }
> }
> 
> //原文链接：https://blog.csdn.net/xx326664162/article/details/68490059
>     
> }
> ```



KAPT/KCP/KSP :  https://juejin.cn/post/6979759813467062309

## 13.1 KAPT/KCP

### KAPT

 **基于APT**的针对kotlin开发的编译处理器，APT无法处理kotlin代码。简单来说kotlin生成

stub文件再生成对应注解的java源码，大程度上影响编译速度

![](pic\a6a51d0c3b7b468b8db59d76607f158d~tplv-k3u1fbpfcp-zoom-in-crop-mark 3024 0 0 0.webp)

**优化建议：**

之前遇到很多项目组，为了方便会创建一个 library.gradle/base.gradle 这样的文件，这个文件中定义了很多通用的 kapt 依赖，随着项目模块化组件化的改造，项目中模块数量越来越多，一些只包含 model 类和接口、完全不需要 kapt 的 api 模块也被统一的使用到了这些 kapt 依赖，使得项目中有大量模块进行了无意义的 kapt 耗时， 因此我们建议：

- 尽量不要在类似于 library.gradle 的文件中为所有 module 添加统一的 kapt 依赖，改成具体模块按需使用。
- 或者有区分度的创建 library.gradle， library-api.gradle ，按照模块类型选择适当的模板文件，如api 类型的模块就不需要 apply kotlin-kapt 的 plugin，也不需要依赖 kapt 库


作者：字节跳动终端技术
链接：https://juejin.cn/post/7070849501166059551



### KCP

Kotlin Compiler Plugin** 在 kotlinc 过程中提供 hook 时机，可以再次期间解析 AST、修改字节码产物等，Kotlin 的不少语法糖都是 KCP 实现的，例如 `data class`、 `@Parcelize`、`kotlin-android-extension` 等, 如今火爆的 Compose 其编译期工作也是借助 KCP 完成的



## 13.2 KSP

`KSP` 即 [`Kotlin Symbol Processing(Kotlin符号处理器)`](https://link.segmentfault.com/?enc=u%2FEAQcke9VtpUFZb9vCzaw%3D%3D.6DNuPQzvJgRJHyRD2Lq36T03GALBUYEfpm85nn2Clug%3D)，KSP 目前只能生成代码，不能修改字节码，第一篇中的问题需要修改字节码，因此 KSP 不能满足需求。生成代码效率最高

# 14.序列化：Serializable和Pracelable

 ## 序列化：

序列化与反序列化都是一个过程，

序列化就是说当我需要保存信息到内存中或者文件里的时候，我要对这些信息做一些标记和标准规范。

反序列化就是 将来要用到这些信息的时候 可以按照你保存时的逻辑 恢复成你保存之前的样子。

## Serializable

空，反序列化还是用到了反射。具体的序列化 反序列化操作都是有jdk完成的。 性能较低。

**网络传输和本地存储时推荐使用**

##  Pracelable
### 示范
```java
public class TestObject implements Parcelable {  
    int aa;  
    String bb;  
    public int getAa() {return aa;}  
    public void setAa(int aa) {this.aa = aa;}  
    public String getBb() {return bb;}  
    public void setBb(String bb) {this.bb = bb;}  
  
    TestObject(Parcel in){  //顺序必须和witer一致，为什么？看后续分析
        bb = in.readString();  
        aa = in.readInt();  
    }  
    
   /**序列化*/  
    @Override  
    public void writeToParcel(@NonNull Parcel dest, int flags) {  
        dest.writeString(bb);  
        dest.writeInt(aa);  
    }  
    
    /**描述信息*/  
    @Override  
    public int describeContents() {  
        return 0;  
    }  
  
   
  
    /**  
     * 负责反序列化  
     */  
    public static final Creator<TestObject> CREATOR = new Creator<TestObject>() {  
        /**  
         * 从序列化对象中，获取原始的对象  
         * @param in  
         * @return  
         */  
        @Override  
        public TestObject createFromParcel(Parcel in) {  
            return new TestObject(in);  
        }  
  
        /**  
         * 创建指定长度的原始对象数组  
         * @param size  
         * @return  
         */  
        @Override  
        public TestObject[] newArray(int size) {  
            return new TestObject[size];  
        }  
    };  
}
```
```java
public interface Parcelable {
    //内容描述接口
    public int describeContents();
    //写入接口函数，打包
    public void writeToParcel(Parcel dest, int flags);
     //读取接口，目的是要从 Parcel 中构造一个实现了 Parcelable 的类的实例处理。因为实现类在这里还是不可知的，所以需要用到模板的方式，继承类名通过模板参数传入。
    //为了能够实现模板参数的传入，这里定义Creator嵌入接口,内含两个接口函数分别返回单个和多个继承类实例。
    public interface Creator<T> {
           public T createFromParcel(Parcel source);
           public T[] newArray(int size);
       }
```

需要程序员来自己实现序列化与反序列化过程，所以Parcelable使用的时候效率要比Serializable高，但是使用的时候要比Serializable复杂 

### 原理
#### 简介
Parcel提供了一套机制,，**可以将序列化之后的数据写入到一个共享内存中，其他进程通过Parcel可以从这块共享内存中读出字节流，并反序列化成对象,下图是这个过程的模型。**

![[94506fc564259f6cca7e044598f1349b.png]]


通过jni在native内存创建一个共享的Pacel.cpp对象，并把该对象地址返回一个到java中的long mNativePtr变量中，后续多次访问时直接`反转`该指针，获取到Parcel对象。

java -> android_os_parcel.cpp 中间件创建parcel.cpp 指针 ->return jlong类型的指针地址
![[20230814021416.png]]

从Parcel.java（frameworks\base\core\java\android\os\Parcel.java）可以看到读写方法都是native实现的，支持基本数据类型、数组、集合、map、对象、文件描述符、IBinder等读写。（基于aosp12进行分析）
![在这里插入图片描述](https://img-blog.csdnimg.cn/c009ae1225a940ec87c09fdc563a4c76.png)

### 分析writeString()/readWrite()过程，java层调用代码如下：

```java
// 写入数据
android.os.Parcel _data = android.os.Parcel.obtain(); // 获取Parcel对象，会创建一个本地对象，并进行初始化
_data.writeInterfaceToken(DESCRIPTOR); // 写入IBinder接口标志，一般为全类名，用户数据校验
_data.writeString(data);// 写入数据
```


### java层Parcel创建

先从缓存池中获取，获取不到就创建新的。可以看到最后调用`nativeCreate()`​来初始化，`mNativePtr`​用来保存native层结果

```java
// frameworks/base/core/java/android/os/Parcel.java
/*** Retrieve a new Parcel object from the pool.*/

public static Parcel obtain() {
	Parcel res = null;...
// When no cache found above, create from scratch; otherwise prepare the
// cached object to be used
if (res == null) {res = new Parcel(0);}

else { res.mReadWriteHelper = ReadWriteHelper.DEFAULT;}
return res;
}
private Parcel(long nativePtr) { 
	init(nativePtr);
}
private void init(long nativePtr) {
	if (nativePtr != 0) {
		mNativePtr = nativePtr;
		mOwnsNativeParcelObject = false;}
		 else {
			 // 传过来mNativePtr为0，执行这个分支
			 mNativePtr = nativeCreate();
			 mOwnsNativeParcelObject = true;
			 }
}

private static native long nativeCreate();
```

#### .native层Parcel创建

接下来我们继续看native层操作，native层Parcel构造方法中初始化相关变量，具体的含义如下：
```cpp
// frameworks\base\core\jni\android_os_Parcel.cpp
//中间件负责与java层操作
static jlong android_os_Parcel_create(JNIEnv* env, jclass clazz)
{
Parcel* parcel = new Parcel();

return reinterpret_cast<jlong>(parcel);

}

// frameworks\native\libs\binder\Parcel.cpp
//真正操作层
Parcel::Parcel(){
	initState();
}
void Parcel::initState(){
	mError = NO_ERROR;
	mData = nullptr;//序列化后数据真正存在的对象的地址
	mDataSize = 0;
	mDataCapacity = 0;
	mDataPos = 0;
}
// 含义
status_t            mError;          // 错误码
const uint8_t*      mData;           // Parcel中存储的数据的起始地址
const uint8_t*       mDataPos;       // mData当前的下标长度
size_t              mDataCapacity;   // 最大存储能力
```
parcel.h中部分代码， 其中data存了共享的对象首地址，position代表指针挪动的位置。
![[20230814050931.png]]

反序列化**读取的时候通过该地址获得指针，并且进行 起始地址+下标位置(之后下标要加上sizeof（val）进行更新) 进行与当前地址位移，直接继续写入或者直接获取下一个值，这也是为什么读取的时候跟序列化时候的数据顺序必须一样的原因**
大体核心流程就是如此.


## 不同

两者最大的区别在于 **存储媒介的不同**，`Serializable` 使用 **I/O 读写存储在硬盘上**，而 `Parcelable` 是直接 **在内存中读写**。很明显，内存的读写速度通常大于 IO 读写，所以在 Android 中传递数据优先选择 `Parcelable`。

 `Serializable` 会使用反射，序列化和反序列化过程需要大量 I/O 操作， `Parcelable` 自已实现封送和解封（marshalled &unmarshalled）操作不需要用反射，数据也存放在 Native 内存中，效率要快很多

 ## 

# 15.图片裁剪和大图显示

## 大图展示
相关内容：[[#43.Bitmap与Bitmap优化]]
1.我们可以通过option先配置图片解码大小，通过inSampleSize加载缩略图图
或者是  **通过option配置inJustDecodeBounds只加载图片的信息，再根据图片情况进行具体配置

```java
BitmapFactory.Options options = new BitmapFactory.Options(); 

options.inJustDecodeBounds = true; 
opt.inSampleSize = 2 
BitmapFactory.decodeResource(getResources(), R.mipmap.qb, options); 

```
2.加载了图片信息，只加载部分区域通过 BitmapRegionDecoder
```kotlin
val opt = BitmapFactory.Options()  
opt.inSampleSize = 2 
BitmapFactory.decodeFile(file, opt)  
//开启复用！！！
opt.inMutable = true  
BitmapRegionDecoder.newInstance(file, false)
```


# 16.Gilde原理分析

扩展 [[#35.Gilde 和Fresco]]

![[aHR0cHM6Ly9pbWcubXVidS5jb20vZG9jdW1lbnRfaW1hZ2UvZWEyMzhlNzgtNTNlNS00ZTk5LTljNDYtZTA1ZjA0MzEwNmY5LTc3MDI4ODQuanBn.png]]


with（），load（），into（）。

**with()**:添加activity或者context或者fragment，获取到requestMannager（管理请求）并创建一个fragment用于监听生命周期（通过lifecycle），以便及时回收资源，终止操作。
初始化with()

首先是Glide.with()方法，通过该方法主要是通过RequestManagerRetriever获取一个RequestManager对象。RequestManager是处理图片加载过程的具体实现类，后面详细讲述。

### 调用链路（需要熟記）：
- (1)Glide.with( activtiy / fragment / framgentActivtiy / context)
- (2)Glide.getRetriever
	- (3)Glide.get(context) 获取==Glide的全局单例，如果 沒有就要初始化，同時初始化緩存池，==处理用户自定义AppGlideModule的情况。通过Annotation Processer在编译时根据@GlideModule注解来生成自定义Glide的代理类（繼承自AppGlideModule，支持用戶通過gildeBuilder自定義bitmapPool，各個綫程池，日志等等）GeneratedAppGlideModuleImpl,通过反射获取代理类的构造方法并实例化返回。
	若都没有自定义的module，则通过GliderBuilder的工厂模式，執行build生成实例。
```java
Glide build(@NonNull Context context) {  
    if (sourceExecutor == null) {  
      sourceExecutor = GlideExecutor.newSourceExecutor();  
    }  
  
    if (diskCacheExecutor == null) {  
      diskCacheExecutor = GlideExecutor.newDiskCacheExecutor();  
    }  
  
    if (animationExecutor == null) {  
      animationExecutor = GlideExecutor.newAnimationExecutor();  
    }  
  
    if (memorySizeCalculator == null) {  
      memorySizeCalculator = new MemorySizeCalculator.Builder(context).build();  
    }  
  
    if (connectivityMonitorFactory == null) {  
      connectivityMonitorFactory = new DefaultConnectivityMonitorFactory();  
    }  
  
    if (bitmapPool == null) {  
      int size = memorySizeCalculator.getBitmapPoolSize();  
      if (size > 0) {  
        bitmapPool = new LruBitmapPool(size);  
      } else {  
        bitmapPool = new BitmapPoolAdapter();  
      }  
    }  
  
    if (arrayPool == null) {  
      arrayPool = new LruArrayPool(memorySizeCalculator.getArrayPoolSizeInBytes());  
    }  
  
    if (memoryCache == null) {  
      memoryCache = new LruResourceCache(memorySizeCalculator.getMemoryCacheSize());  
    }  
  
    if (diskCacheFactory == null) {  
      diskCacheFactory = new InternalCacheDiskCacheFactory(context);  
    }  
  
    if (engine == null) {  
    //初始化圖片加載引擎
      engine =  
          new Engine(  
              memoryCache,  
              diskCacheFactory,  
              diskCacheExecutor,  
              sourceExecutor,  
              GlideExecutor.newUnlimitedSourceExecutor(),  
              animationExecutor,  
              isActiveResourceRetentionAllowed);  
    }  
  
    if (defaultRequestListeners == null) {  
      defaultRequestListeners = Collections.emptyList();  
    } else {  
      defaultRequestListeners = Collections.unmodifiableList(defaultRequestListeners);  
    }  
  
    RequestManagerRetriever requestManagerRetriever =  
        new RequestManagerRetriever(requestManagerFactory);  
//new Glide中會配置更多的東西，例如各種解碼器，編碼器，綫程池
    return new Glide(  
        context,  
        engine,  
        memoryCache,  
        bitmapPool,  
        arrayPool,  
        requestManagerRetriever,  
        connectivityMonitorFactory,  
        logLevel,  
        defaultRequestOptionsFactory,  
        defaultTransitionOptions,  
        defaultRequestListeners,  
        isLoggingRequestOriginsEnabled,  
        isImageDecoderEnabledForBitmaps);  
  }  
}

```

- (4)Glide.getRequestManagerRetriever()
- (5)RequestManagerRetriever.get()
- RequestManagerRetriever.get
方法根据调用的context及是否主线程返回对应的RequestManager。
	context为FragmentActivity时调用：
```java
	public RequestManager get(@NonNull Context context) {
    if (context == null) {
      throw new IllegalArgumentException("You cannot start a load on a null Context");
    } else if (Util.isOnMainThread() && !(context instanceof Application)) {
    //Activity、FragmentActivity情况
      if (context instanceof FragmentActivity) {
        return get((FragmentActivity) context);
      } else if (context instanceof Activity) {
        return get((Activity) context);
      } else if (context instanceof ContextWrapper
          && ((ContextWrapper) context).getBaseContext().getApplicationContext() != null) {
        return get(((ContextWrapper) context).getBaseContext());
      }
    }
    //非UI线程情况
    return getApplicationManager(context);
	}
```

如果context是Fragment时调用：
```java
public RequestManager get(@NonNull Fragment fragment) {
    Preconditions.checkNotNull(
        fragment.getContext(),
        "You cannot start a load on a fragment before it is attached or after it is destroyed");
    //非UI线程
    if (Util.isOnBackgroundThread()) {
      return get(fragment.getContext().getApplicationContext());
    } else {
        //context为Fragment情况
      FragmentManager fm = fragment.getChildFragmentManager();
      return supportFragmentGet(fragment.getContext(), fm, fragment, fragment.isVisible());
    }
}

```
总的来说分三种情况：
	@1. 非UI线程
	@2. UI线程Activity
	@3. UI线程Fragment

@1.先来分析第一种情况，第一种情况较简单，通过getApplicationManager返回一个全局的RequestManager单例，**该RequestManager是通过ApplicationLifecycle()构造的，也就是说只能感知application的生命周期**

@2.再来看UI线程Activity情况
```java
public RequestManager get(@NonNull FragmentActivity activity) {
    if (Util.isOnBackgroundThread()) {
      return get(activity.getApplicationContext());
    } else {
        //断言activity未销毁
      assertNotDestroyed(activity);
      //获取FragmentManager
      FragmentManager fm = activity.getSupportFragmentManager();
      //核心！！通过fm向当前activity添加一个SupportRequestManagerFragment实例
      //通过SupportRequestManagerFragment感知activity的生命周期
      return supportFragmentGet(activity, fm, /*parentHint=*/ null, isActivityVisible(activity));
    }
}
```
	只监听了start，pause，destroy生命周期。

- (6)with最后返回的是 RequestManager，
- requestmanager 根据 load（传入的类型url，drawable，bitmap之类的）返回对应的  requestbuilder
	- **load**（）：加载链接并返回DrawableRequestBuilder，大多数方法  都在这个建造者类中。主要是創建請求
- requestbuilder.into 正常开始请求网络
```java
//此處只看into（ImageView）方法
public ViewTarget<ImageView, TranscodeType> into(@NonNull ImageView view) {
    //断言是否UI线程，非UI线程抛出异常
    Util.assertMainThread();
    //判空
    Preconditions.checkNotNull(view);
    BaseRequestOptions 《?> requestOptions = this;
    //获取裁剪方式配置。注意这里使用的是clone的方式
    //避免修改原始配置参数，导致相同RequestBuilder加载其他目标时配置被修改过。
    if (!requestOptions.isTransformationSet()
        && requestOptions.isTransformationAllowed()
        && view.getScaleType() != null) {
     
      switch (view.getScaleType()) {
        case CENTER_CROP:
          requestOptions = requestOptions.clone().optionalCenterCrop();
          break;
        case CENTER_INSIDE:
          requestOptions = requestOptions.clone().optionalCenterInside();
          break;
        case FIT_CENTER:
        case FIT_START:
        case FIT_END:
          requestOptions = requestOptions.clone().optionalFitCenter();
          break;
        case FIT_XY:
          requestOptions = requestOptions.clone().optionalCenterInside();
          break;
        case CENTER:
        case MATRIX:
        default:
          // Do nothing.
      }
    //@1.核心！！加载图片
    return into(
        //根据类型封装成对应的ViewTarget
        //分为DrawableImageViewTarget、BitmapImageViewTarget
        //分别调用ImageView的setImageDrawable、setImageBitmap来实现图片加载显示
        glideContext.buildImageViewTarget(view, transcodeClass),
        /*targetListener=*/ null,
        requestOptions,
        Executors.mainThreadExecutor());
}

```




**into**（）中的所有操作：发起网络请求、缓存数据、解码并显示图片

```java
private <Y extends Target《TranscodeType>> Y into(
      @NonNull Y target,
      @Nullable RequestListener<TranscodeType> targetListener,
      BaseRequestOptions<<?> options,
      Executor callbackExecutor) {
    Preconditions.checkNotNull(target);
    if (!isModelSet) {
      throw new IllegalArgumentException("You must call #load() before calling #into()");
    }

    Request request = buildRequest(target, targetListener, options, callbackExecutor);

    Request previous = target.getRequest();
    // 这里做了请求优化处理，避免重复资源请求。同时满足如下两个条件时，直接复用前一个request。
    //1.当前request和前一个request相同
    //2.支持内存缓存cacheable=true或者前一个请求未成功完成isComplete=false
    if (request.isEquivalentTo(previous)
        && !isSkipMemoryCacheWithCompletePreviousRequest(options, previous)) {
      //如果前一个request处于非running状态，重新启动该请求
      if (!Preconditions.checkNotNull(previous).isRunning()) {
        previous.begin();
      }
      return target;
    }
    //如果是新请求，更新RequestTracker及TargetTracker
    requestManager.clear(target);
    target.setRequest(request);
    //@2.发送加载请求
    requestManager.track(target, request);

    return target;
}

synchronized void track(@NonNull Target >?> target, @NonNull Request request) {
    //弱引用存储ViewTarget
    targetTracker.track(target);
    //@3.将request加入请求集合并执行
    requestTracker.runRequest(request);
}


```
- runrequest 的時候會放到resuqest任務隊列中，但如果是pause狀態，就會放到一個pending任務列表中等到觀察到 restart 再取出執行。
	或者是直接觀察到了pause，就會從requser任務隊列中取出并且暫停，也是放到一個pending任務列表中等到觀察到 restart 再取出執行。


- 核心2：圖片加載引擎engine，執行engine.load()，，經過**三級緩存**，創建engineJob和decodeJob放入到glide的綫程池開始獲取圖片和解碼圖片。

大致流程：
①初始化各种**参数**，做好准备工作（**配置网络请求**、基于MVP的各种接口回调）

②使用==最原始的HTTPConnect网络连接（HttpURLConnection ），==读取文件流，可以引入okhttp支持

③根据文件判断是GIF动图还是Bitmap静态图片

④通过相关复杂逻辑将下载的图片资源取出来，赋值给ImageView控件。



lfiecycle（观察者模式）在fragment（被观察者）中：

```java
public class RequestManagerFragment extends Fragment {
    // 代码略...，注意此处的lifecycle是gilde自己定义的lifecycle，跟jetpack的没有关系
    private final ActivityFragmentLifecycle lifecycle;
    // 代码略...
    @Override
    public void onStart() {
        super.onStart();
        lifecycle.onStart();
    }

    @Override
    public void onStop() {
        super.onStop();
        lifecycle.onStop();
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        lifecycle.onDestroy();
    }
    // 代码略...
}
```



## 核心思想

1. **维护一个bitmap对象池（lru**），减少创建的成本。

2. **生命周期的绑定**，with方法，工厂方法。

   with方法：

   A.加载@GlideModule自定义配置

   B.配置线程池，bitmap池，缓存策略

   C.with方法通过传入fragment,activity等创建不可见framgnet，lifecyle监听生命周期，回收资源。或者与application绑定生命周期

   onDestroy时回收资源


## Glide的緩存設計（重中之重）
![[20200531193106733.png]]

Glide 缓存机制主要分为2种：

**内存缓存和磁盘缓存** 使用内存缓存的原因是：防止应用重复将图片读入到内存，造成内存资源浪费。

 使用磁盘缓存的原因是：防止应用重复从网络或其它地方下载和读取数据。 

### Glide的三级缓存策略：

(1) Engine.load中先从==内存缓存==中加载图片loadFromMemory
- 先从 ActiveResources活跃缓存中 获取，active緩存也就是正在使用的圖片資源，並是以弱引用的在使用
	- ==获取失败从内存缓存LruResourceCache中获取==，同时将缓存从cache中移到actives中
      //這裏就是Lru緩存了，此處key是保存了大小、資源等信息的。

(3) 执行DecodeEngine.run通过ResourceCacheGenerator从硬盘缓存中读取（第三緩存）

(4) 读取失败  则  通过SourceGenerator执行获取任务，UrlConnection下载网络图片

(5) **==下载成功后经过解码、压缩后，更新硬盘缓存及活跃缓存actives==**

(6) 回调ImageViewTarget最终设置并显示图片到目标ImageView

### 各種復用池
==BitmapPool==，==ByteArrayPool==存儲解碼時的緩衝buff復用。

问题1： Glide加载一个100x100的图片，是否会压缩后再加载？放到一个300x300的view上会怎样？

当我们调整ImageView大小事，因爲緩存的key是基於圖片參數的，==也就是Glide会为每个不同尺寸的ImageView缓存一张图片==，也就是说不管你的这张图片有没有被加载过，只要ImageView的尺寸不一样，那么Glide就会重新加载一次，这时候，他会在加载ImageView之前从网络上重新下载，然后再缓存。
 举个例子，如果一个页面的ImageView是300 * 300像素，而另一个页面中的ImageView是100 * 100像素，这时候想要让两个ImageView是同一张图片，==那么Glide需要下载两次图片，并且缓存两张图片==。



问题2：简单说一下内存泄漏的场景，如果在一个页面中使用Glide加载了一张图片，图片正在获取中，如果突然关闭页面，这个页面会造成内存泄漏吗？

Glide在加载资源的时候，如果是在Activity，Fragment这一类有生命周期的组件上进行的话，会创建一个透明的RequestManagerFragment加入到FragmentManager之中，感知生命周期，当Activity， Fragment等组件进入不可见，或者已经销毁的时候，Glide会停止加载资源。但是如果是在非生命周期的组件上进行时，==会采用Application的生命周期贯穿整个应用==，所以applicationManager只有在应用程序关闭时终止加载。

问题3:  Glide 是如何加载 GIF 动图的？

首先需要区分加载的图片类型，即网络请求拿到输入流后，获取输入流的前三个字节，若为 GIF 文件头，则返回图片类型为 GIF。

确认为 GIF 动图后，会构建一个 GIF 的解码器（StandardGifDecoder），它可以从 **GIF 动图中读取每一帧的数据并转换成 Bitmap**(這裏就引出下面的問題了)，然后使用 Canvas 将 Bitmap 绘制到 ImageView 上，下一帧则利用 Handler 发送一个延迟消息实现连续播放，所有 Bitmap 绘制完成后又会重新循环，所以就实现了加载 GIF 动图的效果。


### Gif卡顿
加载Gif图片的源码得知:Glide在加载Gif的图片帧的时候,==上一帧的渲染以及下一帧的准备是串行的,这个过程中,如果出现下一帧的准备阶段时间超过了Gif间隔播放的时长,就会造成播放卡顿==.而且此过程中,StandardGifDecoder只保留上一帧的数据,每次获取当前需要绘制的帧的时候都会从==java层的BitmapPool中获取新的Bitmap(注意,这是一个新的Bitmap对象)==,因此加载Gif过程中,==Glide至少需要两个Bitmap==.这也就导致内存会消耗的过高.
![[aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8yMzA4OTIwNS03OWJjNTAxZGUwMGE0MjAx.png]]

通过引入==GIFLIB在native层解码GIF==,这样一来内存消耗以及CPU的使用率都可以得到明显的降低和提升.==其次通过FrameSequenceDrawable的双缓冲机制进行绘制GIF动画==,这样就不需要在Java层的BitmapPool中创建多个Bitmap了.

# 17.SharedPreferences
 在内存中会有缓存。线程不安全。
 [[#39.MMKV 与SP]]

#  18.Handle, Looper,Message

 **android异步消息机制架构**，重中之重

handler是消息处理器，用于接收和发送message，Message是消息本体，messagequeue是消息队列，Looper是驱动从messageQueue轮询获取message。每个Looper拥有一个messagequue。

详细架构图：

![[20210524190404296.png]]

## 18.1 Looper

looper其实管理message queue的类。一个线程只有一个looper。主线程拥有隐式的loop， activityThread main中loop。LOOP（）。

消息队列中是无限循环获取message，如果没有信息就会进入阻塞状态。

**prepare**：创建looper对象，通过threadlocal（虽然是全局，但只能将存储到当前线程的数据取出）设置到当前线程。

threadlocal：数组键值对。
已经存储了线程对应的loop，所以一个线程只能创建一个loop
==内部是一个ThreadLoacalMap，key为当前threadlocal的弱引用==
threadlocal能存储每个线程专用的变量，先获取到当前线程t，随后通过getMap方法获取ThreadLocalMap对象，再通过getEntry获取到Entry对象，Entry中的key是弱引用。

[[多线程和锁#ThreadLocal简介]]

在Looper中，ThreadLocal是被这样定义的
```java
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
```
如果有prepare，才会被添加到ThreadLcalMap里面。

**loop**：开启消息循环，从消息队列中不断取出

**quit**&qiutsafety：==退出消息循环，不线程无法回收==

```java
public class Looper {
    ......    
      private static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();   
    	final MessageQueue mQueue;//拥有的消息队列

    //创建新的looper对象，并设置到当前线程中
    public static final void prepare() {        
    if (sThreadLocal.get() != null) {            
	    throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper());
    }
    /**/

    //获取当前线程的looper对象
    public static final Looper myLooper() {       
    	return (Looper)sThreadLocal.get();
    }
    
    private Looper() {
        mQueue = new MessageQueue();
        mRun = true;
        mThread = Thread.currentThread();
    }

}
```



问题：**为什么loop不会导致anr？**

线程阻塞进入休眠状态与anr无关，anr指操作长时间无响应。主线程的所有操作，包括activity和fragment的生命周期都通过消息来操作，只要往loop中传递消息，线程就会唤醒

问题：**多个handler能不能接收消息？**

不能，只有发送消息的handler能接收到消息，尽管同一线程是用同一消息队列，但由于发送消息的时候，message的接收目标被指定是发送msg（msg.taget）的handler，调用的是发送方的handleMessage的方法

## 18.2 Message

消息的载体，实现了parcelable接口

public int what 标识符，**用来识别 message，自带用一个message pool，通过Message.obtain()从全局池中返回一个message实例，避免多次创建message（如new Message）**



public int arg1,arg2 可以用来传递一些轻量型数据如int之类的



public Object obj Message 自带的Object类字段，用来传递对象



Handler target 指代此 message 对象对应的 Handler



messageQueue。enenque（message）里面会上锁

![[v2-50dc75acb87d6cd3b73862cedfb3d674_720w.webp]]

### messageQueue

	存于在Loop中，Loop管理者消息队列，在创建该线程的线程空间，共享该messagequeue的空间实现线程切换。
核心动力点
```java
Message next() {  
    // Return here if the message loop has already quit and been disposed.  
    // This can happen if the application tries to restart a looper after quit    // which is not supported.    final long ptr = mPtr;  
    if (ptr == 0) {  
        return null;  
    }  
  
    int pendingIdleHandlerCount = -1; // -1 only during first iteration  
    int nextPollTimeoutMillis = 0;  
    for (;;) {  
        if (nextPollTimeoutMillis != 0) {  
            Binder.flushPendingCommands();  
        }  
        //没有消息的时候就调用这个native方法阻塞线程
        nativePollOnce(ptr, nextPollTimeoutMillis);  
  
        synchronized (this) {  
            // Try to retrieve the next message.  Return if found.  
            final long now = SystemClock.uptimeMillis();  
            Message prevMsg = null;  
            Message msg = mMessages;  
            if (msg != null && msg.target == null) {  //处理消息屏障的 *同步*  消息，如果没有同步信息解除屏障，则会直接跳过异步信息阻塞或者每次都进入消息屏障
                // Stalled by a barrier.  Find the next asynchronous message in the queue.  
                do { 
                    prevMsg = msg;  
                    msg = msg.next;  
                } while (msg != null && !msg.isAsynchronous());  //取出后续同步的信息
            }  
            
         //不断取消息，取异步消息
            if (msg != null) {  
                if (now 《  msg.when) {  
                    // Next message is not ready.  Set a timeout to wake up when it is ready.  
                    nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);  
                } else {  
                    // Got a message.  
                    mBlocked = false;  
                    if (prevMsg != null) {  
                        prevMsg.next = msg.next;  
                    } else {  
                        mMessages = msg.next;  
                    }  
                    msg.next = null;  
                    if (DEBUG) Log.v(TAG, "Returning message: " + msg);  
                    msg.markInUse();  
                    return msg;  
                }  
            } else {  
                // No more messages.  。-1
                nextPollTimeoutMillis = -1;  
            }  
  
            //调用退出  
            if (mQuitting) {  
                dispose();  
                return null;            }  
//两个条件用IdlerHandler：当前消息队列是空，或者还没到消息运行时间
               if (pendingIdleHandlerCount 《 0  
                    && (mMessages == null || now 《 mMessages.when)) {  
                pendingIdleHandlerCount = mIdleHandlers.size();  
            }  
            if (pendingIdleHandlerCount 《= 0) {  
                // No idle handlers to run.  Loop and wait some more.  
                mBlocked = true;  
                continue;            }  

	
            if (mPendingIdleHandlers == null) {  
                mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];  
            }  
            mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);  
        }  
  
         for (int i = 0; i 《 pendingIdleHandlerCount; i++) {  
            final IdleHandler idler = mPendingIdleHandlers[i];  
            mPendingIdleHandlers[i] = null; // release the reference to the handler  
  
            boolean keep = false;  
            try {  
                keep = idler.queueIdle();  
            } catch (Throwable t) {  
                Log.wtf(TAG, "IdleHandler threw exception", t);  
            }  
  
            if (!keep) {  
                synchronized (this) {  
                    mIdleHandlers.remove(idler);  
                }  
            }  
        }  
  
        // Reset the idle handler count to 0 so we do not run them again.  
        pendingIdleHandlerCount = 0;  
  
        // While calling an idle handler, a new message could have been delivered  
        // so go back and look again for a pending message without waiting.       
         nextPollTimeoutMillis = 0;  
    }  
}

```


1.当没有遍历到链表的尾部时，即p不为null，此时比较的是延迟时间when如果when 《  p.when，那么msg应该被插入到p之前。

2.p = null时，那么即表示已经到链表的尾了，此时循环跳出 msg.next = p; prev.next = msg.这里msg其实是被放到了链表的尾部了。

3.看needWake的赋值操作，**needWake = mBlocked && p.target == null && msg.isAsynchronous();**这个条件判断是比较严格的，什么时候才会唤醒队列呢？队列是睡眠状态并且队列的第一条消息的target为空(Handler)，待插入的msg为异步消息时才会唤醒队列。

tips：这个循环其实是有点绕的，需要理解的是每次遍历之前都更新了prev的值，且将头节点后移了一个p = p.next；然后开始遍历，当条件满足插入的位置时msg的前后节点都可以对应上。通俗的讲，这个prev其实就是msg的prev。需要理解的是p与prev在遍历的过程中是不断变化的。


看一下nativeWake
我们在quit,  enqueueMessage，removeSynBarrier的时候会触发nativeWake，恢复进程。

## 18.3 Handler
```kotlin
val obtain = Message.obtain()  
val handler = object :Handler(Looper.getMainLooper()){  
    override fun handleMessage(msg: Message) {  
        super.handleMessage(msg)  
        xxxx  
    }
}  
  
obtain.obj = 10  
handler.sendMessage(obtain)  
handler.sendMessageDelayed(obtain, 100l)  

handler.post { }  
handler.postAtTime({}, 100l)
```


handler工作需要looper，不然报错。

post，postDelayed（runnable， timemilis）或者sendMessage方法，向消息队列中插入一条消息。

postDelayed：定时执行一次任务，runnable封装成callback，handlemessage时执行callback
会根据delay时间去进行一个时间排序，**通过遍历单链表把delay消息插入到合适的位置，然后通过nativePollOnce进行休眠，如有新消息进入则调用nativeWake唤醒，然后再计算时间差进行休眠，直到msg.when >= now为止**

post方法与send方法的区别

在handler从MessageQueue中取出Message的过程中，可以看到不同的回调过程，源码如下：
```java
public void dispatchMessage(Message msg){
  //如果是post,callback不为空，直接进入handleCallback
  if(msg.callback != null){
    handleCallback(msg);
    }else{
      //如果是sendMessage，且创建handler时没有传入callback，则callback为空，直接进入handleMessage,也就是我们自己复写的处理Message的方法
      if(mCallback !=null){
        if(mCallback.handleMessage(msg)){
          return;
        }
      }
    handleMessage(msg);
    }
}
//直接run并不会启动新线程，所以这就是post的runnable里面可以直接更新UI的原因
private static void handleCallback(Message msg){
  msg.callback.run();
  }

```


（1）post一类的方法发送的是Runnable对象，但是最后还是会被封装成Message对象，将Runnable对象赋值给Message对象中的callback字段，然后交由sendMessageAtTime()方法发送出去。
在处理消息时，会在dispatchMessage()方法里首先被handleCallback(msg)方法执行，实际上就是执行Message对象里面的Runnable对象的run方法。

（2）sendMessage一类方法发送的消息直接是Message对象，处理消息时，在dispatchMessage里优先级会低于handleCallback(msg)方法，是通过自己重写的handleMessage(msg)方法执行。

（3）可以看出，post方法与send方法本质上并没有什么区别，更多的是使用方法上存在着一些差异。

### postDelay

```java
//enqueue()
、、
boolean enqueueMessage(Message msg, long when) {
.....
	msg.markInUse();  
	msg.when = when;
	Message p = mMessages;  //mMessages当前messagequeue的消息
	boolean needWake;
	 //执行时间比它早
	    if (p == null || when == 0 || when 《 p.when) { 
	        // New head, wake up the event queue if blocked.  
	        msg.next = p;  
	        //插入最前
	        mMessages = msg;  
	        needWake = mBlocked;  
	    } else {  
	        // Inserted within the middle of the queue.  Usually we don't have to wake  
	        // up the event queue unless there is a barrier at the head of the queue        // and the message is the earliest asynchronous message in the queue.        needWake = mBlocked && p.target == null && msg.isAsynchronous();  
	        Message prev;  
	        for (;;) {  
	            prev = p;  
	            p = p.next;  
	            if (p == null || when 《 p.when) {  
	                break;  
	            }  
	            if (needWake && p.isAsynchronous()) {  
	                needWake = false;  
	            }  
	        }  
	        msg.next = p; // invariant: p == prev.next  
	        prev.next = msg;  
	    }  
	  
	    // We can assume mPtr != 0 because mQuitting is false.  
	    if (needWake) {  
	        nativeWake(mPtr);  
	    }  
	}  
	return true;
}

```
现在整个调用流程就比较清晰了，以刚刚的问题为例：

1. `postDelay()`一个10秒钟的Runnable A、消息进队，MessageQueue调用`nativePollOnce()`阻塞，Looper阻塞；
2. 紧接着`post()`一个Runnable B、消息进队，**判断现在A时间还没到、正在阻塞，把B插入消息队列的头部（A的前面），然后调用`nativeWake()`方法唤醒线程；**
3. 
4. `MessageQueue.next()`方法被唤醒后，重新开始读取消息链表，第一个消息B无延时，直接返回给Looper；
5. Looper处理完这个消息再次调用`next()`方法，MessageQueue继续读取消息链表，第二个消息A还没到时间，计算一下剩余时间（假如还剩9秒）继续调用`nativePollOnce()`阻塞；
6. 直到阻塞时间到或者下一次有Message进队；

这样，基本上就能保证`Handler.postDelayed()`发布的消息能在相对精确的时间被传递给Looper进行处理而又不会阻塞队列了。

### sendMessageAtFrontOfQueue

 ```java

        public final boolean sendMessageAtFrontOfQueue(Message msg) {
            MessageQueue queue = mQueue;
            if (queue == null) {
                RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
                Log.w("Looper", e.getMessage(), e);
                return false;
            }
            return enqueueMessage(queue, msg, 0);
        }
```
用于向MessageQueue插队消息的方法，它会将Message对象插入到MessageQueue中的最前列，以保证插入的消息最先被Looper取出来，交给Handler处理

1、将Handler对象持有的MessageQueue对象保存在局部变量中

首先获得Handler持有的MessageQueue对象mQueue交给临时变量queue存储

2、检查消息队列，处理未创建消息队列的情况

判断MessageQueue能否为空，创建RuntimeException对象，然后并没有抛出这个异常对象，只是会在logcat中打印这条异常日志的情况，最后返回false代表发送Message失败。



看needWake的赋值操作，**needWake = mBlocked && p.target == null && msg.isAsynchronous();**这个条件判断是比较严格的，什么时候才会唤醒队列呢？队列是睡眠状态并且队列的第一条消息的target为空(Handler)，待插入的msg为异步消息时才会唤醒队列。

## 18.4 HandlerThread
```java
new HandlerThread().getLooper()//可以获取到一个子线程的looper对象

```
HandlerThread本质上是一个**线程类**，它继承了Thread；
HandlerThread有**自己的内部Looper对象**，可以进行looper循环；
**通过获取HandlerThread的looper对象传递给Handler对象，可以在handleMessage方法中执行异步任务。**
创建HandlerThread后必须先调用HandlerThread.start()方法，Thread会先调用run方法，创建Looper对象。

###  HandlerThread原理

当系统有多个耗时任务需要执行时，每个任务都会开启个新线程去执行耗时任务，这样会导致系统多次创建和销毁线程，从而影响性能。  
为了解决这一问题，Google提出了HandlerThread，HandlerThread本质上是一个线程类，它继承了Thread。  
HandlerThread有自己的内部Looper对象，可以进行loopr循环。通过获取HandlerThread的looper对象传递给Handler对象，可以在handleMessage()方法中执行异步任务。创建HandlerThread后必须先调用HandlerThread.start()方法，Thread会先调用run方法，创建Looper对象。当有耗时任务进入队列时，则不需要开启新线程，在原有的线程中执行耗时任务即可，否则线程阻塞。  
它在Android中的一个具体的使用场景是IntentService。  
由于HanlderThread的run()方法是一个无限循环，因此当明确不需要再使用HandlerThread时，可以通过它的quit或者quitSafely方法来终止线程的执行。

---

### HanlderThread的优缺点

- HandlerThread优点是异步不会堵塞，减少对性能的消耗。
    
- HandlerThread缺点是不能同时继续进行多任务处理，要等待进行处理，处理效率较低。
    
- HandlerThread与线程池不同，HandlerThread是一个串队列，背后只有一个线程。
    

  
  
作者：千夜零一  
链接：https://www.jianshu.com/p/fdd9f93da885  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### 线程阻塞和唤醒/nativePollOnce和wake()
主线程的死循环一直运行是不是特别消耗CPU资源呢？ 
其实不然，这里就涉及到Linux pipe/epoll（管道）机制，
简单说就是在主线程的MessageQueue没有消息时，**便阻塞在looper的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生nativeWake()，通过往pipe管道写端写入数据来唤醒主线程工作。**

![[682616-20200517210649824-804004537.png]]

这里采用的epoll机制，==是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作==，本质同步I/O，即读写是阻塞的。 所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。

我们需要引入**IO多路复用**的概念。==多路复用是指使用一个线程来检查多个文件描述符（Fd）（Socket）的[就绪状态，比如调用select和[poll函数]，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时==。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用[线程池]。
**io多路复用**也叫**事件驱动模型**。
[[#4. 管道机制]]
**多路是指**？多个业务方（句柄）并发下来的 IO 。

**复用是指**？复用这一个后台处理程序。


#### Handler为何采用管道而非Binder？

handler不采用Binder，并非binder完成不了这个功能，而是太浪费CPU和内存资源了。因为Binder采用C/S架构，一般用于不同进程间的通信。

- 从内存角度：通信过程中Binder还涉及一次内存拷贝，handler机制中的Message根本不需要拷贝，本身就是在同一个内存。Handler需要的仅仅是告诉另一个线程数据有了。

- 从CPU角度，为了Binder通信底层驱动还需要维护一个binder线程池，每次通信涉及binder线程的创建和内存分配等比较浪费CPU资源。

从上面的角度分析可得，Binder用于进程间通信，而Handler消息机制用于同进程的线程间通信，Handler不宜采用Binder。

[](https://www.cnblogs.com/renhui/p/12875396.html#_labelTop)

#### Android 6.0及以后的机制

在Android 6.0及以前的版本使用管道与epoll来完成Looper的休眠与唤醒的。

在Android 6.0及以后的新版本中使用的是eventfd与epoll来完成Looper的休眠与唤醒的。

感兴趣的可以进一步的学习和了解管道的知识及eventfd的知识，并比较一下两种机制的优劣，进而明白Android官方为何对此机制进行调整。

#### 为什么不用 java的wait 而用 epoll 呢？

说起来 java 中的 wait / notify 也能实现阻塞等待消息的功能，在 Android 2.2 及以前，也确实是这样做的。
那为什么后面要改成使用 epoll 呢？通过看 commit 记录，是需要处理 native 侧的事件，所以只使用 java 的 wait / notify 就不够用了。


## **18.5 消息屏障SyncBarrier**
### SyncBarrier的介绍

我们知道无论是应用启动还是屏幕刷新都需要完整绘制整个页面内容，目前大多数手机的屏幕刷新率为60Hz，也就是耳熟能详的16ms刷新一次屏幕。那么问题来了，如果主线程的消息队列待执行的消息非常多，怎么能保证绘制页面的消息优先得到执行，来尽力保证不卡顿呢

SyncBarrier大家又称它为同步屏障，这是安卓线程消息队列里面的一个新增加的东西，它是一种Handler中的同步屏障机制。**简单可以理解安卓在Hanlder的处理上增加了优先级，优先级最高的就是SyncBarrier。**


Handler中的Message可以分为两类：
- 同步消息体（优先级高）
- 异步消息体（优先级低）

new XXXHandler(Loop loop, false/true)
可以通过`Message.java`的`isAsynchronous()`知道是否为异步消息体

### SyncBarrier的作用
SyncBarrier可以通过MessageQueue.postSyncBarrier()发送一个同步消息体，**该消息唯一的区别点在于Message没有target**

```java
private int postSyncBarrier(long when) {
    // Enqueue a new sync barrier token.
    // We don't need to wake the queue because the purpose of a barrier is to stall it.
    synchronized (this) {
        final int token = mNextBarrierToken++;
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;

        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when  《= when) {
                prev = p;
                p = p.next;
            }
        }
        if (prev != null) { // invariant: p == prev.next
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }
        return token;
    }
}
```

当队列中出现SyncBarrier（具体实现上就是Message#target为null）时，**就会忽略所有同步消息体，寻找异步消息体，然后优先处理它**，这些API全部都是hide的，也就是说app中是无法使用的，谷歌设计初衷也是系统开发人员自己用的

消息队列这东西是在安卓一诞生就有了的东西，大部分时候它也没有什么问题。但有一个事情，就是安卓操作系统的UI流畅度远不及水果平台（iOS），原因就是在于水果平台的UI渲染是整个系统中最高优先执行。于是就有了SyncBarrier机制，这东西就是为了让消息队列有优先级，它发送的消息将会是最高优先级的，会被优先处理，这样来达到UI优先渲染，达到提高渲染速度的目的

**对于异步消息，Looper会遍历消息队列找到异步消息执行，确保像刷新屏幕等高优任务及时得到执行。同步消息得不到处理，这就是为什么叫同步屏障的原因。当使用了同步屏障，记得通过`removeSyncBarrier`移除，不然同步消息不能正常执行。**

**当然，正常情况开发者也不能手动发送和移除同步屏障，因为它们都被hide注释了**

### SyncBarrier的发送

通常我们使用Handler发消息时，**这些消息都是同步消息体，如果我们想发送异步消息体，**那么在创建Handler时使用以下构造函数中的其中一种(async传true)，通过该Handler发送的所有消息都会变成异步消息体

### SyncBarrier的应用

前面说到SyncBarrier并不是给app开发同学用的，很多相关的接口并没有开放出来，这是为了提高UI渲染而设计的东西。因此这东西主要是用在了UI渲染过程中。**仔细查看ViewRootImpl的源码可以发现，每次渲染View之前都会先给主线程插入SyncBarrier**，以挡住异步消息体，保证渲染被主线程优先执行

### SyncBarrier的泄露

Barrier消息像一道栅栏，将消息队列里的普通消息先拦住，多数情况下是正常，但一旦异常，则很容易发生ANR，且ANR的trace都是莫名其妙的，但是也有些情况，是Barrier引起的trace就停在nativePollOnce()，当然这里指的是小部分情况，而非所有的nativePollOnce()都是SyncBarrier引起的，具体情况具体分析

    正常情况：渲染刷新类先优先执行，等执行完以后，撤掉栅栏，普通消息（包括会导致ANR的消息）得以继续执行
    异常情况：Barrier存在泄漏，导致无法释放栅栏，普通消息卡住不动，UI假死，如果期间有Server或者Provider等消息超时，就会引发ANR

一旦发生Barrier的泄露，在取消息的时候优先进入同步屏障的逻辑，**主线程会过滤掉所有非异步消息!msg.isAsynchronous()**，一直在死循环中出不来，只有移除当前的同步屏障后，才得以解开
```java
if (msg != null && msg.target == null) {
    do {
        prevMsg = msg;
        msg = msg.next;
    } while (msg != null && !msg.isAsynchronous());
}
```


如下图，正常情况下是执行1，4，5，2，3，6，而异常情况是Barrier在此没有被移除，导致2，3，6都无法执行

![[6f29502f8ffe416ead3d5c6f571fc31d.png]]

### 如何避免Sync Barrier搞鬼
（摘自博文，触发可能性存疑，因为postinvalidate会做handler进行线程切换）
前面提到过，这套东西都是Frameworks层内部的机制，并没有开放给app使用，而Frameworks内部的逻辑一般来说还是相当健壮的，绝大多数时候并不会出问题。当然了，各个厂商内部搞的各种所谓优化，倒是有可能会引发问题。

在实际开发过程中，引发Sync barrier的最多场景就是自定义View。**对于自定义View，是能够在非主线程调用其invalidate的，当有大量的非主线程调用invalidate时，就有可能恰好与主线程的渲染发生交互**，具体case非常corner要刚巧非主线程在postInvalide，**然后主线程也刚巧在发送异步消息，就可能使得Sync barrier没有被移除，从而导致问题。**
[[#2.2.2 activity启动流程]]
[[#7.2 View绘制流程]]

这就需要我们在编码阶段做好封装，对于自定义View的刷新触发逻辑做好封装，做一下线程切换，以保证是在主线程里面执行invalidate。因为暴露出去的接口，是没有办法控制的，你没有办法让所有调用者都在主线程里面调用你的接口

## 18.6 View.post

1. 更新 UI 操作
2. 获取 View 的实际宽高

详细：[[#3）”黑科技“——View.post（）]]



#  19.ARoute和组件化
[[#52.插件化]]
![[v2-1e7d90a3f802a46d391f88f599006b47_b.webp]]

关键词：postcard，注解，inject（this），apt生成路径，反射



https://blog.csdn.net/dingshuhong_/article/details/104700096


## 示范
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Aroute.getInstance().init(this);
    }

    public void tologin(View view) {
    //直接跳转
        Aroute.getInstance().jumpActivity("app/Component",null);
    }
}
```
```dart
// 需要接收消息的组件，要注册Router注解
@Route(group = "app", path = "/app/Component")
public class ComponentActivity extends AppCompatActivity {

// 通过Router直接发送事件

// 构造参数
Bundle bundle = new Bundle();
        bundle.putString("data", "data from other module");
        // 发送事件
        ARouter.getInstance()
                .build("/app/Component") // 跳转页面路由地址
                .with(bundle) // 传参
                .navigation();
```


## 原理简介
Aroute用到的是apt技术，也就是编译时技术。在编译期执行，包括注解和注解处理器
利用注解、和注解处理工具（APT）
在==编译期间通过**APT动态生成含有类路由信息的映射文件==，在运行期间通过固定的PATH对映射文件进行加载**（并且在这里Arouter进行了优化，也就是分组管理、按需加载；实际上是先加载了root文件，然后按需加载group文件），将所有路由文件都存放到本地仓库中，当用户需要时可以通过路由地址在仓库中找到需要的类信息。然后实现项目中的跳转与服务获取。


通过 `ARouter.init` ，可以对 ARouter 进行初始化，它主要分为两个步骤
1. 遍历 Apk 的 dex 文件，查找存放自动生成类的包下的类的 ClassName 集合。其中为了加快查找速度，==通过一个线程池进行了异步查找，并通过 CountDownLatch 来等待所有异步查找任务的结束。==这个查找过程在非 debug 模式下是有缓存的，因为 release 的 Apk 其自动生成的类的信息必然不会变化
2. 根据 ClassName 的类型，分别构建 RouteRoot、InterceptorGroup、ProviderGroup 的对象并调用了其 loadInto 方法将这些==Group 的信息装载进 Warehouse==（group其实就是为什么path至少要是二级路径 @ARoute("/path/xxActivity")），这个过程并不会将具体的 RouteMeta 装载。这些 Group 中主要包含了一些其对应的下一级的信息（如 RouteGroup 的 Class 对象等），之后就只需要取出下一级的信息并从中装载，不再需要遍历 dex 文件。

第一步生成的中间都放在同一个包路径下， 因此生成路由表的操作，其实就是，遍历这些生成类，然后反射创建对象，并调用loadInto方法，将数据保存到仓库WareHouse
这步有两种方式
        初始化（运行时）耗时遍历base.apk,扫描到这个两个类，就直接反射创建。
        gradle插件(auto-register)：编译期利用ASM操作transforms文件夹下的字节码）实现
        
![[20190110195935498.png]]
![[611753d2a2db4e49a04b8734ab0f9668.png]]


## 注解类型

    @Route ： 用于描述路径，类被该注解标识才能被自动生成路由信息加入到路由表中。
    @AutoWired : 用于标识参数，在该页面被路由打开时，自动赋值传递的参数值
    @Interceptor : 拦截器，可以自定义实现跳转拦截


## SPI——Autoservice切面组件注册
原理：AutoService会自动在==META-INF文件夹下生成Processor配置信息文件==，该文件里就是实现该服务接口的具体实现类。而当外部程序装配这个模块的时候， 就能通过该jar包==META-INF/services/==里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。
(APT 也需要这个)
-  添加依赖

```
 implementation 'com.google.auto.service:auto-service:1.0-rc6'
    kapt "com.google.auto.service:auto-service:1.0-rc6"
kotlin-kapt'
```


 - 使用 @AutoService 注解
```kotlin
public interface BaseShayPlugin {  
    boolean initPlugin();  
}

@AutoService(BaseShayPlugin::class)  
class FirstPlugin:BaseShayPlugin {  
    override fun initPlugin(): Boolean {  
        Log.d("就那样", "FirstPlugin初始化")  
        return true  
    }  
}
```
 - 加载被注解的类
 ```kotlin
 object LoadInitUtil {  
    suspend fun initAll(){  
        val shayPlugins = ServiceLoader.load(BaseShayPlugin::class.java).toList()  
        shayPlugins.asFlow()  
            .filter { true }  
            .collect{//自动初始化所有组件  
                it.initPlugin()  
            }  
    }  
}
 ```

# 20. 响应式编程 RXJAVA

## 20.1 概念

主要目的就是实现异步，可以非常简便地实现异步调用，在复杂的代码调用中仍然可以保持阅读性。提供了很多操作符，减少了handler的获取message的麻烦。

设计原理主要使用了观察者模式的变种，只有一个观察者。



**有四个关键API：**

1. Observable：在观察者模式中称为“被观察者”；有三种常见的类型，fromArray(数组中元素作为onNext)，just(单个事件)，create(发送自定义事件)

2. Observer：观察者模式中的“观察者”，可接收Observable发送的数据；有四个关键方法，onSubscribe， onNext，onError（），onComplete()

3. subscribe：订阅，观察者与被观察者，通过subscribe()方法进行订阅； 

4. Subscriber：也是一种观察者，在2.0中 它与Observer没什么实质的区别. 不同的是 Subscriber要与Flowable(也是一种被观察者)联合使用，该部分内容是2.0新增的，后续文章再介绍。Obsesrver用于订阅Observable，而Subscriber用于订阅Flowable

   

5. subjectSubject在同一时间内，既可以作为Observable，也可以作为Observer
6. 特殊操作符实际上也是新建一个Observable，将上个事件消费处理并传入新建的Observerable

```java
/*     PublishSubject只会给在订阅者订阅的时间点之后的数据发送给观察者。

* BehaviorSubject在订阅者订阅时，会发送其最近发送的数据（如果此时还没有收到任何数据，它会发送一个默认值）。

* ReplaySubject在订阅者订阅时，会发送所有的数据给订阅者，无论它们是何时订阅的。

* AsyncSubject只在原Observable事件序列完成后，发送最后一个数据，后续如果还有订阅者继续订阅该Subject, 则可以直接接收到最后一个值。
*
* ReplaySubject 和 BehaviorSubject 都有粘性的特点，但是Behavior无法保证接收一个事件是需要的event*/
```



## 20.2 基本使用

定义Observable（被观察者），重写subscribe()；
本质上就是subscribe内传入observer，在上游的被观察者中被调用onNext，onComplete等方法。

observer观察者，进行通知接收。
**如果是其他操作符其实也是返回一个new了一个Obeserver的子类型对象，只有调用subscribe之后才会被激活递归调用。**

进行订阅

```java
//被观察者
Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exceptin {
                //执行一些其他操作
                //.............
                //执行完毕，触发回调，通知观察者
                e.onNext("我来发射数据");
                //e.onError("error");
                //
            }
        });
//观察者
Observer<String> observer = new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            //观察者接收到通知,进行相关操作
            public void onNext(String aLong) {
                System.out.println("我接收到数据了");
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        };

//被观察者 被 观察者订阅
obserable.subscribe(observer)；
```

filtermap，对被观察者进行过滤操作。还有map等可以过滤操作

```java
Observable.just(list).flatMap(new Function<List<String>, ObservableSource< ?>>() {
            @Override
            public ObservableSource< ?> apply(List<String> strings) throws Exception {
                return Observable.fromIterable(strings);
            }
        }).filter(new Predicate<Object>() {
            @Override
            public boolean test(Object s) throws Exception {
                String newStr = (String) s;
                if (newStr.charAt(5) - '0' > 5) {
                    return true;
                }
                return false;
            }
        }).subscribe(new Consumer<Object>() {
            @Override
            public void accept(Object o) throws Exception {
                System.out.println((String)o);
            }
        });
```



### **操作符汇总**

![image-20220909120413866](image-20220909120413866.png)





## 20.3 线程切换



  **Scheduler** 相当于线程控制器（静态线程池），RxJava 通过它来指定每一段代码应该运行在什么样的线程。**实现底层也实际是Handler进行通信。**
observeOn()之后的才是


1. **Schedulers.immediate():直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。**
2. **Schedulers.newThread(): 总是启用新线程，并在新线程执行操作**
3. **Schedulers.io():I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler,多数情况下 io() 比 newThread() 更有效率**
4. Schedulers.computation():计算所使用的 Scheduler,不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。
5. Android 还有一个专用的AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。
6. subscribeOn(): 指定Observable(被观察者)所在的线程，或者叫做事件产生的线程。
7. observeOn(): 指定 Observer(观察者)所运行在的线程，或者叫做事件消费的线程。
8. **Disposable, 这个单词的字面意思是一次性用品,用完即可丢弃的。在RxJava中,用它来切断Observer(观察者)与Observable(被观察者)之间的连接，当调用它的dispose()方法时, 它就会将Observer(观察者)与Observable(被观察者)之间的连接切断, 从而导致Observer(观察者)收不到事件。subscribe之后就是返回一个该类型**

## 

## 20.4 retrofit + okhttp + rxjava

```
Retrofit retrofit = new Retrofit.Builder()
        .client(client)
        //添加GSON解析：返回数据转换成GSON类型
        .addConverterFactory(GsonConverterFactory.create())
          // 针对rxjava2.x
          .addCallAdapterFactory(RxJava2CallAdapterFactory.create())//适配器模式
        .baseUrl(baseUrl)
        .build();
        
XXservice service = retrofit.create(XXservice);
生成Observable对象
```



编写service接口，serviceMethod中的信息会生成一个 **OkHttpCall** 接下来就是Call里面的 RxJava 的观察者(obverser)与被观察者(obverseable) ,来完成线程之间的调度 

OkHttp请求网络可以分成三大步

1.队列

2.线程池

3.网络拦截器

OKhttp会通过dispatch事件分发器将请求对象发送到RealCall 类，**将不同的请求放到不同的请求队列当中 同步的请求放置同步请求队列，异步请求放置异步请求队列，在走异步请求队列时会同时缓存一条记录到异步执行队列，最后通过dispatch分发器发送线程池中执行** ，我们**OkHttp所用的线程就是缓存线程池**

**Ok的拦截器总共分为七个，在执行线程的时候就会走Interceptor。**

**最后****生成一个响应体Body由通过被观察者发送一个**事件**(just)**  



## 20.5 源码分析
## Rxjava2.0背压
为什么有背压？是为了解决短时间上游发送太次数据，下游来不及处理，那么消息队列就会越来越长。

**背压模式，上游同步发射总结：**

- **ERROR:上游发射事件不能超过128个，并且上游发射的事件如果下游没有及时处理(调用request()方法)，都会抛出异常。**
- **BUFFER:该模式下不会出现异常，并且发射的事件数量不受限制**
- **DROP:上游发射事件超过128个，将会丢弃掉剩余的事件**
- **LASTEST:上游发射事件超过128个，将会丢弃掉缓存池所有的事件，但是仍然会缓存发送最后的一次事件。**

#  21.动画

## 1.native动画

帧动画、补间动画、属性动画 
- 逐帧动画【Frame Animation】，即顺序播放事先做好的图像，跟电影类似
- 补间动画【Tween Animation】，即通过对场景里的对象不断做图像变换 ( 平移、缩放、旋转 ) 产生动画效果
- 属性动画【Property Animation】，补间动画增强版，支持对对象执行动画
- 过渡动画【Transition Animation】,实现Activity或View过渡动画效果
  
  

![](微信截图_20210314010524%201.png)

==animatior补间动画：我们一般是通过animation xml获取到animation再进行播放==
==animation属性动画：通过animator进行数值变化的监听并进行操作，或者使用ObjectAnimator进行操作。==
```dart

//直接播放一系列的animator
AnimatorSet animatorSet = new AnimatorSet();
        //移动
        ObjectAnimator ty = ObjectAnimator.ofFloat(btn, "translationY", 0,300);
        ty.setDuration(1000);
        //旋转
        ObjectAnimator ry = ObjectAnimator.ofFloat(btn, "rotationY", 0,1080);
        ry.setDuration(1500);
        //透明度
        ObjectAnimator alpha = ObjectAnimator.ofFloat(btn, "alpha", 1,0,0.5f,1);
        alpha.setDuration(2000);
        //缩放
        ObjectAnimator sx = ObjectAnimator.ofFloat(btn, "scaleX", 1,0.5f);
        alpha.setDuration(1000);
        //一起播放
//      animatorSet.playTogether(items);
        animatorSet.play(ry).with(sx).after(ty).before(alpha);
        animatorSet.start();
```

## 2.动画库

https://jfson.github.io/2018/01/08/41-anim/

| 动画库        | Lottie                  | SVGA                                  | Keyframes                                              | Squall                                                    | Spine           |
| ------------- | ----------------------- | ------------------------------------- | ------------------------------------------------------ | --------------------------------------------------------- | --------------- |
| 支持平台      | Android/iOS/Web         | Android/iOS/Web                       | Android/iOS                                            | iOS                                                       | Android/iOS/Web |
| 设计工具支持  | After Effects           | AE & Flash                            | AE                                                     | AE                                                        | AE              |
| 功能边界      | 所有                    | 部分                                  | 矢量图                                                 | 矢量图                                                    | 大部分          |
| 导出工具      | 插件                    | 插件                                  | 脚本                                                   | 插件                                                      | 单独的设计工具  |
| 设计成本      | 需要命名规范            | 无                                    | 需要脚本                                               | 插件                                                      | 单独的设计工具  |
| 资源包大小zip | 2.6M                    | 767k                                  |                                                        |                                                           | 2M              |
| 收费          | N                       | N                                     | N                                                      | Y                                                         | Y               |
| 官网          | [地址](http://svga.io/) | [地址](https://airbnb.design/lottie/) | [地址](https://facebookincubator.github.io/Keyframes/) | [地址](http://www.marcuseckert.com/squall/documentation/) |                 |

- 以上基本排除了Keyframes 和 Squall。



建议采用SVGA或者Lottie

### SVGA 动画库源码思路

- 一帧一帧
- 通过设置帧率，来生成一个配置文件，使得每一帧都有一个配置，每一帧都是关键帧，通过帧率去刷每一帧的画面，这个思路跟gif很像，但是通过配置使得动画过程中图片都可以得到复用。性能就提升上来了。并且不用解析高阶插值（二次线性方程，贝塞尔曲线方程）

### Lottie 动画库源码思路

- 一层一层
- 完全按照设计工具的设计思路来进行还原，将动画脚本导出并解析。动画脚本非常的轻量。
- 将所有的动画拆成多个层级,每个层级layer都有一个动画配置，播放时解析多个layer的配置，并给每个layer做相应的动画。也达到了图片可以复用。当需要解析高阶插值（二次线性方程，贝塞尔曲线方程）时，性能相对而言差一点。

### SVGA

##### 设计成本

- SVGA目不支持种类：

  - 不支持复杂的矢量形状图层
  - AE自带的渐变、生成、描边、擦除…
  - 对设计工具原生动画不友好，对图片动画友好(适合礼物场景)

- 导出工具

  开源

  ##### 开发成本

- 1.优点

  - 资源包小
  - 测试工具齐全
  - 三端可用
  - 回调完整
  - Protobuf 序列化结构数据格式，序列化的数据体更小，传递效率比xml,json 更高。

- 2.缺点

  - 每个礼物播放时都去新解压，需要改一套缓存策略
  - svga 用zlib打包(字节流数据压缩程序库)，不方便解压和追踪包内容。

- 4.插入动画头像功能

  - 支持，需定义一套专属的头像配置的协议。

    ### Lottie

    ##### 设计成本

- 1.Lottie 不支持的设计：

  - 基本满足所有种类的矢量动画和图片动画。

  - 有导出插件

    #### 开发成本

- 1.优点

  - 三端可用
  - 回调完整
  - 项目已经存在一套缓存逻辑
  - 当前的库可以满足业务需求，不需要二次开发

- 2.缺点

  - 资源包相较SVGA而言会大一倍多
  - 图片需要重命名 & 偶先播不出来动效。

- 4.插入动画头像功能

  - 已经支持

# 22.MVC, MVP, MMVM

lifecyle组件viewmodel：https://www.jianshu.com/p/35d143e84d42

## viewmodel
### 解决问题
### 数据持久化

我们知道在屏幕旋转的 时候 会经历 activity 的销毁与重新创建，这里就涉及到数据保存的问题，显然重新请求或加载数据是不友好的。在 ViewModel 出现之前我们可以用 activity 的onSaveInstanceState()机制保存和恢复数据，但缺点很明显，onSaveInstanceState只适合保存少量的可以被序列化、反序列化的数据，假如我们需要保存是一个比较大的 bitmap list ，这种机制明显不合适。  
由于 ViewModel 的特殊设计，可以解决此痛点。



## MVC

**MVC**全名是Model--View--Controller，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。其中Model层处理数据，业务逻辑等；View层处理界面的显示结果；Controller层起到桥梁的作用，来控制View层和Model层通信以此来达到**分离视图显示和业务逻辑层** 

**在android中我们常常把activity的默认为是mvc中的controller**



## MVP

**MVP**模式，由mvc模式演化而来，将MVC中的Controller改为了Presenter，View通过接口与Presenter进行交互，降低耦合，方便进行单元测试。让activity与数据逻辑处理解耦。 交由presenter层处理，presenter层通知v层更新。model层进行数据访问和存储。

在android中一般定义一个契约concat接口，在接口里声明presenter接口的方法和对应acitvity/fragemnt的接口方法，这样便于浏览和管理。再去实现接口类。

优点：

UI层和逻辑层分离，UI层不在涉及业务逻辑代码，某层的改动不需要到处去修改代码；

**测试很方便，你可以直接调用Presenter层写测试用式（可以使用Junit框架）**；

**最后是可维护性和可扩展性，MVP的各个类职责都非常明确且单一，后期的扩展，维护都会更加容易。**

## MVVM

和MVP模式相比，MVVM 模式用**ViewModel替换了Presenter**  ，创建关联，将model和view绑定起来,如此之后，我们model的更改，通过viewmodel反馈给view,从而自动刷新界面。 

通常与databinding和viewmodel结合

### ViewModel



viewmodel通常与livedata结合使用。==并且使用viewmodel的activtiy需要继承于ComponentActivity==
	当页面重建完成,ActivityThread执行了performLaunchActivity方法时，会调用Activity的attach方法,便会把刚刚存储的数据，传递进去。

```java
private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
    //传递之前储存的数据
    activity.attach(......, r.lastNonConfigurationInstances,.....);
  }
```
有时候会用于fragment/activtiy之间的通信

**livedata需要传入ViewModelStoreOwner（lifecycle下的组件），和obsever。一个用于观察生命周期，及时移除观察者（acti，frag）。**

setvalue（主线程）或者postvalue（任意线程，最后还是setvalue）改变其值。

```java
    @MainThread
    public void observe(@NonNull LifecycleOwner owner, @NonNull Observer<T> observer) {
        if (owner.getLifecycle().getCurrentState() == DESTROYED) {
            // ignore
            return;
        }
        LifecycleBoundObserver wrapper = new LifecycleBoundObserver(owner, observer);
        LifecycleBoundObserver existing = mObservers.putIfAbsent(observer, wrapper);
        if (existing != null && existing.owner != wrapper.owner) {
            throw new IllegalArgumentException("Cannot add the same observer"
                    + " with different lifecycles");
        }
        if (existing != null) {
            return;
        }
        owner.getLifecycle().addObserver(wrapper);
    }
```



##### ViewModelStoreOwner和ViewModelStore

`ViewModelStoreOwner`就是一个接口，而`ViewModelStore `则有了一些内容：它通过一个HashMap持有并管理者ViewModel，并且提供了增加和获取ViewModel的方法，以及清空HashMap和所有ViewModel的方法。结合它们名字中的Store。

activty是实现了viewmodelstoreOwner接口，

作者：白瑞德
链接：https://juejin.cn/post/7044416943498985485



#### ViewModel 的生命周期

[`ViewModel`](https://developer.android.com/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 对象存在的时间范围是获取 [`ViewModel`](https://developer.android.com/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 时传递给 [`ViewModelProvider`](https://developer.android.com/reference/androidx/lifecycle/ViewModelProvider?hl=zh-cn) 的 [`Lifecycle`](https://developer.android.com/reference/androidx/lifecycle/Lifecycle?hl=zh-cn)。[`ViewModel`](https://developer.android.com/reference/androidx/lifecycle/ViewModel?hl=zh-cn) ==将一直留在内存中，直到限定其存在时间范围的 [`Lifecycle`](https://developer.android.com/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 永久消失：对于 Activity==，是在 Activity 完成时；而对于 Fragment，是在 Fragment 分离时。



`ViewModel`的生命周期是通过`Lifecycle`与Activity绑定的。
通过==onRetainNonConfigurationInstance==生命周期来保持重启的数据
**每当activity销毁时，onDestory方法就会通过Lifecycle调用ViewModelStore的clear方法，从而达到销毁ViewModel的目的**

![[af19580c836f4d8299742822d16416e9~tplv-k3u1fbpfcp-zoom-in-crop-mark 4536 0 0 0.png]]



需要注意的是：



A. 一般来讲，LiveData 是需要配合 ViewModel 来使用的，但千万不要觉得 LiveData 就一定结合  ViewModel。上面也说道二者只是功能互补。这里为了便于理解，我们先单独学习下 LiveData 的使用。

LiveData 的使用分三步：

1. 创建一个 LiveData 的实例，让它持有一种特定的数据类型，比如 String 或者 User .通常是将 LiveData 放在ViewModel中使用的（这里我们先单独使用）。
2. 创建一个 Observer 对象，并实现其 onChanged(…) 方法，在这里定义当 LiveData 持有的数据发生改变的时候，应该做何操作。可以在这进行UI的更新，一般 Observer 是在 UI controller 中创建，比如 Activity 或者 Fragment 。
3. 通过创建的 LiveData 实例的 observe(…)方法，将 Observer 对象添加进 LiveData 中。方法的原型为`observe( LifecycleOwner owner, Observer observer)`，第一个参数是 LifecycleOwner对象，这也是 LiveData 能监听生命周期的能力来源。第二个参数就是我们的监听器对象 Observer 

B.LiveData 的作用是在使得数据能具有生命周期感知能力，在 Activity 等变为活跃状态的时候，自动回调观察者中的回调方法。也就是说对数据的变化进行实时监听。而 ViewModel 的作用则是，当因系统配置发生改变导致 Activity 重建的时候（比如旋转屏幕），能对 LiveData 进行正确的保存和恢复

当activity/fragment重建时，重新订阅viewmodel会再次收到事件

总结自： https://juejin.cn/post/6844903814173212680
## 对比onSaveInstanceState
onSaveInstanceState对比，不同之处有如下几点：
1. 保存对象限制
==onSaveInstanceState只能保存Bundle对象，而onRetainNonConfigurationInstance则是Object。==
2. 保存的生命周期长短，==onSaveInstanceState可以保存到AMS内部==，就算被系统意外杀死，待下次重启Activity的时候恢复状态数据；==而onRetainNonConfigurationInstance只是本地应用保存==，如果被系统杀死则无法保存数据。

3. onSaveInstanceState为什么能保存？
首先是应用会在stop Activity 的时候把state数据保存在AMS 的 ActivityRecord的icicle变量中， 就算系统杀死应用 ，也不会影响AMS的内部ActivityRecord存储
可以通过dumpsys activity activities 命令查看AMS 的activity 堆栈信息， 在杀死应用前后 ActivityRecord 的对象地址一直未变化；再次启动应用 会复用ActivityRecord信息。

##  “状态” 与 “事件”
在 MVVM 架构中，我们通常使用 LiveData 或者 StateFlow 实现 ViewModel 与 View 之间的数据通信，它们具备的响应式机制非常适合用来向 UI 侧发送更新后的状态（State），但是同样用它们来发送事件（Event），当做 EventBus 使用就不妥了
虽然“状态”和“事件”都可以通过响应式的方式通知到 UI 侧，但是它们的消费场景不同：

    状态（State）：是需要 UI 长久呈现的内容，在新的状态到来之前呈现的内容保持不变。比如显示一个Loading框或是显示一组请求的数据集。
    事件（Event）：是需要 UI 即时执行的动作，是一个短期行为。比如显示一个 Toast 、 SnackBar，或者完成一次页面导航等。

我们从覆盖性、时效性、幂等性等三个维度列举状态和事件的具体区别
	

状态
	

事件

覆盖性
	

新状态会覆盖旧状态，如果短时间内发生多次状态更新，可以抛弃中间态只保留最新状态即可。这也是为什么 LiveData 连续 postValue 时会出现数据丢失。
	

新事件不应该覆盖旧事件，订阅者按照发送顺序接收到所有事件，中间的事件不能遗漏。

时效性
	

最新状态是需要长久保持的，可以被时刻访问到，因此状态一般是“粘性的”，在新的订阅出现时为其发送最新状态。
	

事件只能被消费一次，消费后应该丢弃。因此事件一般不是“粘性”的，避免多次消费。

幂等性
	

状态是幂等的，唯一状态决定唯一UI，同样的状态无需响应多次。因此 StateFlow 在 setValue 时会对新旧数据进行比较，避免重复发送。
	

订阅者需要对发送的每个事件进行消费，即使是同一类事件发送多次。


Jetpack MVVM 七宗罪之四： 使用 LiveData/StateFlow 发送 Events
https://blog.51cto.com/u_15200109/4935605
鉴于事件与状态的诸多差异，如果直接使用 LiveData 或 StateFlow 发送事件，会出现不符合预期的行为。其中最常见的可能就是所谓“数据倒灌”问题。

    我平常不太喜欢使用 “数据倒灌” 这个词，主要是“倒”这个字与单向数据流思想相违背，容易引起误解，我猜测词汇发明者更多的是想用它强调一种“被动”接收吧。

“数据倒灌”问题的发生源于 LiveData 的 "粘性" 设计，同一个订阅者每次订阅 LiveData 都会收到最近的一个事件，因为事件应该具有“时效性”，对于已消费过的事件我们不希望再次响应。

## MVI

# **23.Retrofit**浅析

```
Retrofit retrofit = new Retrofit.Builder()
        .client(client)
        //添加GSON解析：返回数据转换成GSON类型
        .addConverterFactory(GsonConverterFactory.create())
          // 针对rxjava2.x
          .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        .baseUrl(baseUrl)
        .build();
```

Retrofit接口的返回值分为两部分，一部分是前面的**Call**或者**Observable**，另一部分是**String**。
1）addCallAdapterFactory影响的就是第一部分的Call或者Observable，Call类型是默认支持的(内部由DefaultCallAdapterFactory支持)，而如果要支持Observable或者flow，我们就需要自己添加或者自己实现callAdapter



## 流程

建造者模式设置配置，调用create时通过反射获取service api接口方法上的 ==注解 + 形参 的方式等数据==，使用了动态代理的方式生成ServiceMethod方法，并且进行缓存，然后执行对应的方法，
将==对应的方法和参数传入OkHttpCall== ，最终调用Call的enqueue() —> OkHttpCall的enqueue() —> okhttp的enqueue() 。如果需要配置自定义的okhttp拦截器获取日志，则需要添加自己创建的okhttp客户端。

思考：
1. 那么需要把retrofit做成单例吗？
==请求多的时候做成单例比较合适，做一个RetrofitWarrper，这样子调用Retorfit的时候就可以充分利用缓存。单个页面重复某个亲请求多次的时候（比如商品列表），那该activtiy就一直用同一个Retrofit即可。==


2. 那么可以添加多个CallAdapter吗? 
可以，Retofit会自动遍历返回值类型查找对应的callAdapter


## 核心技术
	注解，大量反射，动态代理，泛型


## 注解方法

方法注解：@GET、@POST、@PUT、@DELETE、@PATH、@HEAD、@OPTIONS、@HTTP。
标记注解：@FormUrlEncoded【返回表单】、@Multipart【用于文件上传】、@Streaming【返回数据量特别大使用】。
参数注解：@Query，@QueryMap；@Body；@Field、@FieldMap；@Part，@PartMap。
其他注解：@Path @Header @Headers @Url

## 结构



![img](https://upload-images.jianshu.io/upload_images/625299-29a632638d9f518f.png?imageMogr2/auto-orient/strip|imageView2/2/w/891)



其中反射service的method的存在concurrenthashmap中，进行缓存，减少同一方法反复使用反复

# 24.ANR

ANR:Application Not Responding，即应用无响应，Android系统对于一些事件需要在一定的时间范围内完成，如果超过预定时间能未能得到有效响应或者响应时间过长，都会造成ANR。

一般是会有弹窗，但是还要考虑下面两种情况：

- **后台ANR（SilentAnr）：**之前分析ANR流程我们可以知道，==如果ANR被标记为了后台ANR（即SilentAnr），那么杀死进程后就会直接return，并不会走到产生进程错误状态的逻辑==。这就意味着，后台ANR没办法捕捉到，而后台ANR的量同样非常大，并且后台ANR会直接杀死进程，对用户的体验也是非常负面的，这么大一部分ANR监控不到，当然是无法接受的。
- **闪退ANR：**除此之外，我们还发现相当一部分机型（例如OPPO、VIVO两家的高Android版本的机型）修改了ANR的流程，==即使是发生在前台的ANR，也并不会弹窗==，而是直接杀死进程，即闪退。这部分的机型覆盖的用户量也非常大。并且，确定两家今后的新设备会一直维持这个机制。


输入超过5秒未响应，或者广播接收器10秒没有执行完毕

一般地，这时往往会弹出一个提示框，告知用户当前xxx未响应，用户可选择继续等待或者Force Close 
### ANR流程

基本都是在system_server进程内完成的，
 无论ANR的来源是哪里，最终都会走到_ProcessRecord_中的_appNotResponding_，这个方法包括了ANR的主要流程，所以也比较长，我们找出一些关键的逻辑来分析：_**frameworks/base/services/core/java/com/android/server/am/ProcessRecord.java：**_
发生ANR后，为了能让开发者知道ANR的原因，方便定位问题，**会dump很多信息到ANR Trace文件里**
Android 系统中，==ANR 的检测是通过系统服务 ActivityManagerService （AMS）和 Window Manager Service （WMS）来进行的。==

![[a173a9370249d43814ad1029aa7cd50e.png]]

1. 在 AMS 中，主要检测包括：
    
    - BroadcastReceiver：默认情况下，Broadcast Receiver 的 onReceive()方法运行在 UI 线程，如果 ==onReceive()方法在10秒内没有执行完==，系统就会触发 ANR。
        
    - Service：==对于 Service，如果 onStartCommand() 或者 onBind() 没有在20秒内执行完毕，系统也会触发 ANR。==
        
    - ContentProvider：==如果 Content Provider 中的 CRUD 操作在10秒内没有执行完，系统也会触发 ANR。==
        
2. 在 WMS 中，主要检测包括：
    
    - InputDispatching：如果 InputDispatching 阶段的事件在 5 秒内没有得到响应，也会触发 ANR。


https://www.jianshu.com/p/d19c34e7e9bd

各个应用进程和系统进程的函数堆栈信息都输出到了/data/anr/traces.txt的文件中 ，由此定位anr

**traces文件有哪些信息**

- ANR的进程id、时间和进程名称。
- 线程的基本信息
- 线程的优先级（默认5）、线程锁id和线程状态
- ==线程的调用栈信息(这里可查看导致ANR的代码调用流程，分析ANR最重要的信息)==

系统的 ==**system_server 进程在检测到 App 出现 ANR 后，会向出现 ANR 的进程发送 SIGQUIT (signal 3) 信号**。== 正常情况下，系统的 libart.so 会收到该信号，**并调用 Java 虚拟机的 dump 方法生成 traces**。

以友盟+的==U-APM 应用性能监控平台为例，集成SDK 后，SDK 会拦截 SIGQUIT。在出现 ANR 时，libcrashsdk.so 会优先收到信号，并生成 traces 和 ANR 日志==。在 SDK 处理完信号后，会将信号继续传递给系统的 libart.so，让系统生成 ANR traces.txt。
 
概要
![[c8177f3e6709c93d8caf52a3e1cc23dbd000547a.webp]]

資源使用情況
![[21a4462309f79052a86fca5976020ccd7acbd55a.webp]]


原因：

- ==CPU密集==，导致主线程没法抢占cpu时间片,要注意cpu占用高的进程。**需要注意的是gc线程进行时会挂起所有线程，当内存抖动时会触发频繁gc。**

- 高IO，如不当访问数据库导致数据库负载过重时(log中cpu的使用iowait占比高)

- 低内存（low memory），如内存不足导致block在创建bitmap上

- 死锁引发ANR, 非主线程持有主线程需要的锁对象,导致主线程等待超时,通常log中会有以下字段 Blocked  | - locked | waiting to lock | held by thread,这个时候cpu多数是空闲，使用占比很低

- 当前应用进程进行进程间通信请求其他进程，==其他进程的操作长时间没有反馈,例如操作硬件Camera==，则需要做异步处理

- Service ==binder数量达到上限==，binder线程池爆满

- 在system_server中触发WatchDog ANR

- ==消息屏障泄漏==，子线程调用postInvalidate调用。 [[#**invalidate及其流程**]]
- **广播分为==有序广播==和==无序广播==，同时又有==前台广播==和==后台广播之分==；==只针对有序广播设置超时监控机制==，并根据前台广播和后台广播的广播类型决定了超时时长；例如后台广播超时时长 60S，前台广播超时时长只有 10S；
### 日志分析
[ANR日志分析](https://www.xjx100.cn/news/5777.html?action=onClick)

# nativePollOnce

```
public class MainActivity extends AppCompatActivity {  
  
    static {  
        Thread.setDefaultUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {  
            @Override  
            public void uncaughtException(final Thread t, Throwable e) {  
                Log.i("uncaught", Thread.currentThread().toString());  
                Log.i("uncaught", t.toString());  
            }  
        });  
    }
```
如果当一个 Java 线程抛出了未捕获的异常时，JVM 先会调用到 UncaughtExceptionHandler，==然后再会把此线程停止掉==。所以这段代码中，如果主线程抛出异常，==那么第 6 行的方法结束后，主线程就会被 JVM 给停止掉，既然主线程都停止掉了，那自然就无响应了，也就会发生 ANR 了==。

一般我们设置自定义 UncaughtExceptionHandler 时，都会在自定义的 uncaughtException 方法最后再调用一遍被我们顶替掉的系统默认的 UncaughtExceptionHandler，以便把应用 kill 掉，而这个例子，充分的显示了，当未捕获异常发生后，就算赖着不 kill 掉应用也是不行的，因为可能主线程都已经被停掉了。

 ### ANR 监控方案
1. ANR-WatchDog   [[#参考了ANR-WatchDog机制**]]
2. Native层拦截Signal信号
博客：[手把手教你高效监控ANR](https://blog.csdn.net/idaretobe/article/details/128257240) 



# 25. [Listview和RecyclerView](https://www.cnblogs.com/nyyy/p/6442478.html)

常见问题：https://blog.csdn.net/pgg_cold/article/details/79487695



## 区别（重要）

**1 Listview中ViewHolder是需要自定义的，在RecyclerView中ViewHolder是谷歌已经封装好的**

**2 Listview中的Item是`只能垂直滑动的`，RecyclerView可以`水平滑动或者垂直滑动`，针对多种类型条目的展示效果，如瀑布流 网格 支持多种类型**

**3 Listview中删除或添加item时，item是无法产生动画效果的，在RecyclerView中添加、删除或移动item时有`两种默认的效果可以选择SimpleItemAnimator（简单条目动画） 和 DefaultItemAnimator`（原样的条目动画）**



**4。listview有二级缓存（当前显示，屏幕外的），recyclerview有四级缓存**

## listview优化

https://www.cnblogs.com/yuhanghzsd/p/5595532.html

最优：**Google推荐优化方案：**　

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
public View getView(int position, View convertView, ViewGroup parent) {
        Log.d("MyAdapter", "Position:" + position + "---"
                + String.valueOf(System.currentTimeMillis()));
        ViewHolder holder;
     if (convertView == null) {
            final LayoutInflater inflater = (LayoutInflater) mContext
                    .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            convertView = inflater.inflate(R.layout.list_item_icon_text, null);
           holder = new ViewHolder();
           holder.icon = (ImageView) convertView.findViewById(R.id.icon);
           holder.text = (TextView) convertView.findViewById(R.id.text);
           convertView.setTag(holder);
       } else {
           holder = (ViewHolder) convertView.getTag();
       }
       holder.icon.setImageResource(R.drawable.icon);
      holder.text.setText(mData[position]);
       return convertView;
   }
   
   static class ViewHolder {
       ImageView icon;
    
       TextView text;
```

## recyclerview缓存



recyclerview缓存详解：http://www.360doc.com/content/19/0712/11/36367108_848240455.shtml

recycleview有四级缓存：
//重用的其实是viewholder，但是实际viewholder里面也有itemview

- mAttachedScrap(屏幕内)，用于屏幕内itemview快速重用，不需要重新createView和bindView

- mCacheViews(屏幕外)，保存最近移出屏幕的ViewHolder，包含数据和position信息，==复用时必须是相同位置的ViewHolder才能复用，应用场景在那些需要来回滑动的列表中，当往回滑动时，能直接复用ViewHolder数据==，不需要重新bindView。

 - mViewCacheExtension(自定义缓存)，不直接使用，需要用户自定义实现，默认不实现。

  - mRecyclerPool(缓存池)，==**当cacheView满了后或者adapter被更换**，将cacheView中移出的、ViewHolder放到Pool中==，**放之前会把ViewHolder数据清除掉，所以复用时需要重新bindView**。这个缓存池是一个二维数组 外部是ScrapData 的SparseArray数组，内部是ArrayList数组。

```java
public final class Recycler {
            //一级缓存中用来存储屏幕中显示的ViewHolde
        final ArrayList<ViewHolder> mAttachedScrap = new ArrayList<ViewHolder>();
        private ArrayList<ViewHolder> mChangedScrap = null;
          //二级缓存中用来存储屏幕外的缓存
        final ArrayList<ViewHolder> mCachedViews = new ArrayList<ViewHolder>();
           //暂可忽略 mAttachedScrap的不可变视图
        private final List<ViewHolder>
                mUnmodifiableAttachedScrap = Collections.unmodifiableList(mAttachedScrap);
            //当前屏幕外缓存大小，数量为2，即本代码片最后一个DEFAULT_CACHE_SIZE 成员的值，可变。
        private int mViewCacheMax = DEFAULT_CACHE_SIZE;
            //四级缓存当屏幕外缓存的大小大于2，便放入mRecyclerPool中缓存。
        private RecycledViewPool mRecyclerPool;
              //三级缓存自定义缓存，根据coder自己定义的缓存规则。
        private ViewCacheExtension mViewCacheExtension;
        //默认屏幕外缓存大小。
        private static final int DEFAULT_CACHE_SIZE = 2;
        }
```

 **mAttachedScrap** 与 **mChangedScrap** （并称**scrap**缓存）这两个缓存一般是说的布局的时候的缓存（比如调用remove，notifyItemRangeChanged 等等）。
 布局分为pre-layout与post-layout，两个布局的整体流程都差不多：detachViewAt(移除view从ViewGroup 即recyclerView) -> recycler.scrapView（缓存页面上的view到mAttachedScrap 或者mChangedScrap）->fill(重新布局)
 pre-layout 与post-layout的唯一区别就是预布局执行fill的时候会忽略移除view的空间，并将最终需要显示的view提前加载在屏幕外面，方便post-layout计算各个view的移动距离，好做移动动画。


**mCachedViews** 与 **mRecyclerPool** 这两个缓存一般说的是页面滑动的时候的缓存。

  mAttachedScrap 与 mChangedScrap 如果在布局完成之后还有多余的ViewHolder 也会直接全部丢进mRecyclerPool
![[306d8170728837dcfe71a2244ae2b7c2.png]]


##  适配器

一次notifyDataSetChange会触发两次onBindViewHolder(), 包括初次创建适配器时


#  26.启动crash捕获（重要）

UncaughtExceptionHandler存在于Thread中.当异常发生且未捕获时.异常会透过`UncaughtExceptionHandler`抛出，并且该线程会消亡，==所以在Android中**子线程死亡是允许的**.**主线程死亡就会导致ANR==。**


Crash异常捕获的简单使用
创建Crash异常捕获很简单，主要的步骤有：

创建BaseApplication继承Application并**实现Thread.UncaughtExceptionHandler**(接口)
通过Thread.setDefaultUncaughtExceptionHandler(this)设置默认的异常捕获
最后在manifests中注册创建的BaseApplication

```java
public class BaseApplication extends Application implements Thread.UncaughtExceptionHandler {
    @Override
    public void onCreate() {
        super.onCreate();
        //设置异常捕获
        Thread.setDefaultUncaughtExceptionHandler(this);
    }

    @Override
    public void uncaughtException(final Thread thread, final Throwable ex) {
        //当有异常产生时执行该方法
        //输出日志等
    }
}
```

原文链接：https://blog.csdn.net/qq_30379689/article/details/53731646

## native异常捕获
native通过在native层开启子线程，注册信号，等待捕获异常，当获取到对应信号时，输出信号对应的线程名到java层，有java层dump对应的线程栈。
#### Native日志分析方式：

将LogCat输出的Native崩溃日志，拷贝到crash.log（注意：最好以星号这行开始），并复制到build目录下的cmake编译后的so文件目录下，需要注意的是编译目录armeabi要和crash.log对应，cmake\debug\obj\arm-XXX目录下要有so文件,这个是编译的时候生成的。

例如，我们运行的so文件是armeabi-v7a下的，那么拷贝到此下面，执行如下命令：
```shell
ndk-stack -sym C:\Users\maomao\Desktop\WukongMemo\MaoMaoMedia\player\build\intermediates\cmake\debug\obj\armeabi-v7a -dump crash.log
```

然后我们就能看到带文件名、函数名和行号的log日志了，进而我们就可以进一步排查和修复问题了。


##### 注意事项：

必须将 Logcat 设置为 No Filter 才能看到全部错误信息，否则只看到 signal 11 (SIGSEGV), code 1 (SEGV_MAPERR)  。

简化方式，cd到build\intermediates\cmake\debug目录下，输入如下命令：
```shell
ndk-stack -sym obj/armeabi-v7a -dump obj/armeabi-v7a/crash.log
```

#  **27.viewpage，pageadpter**

## 27.1 fragment和viewpage结合

 FragmentPagerAdapter和FragmentStateAdapter,可以实现滑动效果，并且点击切换的时候也有,说明一下，**一个是适用于显示fragment数量较少，另一个是显示fragment数量较多的时候使用**。

setOffest（）设置预先加载的fragment数量



## 27.2 FragmentPagerAdapter和FragmentStatePagerAdapter

FragmentPagerAdapter和FragmentStatePagerAdapter都是继承自PagerAdapter。当然也可以自己继承PagerAdapter。



### 27.2.1 FragmentPagerAdapter

该Adapter通过FragmentManager管理保留已经创建的Fragment，启动的时候会通过**调用instantiateItem()**，判断是否已经创建对应的fragment，如果存在则会进行attach，否则调用getItem重新创建。

destroyItem只是detach

### 27.2.2 FragmentStatePagerAdapter

只保留当前显示的fragment，如果关闭则会销毁对应的fragment节省内存

destroyItem是fm进行了remove

#  28.Dalvik、Art虚拟机 

**基类进程zygote用来创建和初始化DVM实例** 



## 28.1 Dalvik

针对Android系统面向Linux设计的虚拟机，

**每一个应用进都程拥有一个独立的Dalvik实例，**

**每一个Dalvik实例都运行在一个独立的进程空间中**，所以不会某个Dalvik虚拟机崩溃导致所有程序都关闭。

![img](https://img-blog.csdnimg.cn/2019080115394598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDkyOTM1Mw==,size_16,color_FFFFFF,t_70) 

1. Dalvik与Java虚拟机区别

   1）**基于架构不同**：**Dalvik虚拟机基于寄存器架构** 数据访问通过寄存器间直接传递；Java虚拟机基于**栈架构**。
   2）**执行字节码不同**：**Java虚拟机执行.class文件**；Dalvik虚拟机需要把所有的.**class文件打包到一个.dex文件中**。
   3）共享机制：Dalvik虚拟机**不同应用之间在运行时可以共享相同的类**；Java虚拟机**不同程序打包以后时独立的**。
   
   如果要分dex需要引入multi-dex

## 28.2 ART

**Android4.4之后加入的模式**，可以理解为是Dalvik的升级。

1. ART与Dalvik的区别

   1）Dalvik是JIT（**即时编译**, 开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，这一机制并不高效 ），**安装快，加载慢**；ART是AOT（**预编译**），**安装慢，加载快**。
   2）**Dalvik为32位；ART是64位并兼容32位**。
   3）**ART对GC机制进行了优化**。
   4）运行时堆空间划分不同。具体可参考Android内存优化(如下)
   5）支持多dex加载，优先加载classes.dex
   

   Dalvik内存管理特点是:   内存碎片化严重，当然这也是Mark and Sweep算法带来的弊端

   [![img](https://camo.githubusercontent.com/b552fa6b7ee884a669d9ab772aa55b0d412a92b0/687474703a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f333938353536332d663137306434386630383939326233642e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f772f31323430)](https://camo.githubusercontent.com/b552fa6b7ee884a669d9ab772aa55b0d412a92b0/687474703a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f333938353536332d663137306434386630383939326233642e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f772f31323430)

   可以看出每次gc后内存千疮百孔，本来连续分配的内存块变得碎片化严重，之后再分配进入的对象再进行内存寻址变得困难。
   
   ART的解决: ==在ART中,它将Java分了一块空间命名为Large-Object-Space==, **这块内存空间的引入用来专门存放large object**。同时ART又引入了moving collector的技术,即将不连续的**物理内存块进行对齐**.对齐了后内存碎片化就得到了很好的解决.Large-Object-Space的引入一是因为moving collector对大块内存的位移时间成本太高,而且提高内存的利用率 根官方统计，ART的内存利用率提高10倍了左右。
   
   ART 有多个不同的 GC 方案，这些方案包括运行不同垃圾回收器。**默认方案是 CMS（==并发标记清除==）
   
   ART主要使用粘性 CMS 和部分 CMS。粘性 CMS 是 ART 的不移动分代垃圾回收器。它仅扫描堆中自上次 GC 后修改的部分，并且只能回收自上次 GC 后分配的对象。除 CMS 方案外，当应用将进程状态更改为==察觉不到卡顿的进程状态（例如，后台或缓存）时，ART 将执行堆压缩。==也就是说，可以认为是CMS(Concurrent Mark-Sweep)，但是不同情况又有不同的使用，主要是粘性CMS和分部CMS的区别。





**Dalvik虚拟机执行的是dex字节码，ART虚拟机执行的是本地机器码**。但是ART也是兼容的dex字节码的。

## dex结构
dex文件结构, 与class的区别在与索引头区里，DEX文件直接是
![[5bd2a1148993118ec8377b58b474092a.jpg]]


![[20200724005012224.png]]
## odex文件结构

Odex文件的结构可以理解为dex文件的一个超集。它的结构如下图所示，odex文件在dex文件头部添加了一些数据，然后在dex文件尾部添加了dex文件的依赖库以及一些辅助数据。
Dalvik虚拟机将dex文件映射到内存中后是Dalvik格式，在Android系统源码的dalvik/libdex/DexFile.h文件中它的定义如下。

DexFile结构中存入的多为其他结构的指针。DexFile最前面的DexOptHeader就是odex的头，DexLink以下的部分被成为auxillary section,即辅助数据段，它记录了dex文件被优化后添加的一些信息。然而，DexFile结构描述的是加载进内存的数据结构，还有一些数据是不会加载进内存的，经过分析，odex文件结构定义整理如下.

## dexopt和dex2oat
Android提供了一个专门验证与优化dex文件的工具dexopt。其源码位于Android系统源码的dalvik/dexopt目录下，Dalvik虚拟机在加载一个dex文件时，通过指定的验证与优化选项来调用dexopt进行相应的验证与优化操作。

### dexopt和dex2oat对比
![[7182360-9c46c9a5683bf00f.webp]]
通过上图可以很明显的看出 dexopt 与 dex2oat 的区别，前者针对 Dalvik 虚拟机，后者针对 Art 虚拟机。

- class：java编译后的⽂件，每个类对应⼀个class⽂件
- dex：Dalvik EXecutable把class打包在⼀起，⼀个dex可以包含多个class⽂件
- odex：Optimized DEX针对系统的优化，例如某个⽅法的调⽤指令，==会把虚拟的调⽤转换为使⽤具体的index，这样在执⾏的时候就不⽤再查找了==dexopt主要在应用安装时使用，其主要目的是对 DEX 文件进行==验证、优化和生成 ODEX (Optimized DEX) 文件==。这个过程允许Android系统为新安装的应用生成特定于设备的优化版本。
- oat：Optimized Androidfile Type。==使⽤AOT策略对dex预先编译（解释）成本地指令，这样再运⾏阶段就不需再经历⼀次解释过程，程序的运⾏可以更快==
- AOT：Ahead-Of-Time compilation预先编译

- dexopt 是对 dex 文件 进行 verification 和 optimization 的操作，其对 dex 文件的优化结果变成了 odex 文件，==这个文件和 dex 文件很像，只是使用了一些优化操作码（譬如优化调用虚拟指令等）。==
    
- dex2oat 是对 dex 文件的== AOT 提前编译操作，其需要一个 dex 文件，然后对其进行编译，结果是一个本地可执行的 ELF 文件，可以直接被本地处理器执行==。
    

除此之外在上图还可以看到 Dalvik 虚拟机中有使用 JIT 编译器，也就是说其也能将程序运行的热点 java 字节码编译成本地 code 执行，所以其与 Art 虚拟机还是有区别的。==Art 虚拟机的 dex2oat 是提前编译所有 dex 字节码==，而 Dalvik 虚拟机==只编译使用启发式检测中最频繁执行的热点字节码。==

## dex加載流程

[[#52.插件化]]

APK安装后的DEX文件通常位于手机的/data/dalvik-cache目录下。具体位置会因手机版本、Android系统版本、厂商定制等因素而有所不同。一般来说，在/data/dalvik-cache目录下的文件，以apk文件名命名的文件夹内。

DexFile.loadDex尝试把一个dex文件解析并加载到native内存，在加载到native内存之前，==如果dex不存在对应的odex，那么Dalvik下会执行dexopt操作，Art下会执行dex2oat操作==，最后得到的==都是一个优化后的odex==。实际上最后虚拟机上==执行的是这个odex而不是dex==。

  
![[3769423-719ef95f2b18e231.webp]]

## 28.3常见问题

应用程序如何突破dalvik.vm.heapsize 的限制。

- 创建子进程。创建一个新的进程，那么我们就可以把一些对象分配到新进程的heap上了，从而达到一个应用程序使用更多的内存的目的。
- 使用jni在native heap上申请空间（推荐使用）。nativeheap的增长并不受dalvik vm heapsize的限制。只要RAM有剩余空间，程序员可以一直在native heap上申请空间，当然如果 RAM快耗尽，memory killer会杀进程释放RAM。
- 使用显存。使用 OpenGL textures 等 API ， texture memory 不受 dalvik vm heapsize 限制。

  
  
# 29.LruCache



LruCache是一种缓存策略，持有的是强引用，但是会控制在一个峰值下。**它内部维护了一个队列**，**每当从中取出一个值时**，**该值就移动到队列的头部**。**当缓存已满而继续添加时，会将队列尾部的值移除，方便GC**。LruCache用于内存缓存，在避免程序发生OOM和提高执行效率有着良好表现。

**缓存系统的性能主要取决于查找数据和淘汰数据是否高效。** 下面，我们用递推的思路推导 LRU 缓存的实现方案，主要分为 3 种方案：

- **方案 1 - 基于时间戳的数组：** 在每个数据块中记录最近访问的时间戳，当数据被访问（添加、更新或查询）时，将数据的时间戳更新到当前时间。当数组空间已满时，则扫描数组淘汰时间戳最小的数据。  
    

- 查找数据： 需要遍历整个数组找到目标数据，时间复杂度为 O(n)；
- 淘汰数据： 需要遍历整个数组找到时间戳最小的数据，且在移除数组元素时需要搬运数据，整体时间复杂度为 O(n)。

  
- **方案 2 - 基于双向链表：** 不再直接维护时间戳，而是利用链表的顺序隐式维护时间戳的先后顺序。当数据被访问（添加、更新或查询）时，将数据插入到链表头部。当空间已满时，直接淘汰链表的尾节点。  
    

- 查询数据：需要遍历整个链表找到目标数据，时间复杂度为 O(n)；
- 淘汰数据：直接淘汰链表尾节点，时间复杂度为 O(1)。

- **方案 3 - 基于双向链表 + 散列表：** 使用双向链表可以将淘汰数据的时间复杂度降低为 O(1)，但是查询数据的时间复杂度还是 O(n)，我们可以在双向链表的基础上增加散列表，将查询操作的时间复杂度降低为 O(1)。  
    

- 查询数据：通过散列表定位数据，时间复杂度为 O(1)；
- 淘汰数据：直接淘汰链表尾节点，时间复杂度为 O(1)。

方案 3 这种数据结构就叫 “哈希链表或链式哈希表”，我更倾向于称为哈希链表，因为当这两个数据结构相结合时，我们更看重的是它作为链表的排序能力。



Lru在linkHashMap就是第三种方案已经由内部实现，直接开启即可。
```java
public class LRUCache<K,V> extends LinkedHashMap<K,V> {
    
  private int cacheSize;
  
  public LRUCache(int cacheSize) {
      super(16,0.75f,true);
      this.cacheSize = cacheSize;
  }

  /**
   * 判断元素个数是否超过缓存容量
   */
  @Override
  protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
      return size() > cacheSize;
  }
}


```

手动实现可以通过实现双向链表。

## LRU适用例子
- Bitmap复用池
- byte[]复用池
- 内存http缓存
- 内存图片缓存
- okhttp的sokcet连接池

## 其他算法
```
**最不经常使用算法（LFU）：**这个缓存算法使用一个计数器来记录条目被访问的频率。通过使用LFU缓存算法，最低访问数的条目首先被移除。这个方法并不经常使用，因为它无法对一个拥有最初高访问率之后长时间没有被访问的条目缓存负责。

**最近最少使用算法（LRU）：**这个缓存算法将最近使用的条目存放到靠近缓存顶部的位置。当一个新条目被访问时，LRU将它放置到缓存的顶部。当缓存达到极限时，较早之前访问的条目将从缓存底部开始被移除。这里会使用到昂贵的算法，而且它需要记录“年龄位”来精确显示条目是何时被访问的。此外，当一个LRU缓存算法删除某个条目后，“年龄位”将随其他条目发生改变。

**自适应缓存替换算法(ARC)：**在IBM Almaden研究中心开发，这个缓存算法同时跟踪记录LFU和LRU，以及驱逐缓存条目，来获得可用缓存的最佳使用。

**最近最常使用算法（MRU）：**这个缓存算法最先移除最近最常使用的条目。一个MRU算法擅长处理一个条目越久，越容易被访问的情况。

**先进先出算法（FIFO）**：FIFO是英文First In First Out 的缩写，是一种先进先出的数据缓存器，他与普通存储器的区别是没有外部读写地址线，这样使用起来非常简单，但缺点就是只能顺序写入数据，顺序的读出数据，其数据地址由内部读写指针自动加1完成，不能像普通存储器那样可以由地址线决定读取或写入某个指定的地址
```

# 30.广播BroadCastReciever

android 内置了很多系统级别的广播，我们可以在应用中通过监听这些广播来得到各种系统的状态信息。比如手机开机后会发送一条广播，电池的电量发生变化会发出一条广播，时间或时区发生改变也会发出一条广播等等。如果想要接受这些广播，就需要使用广播接收器。

 动态：

```
    //DYNC_BROADCAST可以随意,但注册广播和发送广播时action应该保持一致
    private static final String DYNC_BROADCAST = "guo.com.example.dyncBroadcastReceiver";
    private DyncBroadcastReceiver dyncBroadcastReceiver = new DyncBroadcastReceiver();
    
protected void onCreate(Bundle savedInstanceState) {
        .....
        // 注册广播
        IntentFilter iFilter = new IntentFilter();
        iFilter.addAction(DYNC_BROADCAST);
        registerReceiver(dyncBroadcastReceiver, iFilter);
    }


    // 发送广播通知
    protected void sendBroadcast(){
        Intent intent = new Intent();
        intent.setAction(DYNC_BROADCAST);
        sendBroacdcast(intent);
    }

　　
　　@Override
　　protected void onDestroy() {
　　　　unregisterReceiver(dyncBroadcastReceiver);
    　　super.onDestroy();
　　　　
　　}
```

 

 

- 无序广播 

也就是最常见的标准（**普通** ）广播，通过SendBroadcast()方法发送。



- **有序广播（Ordered Broadcast）：**

  **优缺点**

  **优点：1，按优先级的不同，优先Receiver可对数据进行处理，并传给下一个Receiver**

  **2，通过abortBroadcast可终止广播的传播**

  **缺点：效率低**

  **发送广播的方法：sendOrderedBroadcast()**

  **优先接收到Broadcast的Receiver可通过setResultExtras(Bundle)方法将处理结果存入Broadcast中，**

  **下一个Receiver 通过 Bundle bundle=getResultExtras(true)方法获取上一个Receiver传来的数据**


- 隐式广播
隐式广播，顾名思义，是并没有直接指定接收该广播的应用的广播。系统或者应用在发送隐式广播的时候，通常只指定一个广播的动作（Action）或者分类（Category），然后任何对这个动作或者分类感兴趣的应用都可能接收到这个广播。系统将传送这些广播到订阅了匹配的 intent filter 的所有 receiver。==android8.0之后禁止静态注册隐式广播，处于对性能的考虑，只能动态注册。==
- 显式广播
直接指定接收的组件。
```java
Intent intent = new Intent();
intent.setComponent(new ComponentName(“com.example.app”, “com.example.app.MyBroadcastReceiver”));
sendBroadcast(intent);

```

- 本地广播 

通过系统LocalBroadcastManager发送，只能在**当前应用内接收**。相对于其他类型广播而言，**安全性高&效率高。本地广播只能通过LocalBroadcastManager动态注册。**



- 全局广播

**发出去的广播可以被任何应用程序接收到，也可以接受来自任何应用程序的广播** 



- 系统广播 

有的地方把这个也算一个分类，这里也提一下，系统广播就是Android系统内置的广播，用来通知应用一些系统状态的改变，如：息屏亮屏，电量变化，网络状态变化。使用者只需注册对应的Action, 系统有相关操作时会自动广播。 



粘性消息：粘性消息在发送后就一直存在于系统的消息容器里面，等待对应的处理器去处理，==如果暂时没有处理器处理这个消息则一直在消息容器里面处于等待状态，粘性广播的Receiver如果被销毁，那么下次重建时会自动接收到消息数据。== 



**广播接收器注册一共有两种形式 : 静态注册和动态注册.**

两者及其接收广播的区别:

1.动态注册广播不是常驻型广播，也就是说广播跟随activity的生命周期。注意: 在activity结束前，移除广播接收器。

 静态注册是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。

 2. 当广播为 有序广播 时 ：

​          1 优先级高的先接收

​          2  **同优先级的广播接收器， 动态优先于静态**

​          3 同优先级的同类广播接收器，**静态：先扫描的优先于后扫描的，动态：先注册的优先于后注册的。**

  

3、当广播为 普通广播 时：

​         1 无视优先级，**动态广播接收器优先于静态广播接收器**

​         2 同优先级的同类广播接收器，静态：**先扫描的优先于后扫描的**，动态：**先注册的优先于后注册的。**



动态注册代码:

```java
UpdateBroadcast  broadcast= new UpdateBroadcast();  
IntentFilter filter = new IntentFilter("com.unit.UPDATE");  
registerReceiver(broadcast, filter); 
```

静态注册代码(在配置文件中添加:

1. ```
   <receiver android:name="net.youmi.android.AdReceiver" >  
               <intent-filter>  
                   <action android:name="android.intent.action.PACKAGE_ADDED" />  
                   <data android:scheme="package" />  
               </intent-filter>  
    </receiver>  
   
   ```


# 31.断点下载

**多线程（下载完成后文件流输入流文件合并） + 断点续传** （记录暂停位置的字节位置，发送断点的位置的请求到服务器，使用RandomAccessFile（java.io，普通file类不支持断点）从记录 的位置写入）

https://www.jianshu.com/p/2b82db0a5181

# 32.View之间继承关系

### [     android 中组件继承关系图，一目了然         ](https://www.cnblogs.com/xuan52rock/p/5391252.html)         

View继承关系图

![img](https://images2015.cnblogs.com/blog/379538/201604/379538-20160414152433941-1721780657.png)

 



# 33.findviewbyid()

实际上是对view树的遍历查找，activity调用window.findViewById，windows调用decordview.findViewById()--->view.findViewById实际上是调用了findViewTraversal()，与mId比较，相等就返回，如果是ViewGroup（已经重写了findViewTraversal）就递归遍历

- 重写

![img](https://img-blog.csdn.net/20150624122408822?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmxlc3MyMDE1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


# 34.apk签名过程

打开待签名apk----遍历条目-----跳过目录，文件



# 35.Gilde 和Fresco

注意缓存原理，以及动图的加载等等

## 35.1. Gilde

https://github.com/bumptech/glide
 Google

优点：

1. 除了支持gif和webP图片，还支持缩略图和video（可以将任何的本地视频解码成一张静态图片）；
2. 生命周期集成（根据Activity或者Fragment的生命周期管理图片加载请求；方便处理bitmap，比如 Paused状态在暂停加载，在Resumed的时候又自动重新加载。）
3. 有高效的缓存策略，**支持原始图片和结果图片的缓存**，而Picasso和Fresco只会缓存原始尺寸的图片，**由于不需要再次处理图片大小，加载比较快**。
4. 加载速度在**静态图上是比Fresco快**的，在内存上，javaheap内存开销比Fresco大，但是NATIVE HEAP上不大。
5. Glide可以很容易的获取Bitmap
    问题：
6. 相同URL的不同控件之间无法复用缓存。确认
7. 在加载gif图时时间比较慢
8. Glide没有文件缓存
9. Glide加载的图片没有Picasso那么平滑
10. Glide只有占位图
11. 没有缓存进度

默认Bitmap格式是RGB_565  javaHeap —JAVA虚拟机
 native heap –  C/C++直接操纵的系统堆内存

## 35.2. Fresco

https://github.com/facebook/fresco
 https://www.fresco-cn.org/

优点：

1. Fresco会**自动复用相同URL的缓存**，两个相同URL同时进行请求Fresco也只会进行一次网络请求，然后第二个进行复用。
2. Fresco 中设计有一个叫做 **Drawees 模块**，方便地显示loading图，当图片不再显示在屏幕上时，及时地释放内存和空间占用。
3. 在5.0以下系统，Fresco将图片放到一个**特别的内存区域**。这会使得APP更加流畅，减少因图片内存占用而引发的OOM。在更底层的Native层对OOM进行处理，图片将不再占用App的内存
4. 支持**渐进式的图片呈现**
5. 在加载gif图中，==Fresco的**java heap基本保持较低平稳状态**，而Glide的java heap基本为Fresco的一倍。但是nativheap中Fresco高出很多==
6. 支持**圆角图**，（下载失败之后点击重新下载，自定义展位图，overlay或者进度条，指定用户按压时的overlay）
7. 加载上：Fresco 的 image pipeline  设计，允许用户在多方面控制图片的加载：为同一个图片指定不同的远程路径，或者使用已经存在本地缓存中的图片。加载完成回调通知。对于本地图，如有EXIF缩略图，在大图加载完成之前，可先显示缩略图，缩放或者旋转图片，处理已下载的图片
    问题：
8. 当Fresco加载384张图片以后，就无法显示出图片了。当activity销毁数值会释放的。
9. Fresco依赖的库很多
10. 布局依赖SimpleDraweeView控件

# 36.隐式intent

Activity间通过隐式Intent的跳转，在发出Intent之前必须通过resolveActivity检查，避免找不到合适的调用组件，造成

ActivityNotFoundException的异常。

正例：
```java
public void viewUrl(String url, String mimeType) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setDataAndType(Uri.parse(url), mimeType);
        if (getPackageManager().resolveActivity(intent, PackageManager.MATCH_DEFAULT_ONLY) != null) {
                 startActivity(intent);
         }else {//找不到指定的Activity
        }
}
```

反例
```java
Intent intent = new Intent();
intent.setAction("com.example.DemoIntent");
try {
        startActivity(intent);
		}catch (ActivityNotFoundException e) {
         e.printStackTrace();
}
```
# 37.应用安全

## 37.1 反编译与防止反编译



### 37.1.1 代码混淆

配置proguard-rules.pro文件设置混淆规则，有利于安全的同时还能减小apk包的体积

主项目的build.gradle设置minifyEnabled true，proguard-rules.pro加入混淆规则。

 - 1. 压缩（Shrink）：检测并移除代码中无用的类、字段、方法和特性（Attribute）；

- 2. 优化（Optimize）：对字节码进行优化，移除无用的指令。优化代码，非入口节点类会加上private/static/final，没有用到的参数会被删除，一些方法可能会变成[内联代码]

- 3. 混淆（Obfuscate）：使用a、b、c、d这样简短而无意义的名称，对类、字段和方法进行重命名；

- 4. 预检（Preveirfy）：在Java平台上对处理后的代码进行预检，确保加载的[class文件]
是可执行的。
  
作者：网易数帆  
链接：https://www.zhihu.com/question/24027474/answer/370741770  

### 37.1.2 apk签名

签名实际上是对app加上开发者的签名，证明app与开发者的关系，实际上就是使用了签名keystore文件，签名使用私钥对内容进行加密，签后的apk携带公钥，安装时去取出公钥，对apk进行验证，查看是否使用正确的私钥加密。

### 37.1.3 apk加壳

![](D:\shayue\笔记\pic\apk_add.png)

## 37.2 KeyStore

传统理解为密钥库。一个keystore可以有多组密钥，每组密钥都有姓名，单位组织城市省份等等，可以通过别名获取。Android Keystore又称Android 密钥库系统，androidkeystore是主要用于存储密钥，可以存在于系统中

#### 提取防范

Android 密钥库密钥使用两项安全措施来避免密钥材料被提取：

- 密钥材料永不进入应用进程。通过 Android  密钥库密钥执行加密操作时，应用会在后台将待签署或验证的明文、密文和消息馈送到执行加密操作的系统进程。如果应用进程受到攻击，攻击者也许能使用应用密钥，但无法提取密钥材料（例如，在 Android 设备以外使用）。
- 
- 您可以将密钥材料绑定至 Android 设备的安全硬件，例如可信执行环境 (TEE) 和安全元件  (SE)。为密钥启用此功能时，其密钥材料永远不会暴露于安全硬件之外。如果 Android  操作系统受到攻击或者攻击者可以读取设备内部存储空间，攻击者也许能在 Android 设备上使用任意应用的 Android  密钥库，但无法从设备上提取这些数据。只有设备的安全硬件支持密钥算法、分块模式、填充方案和密钥有权配合使用的摘要的特定组合时，才可启用此功能。要检查是否为密钥启用了此功能，请获取密钥的 `KeyInfo` 并检查 `KeyInfo.isInsideSecurityHardware()` 的返回值。

# 38. 多语言/国际化

## 38.1 

语言切换的代码，在高版本中废弃了updateConfiguration方法，替代方法为createConfigurationContext。该方法返回一个context。

但是實測時還是需要使用updateConfiguration才能生效

## 38.2海外推送和统计平台
推送可以集成fcm（基于firebase），统计平台googleplay不支持其他 平台例如友盟的统计api，也只能集成firebase来埋点。


# 39.MMKV 与SP
[[#sharedPrefenrence (简介， 详细对比见39)]]

**此处讲一下数据拷贝**
一般来说，linux系统读取文件需要两次拷贝，一次是从==磁盘拷贝到内核缓冲区==，==再拷贝到用户空间==，那么就是拷贝两次。

我们讲的==mmap==技术是指==内核空间==缓冲区直接==映射到用户空间==，零拷贝指的是内核空间和用户空间操作的时候零次拷贝。如果操作文件的话，那么其实是可以实时同步内核区到文件，或者是等待缓冲区再同步到文件。

关于==Binder==的‘一次拷贝’，正常的binder不涉及文件操作，那么它的一次拷贝是哪里来的呢？
他的一次拷贝是因为 c/s架构的设计，我们知道，==进程间的用户空间是隔离的，共享数据需要在内核空间之间拷贝==，那么直接把内核空间映射到服务端的用户空空间，那么拷贝只有==客户端需要进行，所以这是一次拷贝==。

## **sharedPrefences：**

MMKV 是sharedP的优化, ==sp初始化子线程读取xml存在arraymap中==，final map存在k name和v xml文件 ，会持续占用内存

commit（同步） apply（异步）都会anr 异步任务过多，等待锁过多（锁会放到Queuework中），--==activity onstop==或者==service onstop= onstartcommand==时候会等待queuework释放（Queuework.waitToFinish）



**sp更新是全量更新**
文件的读取==需要拷贝两次，先拷贝到内核空间，再拷贝到用户空间，才能操作。==

## **MMKV:**（优化sp）
![[f8ef752d3660ec4f0e6951c186a5d17b.jpg]]

高性能，支持多进程（==文件锁，数据同步用的是校验码==）
MMKV对于多进程访问提供了基本的保护，但在多线程访问情况下，为了确保线程安全，你需要自行处理线程同步问题。
### mmkv的优化：
- 解决方案1：mmap内存映射→零拷贝技术。java filechannel底层也是mmap。m-map映射之后存在map中。
> 内存映射有两种：文件映射和虚拟空间映射，mmkv是文件映射，binder、共享内存那种是内核空间和用户空间的映射。

- 解决方案2：数据格式protobuf（key长度值动态长度，比如arraylist扩容同理），类似二进制链表，总长-key长-key-v长-v- 总长...，**每次读取都是最新的。而且数据紧凑，没有太多多余数据**

解决方案3：局部更新，增量更新，增加时候直接添加数据在“链表”后。

缺点：
- 1.重复数据越来越多，需要去重，达到最大值时，进行一次全量更新 
- 2、动态扩容，占用的大小就越来越大

# 40.LeakCanary

LeakCanary的基础是一个叫做ObjectWatcher Android的library。

`它通过lifcyclercallback   去hook了Android的生命周期，当activity和fragment 被销毁并且应该被垃圾回收时候自动检测。`

在application注册lifecyclercallback，对fragment使用fragmentmanger和子fragment使用childfragmentmanager也进行注册callback。

这些被销毁的对象被传递给ObjectWatcher，`ObjectWatcher持有这些被销毁对象·弱引用（weak references）`。如果`弱引用在等待5秒钟并运行垃圾收集器后仍未被清除`，那么被观察的对象就被认为是保留的（retained，在生命周期结束后仍然保留），并存在潜在的泄漏。

接下来输出dump日志分析。


# 41.Hook

一般通过 反射 或者动态代理。插桩也可以，还有activtiy的lifecyclercallback。符合切面编程的修改执行流程都能叫hook。
我们在热修复、插件化中大量hook的实现。

还有root后的xposed框架。
# 42.插件化


[[#52.插件化]]

# 43.Bitmap与Bitmap优化
相关：[[#15.图片裁剪和大图显示]]

## 简述
何为bitmap?
Bitmap负责对二进制的图片资源进行解码、显示、编辑等操作。
bitmap在在native层创建，并且有大量操作在navive层。
图片多大，bitmap就加载多大，跟iamgerview的大小没关系。
读取位图Bitmap时，如果分给虚拟机中的图片的堆栈大小只有8M，超出了，就会出现OutOfMemory异常。

加载 Bitmap到内存里以后，是包含两部分内存区域的。简单的说，==一部分是Java部分的，一部分是C部分的==。这个==Bitmap对象是由Java部分分配的，不用的时候系统就会自动回收了==，但是那个对应的C可用的内存区域，虚拟机是不能直接回收的，这个只能调用底层的功能释放。所以需要调用 ==recycle()方法来释放C部分的内存==。从Bitmap类的源代码也可以看到，recycle()方法里也的确是调用了JNI方法了的。


## 占用大小

	  实际大小 = bitmap.width() * bitmap.height() * 4。(RGB：4 ,565：2)

原理：==bitmap生成时取决于density与targetDenstiry参数，前
者取决于分辨率文件夹，后者取决于屏幕密度==

结论：

Bitmap在内存当中占用的大小其实取决于以下三点：

- 色彩格式（colorType）
	rgb565、RGB8888等等

- 原始文件存放的资源目录（density）
	根据所加载的资源目录，图片的实际尺寸可能会有所不同。例如，==当加载一张在 hdpi 目录下的图片到一个 mdpi 密度的设备上时，图片实际尺寸会进行相应的调整==。
- 目标屏幕的密度（targetDenstiry）
	- 当加载的图片资源与目标屏幕密度不匹配时，系统会根据目标屏幕密度对图片进行缩放。这会导致实际加载到内存的 Bitmap 尺寸与原始文件尺寸的差异，从而影响内存占用。

图片格式

|格式|像素位数|有无alpha通道|质量|大小|
| ---- | ---- | :----: | ---- | ---- |
|ARGB8888|32位|有|高|大|
|RGB565|16位|无|略差|较小|
|RGB444|12位|无|差|小|
|Alpha_8|8位|纯透明|无|小|

注意：Alpha_8一般用于遮罩，比如：绘制圆角图片
## 注意事项

- 4.4 以上解码Bitmap 要使用decodeStream, 同时给decodeStream的文件流是BufferedInputStream(可以根据文件大小指定恰当的大小，避免浪费)，decodeFile是fileinputstream

- decodeResoure 是项目中常见的API，同样也存在此问题，可以使用decodeResourceStream代替

## Bitmap源码解析
通过native方法的naitivecreate创建在本地内存。可以看到，**内存大小跟bitmap长宽和颜色类型有关**，用的是skia渲染。

![[截图20230826162811.png]]

```cpp
//frameworks\base\core\jni\android\graphics
static jobject nativeDecodeStream(JNIEnv* env, jobject clazz, jobject is, jbyteArray storage,  
        jobject padding, jobject options) {  
    //创建一个bitmap对象  
    jobject bitmap = NULL;  
    //创建一个输入流适配器,(SkAutoTUnref)自解引用  
    SkAutoTUnref<SkStream> stream(CreateJavaInputStreamAdaptor(env, is, storage));  
  
    if (stream.get()) {  
        SkAutoTUnref<SkStreamRewindable> bufferedStream(  
                SkFrontBufferedStream::Create(stream, BYTES_TO_BUFFER));  
        SkASSERT(bufferedStream.get() != NULL);  
        //图片解码  
        bitmap = doDecode(env, bufferedStream, padding, options);  
    }  
    //返回图片对象,加载失败的时候返回空  
    return bitmap;  
}

```

native使用示例：
```cpp
extern "C"  
JNIEXPORT jstring JNICALL  
Java_com_example_flu_MainActivity_loadBitmap(JNIEnv *env, jobject thiz, jobject bitmap) {  
    AndroidBitmapInfo *info;  
    AndroidBitmap_getInfo(env, bitmap, info);  
    int width = info->width;  
    int height = info->height;  
    
    int* pixel = nullptr;  
    AndroidBitmap_lockPixels(env, bitmap, reinterpret_cast<void **>(pixel));  
    int* pxl = pixel;//拿到像素点  
    int *line;  
    
    for (int x= 0; x < height;x++){  //操作像素点
        line = pxl;  
        for(int y = 0; y < width; y++){  
            line[y] = 0xFF0000FF;  
        }  
    }  
}

```
## bitmap的复用
源码上：主要指的是复用native内存块。==4.4之前复用只能使用相同大小的bitmap内存区域，4.4之后只要比将要分配的内存大就可以复用==。我们在应用层==最好用LRUcache来复用==。

使用inBitmap机制，首先需要收集不再使用的Bitmap，==必须保证收集到的Bitmap确实不再使用，否则==，如果解析新图片时，将新图片的数据填充到了还在使用中的Bitmap上，会导致业务方引用的Bitmap诡异地变成了另一张图。
所以可以使用引用计数法来解决。

### 通过Drawable缓存
我们通过drawable来加载本地图片，bitmap内部native解码使用启动缓存bitmap

```java
new BitmapDrawable(resoure, bitmap)
```
### 通过inBitmap 
通过设置option.inBitmap = true，他可以复用native中的bitmap
```java
val option = BitmapFactory.Options();
//开启复用！
option.inMuable = true；
//设置复用的map！
option.inBitmap = preloadBitmap;

//复用perloadBitmap
val newBitmap = BitmapFactory.deccodeStream(filePath, option)
```
当然我们最后最好用LRU来存储

```java

int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
int cacheSize = maxMemory / 8;

//初始化大小
LruCache<String, Bitmap> mMemoryCache = new LruCache<String, Bitmap>(cacheSize) {
    @Override
    protected int sizeOf(String key, Bitmap bitmap) {
    //每个的大小
        return bitmap.getByteCount() / 1024;
    }
};

```

## 使用注意事项
- 当activity退出的时候，一定要回收Bitmap.recyler。
- 创建bitmap时，根据可能会出现的大小进行==try.catch避免oom的crash==

当使用Bitmap加载过大的图片时，可能会导致OutOfMemoryError（OOM）错误。原因主要有以下几点：

1. 内存限制：尤其在移动设备（比如Android或iOS）上，每个应用的运行内存是有限的。操作系统针对每个应用分配了一定的内存上限，以保证其他应用的正常运行。应用加载过大的Bitmap时，需要申请的内存空间可能超过了应用所允许的最大内存限制，从而引发OOM。
    
2. 图片解码：当加载一张图片时，操作系统需要将其从文件系统中读取，然后解码成Bitmap数据。解码后的图片会以非压缩形式的数据存储在内存中，这意味着一张原始大小为2MB的图片解码后可能占用10MB或更多的内存。因此，==当一张图片尺寸过大时，解码后的Bitmap在内存中消耗的资源也会显著增加，可能导致内存不足并触发OOM。==
    
3. 内存碎片：内存碎片是由于程序长时间运行和反复的内存申请释放操作而产生的空间碎片。当加载大型Bitmap时，如果内存碎片太多，可用的连续内存区域可能不足以容纳全新的Bitmap，即便总的可用内存空间还有剩余，也会出现OOM。

# 44.启动优化

**启动模式：**

LaunchState: COLD （冷启动）

LaunchState: HOT （热启动）

LaunchState: WARM （温启动）

LaunchState: UNKNOWN (0)  （应用在前台时，调用adb命令）

**冷启动**

冷启动是指应用从头开始启动：系统进程在冷启动后才创建应用进程。发生冷启动的情况包括应用

自设备启动后或系统终止应用后首次启动。

**热启动**

在热启动中，系统的所有工作就是将 Activity 带到前台。只要应用的所有 Activity 仍驻留在内存

中，应用就不必重复执行对象初始化、布局加载和绘制。例如按Home键

**温启动**

温启动包含了在冷启动期间发生的部分操作；同时，它的开销要比热启动高。有许多潜在状态可视

为温启动。例如

- 用户在退出应用后又重新启动应用。进程可能未被销毁，继续运行，但应用需要执行onCreate() 从头开始重新创建 Activity。
- 系统将应用从内存中释放，然后用户又重新启动它。进程和 Activity 需要重启，但传递到onCreate() 的已保存的实例 state bundle 对于完成此任务有一定助益。

作者：maybe0813
链接：https://www.jianshu.com/p/7a909d052e4b
来源：简书

## 1.老三样

a. view布局避免过深，使用约束布局等代替嵌套，还有使用==viewstub替换==

b.在application初始化时，第三方sdk懒加载。启动页不重要的使用子线程加载，但是需要注意子线程尚未加载/加载未成功但是在mainThread使用的情况

c.启动页黑白屏, AppTheme设置<item name="android:windowBackground">XXXX(图片，颜色)</item>

### viewstub
1.  继承自view
2. 实际上是一个宽高都是0，并且不可见
3. 可以通过布局文件的android:inflatedId或者调用ViewStub的setInflatedId方法为懒加载视图的跟节点
```xml
<ViewStub
        android:id="@+id/stub"
        android:inflatedId="@+id/subTree"
	    android:layout="@layout/my_sub_tree"//真正的布局
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```
在==调用inflate方法之前，ViewStub一直存在于视图树中，当调用inflate之后，ViewStub被加载的视图替换==，到此，ViewStub的作用完成，之后ViewStub可能被内存回收（如果没有声明成成员变量的话，也就是没有强引用）

它的作用就是==延迟infate解析==，等到后续调用的时候==再解析对应的布局view到viewtree中==。
如果我们给view直接设置gone/invisible，那么它只是在绘制的时候会被跳过，但是依然会被解析。

## 2.Multidex

分dex越多，速度也越慢（multidex生成方法数）


### 为什么要分包？

#### 1、65536问题

- 导致因素
    
    随着项目apk的庞大以及加入更多的第三方库，app的方法数已经超过了65536，会导致程序根本跑不起来。
    
- 原因  
    在生成.dex文件后由于有很多冗余的资源，所以Android中会对dex文件进行优化，Davlik模式下利用`dexopt`工具进行优化，而dexopt有两个问题：
    
    - `Dexopt` 会把每一个类的方法 id 检索起来，存在一个链表结构里面，但是这个链表的长度是用一个 `short 类型`来保存的，导致了方法 id 的数目不能够超过65536个, 当一个项目足够大的时候，显然这个方法数的上限是不够的；
    - Dexopt 使用 `LinearAlloc` 来存储应用的方法信息, Dalvik LinearAlloc 是一个固定大小的缓冲区。在Android 版本的历史上，LinearAlloc 分别经历了4M/5M/8M/16M限制。Android 2.2和2.3的缓冲区只有5MB，Android 4.x提高到了8MB 或16MB。当方法数量过多导致超出缓冲区大小时，也会造成dexopt崩溃;
- 在`ART模式`下 ，采用的是`dexoat`工具，对应生成art虚拟执行可执行的`.oat`文件，这个是包含多个dex文件；
    

#### 2、怎么解决这个问题

- 在gradle中添加MultiDex支持,加载classes2.dex
gradle开启multiDexEnable，
在 application配置 
```java
void attachBaseContext(){ 
	MultiDex.install(this);}

```
  


## 3.分析

```shell
#使用以下命令检测启动时间
adb shell am start -S -W [packageName]/[activityName]
```

使用DebugAPI及profiler 工具诊断问题

```java
 public MyApplication() {
      //相对sd卡路径，会在sd卡中生成一个app.trace文件（需要sdcard读写权限）。将手机中的trace文件保存至电脑，随后拖入Android Studio即可。
       //跟踪方式
        Debug.startMethodTracing("app");
          //采样方式，间隔1s
          //Debug.startMethodTracingSampling("app",8*1024*1024,1000);
    }
```

在启动页的Activity中添加stopMethodTracing

```java
@Override 
public void onWindowFocusChanged(boolean hasFocus) { 
  super.onWindowFocusChanged(hasFocus); 
  Debug.stopMethodTracing(); 
}
```

生成的tarce文件使用profiler优先查看耗时较长的方法并分析代码

定位问题：避免I/O操作、反序列化、网络操作、布局嵌套等。

//例：初始化application的时候使用了sp进行io


## SplashActivity和MainActivity合二为一




# 45.热修复

相关：[[#52.插件化]]

一个叫做BootClassLoader，它负责加载Android的SDK，当代码中引用到Android框架的本身类的时候都是通过BootClassLoader进行加载的。
android类加载机制：Android的类加载和Java的类加载比较类似，都是通过ClassLoader类加载器进行加载，唯一的区别就是Android类加载器加载的是dex文件，所以在Android中加载器的基类叫做BaseDexClassLoader，这个类之下会有两个子类

- ==PathClassLoader 和 DexClassLoader **都能加载外部的 dex／apk**==，只不过区别是 ==DexClassLoader 可以**指定 optimizedDirectory**，也就是 dex2oat 的优化产物 .oat 存放的位置==，
- 而 PathClassLoader 只能使用系统默认位置。但是这个 optimizedDirectory 在 Android 8.0 以后也被舍弃了，只能使用系统默认的位置了。

对于Dex修复这种方案，基本上可以按照以下步骤：

1. 对比原始 dex 文件和新的 dex 文件，合并生成一个差分包（这个差分包包含修复后的类信息）。
2. 在应用程序运行时，下载差分包并将其放在合适的位置，例如 data/data/应用程序包名/files/dex 目录下。
3. ==重启应用程序时==，使用特定的类加载器（如DexClassLoader）读取差分包中的dex文件，并将合并后的类替换原有的类。

但是需要注意的是，热修复对于运行时错误（由于特定的运行环境导致的问题）可能效果有限。另外这种方法可能会受到Android版本和系统限制的影响，适用性可能会受限。许多第三方库（如Tinker、AndFix和Sophix）和一些开源项目提供了更为成熟、可靠的热修复方案。

还会带来一个问题就是，==跳过了oat/odex的优化，所以其实也是会降低效率的。==（因为不加载优化过的odex/oat文件而是加载合并后fix过的dex的文件）

推荐

阿里百川实现层面分析三大热修复流派（2016年文章）：https://mp.weixin.qq.com/s/uY5N_PSny7_CHOgUA99UjA?spm=a2c4g.11186623.0.0.20441896RrULI1

## 常见原理

### 1、插桩

[[#54. 字节码插桩]]

![[3b40d1c776ea80ad64be32e152cb0c86~tplv-t2oaga2asx-zoom-in-crop-mark 3024 0 0 0.webp]]

多个dex文件加载到内存中时形成**dexElement数组**，DexClassLoader当系统需要加载会遍历该数组，**将补丁dex放到该数组第一个**，第一个dex先加载了该类，后面执行的时候就会优先读取 。
类似 [[JAVA基础--JAVA数据结构--JVM#0 类与其加载机制和加载器]]

而实际情况中，做这样的方案是不行的。直接进行替换就会出现预校验的问题。

![[e3a6234eb5a639f91db1e52ea5827aa0~tplv-t2oaga2asx-zoom-in-crop-mark 3024 0 0 0.webp]]


==预检验问题是app安装时会存在dex opt操作变成odex文件==（优化dex的操作）

当==**某一个类所有的构造方法、私有方法以及重载方法所引用的其他类和这个类本身都来自于同一个dex文件的时候**，这个类就会被打上**class_ispreverified**标签。==所以如果加载的类来自于补丁文件，**而补丁文件和之前的文件必然不属于同一个dex**，而本身的那个类已经被打上了class_ispreverified标签，但是在运行时又引用了其他dex的类，这样就必然会出现错误。

解决的思路是当这些类已经被编译完成之后，在**字节码的层面去注入一些来自于其他dex的类**,也就是**插桩**。[[#54. 字节码插桩]]


### 如何防止我们源代码中所有的类被打上CLASS_ISPPREVERIFIED标记？

Android的gradle插件也提供了这样的一些接口，叫做Transform的API。这个API会提供一个调用的时机，当代码文件被编译成JAR但是还没有被打成dex的时候，提供了在这个时期做一些事情的接口。
正是利用这个接口，当拿到编译完成的字节码文件之后，可以对其进行字节码的注入，进行所谓的插桩，插入一些来自于其他dex文件的类，这样当App再被安装并执行dex    opt过程的时候就不会再被打上预校验的标签。

理论上，一个android工程中所有的java类（除了Application之外）都有可能需要热修复。==如果让这些类都去引用一个另一个dex文件中的Class，就能防止在dex进行oat的时候被打上CLASS_ISPPREVERIFIED标记。  ==
但是这样有一个弊端，==就是 CLASS_ISPPREVERIFIED带来的性能提升将会消失。但是既然出现bug，要解决，总要付出一点代价。==

==我们的思路是 在java变成class之后，在class变成 dex之前，将class进行ASM插桩引用dex文件中的类AntilazyLoad。==

所以，我们要找的 gradle task 是 ： transformClassesWithDexBuilderForRelease 或者 transformClassesWithDexBuilderForDebug 给它重写doFirst。 也可以 找到 gradle task ： compileReleaseJavaWithJavac 或者 compileDebugJavaWithJavac. 给它重写 doLast。效果相同。
> - 创建一个空白java类 AntilazyLoad。编译它，得到 AntilazyLoad.class 然后用dx命令，将它打包成hack.dex

> - 使用gradle插桩的方式，干涉gradle打包流程，==注册自定义transform(执行时机是在生成javac命令之后，在dx命令之前)获取所有经过编译的class文件==，在其构造函数中加上 AntilazyLoad 的直接使用(反射引用是不行的)。





**优势：**

1. 没有合成整包（和微信Tinker比起来），产物比较小，比较灵活
2. 可以实现类替换，兼容性高。（某些三星手机不起作用）

**不足：**

1. 不支持即时生效，必须通过重启才能生效。

2. 为了实现修复这个过程，必须在应用中加入两个dex！dalvikhack.dex中只有一个类，对性能影响不大，但是对于patch.dex来说，修复的类到了一定数量，就需要花不少的时间加载。对手淘这种航母级应用来说，启动耗时增加2s以上是不能够接受的事。

3. 在ART模式下，如果类修改了结构，就会出现内存错乱的问题。为了解决这个问题，就必须把所有相关的调用类、父类子类等等全部加载到patch.dex中，导致补丁包异常的大，进一步增加应用启动加载的时候，耗时更加严重。

### 2、instant  run方案

a. instant run 增量编译，apk生成过程

![[a8bec558f5bf9e483f0b70c6b6197edf~tplv-t2oaga2asx-zoom-in-crop-mark 3024 0 0 0.webp]]

b. 在App刚开始启动的时候，Instant Run会做以下三件事情：

1.  当bootstrap application启动之后会首先加载classes.dex和classes2.dex这两个主dex文件，当这两个主dex文件启动之后，就会启动AppServer服务。这里可以将AppServer理解为一个服务器，它会与IDE也就是Android Studio建立连接。当连接建立之后，后续在开发的过程中的代码改动所形成的补丁包都会通过这个连接下发到App上，并且通过AppServer接收，再通过相应的处理使得补丁生效。
2.  当完成了第一个步骤之后，会用本身的ClassLoader去加载instantrun.zip包里面真正的工程代码。
3.  最后一步，将宿主application替换成真实的realApplication，然后真正地运行自定义application里面的逻辑，达到隐藏自身的效果。


来源链接：https://juejin.cn/post/6844903508865449991

## 3、Tinker-- 腾讯-微信
比起之前的合并，而是==差量的方式给出patch.dex==，（将出现bug的apk包和修复后的apk包做对比）然后将patch.dex与应用的classes.dex合并，然后==整体替换掉旧的DEX==，达到修复的目的。
也就是instant  run方案：
1. 支持类替换、So 替换，资源替换是采用类似 instant-run 的方案
2. 补丁包较小，自研 diff 方案，下发的是差量包，包括的是变更的内容
3. 支持 gradle，提供了 gradle-plugin，允许我们配置很多内容
4. 采用全量 Dex 更新，不需要额外处理 `CLASS_ISPREVERIFIED` 问题
![[WEIXIN_640.jpg]]

## QQ空间超级补丁
修复CLASS_ISPREVERIFIED标志导致的unexpected DEX problem异常、加载修复的DEX。

![[QQ_640.jpg]]
优势：

1. 没有合成整包（和微信Tinker比起来），产物比较小，比较灵活
    
2. 可以实现类替换，兼容性高。（某些三星手机不起作用）
    

不足：

1. 不支持即时生效，必须通过重启才能生效。

2. 为了实现修复这个过程，必须在应用中加入两个dex！dalvikhack.dex中只有一个类，对性能影响不大，但是对于patch.dex来说，修复的类到了一定数量，就需要花不少的时间加载。对手淘这种航母级应用来说，启动耗时增加2s以上是不能够接受的事。

3. 在==ART模式下，如果类修改了结构，就会出现内存错乱的问题==。为了解决这个问题，就必须把==所有相关的调用类、父类子类等等全部加载到patch.dex中==，导致补丁包异常的大，进一步增加应用启动加载的时候，耗时更加严重。

## AndFix-阿里
核心就在于通过native层获取dvm加载的类把方法指针进行替换。

![[andfix_640.jpg]]


对于Dalvik来说，遵循JIT即时编译机制，需要在运行时装载libdvm.so动态库，获取以下内部函数：

1） dvmThreadSelf( )：查询当前的线程；

2）dvmDecodeIndirectRef()：根据当前线程获得ClassObject对象。
![[andfix_2.jpg]]

最大的优势在于

1. BUG修复的即时性
    
2. 补丁包同样采用差量技术，生成的PATCH体积小
    
3. 对应用无侵入，几乎无性能损耗
    

不足：  

1. 不支持新增字段，以及修改<init>方法，也不支持对资源的替换。
    
2. 由于厂商的自定义ROM，对少数机型暂不支持。

## 4、sophix-阿里

## so库修复方案

本质是对native方法的修复和替换。类似类修复反射注入方式，将==补丁so库的路径插入到nativeLibraryDirectories数据最前面==。
## 对比表格
![[hot_fix.jpg]]
![[3769423-a02b417960bd228e.webp]]

## Class文件转Dex文件

- 在AS build -> javac 文件中拷贝class 文件。
    
    ![](http://popus6lor.bkt.clouddn.com/Image%201.png)
    
- 在sdk 根目录下，有一个build-tools目录随便点击进入一个版本，其中有一个dx.bat文件，就是转dex 文件的脚本，命令格式：dx --dex --output=out.dex 当前class所在目录。
# 46.JETPACK--ROOM

JETPACK
# 47.Kotlin特性、编译、协程
## 基础
Kotlin编译器不会将Kotlin代码编译成Java代码。相反，它将Kotlin代码编译成Java虚拟机（JVM）==可以理解的字节码==。这意味着Kotlin代码可以与Java代码无缝交互，并且可以在JVM上运行。Kotlin编译器的编译过程包括以下步骤：词法分析、语法分析、语义分析和中间代码生成，以及目标代码生成

### 修饰符
- `public`修饰符表示 _公有_ 。此修饰符的范围最大。当不声明任何修饰符时，系统会默认使用此修饰符。
- `internal`修饰符表示 _模块_ 。对于`模块`的范围在下面会说明。
- `protected`修饰符表示 _私有`+`子类_。值得注意的是，此修饰符不能用于`顶层`声明，在下面可以看到。
- `private`修饰符表示 _私有_ 。此修饰符的范围最小，即可见性范围最低。

### 常见操作符
#### let

	1、let 是将一个lambda函数块block作为参数的函数，并提供一个默认it对象进行操作
	2、返回值为函数块的**最后一行或指定return表达式**

#### with:
with(CLASS){ return xxx; }

	1、with函数接受两个参数，第一个参数可以是一个任意类型的对象，第二个参数是一个Lambda表达式。
	2、with函数会在Lambda表达式中提供第一个参数对象的上下文，**返回值为表达式中的最后一行或指定return表达式**
	3、with提供第一个参数对象的上下文时，不改变参数对象的内部属性或方法

#### run:

	1、run函数只接受一个Lambda参数，并且在Lambda表达式中提供调用对象的上下文
	2、使用Lambda表达式中的**最后一行代码**作为返回值返回
	3、run()函数和with()函数区别不大，with()函数需要传入一个参数，然后以这个参数作为上下文进行操作。run()函数不需要传入参数，但是他需要在一个对象的基础上进行操作

#### apply:

	apply与run用法相似，不同点在于函数无法定义返回值，而是会自动返回调用对象本身而不是表达式最后一行。
#### also:
	与alppy相似，但是跟let一样提供了一个it操作

![[d9bd1d1899d040248f7b99844d80dff4.png]]
## 协程

原理：
![[344d62b5db27091361983bbf22bd9a93.png]]


## 1, 三个启动函数

lanch，async， runBlocking
- lanch：我们启动一个协程就会返回一个Job类型，可以通过该job类型进行协程的协程调用，比如job.join()类似Thread的thread.join(), 等待该内容执行完

- async：返回的是Deferred
Deferred是Java中Future的一种类似物：in封装了一个操作，该操作将在初始化后的某个时候完成。但也与Kotlin中的协程有关。async.await()来挂起当前协程，等待async中的内容执行完。

从文档：

> Deferred value is a non-blocking cancellable future — it is a Job that has a result.

因此，Deferred是具有结果的job：

## 2、suspend函数

协程代码块内必须是挂起函数，可以携带除了上列启动函数外，还可以使用withContext执行线程切换



suspend koltin编译过程（cps过程）

![[4714178-00aaf3c27f707227.webp]]

## 3、协程任务并发处理

1. 在启动更多协程之前**取消之前的任务**；
2. **让下一个任务排队**等待前一个任务执行完成；（ mutex() ）
3. 如果有一个任务正在执行，**返回该任务**，而不是启动一个新的任务。newSingleThreadContext("xxxxx")协程context，但不适合任务过多的情况，频繁切换线程上下文导致效率低下

同一个协程作用域中的异步任务遵守顺序原则开始执行; 适用于串行网络请求, 在一个异步任务需要上个异步任务的结果时.

==协程挂起需要时间, 所以异步协程永远比同步代码执行慢==

```kotlin
fun main() = runBlocking<Unit> {
    launch {
        System.err.println("(Main.kt:34)    后执行")
    }

    System.err.println("(Main.kt:37)    先执行")
}

链接：https://juejin.cn/post/6844904037586829320

```

**注意：**其实runBlocking从名字就能看的出来，是阻塞的意思，所以它启动的协程特点就是**会阻塞线程执行**。

这里其实可以从2个方面来理解，一个是阻塞线程，比如Android在主线程调用runBlocking函数，**这时线程将进入等待状态，等待runBlocking函数执行完成**，假如该函数执行太久，就会导致主线程ANR

**正常项目中，是特别不推荐使用runBlocking的**，建议只在测试中使用。

尽可能使用async.await替代



当在协程作用域中使用`async`函数时可以创建并发任务

```kotlin
public fun <T> CoroutineScope.async(
    context: CoroutineContext = EmptyCoroutineContext,
    start: CoroutineStart = CoroutineStart.DEFAULT,
    block: suspend CoroutineScope.() -> T
): Deferred<T>
复制代码
```

示例

```kotlin
fun main() = runBlocking<Unit> {
	val name = async { getName() }
    val title = async { getTitle() }

    System.err.println("(Main.kt:35)    result = ${name.await() + title.await()}")
    delay(2000)
}
链接：https://juejin.cn/post/6844904037586829320
来源：稀土掘金
```

1. 返回对象`Deferred`; 通过函数`await`获取结果值

2. Deferred集合还可以使用`awaitAll()`等待全部完成

3. 不执行`await`任务也会等待执行完协程关闭

4. 如果Deferred不执行await函数则async内部抛出的异常不会被logCat或tryCatch捕获, 但是依然会导致作用域取消和异常崩溃; 但当执  行await时异常信息会重新抛出

   ## 4. 协程间的通信

   协程与协程间不能直接通过变量来访问数据，会导致数据原子性的问题，所以协程提供了一套==Channel==机制来在协程间传递数据。

   Channel 是一个和 ==BlockingQueue==非常相似的概念。其中一个不同是它代替了阻塞的 put 操作并提供了挂起的 send，还替代了阻塞的 take`操作并提供了挂起的 receive。

   `Channel`发送和接收操作是 公平的 并且尊重调用它们的多个协程。它们遵守先进先出原则，可以看到第一个协程调用 receive 并得到了元素

   ```javascript
   import kotlinx.coroutines.*
   import kotlinx.coroutines.channels.*
   
   fun main() = runBlocking {
       val channel = Channel<Int>()
       launch {
           // 这里可能是消耗大量CPU运算的异步逻辑，我们将仅仅做5次整数的平方并发送
           for (x in 1..5) channel.send(x * x)
           channel.close()
       }
       // 这里我们打印了5次被接收的整数：
       // channel.receive()是阻塞的，等待发送数据发送完毕
       repeat(5) { println(channel.receive()) }
       println("Done!")
   }
   ```

   当发送完毕后，记得调用==channel.close()==，==close()==操作就像向通道发送了一个特殊的关闭指令。 这个迭代停止就说明关闭指令已经被接收了。所以这里保证所有先前发送出去的元素都在通道关闭前被接收到。当然你不关闭，要继续发送也可以，mvi模式的通信就是基于channel。


## 协程的实现原理
### 协程的主要构成是：
1. 挂起函数suspend，由编译器支持的挂起函数，其实就是一个标识，该函数可以在不阻塞线程的情况下挂起，他是可以暂停的，直到恢复再重新继续运行。实际上挂起函数类型最后编译结果传入Continuation类进行状态控制。
2. 协程构建器，我们调用协程的启动函数==async，lanch，runBlocking时，是把代码块封装在suspend类型的lambda表达式内==，用于后续调度。
3. 协程的作用域CoroutineScope，代表该协程是属于的范围，==可以理解为一个管理器，管理加进来的协程==，同一作用域内的协程的生命周期会受CoroutineScope影响。
4. CoroutineContext，创建的每个协程内都会有一个该上下文，主要就是存储携程的job，**CoroutineDispatcher**，协程名，**CoroutineExceptionHandler**（错误处理器，如果异常没处理会调用该处理器，如果设置了的话）
5. CoroutineDispatcher， 也就是线程调度器，和rxjava一样，Dispatcher.IO时是在维护的线程池中运行，当然前提是withCotext的时候传入的是Dispatcher.IO，才会使用线程池，不然就处理其他的线程逻辑，如 Main，newThread，newSingleThread之类的。

这里先总结出Kotlin协程在执行过程中会出现的一些概念，避免在后续源码分析中出现混淆：

- 协程体：协程中要执行的操作，它是一个被suspend修饰的lambda 表达式;
- 协程体类:  编译器会将协程体编译成封装协程体操作的匿名内部类;==即Continuation状态机控制==
- 协程构建器:  用于构建协程的函数，比如launch，async;
- 挂起函数:  由suspend修饰的函数，挂起函数只能在挂起函数或者协程体中被调用,可以在挂起函数中调用其它挂起函数实现不阻塞当前执行线程的线程切换，比如withContext()，但挂起函数并不一定会挂起，如果没有执行挂起操作;
- 挂起点：一般对应挂起函数被调用的位置;
- 续体:   续体的换概念可以理解为挂起后，协程体中剩余要执行代码，笔者在文章中，将其看作为协程体类，在协程体类中封装了协程的要执行的操作，由状态机的状态将操作分割了成不同的片段，每一个状态对应不同代码片段的执行，可以与续体的概念对应。

### 挂起/恢复/调度的原理
Kotlin 协程的挂起和恢复由==编译器和协程库共同处理==。编译器将==挂起函数转换为状态机==，以便在运行时轻松地暂停和恢复；协程库负责在需要时恢复协程的执行。==这种设计使得协程可以无需阻塞底层线程并高效地进行异步编程。==

协程不用跟线程一切换线程上下文，他的协程的数据是用户态上，不切换线程也就是不用内核态和用户态之间切换，只是在用户层面用==Dispatcher调度器模拟了线程的调度和异步效果==。

# 48.JETPACK--Navigation

简述：主要应用于导航栏之间fragment的跳转，或者fragmnet->activtiy

1.navigation虽然navigate切换fragment会重新创建，但是view的状态会被保存和恢复，除了checkbox

2.navigation控制跳转的是FragmentNavigator的navigate，其中使用的是fragmnetManager的replace的，所以每次都会被重新创建fragment，可以通过继承该FragmentNavigator()自定义重写navigate方法，改为Fragment的hide showd

# 49.Dragger2

简而言之，dagger2中组成内容的对应关系：

    类Module：使用@Module修饰，装载对象的容器。带有此注解的类，用来提供依赖，里面定义一些用@Provides注解的以provide开头的方法，这些方法就是所提供的依赖，Dagger2会在该类中寻找实例化某个类所需要的依赖。
    
    接口Component：使用@Component修饰，存放这些容器的仓库。它是连接Module和依赖注入对象的桥梁。
    
    注解@Provides：该容器创建对象的动作。
    
    注解@Inject:从容器中取出这个对象的动作。Dagger2会实例化有此注解的类


​    
​    注解@Scope：从容器取出对象的有效期，即生命周期。：Dragger2可以通过自定义注解限定注解作用域。一般说来每一个Component都有一个自己的作用域
​    
​    注解@Qualifier：用来给@Inject和@Provides贴上关联标签(进行注解)。如果一个对象可以由一个或多个容器的@Provides修饰提供，这时候就需要用Qualifier进行标签关联。当类的类型不足以鉴别一个依赖的时候，我们就可以使用这个注解标示。例如：在Android中，我们会需要不同类型的context，所以我们就可以定义qulifier注解"@perApp"和"@perActivity:，这样当注入一个context的时候，我们就可以告诉Dagger我们想要那哪种类型的context。
​    
    链接：https://www.jianshu.com/p/b5e65490e7fe
-----------------------------------
©著作权归作者所有：来自51CTO
依赖注入之Dagger2初探
https://blog.51cto.com/u_10500635/3818223

## 1.Module方式



    @Module
    public class LoginModule {
        @ActivityScope
        @Provides
        LoginPresenter getPresenter() {
            return new LoginPresenter();
        }
    
    }

3.编写Component装载module



    （1）使用Dagger2单独编写Component装载module
    
    @ActivityScope
    @Component(modules = {LoginModule.class})
    public interface LoginComponent {
        void inject(LoginActivity activity);
    }

（2）使用Dagger-Android 统一生成装载module



    @ActivityScope
    @ContributesAndroidInjector(modules = LoginModule.class)
    abstract/interface LoginActivity contributeSecondActivityInjector();

4.编译工程

     AndroidStudio -> Build -> Make Project

5.进行依赖注入

(1)使用Dagger2,通过Component取出module注入依赖



    @Override
    protected void onResume() {
       super.onResume();
       DaggerLoginComponent.builder().loginModule(new LoginModule()).build().inject(this);
    
    }

(2)使用Dagger-Android在BaseActivity中统一AndroidInjection统一取出module注入依赖

```
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    AndroidInjection.inject(this);  //统一注入
    super.onCreate(savedInstanceState);
    setContentView(getLayoutId());
```



## 2.Field Inject

1.表明注入的类

```kotlin
class Menu @Inject construct(){
    fun cake()
}
```



```java
class Cook{
    @Inject
    Menu menu;
    
}
```

# 50.自动化测试

## 1.MonkeyTest

## 2.Mockito

## 3.Espresso

# 51.跨进程通信

## 传统IO
![[截图20230818021640.png]]

## 2.广播
基于binder

## 3.SOCKET 套接字
两种：
网络：internet socket 
系统间：Unix Domian SOCKET

实际上还是==内核空间通信，拷贝到内核空间的写入数据缓冲区，内核空间输出缓冲区再复制到对应的用户空间。==
==两次拷贝==，c/s安全性不可靠由上层自己控制，控制不好就会导致身份不可靠、


## 4. 管道机制
Handler  采用的管道通信[[#线程阻塞和唤醒/nativePollOnce和wake()]]

## 5. 共享内存
无需拷贝到内核空间，实现和控制复杂，双端自行处理并发同步等问题。访问接入点不安全，访问内存的身份不确定。

## Messager
Messenger底层也是通过aidl实现，不过封装了一层，aidl支持多线程并发。messenger是同步，如果没有多线程并发要求，就可以使用轻量级的Messenger。

注册service
```xml
<service android:name="[你的包名].main.MessengerService"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
            <action android:name="随便写xxx.MessengerService" />
            </intent-filter>
        </service>

```
```java
/**
 * //0. 去清单文件中配置service并设置启动action
 */
public class MessengerService extends Service {
    private static final String TAG = "MessengerService";

//    1.创建接受信息的handler
    @SuppressLint("HandlerLeak")
    private Handler msgHandler = new Handler(){
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            // 4.接口客户端消息
            Bundle data = msg.getData();
            String clientMsg = data.getString("client");
            Log.i(TAG, "客户端消息: "+clientMsg);

            //5.获取Messenger对象发送消息
            Message message = Message.obtain();
            Bundle serviceBundle = new Bundle();
            serviceBundle.putString("service","服务端收到消息");
            message.setData(serviceBundle);
            try {
                Messenger clientMessenger = msg.replyTo;
                clientMessenger.send(message);
            } catch (RemoteException e) {
                e.printStackTrace();
            }
        }
    };
//    2.创建Messenger对象,并绑定Handler
    private final Messenger mServiceMessenger = new Messenger(msgHandler);

//    3.通过Messenger获取Binder对象
    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        // IMessenger.asBinder()
        return mServiceMessenger.getBinder();
    }
}
```

```java
 //1. 绑定service，获得IBinder回调
    ServiceConnection conn = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            Log.i(TAG, "onServiceConnected: " + name);
            //3. 获取服务端的Messenger对象
            Messenger serviceMessenger = new Messenger(service); // 利用Messenger的构造方法获取IMessenger.Stub.asInterface(service);是底层生成好的aidl实现类IMessenger
            //4. 准备消息
            Message message = Message.obtain();
            Bundle b = new Bundle();
            b.putString("client", "服务端你好，我是客户端");
            message.setData(b);

            //7.在消息中添加客户端回复入口Messenger，Optional Messenger where replies to this message can be sent.
            message.replyTo = mClientMessenger;
            //8.发送消息，通过IMessenger发送消息
            try {
                serviceMessenger.send(message);
            } catch (RemoteException e) {
                e.printStackTrace();
            }

        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            Log.i(TAG, "onServiceDisconnected: " + name);
        }
    };

    //2. 绑定服务端服务
    private void connectService() {
        Intent intent = new Intent(SERVICE_ACTION);
        intent.setPackage(SERVICE_PACKED);
        boolean b = bindService(intent, conn, Context.BIND_AUTO_CREATE);
        Log.i(TAG, "connectService: 绑定服务 " + b);
    }

    //5 创建客户端接受消息的handler
    @SuppressLint("HandlerLeak")
    private Handler handler = new Handler() {
        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);
            Bundle bundle = msg.getData();
            String service = bundle.getString("service");
            Log.i(TAG, "服务端的回复： " + service);

        }
    };

    //6.创建客户端Messenger，并绑定handler
    private Messenger mClientMessenger = new Messenger(handler);
```
## BINDER

启动时机：[[#ServiceManager]]
binder是linux内核的一个重要驱动，是Android系统上核心的进程通信方式，系统进程初始化时的通信，intent数据，ams与acitviy，wms与window，windowmanager通信都是通过Binder机制。
**实现原理是通过内存映射mmap进行的，实现一次拷贝, 基于C/S架构安全稳定。==内核添加了PID身份识别**==

![[截图20230818021308.png]]


由idle进程创建时挂载驱动，由serviceMangaer进程创建时通过ProcessState初始化binder驱动，并且由它管理各个service的binder。

![[截图20230817200243.png]]


为什么设计成驱动的形式？
第一点是为了程序源码和Binder分离，不用关心Bidner内部的的资源分配和实现。
第二点主要是为了方便调用，不同代码的都可以调用Binder虚拟驱动。

什么是ioctl？
ioctl是设备驱动程序中对设备的I/O通道进行管理的函数。所谓对I/O通道进行管理，就是对设备的一些特性进行控制，例如串口的传输波特率、马达的转速等等。可以通过在用户程序中使用ioctl函数来控制设备的I/O通道。
### ProcessState的初始化

ProcessState的主要关键点有以下几个：

- 保证同一进程只有一个ProcessState实例，且只有在ProcessState对象建立时才打开Binder设备以及做内存映射
- 向上层提供IPc服务
- 与IPCThreadState分工
- 其实ProcessState还是顾名思义是查看进程状态的，IPCThreadState才是transact进行Binder通信的地方


我们先来分析main中的第一步，也就是ProcessState::self()的过程。

```cpp
    @ProcessState.cpp(google-code\frameworks\native\libs\binder\)sp<ProcessState>
     ProcessState::self() {
     if (gProcess != NULL) 
	     return gProcess;
     AutoMutex _l(gProcessMutex);
     if (gProcess == NULL) 
	     gProcess = new ProcessState;
     return gProcess;}
```

这里我们看到， **ProcessState是一个单例模式，一个进程有一个ProcessState对象**。如果初始化过了（gProcess!=NULL），就直接返回给客户端使用即可，没有的话需要创建ProcessState对象。这里我们假设当前没有被初始化过，那么就会调用他的构造函数：

```cpp
//大小，1m - 两个内存分页大小 = 1mb - 8kb
	#define BINDER_VM_SIZE_(1 * 1024 * 1024 - sysconf(_SC_PAGE_SIZE * 2))
	
    ProcessState::ProcessState(): 
    mDriverFD(open_driver()), //打开驱动
    mVMStart(MAP_FAILED), 
    mManagesContexts(false), 
    mBinderContextCheckFunc(NULL), 
    mBinderContextUserData(NULL), 
    mThreadPoolStarted(false), 
    mThreadPoolSeq(1) {
	    if (mDriverFD >= 0) {
	    
	    //映射内存，返回的是首地址
	    //mDriverFD 驱动文件的文件描述符
	    mVMStart = mmap(0, BINDER_VM_SIZE, PROT_READ, MAP_PRIVATE | MAP_NORESERVE, mDriverFD, 0);
	    if (mVMStart == MAP_FAILED) {
		    close(mDriverFD);
		    mDriverFD = -1;
	    }}
    }
```

        在他的初始化过程中，主要完成了两个重要步骤：  
        1、 **通过open_driver打开binder驱动**，并把binder设备句柄传给mDriverFD  
        2、 **通过mmap()将一块内核地址映射到用户空间，作为buffer传输数据**。  

        由此，就完成了ProcessState的初始化工作。
        
### IPCThreadState

Binder最后调用是在native层的IPCThreadState，负责管理Binder线程和通信。
![[20200908182002616.png]]
**IPCThreadState并不是一个单独的进程**，而是属于一个进程的一个线程。在Android系统中，每个应用程序进程都会有一个与之关联的Binder线程池，其中包括多个IPCThreadState线程。这些线程可以与ServiceManager和其他进程进行通信，以实现跨进程通信。

代码位置：/frameworks/native/libs/binder/IPCThreadState.cpp

1. 单实例构造函数：
```c
IPCThreadState* IPCThreadState::self()
{
    if (gHaveTLS) { 
    //当执行完第一次之后，再次运行的时候就已经有IPCThreadState实例，只需要获取就可以使用
restart:
        const pthread_key_t k = gTLS;
        IPCThreadState* st = (IPCThreadState*)pthread_getspecific(k);
        if (st) return st;
        return new IPCThreadState;
    }

    if (gShutdown) {
        ALOGW("Calling IPCThreadState::self() during shutdown is dangerous, expect a crash.\n");
        return NULL;
    }

    pthread_mutex_lock(&gTLSMutex);
    if (!gHaveTLS) {       
    //初始的gHaveTLS的值false，所以第一次调用的时候，会执行这里的代码
    //随后将gHaveTLS设置为true
        int key_create_value = pthread_key_create(&gTLS, threadDestructor);
        if (key_create_value != 0) {
            pthread_mutex_unlock(&gTLSMutex);
            ALOGW("IPCThreadState::self() unable to create TLS key, expect a crash: %s\n",
                    strerror(key_create_value));
            return NULL;
        }
        gHaveTLS = true;
    }
    pthread_mutex_unlock(&gTLSMutex);
    goto restart;
}
```
- 现有的调用分析顺序是：</br>  
    getService@ServiceManagerProxy-->transact@BinderProxy-->transact@BpBinder-->transact@IPCThreadState</br>
- 不管是读取还是写入，Binder驱动都只是发挥中间人的作用，真正处理请求的还是Binder Client以及Binder Server双方。
- 真正与Binder打交道的地方时talkWithDriver中的ioctl()


### 问题
1. binder支持多线程吗？支持，内部会上同步锁，这也是为什么forkZygote使用socket(UDS)防止新进程死锁。

3. intent为什么不支持传输大数据？

## 4.AIDL
基于binder

AIDL机制Binder机制图。


![[aidl.webp]]
aild创建说明:


在实现 AIDL 接口时，您应注意遵守以下规则： 

- 由于无法保证在主线程上执行传入调用，因此您一开始便需做好多线程处理的准备，并对您的服务进行适当构建，使其达到线程安全的标准。
- **默认情况下，RPC 调用是同步调用**。如果您知道服务完成请求的时间不止几毫秒，则不应从 Activity  的主线程调用该服务，因为这可能会使应用挂起（Android 可能会显示“Application is Not Responding”对话框）—  通常，您应从客户端内的单独线程调用服务。 //服务端运行在服务端的binder线程池，但是客户端的会被挂起。
- 您引发的任何异常都不会回传给调用方。
## 基本使用
创建一个aidl文件，语法跟接口一样
```java
package android.os;

/** {@hide} */
interface IMyTestService
{
    void open();
    void close();
}
```


## AIDL原理解析

AIDL实现的binder是没有在serviceManger中注册的，也就是意味着他是匿名的binder，
![[截图20230823031210.png]]
![[20140405215455890.png]]
**我们如果通过AIDL去编写的话，Application.mk会帮我们编译aidl文件生成对应的stub类、proxy类和对应的客户端可调用的接口。**
asInterface()：客户端在ServiceConnection通过IStudentService.Stub.asInterface(IBinder)，
会根据是同一进行通信， 还是不同进程通信，返回Stub()实体，或者Stub.Proxy()代理对象
**以下是aidl生成的代理类简化模板**
```kotlin
interface IDieDieService: IInterface{//用于ServiceConnection时的IBind.asInterface  
    fun goDie()  
    fun goDieDie(parcel:String):String?  
  
     abstract class Stub: Binder(), IDieDieService {  
         val first = 1;  
         val goDie = first +1  
         val goDieDie = first +2  
        companion object{  
            val DESCRIPTOR = "com.example.asmbytecodeinsertapp.aidl.Iservice"  
           val TRANSACTION_goDie = IBinder.FIRST_CALL_TRANSACTION + 0;  
           val TRANSACTION_goDieDie = IBinder.FIRST_CALL_TRANSACTION + 1;  
  
            fun asInterface( obj:IBinder):Proxy?{  
  
                if (obj == null) {  
                    return null;  
                }  
  
                 val iin: IInterface? = obj.queryLocalInterface(DESCRIPTOR);  
                if (iin != null && (iin is IDieDieService)) {  
                    return iin as IDieDieService;  
                }  
  
                // 传入binder对象，返回一个服务代理对象  
                return  Proxy(obj);  
  
            }  
        }  
  
         override fun onTransact(code: Int, data: Parcel, reply: Parcel?, flags: Int): Boolean {  
             when (code) {  
                 TRANSACTION_goDieDie->{  
                     // 获取IBinder接口标志  
                     data.enforceInterface(DESCRIPTOR);  
                     val readString = data.readString()//代理对象传过来  
                     return if (!TextUtils.isEmpty(readString)){  
                         //这个是服务端自己实现 方法,获取返回值  
                         val stringResult = this.goDieDie(readString);  
                         reply?.writeString(DESCRIPTOR);  
                         true;                     }else{  
                         false;  
                     }  
  
                 }else{  
                    return false;  
                 }  
             }  
         }  
    }  
  

    class Proxy(val remote: IBinder) :IDieDieService{  
        init {  
            Log.e(  
                "test",  
                "===Proxy中 mRemote 是一个什么对象: " + remote.javaClass.canonicalName  
            )  
        }  
  
  
        override fun asBinder(): IBinder {  
            return remote  
        }  
        @Throws(RemoteException::class)  
        override fun goDie() {  
            var data = Parcel.obtain() // 跨进程传输数据对象  
            var reply = Parcel.obtain() // 跨进程传输返回结果  
                //....  
        }  
        @Throws(RemoteException::class)  
        override fun goDieDie(parcel: String): String? {  
            var data = Parcel.obtain() // 跨进程传输数据对象  
            var reply = Parcel.obtain() // 跨进程传输返回结果  
            try {  
                data.writeInterfaceToken(Stub.DESCRIPTOR);  
                data.writeString(parcel)  
                // 调Stub的onTransact方法进行Stub.TRANSACTION_getString，远端返回  
                Log.e("test", "mRemote transact 当前线程 :" +   Thread.currentThread().getName());  
                remote.transact(Stub.TRANSACTION_goDie, data, reply, 0);  
  
                reply.readException();  
                return reply.readString()  
            }finally {  
                data.recycle();  
                reply.recycle();  
            }  
        }  
  
    }  
  
}

```

```kotlin

class DieDieService: Service() {  
    override fun onBind(intent: Intent?): IBinder? {  
        return object :IDieDieService.Stub(){  
            @Throws(RemoteException::class)  
            override fun goDie() {  
  
            }  
  
            override fun goDieDie(parcel: String): String? {  
                return parcel + "你大爷"  
            }  
  
  
            override fun asBinder(): IBinder {  
                return this;  
            }  
        }  
    }  
}
```

从字面意思看，Stub是存根，Proxy是代理，而这个Stub是谁的存根呢？Proxy又是谁的代理呢？


我理解的是 :  

Stub是 服务端Binder实体的存根.

Proxy则是Stub的代理. 

#### Stub类
服务实体，Binder的实现类，服务端一般会实例化一个Binder对象，在服务端onBind中绑定， 客户端asInterface获取到Stub。==这个类在编译aidl文件后自动生成，它继承自Binder，表示它是一个Binder本地对象； 它是一个抽象类，实现了IInterface接口，表明它的子类需要实现Server将要提供的具体能力（即aidl文件中声明的方 法）==
stub 类是抽象类，里面实现了服务端接收到binder信息后根据 TRANSACTION_XXX() 字符串匹配对应的处理方法并且调用对应的抽象方法（也就是我们在service中实现的方法），抽象方法在service中新建new stub的时候实现。

#### Stub.Proxy类
客户端调用在serviceConnection的bind回调的时候的Ibinder对象（也就是上面的Stub实体），asInterface在stub中实现了新建并返回了Proxy对象，所以我们调用的方法时候是会被该Proxy对象代理的。

它实现了IInterface接口，说明它是 Binder通信过程的一部分；它实现了aidl中声明的方法，但实际上调用帮我们使用了parcel进行数据的传输和接收，==调用remote.transact(xxxx)开始进程通信直到调用到服务端Stub中方法==。调用IPCThread的transact方法进行通信[[#IPCThreadState]]


# 52.断点续传

## Http 怎么支持断点续传的？

Http 1.1 协议中默认支持获取文件的部分内容，这其中主要是通过头部的两个参数：Range 和 Content Range 来实现的。客户端发请求时对应的是 Range ，服务器端响应时对应的是 Content-Range。

客户端想要获取文件的部分内容，那么它就需要请求头部中的 Range 参数中指定获取内容的起始字节的位置和终止字节的位置，它的格式一般为：

```perl
Range:(unit=first byte pos)-[last byte pos]
```

例如：

```ini
Range: bytes=0-499      表示第 0-499 字节范围的内容 
Range: bytes=500-999    表示第 500-999 字节范围的内容 
Range: bytes=-500       表示最后 500 字节的内容 
Range: bytes=500-       表示从第 500 字节开始到文件结束部分的内容 
Range: bytes=0-0,-1     表示第一个和最后一个字节 
Range: bytes=500-600,601-999 同时指定几个范围
```

### Content Range

在收到客户端中携带 Range 的请求后，服务器会在响应的头部中添加 Content Range 参数，返回可接受的文件字节范围及其文件的总大小。它的格式如下：

```perl
Content-Range: bytes (unit first byte pos) - [last byte pos]/[entity legth]

```

例如：

```less
Content-Range: bytes 0-499/22400    // 0－499 是指当前发送的数据的范围，而 22400 则是文件的总大小。
```

**使用断点续传和不使用断点续传的响应内容区别**

- 不使用断点续传

```rust
HTTP/1.1 200 Ok
```

- 使用断点续传

```css
HTTP/1.1 206 Partial Content
```

## 处理请求资源发生改变的问题

在现实的场景中，服务器中的文件是会有发生变化的情况的，那么我们发起续传的请求肯定是失败的，那么为了处理这种服务器文件资源发生改变的问题，在 RFC2616 中定义了 **Last-Modified** 和  **Etag** 来判断续传文件资源是否发生改变。

**Last-Modified & If-Modified-Since（文件最后修改时间）**

- **Last-Modified**：记录 Http 页面最后修改时间的 Http 头部参数，Last-Modified 是由服务端发送给客户端的
- **If-Modified-Since**：记录 Http 页面最后修改时间的 Http 头部参数，If-Modified-Since 是有客户端发送给服务端的
- 验证过程
  - step 1：客户端缓存从服务端获取的页面
  - step 1：客户端访问相同页面时，客户端将服务器发送过来的  Last-Modified 通过 If-Modified-Since 发送给服务器
  - step 2：服务器通过客户端发送过来的 If-Modified-Since 进行判断客户端当前的缓存的页面是否为最新的
    - **如果不是最新的，那么就发送最新的页面给客户端**
    - **如果是最新的，那么就发送 304 告诉客户端它本地缓存的页面是最新的**

## OkHttp 断点下载

### 断点下载思路

- step 1：判断检查本地是否有下载文件，若存在，则获取已下载的文件大小 downloadLength，若不存在，那么本地已下载文件的长度为 0
- step 2：获取将要下载的文件总大小（HTTP 响应头部的 content-Length)
- step 3：比对已下载文件大小和将要下载的文件总大小（contentLength），判断要下载的长度
- step 4：再即将发起下载请求的 HTTP 头部中添加即将下载的文件大小范围（Range: bytes = downloadLength - contentLength)



### 主要要点



```java
  /** HTTP请求是有一个Header的，里面有个Range属性是定义下载区域的，它接收的值是一个区间范围，        
  * 比如：Range:bytes=0-10000。这样我们就可以按照一定的规则，将一个大文件拆分为若干很小的部分，        
  * 然后分批次的下载，每个小块下载完成之后，再合并到文件中；这样即使下载中断了，重新下载时，         
  * 也可以通过文件的字节长度来判断下载的起始点，然后重启断点续传的过程，直到最后完成下载过程。*/
```
# Gradle管理
## 依赖管理
api和implementation的区别

如下表所示：

||api|implementation|
| ---- | ---- | ---- |
|编译时|能进行依赖传递；底层变，上层全部都要变；编译速度慢	|不能进行依赖传递；底层变，上层不会变化；|
|运行时|运行时会加载，所有模块的类都会被加载	|编译速度快,运行时会加载，所有模块的类都会被加载|
|应用场景|	适用于多模块依赖，避免重复依赖	|多数情况下使用implementation|

# 52.插件化
紧密关联：
[[#45.热修复]]
[[#19.ARoute和组件化]]
[[#换肤框架]]
[[#dex加載流程]]]


![[v2-1712eb8d789f4aff59a805f4a641aa55_720w.webp]]
App的部分功能模块在打包时并不以传统⽅式打包进apk⽂件中，⽽是以另⼀种形式⼆次封装进apk内部，或者放在⽹络上适时下载，在需要的时候动态对这些功能模块进⾏加载，称之为插件化。这些单独⼆次封装的功能模块apk，就称作插件，初始安装的apk称作宿主。插件化是组件化的更进⼀步推进。

## 类加载原理



说起热修复就不得不提类的加载机制，和常规的JVM类似，在Android中类的加载也是通过ClassLoader来完成，具体来说就是PathClassLoader 和 DexClassLoader 这两个Android专用的类加载器，这两个类的区别

PathClassLoader 和 DexClassLoader **都能加载外部的 dex／apk**，只不过区别是 DexClassLoader 可以**指定 optimizedDirectory**，也就是 dex2oat 的产物 .odex 存放的位置，而 PathClassLoader 只能使用系统默认位置。但是这个 optimizedDirectory 在 Android 8.0 以后也被舍弃了，只能使用系统默认的位置了。


这两个类都是继承自**BaseDexClassLoader**，我们可以看一下BaseDexClassLoader的构造函数。

```java
    public BaseDexClassLoader(String dexPath, File optimizedDirectory,
            String libraryPath, ClassLoader parent) {
        super(parent);
        this.pathList = new DexPathList(this, dexPath, libraryPath, optimizedDirectory);
    }
```

这个构造函数只做了一件事，就是通过传递进来的相关参数，初始化了一个DexPathList对象。**DexPathList的构造函数，就是将参数中传递进来的程序文件（就是补丁文件）封装成Element对象，并将这些对象添加到一个Element的数组集合dexElements中去**。

> ClassLoader 的加载机制是一种特别聪明的方式，**双亲委托机制**，在这种机制下，一个Class只会被加载一次。

bootclassLoader：android sdk加载使用的classloader,一般是最父级
[[类加载器]]

//该图对于PathClassLoader和DexClassLoader的说明有误，请忽略
![[8623410-5df3d39a60d8aaf3.webp]]

（全程使用反射，因为很多类无法直接获取到或者直接操作）

我们通过反射获取当前宿主的类加载器的dexpathlist，**再反射获取dexElements[]->也就是多dex, 一个element都是对应一个dexfile信息**

![](微信图片_20230310231251.png)

然后**新创DexClassLoader加载我们的补丁dex/apk，获取到补丁的dexElement[]**

创建新的dexElement[]，把两个dexElenemts[]合并进去，设置到原先类加载器中，把原先的dexElement替换成新的

![](微信截图_20230310232926.png)
```kotlin
//代码整合一下
fun loadPlugin(context:Context){  
    val dexPathClass = Class.forName("dalvik.system.DexPathList")  
    val dexElementField = dexPathClass.getDeclaredField("dexElements")  
    dexElementField.isAccessible = true  
  
    //把系统的classLoader中的pathlist偷偷操作，  
    val classLoader = Class.forName("dalvik.system.BaseDexClassLoader")  
    val pathListField = classLoader.getDeclaredField("pathList")  
    pathListField.isAccessible = true  
    val realClassLoader = context.classLoader  
    val pathList = pathListField.get(realClassLoader)  
    val hostDexArray = dexElementField.get(pathList) as Array<Any>  
  
  
    //  
    val pluginClassLoader = DexClassLoader(apkPath, context.cacheDir.absolutePath, null, realClassLoader)  
    val pluginPathList = pathListField.get(pluginClassLoader)  
    val pluginDexArray = dexElementField.get(pathList) as Array<Any>  
      
    //接下来把 hostDexArray 和 pluginDexArray 合并就完事了  
    //在设置到真实的ClassLoder里面即可  
}
```
之后我们就可以直接用反射获取到插件中的类了


## 常见问题
### 问题⼀：未注册的组件（例如Activity）不能打开
	下面介绍如何通过hook的方式启动插件中的Activity，需要解决以下两个问题：
		- 插件中的Activity没有在AndroidManifest中注册，如何绕过检测。
		- 如何构造Activity实例，同步生命周期

- 解决⽅式⼀：代理Activity
	##### 代理模式（DL框架）

ProxyActivity + 插件中没注册的Activity = 标准的Activity
其实就是通过proxyActivtiy对应的生命周期反射初始化、执行真实的activity的对应生命周期。
这样完全依赖于代理activity。

![](https://upload-images.jianshu.io/upload_images/8623410-3fe0083075551219.png?imageMogr2/auto-orient/strip|imageView2/2/w/667/format/webp)
### 解决⽅式⼆：欺骗系统

其实有很多hook点，Instrument或者AMS proxy的hook
这里说一下Instrument的hook
下面介绍如何通过hook的方式启动插件中的Activity，需要解决以下两个问题：

- 插件中的Activity没有在AndroidManifest中注册，如何绕过检测。
- 如何构造Activity实例，同步生命周期

解决方法有很多种，以VirtualAPK为例，核心思路如下：

1. 先在Manifest中预埋StubActivity，启动时hook上图第1步，将Intent替换成StubActivity。
2. hook第10步，通过插件的ClassLoader反射创建插件Activity
3. 之后Activity的所有生命周期回调都会通知给插件Activity

**替换系统Instrumentation**

VirtualAPK在初始化时会调用hookInstrumentationAndHandler，该方法hook了系统的Instrumentaiton类，由上文可知该类和Activity的启动息息相关。

![](https://pic2.zhimg.com/80/v2-92017c4e0195812284b412c61d266aa9_720w.webp)

该段代码将主线程中的Instrumentation对象替换成了自定义的VAInstrumentation类。在启动和创建插件activity时，该类都会偷偷做一些手脚。

**hook activity启动过程**

VAInstrumentation类重写了execStartActivity方法，相关代码如下：

![](https://pic4.zhimg.com/80/v2-cabeb9d3ea1da37e40b4156a6420d25f_720w.webp)

先通过隐式启动activtiy
```java
intent.setComponent( new Componen("插件的包名", " 目标activity") )
```

execStartActivity中会先去处理隐式intent，如果该隐式intent匹配到了插件中的Activity，将其转换成显式。之后通过markIntentIfNeeded将待启动的的插件Activity替换成了预先在AndroidManifest中占坑的StubActivity，并将插件Activity的信息保存到该intent中。其中有个dispatchStubActivity函数，会根据Activity的launchMode选择具体启动哪个StubActivity。VirtualAPK为了支持Activity的launchMode在主工程的AndroidManifest中对于每种启动模式的Activity都预埋了多个坑位。

**hook Activity的创建过程**



上一步欺骗了系统，让系统以为自己启动的是一个正常的Activity。当来到图 3.2的第10步时，再将插件的Activity换回来。此时调用的是VAInstrumentation类的newActivity方法。

![](https://pic4.zhimg.com/80/v2-522e144df4c3b17a25fbe19db67e9897_720w.webp)

由于AndroidManifest中预埋的StubActivity并没有具体的实现类，所以此时会发生ClassNotFoundException。之后在处理异常时取出插件Activity的信息，通过插件的ClassLoader反射构造插件的Activity。

**其他操作**

插件Activity构造出来后，为了能够保证其正常运行还要做些额外的工作。

![](https://pic2.zhimg.com/80/v2-7c1fd17dc4dde4f4c3ec8a40e25ecc8d_720w.webp)

这段代码主要是将Activity中的Resource，Context等对象替换成了插件的相应对象，保证插件Activity在调用涉及到Context的方法时能够正确运行。

经过上述步骤后，便实现了插件Activity的启动，并且该插件Activity中并不需要什么额外的处理，和常规的Activity一样。
那问题来了，之后的onResume，onStop等生命周期怎么办呢？
答案是所有和Activity相关的生命周期函数，系统都会调用插件中的Activity。**原因在于AMS在处理Activity时，通过一个token表示具体Activity对象，而这个token正是和启动Activity时创建的对象对应的，而这个Activity被我们替换成了插件中的Activity，所以之后AMS的所有调用都会传给插件中的Activity**。

#### 总结
Instrument或者AMS proxy（也就是hoosk app的IActivtiyManger）其实都是通过在即将于ams通信的时候把proxy对象传给远端AMS去启动，用注册过的proxy activity欺骗插件 activtiy 没有注册这一件事，区别的在于hook点不同。

AMS hook是通过在发起binder之前修改intent信息为proxy的activtiy，在收到ams回调之后的activityThread中handleLanchActivtiy时候的 handler的msg携带的ActivityClientRecord进行了信息修改成代理的acitivtiy信息。（替换时机比Instrument.newActivtiy要早）

  ### 解决⽅式三：重写gradle打包过程，合并AndroiManifest.xml
强制写入但是缺点是启动的activtiy也已经是固定了，灵活性不够高

### 问题⼆：资源⽂件⽆法加载
相关：[[#换肤框架]]
解决⽅式：⾃定义AssetManager和Resources对象

```java
   private AssetManager createAssetManager (String dexPath) {
        try {
            AssetManager assetManager = AssetManager.class.newInstance();
            //就是这个addAssetPath
            Method addAssetPath = assetManager.getClass().getMethod("addAssetPath", String.class);
            addAssetPath.invoke(assetManager, dexPath);
            return assetManager;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
```
## 插件化有什么用？

- 早期：解决dex 65535问题。⾕歌后来也出了multidex⼯具来专⻔解决
- 懒加载来减少软件启动速度：有可能，实质上未必会快
- 减⼩安装包⼤⼩：可以
- 项⽬结构拆分，依赖完全隔离，⽅便多团队开发和测试，解决了组件化耦合度太⾼的问题：这个使⽤模块化就够了，况且模块化解耦不够的话，插件化也解决不了这个问题
- 动态部署：可以
- 热修复：可以

提醒：无论是热修复还是插件化，一定要注意的是不同android版本下的环境大概率会有不同。

# 53.webview优化
## 前言
跨平台和动态性要求，现在很多app都会嵌入h5页面，称为混合开发（HyBrid技术），在需要与Native交互时（调用原生接口）再通过jsBrigde与本地交互。
拓展说明：[[Android跨平台--Flutter混合开发]]
[[Android跨平台--Flutter混合开发#2. Hybird技术：]]


## 53.1. webview优化
### 一般的耗时操作：
- 初始化: 耗费时间最多,第二次加载就快了。特别是单进程webview，初次初始化时需要初始化进程。
- 初始化hybrid，解析hybrid的模板头部和body解析。
- 网络请求，加载正文和渲染界面，解析界面，以及加载界面js文件，等待数据响应，解析json生成dom。最耗时
- 加载图片，除了请求图片之外还要缓存图片（io操作），缓存之后再读取（二次io）出来。第二耗时

补充图--hybird工作流程：![](d2b9c7c289444b75a8b744e65cf55d3c.jpg)

### 优化方向：
1. webview初始化，初始化web内核的时间会比较久，可以提前初始化，加载时机可以选择在线程空闲的时候加载，比如通过IdleHandler初始化，即可以在MessageQueue空闲时操作。由于初始化需要activity的context，可以通过先传入MutableContextWrapper, 用applicationcontext初始化， 实际绑定的时候的再传入activtiy context.

2. 固定网络模板的请求
预制离线包，抽取公用的js和css作为通用模板，按照业务类型分多套模板，模板不同版本号，app再后台时定时默认更新。js和css或者通过内联到一个html中，减少每次都要下载和读取的io操作。
适合与页面不经常变化的场景。

3. 并行请求
h5加载模板文件的时候，用native加载正文（主要内容）后通过js传h5渲染。

4. 预加载
 当模板和正文分离之后，预加载webview的时候，先预加载模板。

5.静态直出
 
6.复用webview
在第一点中创建缓存池，每次使用后清空正文数据存入缓存池。这样不用反复创建webview和预热模板。

平时使用WebView的时候我们都是动态新建并添加到ViewGroup中，新建时就传入了Context，那如果使用缓存池，Context该怎么传呢？

**一种方案是直接用ApplicationContext新建WebView；另一种方案是使用MutableContextWrapper，如果在某个Activity中被使用了就改为该Activity的Context，当 Activity 退出时为了防止内存泄漏再将 WebView 的 Context 替换回ApplicationContext。**一个简易版的WebView缓存池实现如下。

注意在APP启动时应该就要调用WebViewPool的初始化方法新建一个WebView，不然启动第一个包含WebView的页面时耗时也会很久。

  ```csharp
public class WebViewPool {

    private List<DetailWebView> mIdleWebViewList;
    private List<DetailWebView> mUsingWebViewList;

    private static class Holder {
        private static WebViewPool sInstance = new WebViewPool();
    }

    public static WebViewPool getInstance() {
        return Holder.sInstance;
    }

    private WebViewPool() {
    }

    /**
     * 在 APP 启动时调用, 直接新建一个备用的WebView
     */
    public void init() {
        mIdleWebViewList = new CopyOnWriteArrayList<>();
        mUsingWebViewList = new ArrayList<>();
        MutableContextWrapper contextWrapper = new MutableContextWrapper(App.getContext());
        DetailWebView detailWebView = new DetailWebView(contextWrapper);
        mIdleWebViewList.add(detailWebView);
    }

    public DetailWebView acquireWebView(Context context) {
        if (mIdleWebViewList != null && mIdleWebViewList.size() > 0) {
            DetailWebView webView = mIdleWebViewList.remove(0);
            MutableContextWrapper contextWrapper = (MutableContextWrapper) webView.getContext();
            contextWrapper.setBaseContext(context);
            mUsingWebViewList.add(webView);
            return webView;
        } else {
            MutableContextWrapper contextWrapper = new MutableContextWrapper(context);
            DetailWebView webView = new DetailWebView(contextWrapper);
            mUsingWebViewList.add(webView);
            return webView;
        }
    }

    public void recycleWebView(DetailWebView webView) {
        if (webView == null) {
            return;
        }
        ViewGroup viewParent = (ViewGroup) webView.getParent();
        if (viewParent != null) {
            viewParent.removeView(webView);
        }
        webView.loadUrl("about:blank");
        if (mUsingWebViewList != null && mUsingWebViewList.contains(webView)) {
            mUsingWebViewList.remove(webView);
            MutableContextWrapper contextWrapper = (MutableContextWrapper) webView.getContext();
            contextWrapper.setBaseContext(App.getContext());
            webView.setWebViewClient(null);
            webView.setWebChromeClient(null);
            mIdleWebViewList.add(webView);
        } else {
            webView.clearHistory();
            webView.destroy();
        }
    }
}
```


7.Http缓存策略
1. http缓存cache-control

2. 使用okhttp代理请求，进行客户端http缓存。
总结：
页面js，css，模板分离一类的机制都需要前端的配合操作才能实现。

## 53.2. 多进程

多进程预加载

# 54. 字节码插桩

[[#45.热修复]]

所谓字节码插桩，*就是在class文件里动态注入新的字节码，增强原有字节码的功能。当然也可以修复和删除原有字节码*。它可以不用写java代码来实现功能的新增。（埋点、方法执行时间--卡顿检测、内存泄漏、热修复）
一般是
**通过Gradle的Transform API来过滤出class，**
然后通过ASM框架或者Javaassit框架注入字节码。
Transform是Gradle的一个类似拦截器的功能，可以获取上一个Transform的输出，作为当前Transform的输入，然后修改输入内容，作为当前Transform的输出，同时作为下一个Transform的输入。
这些输入输出可以是class文件、jar文件、dex文件等，
在Transform中可以定义过滤规则。

熟悉字节码插桩技术需要先了解常见的字节码指令以及Jvm执行字节码的过程，字节码指令其实是将中缀表达式转化成了后缀表达式，也叫逆波兰表达式，在这里就不讲解了，有兴趣查阅专业资料。

动态注入字节码，一般不需要我们手动去写，而是先用Java写好一个模板类，然后查看它的字节码，将相应的字节码复制到Transform中。当然先要对字节码语法有基本的了解。

![[1638147-4f4b6463869dac87.webp]]
## ASM插装
按照class文件的格式解析、修改、生成的新的class的一个框架。使用FileInputStream读取待插装class，使用ASM 的classReader，ClassWirter，classVisitor等等操作字节码
。

#javaassist
类似ASM

## Gradle + Transformer
使用Transformer提供的接口在打包

# 启动优化（重点）

### App的启动优化：


(1) Application的创建过程中==尽量少的进行耗时操作（創建application）==

(2) 如果用到==SharePreference, 尽量在异步线程中操作==

(3) 减少布局的层次,并且生命周期回调的方法中尽量减少耗时的操作==（解析viewtree）
	可以使用idlehandler==

(4)减少资源的大小和数量，用webp和矢量图（SVG）替代

(5) (黑科技)==dex分包预加载，多开一个进程单独进行dex的预加载。==
	MultiDex第一次加载出现ANR的原因是因为**提取Dex以及DexOpt这两个过程都是耗时的操作**
	![[f6bnx1so0r.jpeg]]


#### StartUp

App StartUp是Google Jetpack新增的一个组件，该组件在App启动时提供了一个直接、高效的方式来初始化组件，同时开发者可以使用App Startup这个组件来简化启动顺序并且显示地设置初始化顺序，通过App Startup这个组件提供了一个ContentProvider来运行所有依赖项的初始化，避免第三方库单独通过ContentProvider进行初始化，从而提高了应用的启动速度(==过多的ContentProvider会导致启动慢，因为contentprovider早于applicationcontext启动==)

要解决的问题，任务执行顺序问题和并发执行。


InitializationProvider其实也是利用了 ContentProvider 的启动机制，在ContentProvider#onCreate(...)中执行初始化;


可自动初始化和延迟初始化
![[7cfb0ce5d1d7b947c4d6e3b62020cd51.jpg]]


```java
//定义初始化的组件
class LibaryA : Initializer<WorkManager> {
    override fun create(context: Context): WorkManager {
        // 初始化LibaryA
        val configuration = Configuration.Builder().build()
        WorkManager.initialize(context, configuration)
        return WorkManager.getInstance(context)
    }
    override fun dependencies(): List<Class<out Initializer<*>>> {
        //LibaryA实例化不需要依赖其他
        return emptyList()
    }
}

```
##### 自动初始化


```xml
// AndroidManifest.xml文件中添加
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    android:exported="false"
    tools:node="merge">


    <!-- 自动初始化  如果要手动初始化建议使用tools:node="remove"，而不是直接删掉-->
    <meta-data
        android:name="com.wjx.android.weather.common.startup.LibaryA"
        android:value="androidx.startup" 
        
        />
</provider>

```

##### 自动初始化
```java
override fun create(context: Context): WorkManager {
    // 手动初始化LibaryA，已经手动初始化过的组件，在此调用不会执行初始化工作
    AppInitializer.getInstance(context).initializeComponent(LibaryA::class.java)
    return WorkManager.getInstance(context)
}
```

### 为什么startup可以避免ANR?
StartUp启动框架能避免ANR（应用程序无响应）的原因有以下三点：

1. 它解决了任务执行顺序的问题。==部分任务存在依赖关系，StartUp通过拓扑排序，确保任务按照正确的顺序执行==。
2. StartUp引入了==并行执行任务的功能==。这样，一些需要较长执行时间且对主线程无害的任务可以在==子线程中执行，不会阻塞主线程==，从而避免ANR。
3. StartUp框架支持==异步任务和多进程==，可以单独配置任务需要执行的进程，从而在减轻系统负载的同时提升APP启动速度。

请注意，尽管StartUp启动框架能避免ANR，但并不是所有的ANR问题都可以通过它解决。在某些情况下，仍然可能发生ANR错误。


#### 黑白屏
App启动白屏或黑屏的原因：是因为已进入到Activity,但是布局未加载到window中，**就先显示来windows窗口的背景。黑屏/白屏就是显示的windows背景（这个就是theme的设置）**

**App启动白屏或黑屏解决方案**：

(1) 为Theme设置背景图(会给人一种快速加载的感觉)

<stylename="Theme.AppStartLoad"parent="android:Theme">

<itemname="android:windowBackground">@drawable/ipod_bg</item>

<itemname="android:windowNoTitle">true</item>

</style>

(2) 为Theme设置透明属性(会给人较慢加载出来感觉)

<stylename="Theme.AppStartLoadTranslucent"parent="android:Theme">

<itemname="android:windowIsTranslucent">true</item>

<itemname="android:windowNoTitle">true</item>

</style>


# OOM（重点）
## OOM问题分类

很多人对于OOM的理解就是Java虚拟机内存不足，但通过线上OOM问题分析，OOM可以大致归为以下3类：
==
1. ==线程数太多==
2. ==打开太多文件==
3. ==内存不足==

`pthread_create`里面会调用Linux内核创建线程，那什么情况下会创建线程失败呢？
android系统源码中此时会抛出oom

```java
void Thread::CreateNativeThread(JNIEnv* env, jobject java_peer, size_t stack_size, bool is_daemon) {
  ...
  pthread_create_result = pthread_create(...)
  //创建线程成功
  if (pthread_create_result == 0) {
      return;
  }
  //创建线程失败
  ...
  {
    std::string msg(child_jni_env_ext.get() == nullptr ?
        StringPrintf("Could not allocate JNI Env: %s", error_msg.c_str()) :
        StringPrintf("pthread_create (%s stack) failed: %s",
                                 PrettySize(stack_size).c_str(), strerror(pthread_create_result)));
    ScopedObjectAccess soa(env);
    soa.Self()->ThrowOutOfMemoryError(msg.c_str());
  }
}
```
### 查看系统对每个进程的线程数限制
> cat /proc/sys/kernel/threads-max

![](https://pic2.zhimg.com/80/v2-7c256acaca6acb71600bbde988ffb5a5_720w.webp)

==不同设备的threads-max限制是不一样的==，有些厂商的低端机型threads-max比较小，容易出现此类OOM问题。

输入以下指令获取
	cat proc/{pid}/status

优化方案：
如果是线程太多，第三方或者遗留代码难以修改都可以尝试使用插桩方式，对newThread进行线程池替换。

# 55. 界面卡顿优化（重点）
## 卡顿是什么导致的？
## 卡顿时怎么观测到的？
###  表面上的直观看到的，开启==帧率显式==和==GPU呈现模式==
### GPU呈现模式

三条水平线：其中；绿色线是16ms线。

	红色：“执行时间”，指的是Android渲染引擎执行盒子中这些绘制命令的时间
	黄色： 指的是CPU和GPU会话时间
	蓝色： 视图绘制（draw）所花费的时间，表示视图在界面发生变化（更新）的用时情况
		当蓝色低于绿线时不会出现卡顿
	绿色：测量和布局的回调时间。
![[20201112115239934.webp]]
### 帧率显示
1. 开发过程直接在开发者模式中开启
2. 监听vsync信号。
	1. Choreographer doFrame流程。使用 Choreographer.FrameCallback 的 doFrame(frameTimeNanos: Long) 方法，在每一次的回调里计算两帧之差，通过计算可以得到 FPS。
	2. 设置当前帧的开始绘制时间，上面说过开始绘制要在vsync信号来的时候开始，保证两者时间对应；所以如果时间没对上，就是发送了跳帧，那么就要修正这个时间，保证后续的时间对应上
	3. 执行所有的Callback任务，计算两次vsync信号的间隔。（当然）

  
  
作者：特立独行的佩奇  
链接：https://www.jianshu.com/p/6bb25d2af57f  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### 过度重绘

检查过度绘制

过度绘制工具可以帮助开发者检查是否进行了不必要的渲染工作。该工具使用颜色来标识过度绘制，当应用在同一帧内多次绘制同一像素时，就表明发生过度绘制，并使用不同的颜色进行标记。过度绘制表明GPU付出了额外的工作来渲染用户看不见的像素，这样会影响性能，应该尽可能修复。

过度绘制工具可以在开发者选项中打开，设置->开发者选项->==调试GPU过度绘制==。打开工具后，应用中如果发生过度绘制，会表现类似下图。
![[e6b859275b9b0504df33e1b61fb5dcd8.png]]

图中的不同颜色表示像素在一帧内重绘的次数，

- 真实颜色：未发生重绘。
- 蓝色：重绘1次。
- 绿色：重绘2次。
- 粉色：重绘3次。
- 红色：重绘4次以及4次以上。

避免重绘：
	除了我们可能逻辑上调用的重绘
	- viewtree过深
	- 去除布局中不必要的背景。（比如小控件和主页面背景色一致的情况）
	- 少使用半透明颜色，如果是颜色叠加的效果，那么颜色叠加直接使用最终效果的颜色即可。
### Systrace分析
Systrace 是 Android4.1 中新增的性能数据采样和分析工具。它可帮助开发者收集 Android 关键子系统（如 SurfaceFlinger/SystemServer/Kernel/Input/Display 等 Framework 部分关键模块、服务，View系统等）的运行信息，从而帮助开发者更直观的分析系统瓶颈，改进性能。

Systrace 的功能包括跟踪系统的 I/O 操作、内核工作队列、CPU 负载以及 Android 各个子系统的运行状况等。CPU频率等都可以看到。
可以通过命令行操作，或者是开发者选项中设置即可。
这里只讲开发者中设置。设置完毕后。点击开始记录trace，复现问题，然后点击关闭trace，这样trace 文件就会保存在`/data/local/traces`目录下，然后pull 出来 。
导出的***.cstrace文件可以通过perfetto网站：https://ui.perfetto.dev/，点击 open with legacy UI打开
![[截图20230903180810.png]]
### android studio profiler 性能分析
android studio上的仪表盘。该不会还有人没用过吧？
分析资源情况和内存情况。

### 参考了ANR-WatchDog机制**

ANR-WatchDog机制原理不复杂，**它内部启动了一个子线程，定时通过主线程Handler发送Message，然后定时去检查Message的处理结果。**（messageQueue取出消息后，会给message打上标签 -> msg.markInUse();）

通俗来说就是利用了Android系统MessageQueue队列的排队处理特性。

通过**主线程Handler发送消息到MessageQueue队列**，**5秒去看下这个Message有没有被消费**如果消费了则代表没有卡顿，如果没有，则代表有卡顿，当然这个5秒是可调节的。


这种监控非常简单，接入成本较低，==用于监控ANR是非常不错的选择，但是对于监控卡顿有弊端==，比如

- 1. 它会漏报情况，举个例子，比如我们以5秒未响应作为卡顿阈值，如果我们发送监听Message的时间在上一个消息处理的第2-5秒之间，那这种就会产生漏报。
- 2. 监听间隔越小，系统开销越大。
- 3. 即便监听到了，不好区分卡顿链路，无法提供准确的卡顿堆栈。

### **参考BlockCanary开源库，利用Looper.loop() Printer打印机制**

每次Message处理(也就是dispatchMessage(msg))都会在==处理前，和处理后通过Looper.mLogging打印日志。==
```java
//Looper取消息的接口
Looper.getMainLooper().setMessageLogging(CustomPrinter())

public void println(String reason) {
    if (reason.charAt(0) == '>') {
        Message message = getMessage();
        if (message.getTarget() == null) {
            Log.e("Hensen", "[token=" + message.arg1 + "]" + "[target=" + message.getTarget() + "]  [when=" + message.getWhen() + "]" + "[next=" + getNext(message) + "]");
        }
    }
}

```

我们只需知道打印日志的时间差即可知道Message的处理耗时。当耗时超过我们的阈值时我们即可收集调用堆栈，然后根据堆栈进行针对性优化即可。

这种方案也有弊端，比如

- 1.View的TouchEvent事件是在queue.next()中处理的，只统计dispatchMessage(msg)前后耗时，不会覆盖到View的TouchEvent耗时。
- 2.IdleHandler.queueIdle()也在queue.next()中，当主线程空闲会调用IdleHandler，此时IdleHandler也是在主线程执行，当过于耗时时也可能出现卡顿，甚至到只ANR，这种场景也无法监控。
- 3.SyncBarrier（同步屏障）的泄漏同样无法被监控到，这种情况比较少见，参考了微信的监控方案。

### IdleHandler

利用IdleHandler

### JankStats
大部分手机的屏幕都是60Hz的刷新率，系统为了配合屏幕的刷新频率，每过16.6ms就会发出Vsync信号来通知应用进行绘制。如果每个Vsync周期应用都能完成渲染逻辑，那么应用的FPS就是60，给用户的感觉就是非常流畅。

在应用层，实现上述机制的关键类就是`Choreographer`。每隔16.6ms，Vsync 信号唤醒 Choreographer来做应用的绘制操作。[[#Choreographer]]
在应用层就是通过Choreographer来接受VSync信号并执行每一帧的渲染逻辑。  
每当Vsync到来时，会往主线程[消息队列](https://so.csdn.net/so/search?q=%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97&spm=1001.2101.3001.7020)里添加一个Message，最终其doFrame函数将被调用：

```csharp
//Choreographer.java
public void onVsync(long timestampNanos, long physicalDisplayId, int frame) {    ......   
mTimestampNanos = timestampNanos;    
mFrame = frame;   
Message msg = Message.obtain(mHandler, this);   
msg.setAsynchronous(true);   
mHandler.sendMessageAtTime(msg, timestampNanos / TimeUtils.NANOS_PER_MS);} 
public void run() {    
......    doFrame(mTimestampNanos, mFrame);
}
```

那么我们可以通过反射插入


 1.内存抖动
可以在profile中看stack内存的变化

2.嵌套布局
嵌套布局过深会导致我们测量、绘制的时候viewtree遍历（自上向下或者自下向上）事件或者测量时间过长（因为我们测量的时候父布局可能是受子布局影响的），


GLsurface

# 56. 内存优化
## 1.内存泄漏
### 1. 常见内存泄漏
#### 1. Handler造成内存泄露的原因

在Activity中，将Handler声明成非静态内部类或匿名内部类，这样Handle默认持有外部类Activity的引用。查看javac产物可知生成的匿名内部类构造函数默认持有外部类的对象。
如果Activity在销毁时，Handler还有未执行完或者正在执行的Message，而Handler又持有Activity的引用，导致GC无法回收Activity，导致内存泄漏。如以下两种情形可能导致内存泄漏

1、在Activity内将Handler声明成匿名内部类

       //匿名内部类
       private Handler mHandler = new Handler() {
            @Override
            public void handleMessage(@NonNull Message msg) {
                super.handleMessage(msg);
            }
        };

       new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                //大量的操作，activity要销毁时还没结束
            }
        },1000);

2、在Activity内将Handler声明成非静态内部类：

       //非静态内部类
       private class MyHandler extends Handler{
            @Override
            public void handleMessage(@NonNull Message msg) {
                super.handleMessage(msg);
            }
        }
     
解决方案

1、静态内部类 + 弱引用

       private static class MyHandler extends Handler {
            //弱引用，在垃圾回收时，activity可被回收
            private WeakReference<MainActivity> mWeakReference;
     
            public MyHandler(MainActivity activity) {
                mWeakReference = new WeakReference<>(activity);
            }
     
            @Override
            public void handleMessage(@NonNull Message msg) {
                super.handleMessage(msg);
            }
        }

2. **在Activity销毁时，清空Handler中未执行或正在执行的Callback以及Message：**

        @Override
        protected void onDestroy() {
            super.onDestroy();
            //清空handler管道和队列
            mHandler.removeCallbacksAndMessages(null);
        }
        
3：非静态内部类 + 弱引用，在activity要回收时清除引用（麻烦，不推荐）

        private class MyHandler extends Handler {
            //弱引用，在垃圾回收时，activity可被回收
            private WeakReference<MainActivity> mWeakReference;
     
            public MyHandler() {
                this.mWeakReference = new WeakReference<>(MainActivity.this);
            }
     
            @Override
            public void handleMessage(@NonNull Message msg) {
                super.handleMessage(msg);
            }
        }
        
        private MyHandler mHandler = new MyHandler();
     
        @Override
        protected void onDestroy() {
            super.onDestroy();
            //手动清除应用
            mHandler.mWeakReference.clear();
        }

4. destroy的时候mHandler.removeAllCallbackAndMessage()

 #### 2. Thread造成内存泄露

匿名内部类构建的Thread类，默认持有外部类的实例
1. 改成lambda表达式
2. kotlin匿名内部类不会默认持有外部的实例，调用外部的方法或者对象时要显式调用
 ```koltin
 val thread = object:Thread(){
	override fun run(){
		val a = this@MainActivity do(); 
	}
 }
 
 ```
#### 3.Native导致的泄露
JNI引用的对象。
#### 4.方法区中静态属性引用的对象
特别是 activity或者对应的context，view。
#### 5.synchronized锁持有的对象

#### 6.资源没有正确释放

比如==数据库链接，Bitmap，stream对象，音频资源==等等。
==webview。==

Bitmap可以采用第三方框架来管理。

==weiview记得destroy的时候，stoploading，调用onPause，clearCache，removeallviews, destroy等等。==

## 监视工具
1. as 的Profile
2.  DDMS androidSDK中的可以监视进程 
3. Matrix 腾讯的开源项目。
 Matrix for Android
	Matrix-android 当前监控范围包括：应用安装包大小，帧率变化，启动耗时，卡顿，慢方法，SQLite 操作优化，文件读写，内存泄漏等等。
	- APK Checker: 针对 APK 安装包的分析检测工具，根据一系列设定好的规则，检测 APK 是否存在特定的问题，并输出较为详细的检测结果报告，用于分析排查问题以及版本追踪
    
	- Resource Canary: 基于 WeakReference 的特性和 [Square Haha](https://github.com/square/haha) 库开发的 Activity 泄漏和 Bitmap 重复创建检测工具
    
	- Trace Canary: 监控ANR、界面流畅性、启动耗时、页面切换耗时、慢函数及卡顿等问题
    
	- SQLite Lint: 按官方最佳实践自动化检测 SQLite 语句的使用质量
    
	- IO Canary: 检测文件 IO 问题，包括：文件 IO 监控和 Closeable Leak 监控
    
	- Battery Canary: 监控 App 活跃线程（待机状态 & 前台 Loop 监控）、ASM 调用 (WakeLock/Alarm/Gps/Wifi/Bluetooth 等传感器)、 后台流量 (Wifi/移动网络)等 Battery Historian 统计 App 耗电的数据
    
	- MemGuard
    
    检测堆内存访问越界、使用释放后的内存、重复释放等问题
4. koom
	KOOM框架  
	快手KOOM核心流程包括：配置下发决策、监控内存状态、采集内存镜像、解析镜像文件(以下简称hprof)生成报告并上传、问题聚合报警与分配跟进。  
  
	无主动触发GC不卡顿  
	==之前行业的普遍做法是通过在Activity.onDestroy()后连续触发两次GC（**Leakcanary会在onDestory方法中进行2次GC（为啥要多次GC，其实是因为一次GC并不能保证对象被回收，可以通过上面的例子中看出）**）==，并检查引用队列，判定Activity是否发生了泄漏，但频繁GC会造成用户可感知的卡顿，快手为实现无感触发设计了全新的监控模块，通过无性能损耗的内存阈值监控来触发镜像采集。将对象是否泄漏的判断延迟到了解析时，阈值监控只要在子线程定期获取关注的几个内存指标即可，性能损耗忽略不计。  
  
内存监控流程图  
  
高性能镜像DUMP  
采集内存镜像传统方案会造成应用完全冻结长达几秒，期间用户完全不能操作，严重损害用户体验。快手利用系统内核COW(Copy-on-write，写时复制)机制，==每次dump内存镜像前先暂停虚拟机，然后fork子进程来执行dump操作，父进程在fork成功后立刻恢复虚拟机运行，整个过程对于父进程来讲总耗时只有几毫秒，对用户完全没有影响==。  
==暂停虚拟机需要调用虚拟机的art::Dbg::SuspendVM函数==，谷歌从Android 7.0开始对调用系统库做了限制，==快手自研了kwai-linker组件，通过caller address替换和dl_iterate_phdr解析绕过了这一限制。==

## Android dump日志详解
简介

在Android开发过程中，我们经常会遇到一些难以调试的问题，比如应用崩溃、内存泄漏等。为了解决这些问题，我们需要收集相关的调试信息。Android系统提供了一种称为dump log的机制，可以帮助我们收集与应用有关的日志信息。本文将介绍Android dump日志的原理、用法及示例代码。
什么是dump日志

Android的dump日志机制允许我们在应用发生崩溃或其他异常情况时，将调试信息输出到日志文件中。这些调试信息可以包括应用的堆栈跟踪、内存使用情况、线程信息等。通过分析这些日志，我们可以更好地理解应用的运行状况，帮助我们定位和解决问题。
使用dump日志

使用Android的dump日志功能，我们需要先在应用中添加相关的代码。以下是一个简单的示例：

```
import android.os.Debug;
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        if (BuildConfig.DEBUG) {
            //这种
            Debug.dumpHprofData("/sdcard/myapp.hprof");
            //或者这种
            Thread.dumpStack();
        }
    }
}
```

以上代码是在应用的Application类中调用了Debug.dumpHprofData()方法，将堆栈信息以Hprof格式输出到文件/sdcard/myapp.hprof中。这里需要注意的是，该方法只在debug模式下起作用，因此我们需要使用BuildConfig.DEBUG进行判断。

值得一提的是，除了Debug.dumpHprofData()方法，Android还提供了其他一些dump方法，如Debug.dumpMemoryInfo()用于输出内存信息、Debug.dumpNativeBacktrace()用于输出Native层的堆栈信息等。根据实际需求，我们可以选择适合的方法进行调用。
分析dump日志

一旦日志文件生成，我们就可以使用各种工具对其进行分析了。==Android Studio提供了一个名为Android Profiler的工具，可以方便地查看和分析dump日志。==

首先，我们需要将日志文件导入到Android Studio中。在Android Profiler界面的左下角，点击Import HProf按钮，==选择日志文件进行导入==。导入后，我们可以在Memory选项卡中查看堆栈信息、内存使用情况等。

另外，还可以使用一些第三方工具进行分析，比如MAT（Memory Analyzer Tool）。该工具可以帮助我们更深入地分析dump日志，找出应用中的内存泄漏等问题。
总结

通过使用Android的dump日志功能，我们可以收集应用的调试信息，帮助我们定位和解决问题。本文介绍了如何使用dump日志功能，并提供了示例代码。希望本文对你理解和使用Android dump日志有所帮助。

# 57. NDK开发

这本来应该是个大章节独立一个md文档出来的，但是这里只介绍基础和核心的部分。

## 1.简介：ndk与jni
`NDK`全称`Native Development Kit`，是Android的一个开发工具包,与Java并没有什么关系.
`NDK`的核心目的之一是让您将 C 和 C++ 源代码构建为可用于应用的共享库。提供了交叉编译的功能.
Android NDK (Native Development Kit) 主要使用的编译器是 GCC (GNU Compiler Collection) 和 Clang。但是从 NDK r18 开始，Google 不再支持 GCC，只支持 Clang 编译器。

Clang 是一个基于 LLVM 的编译器前端，支持 C, C++ 和其他语言。Android 鼓励开发人员使用 Clang，因为在这个工具链上他们提供更全面的支持和更新。Clang 也为开发者提供了更现代的一些特性以及更好的错误诊断信息等优点。

### JNI
即 Java Native Interface ，是 Java 提供用来与其他语言通信的 api ，“其他语言”意味不止局限于 C 或 C++ ,也可以调用除 C 和 C++ 之外的语言，只是大多数情况下调用 C 或 C++ ; “通信”意味着 Java 和 其他语言之间可以相互调用，不止局限于 Java 调用其他语言，其他语言也可以主动调用 Java .
Android 中包含 native 进程和 java进程。
	
- 1、nativie 进程：采用C/C++ 实现，不包含dalvik实例的进程。/system/bin/目录下面的程序文件运行后都是以native进程形式存在的。  
- 2、java进程：Android中运行与dalvik 虚拟机之上的进程。  
	dalvik虚拟机的宿主进程由fork()系统调用创建，**所以每一个java进程都是存在于一个native进程中**，因此，java进程的内存分配比native进程复杂，因为进程中存在一个虚拟机实例。
	- java程序发生OMM并不是表示RAM不足。而是堆内存超出了dalvik.vm.heapsize的限制。如果RAM真的不足，会发生什么呢？这时Android的memory killer会起作用，当RAM所剩不多时，memory killer会杀死一些优先级比较低的进程来释放物理内存，让高优先级程序得到更多的内存。
	- dalvik.vm.heapsize 的显示，仅是对dalvik进程中java对的限制。对native 堆 并没有限制。所以在android程序中natvie 堆的内存可以很大
### Native Memory
也称为C-Heap，供Java Runtime进程使用的，**没有相应的参数来控制其大小，其大小依赖于操作系统进程的最大值。**

Java应用程序都是在Java Runtime Environment（JRE）中运行，而Runtime本身就是由Native语言（如：C/C++）编写程序。Native Memory就是操作系统分配给Runtime进程的可用内存，它与Heap Memory不同，Java Heap 是Java应用程序的内存。（JVM只是JRE的一部分，JVM的内存模型属于另一话题）[[JAVA基础--JAVA数据结构--JVM#13.1 内存模型（JMM）]]

Native Memory的主要作用如下：

- 管理java heap的状态数据（用于GC）;  
- JNI调用，也就是Native Stack；  
- JIT（即时编译器）编译时使用Native Memory，并且JIT的输入（Java字节码）和输出（可执行代码）也都是保存在Native Memory；

  
JNI 的实现步骤很简单，如下：

- 1.编写带有 native 方法的 Java 类

- 2.生成该类扩展名为 .h 的头文件

- 3.创建该头文件的 C/C++ 文件，实现 native 方法

- 4.将该 C/C++ 文件编译成动态链接库so文件打包到apk中

## jni注册
### 静态注册
我們在對應類java類，就已經自動注冊了
```java
static{
 System.load("cpp/xxxxx.so");
}
```
当Java层调用navtie函数时，会在JNI库中根据函数名查找对应的JNI函数。如果没找到，会报错。**如果找到了，则会在native函数与JNI函数之间建立关联关系，其实就是保存JNI函数的函数指针。下次再调用native函数，就可以直接使用这个函数指针。**

JNI函数名格式（包名里面的”.”需要改为 _ ）
Java_ + 包名（com_example_auto_jnitest）+ 类名(_MainActivity) + 函数名(_stringFromJNI)


### 动态注册

动态注册通过手动注册实现虚拟机与jni的连接。优点是节约了搜索所需的开销，缺点是需要额外进行函数编写，增加了人工工作量。

动态注册的步骤如下：

- 通过 vm（ Java 虚拟机）参数获取 JNIEnv 变量
- 通过 FindClass 方法找到对应的 Java 类
- 通过 RegisterNatives 方法，传入 JNINativeMethod 数组，注册 native 函数

对于 JNINativeMethod 结构而言，签名是其非常重要的一项元素，它用于区分 Java 中 native 方法的各种重载形式，下面将介绍方法的签名。

# 4 方法签名

方法签名的组成规则为：

**(参数类型标识1参数类型标识2…参数类型标识n)返回值类型标识**

类型标识对应关系如下：

|类型标识|Java数据类型|
|---|---|
|Z|boolean|
|B|byte|
|C|char|
|S|short|
|I|int|
|J|long|
|F|float|
|D|double|
|L包名/类名;|各种引用类型|
|V|void|

另外，当 Java 类型为数组时，在标识前会有“[”符号，例如：String[] 类型标识为 [Ljava/lang/String;（不要漏掉英文分号），如果有内部类则用 $ 来分隔，如：Landroid/os/FileUtils$FileStatus;

可以根据上面的规则手动书写方法签名，当然还有一种自动获取的方法。  
如果是 ndk-build 构建的项目在\build\intermediates\classes\debug 目录下执行，如果是 CMake 构建的项目在\build\intermediates\javac\classes 目录下执行：

```powershell
javap -s 全类名
```

创建一个java类，里面声明两个jni函数：

    package com.example.hello_cmake;
     
    public class TestJni {
        static {
            System.loadLibrary("native-lib");
        }
        public native int text(String message);
        public static native int static_text(String message);

cpp中：

```cpp
#include <jni.h>
#include <string>
#include <android/log.h>
#define LOG_TAG "CPPLOG"
#define LOGD(...) __android_log_print(ANDROID_LOG_DEBUG, LOG_TAG, __VA_ARGS__)
#define LOGI(...) __android_log_print(ANDROID_LOG_INFO, LOG_TAG, __VA_ARGS__)
#define LOGE(...) __android_log_print(ANDROID_LOG_ERROR, LOG_TAG, __VA_ARGS__)
//jni函数实现1
    const char *jmsg = env->GetStringUTFChars(msg, JNI_FALSE);
    LOGD("message = %s",jmsg);
    return 0;
}
jint native_text(JNIEnv *env, jobject jobject1, jstring msg){
//jni函数实现2
jint native_static_text(JNIEnv *env, jobject jobject1, jstring msg){
    const char *jmsg = env->GetStringUTFChars(msg, JNI_FALSE);
    LOGD("message = %s",jmsg);
    return 0;
}
 
//方法注册结构体
static const JNINativeMethod nativeMethod[] = {
        {"text",        "(Ljava/lang/String;)I", (void *) native_text},
        {"static_text", "(Ljava/lang/String;)I", (void *) native_static_text}
};
 
//注册函数
static int registNativeMethod(JNIEnv *env) {
    int result = -1;
 
    jclass class_text = env->FindClass("com/example/hello_cmake/TestJni");
    if (env->RegisterNatives(class_text, nativeMethod,
                             sizeof(nativeMethod) / sizeof(nativeMethod[0])) == JNI_OK) {
        result = 0;//返回0代表成功
    }
    return result;
}
 
//重载JNI_OnLoad，加载动态库
jint JNI_OnLoad(JavaVM *vm, void *reserved) {
    JNIEnv *env = NULL;
    int result = -1;
 
    if (vm->GetEnv((void **) &env, JNI_VERSION_1_1) == JNI_OK) {
        if (registNativeMethod(env) == JNI_OK) {
            result = JNI_VERSION_1_6;
        }
        //返回值代表动态库需要的jni版本
        return result;
    }
}
 
```

## 交叉编译

在某个平台上，编译该平台的可执行程序，叫做本地编译，比如在 Windows 平台上编译 Windows 自身的可执行程序；在 x86 平台上，编译 x86 平台自身的可执行程序。
如果不编译的话也可以直接导入。

**在某个平台上，编译另一种平台的可执行程序，就是交叉编译，比如在 x86 平台上，编译 arm 平台的可执行程序，这也是 Android 端使用最多的交叉编译类型。**

编译完成后就可以把生成的.so文件集成到我们的项目中了,来看看步骤（ffmpeg为例子）:

    项目关联NDK,按这里的教程执行向您的项目添加C和C++ 代码;
    拷贝生成的.so文件到libs目录下(或是jniLibs);
    拷贝生成的include文件夹到cpp目录;
    拷贝ffmpeg\fftools目录下文件到cpp目录;
    编写native方法


CMakeLists.txt的写法
例子： add_library 使用指定的源文件将库添加到项目中

    普通库

    // 添加普通库的语法 库名 + 加载方式
    add_library(<name> [STATIC | SHARED | MODULE]
        [EXCLUDE_FROM_ALL]
        [source1] [source2 ...])
     
    // 创建ndk项目中默认生成的例子，加入了才能被开发环境识别
    add_library( # Sets the name of the library.
         native-lib
         # Sets the library as a shared library.
         SHARED
         # 源文件位置，以下为举例.cpp文件夹或者是具体文件
         ${CMAKE_SOURCE_DIR}/ffmpeg
         src/main/cpp/native-lib.cpp )

 ## extern "C" 
/*extern "C" 的作用是为了能够正确在 C++ 代码中调用 C 语言代码。  
 * 加上 extern "C" 后指示编译器按 C 编译器编译这部分代码。  
 * 使用它的本质的原因是 C++ 函数重载，  
 * C++ 中函数重载在编译后生成的不止是函数名，也会带上参数类型。  
 * 而 C 编译器编译函数时不会带上函数的参数类型。  
*/


```java
System.load("/sdcard/path/libA.so")
```
findLibrary() 将到 ClassLoader 中查找 lib，取决于各 JVM 的具体实现。比如可以看看 Android 上的实现。

1. 到 DexPathList 的具体实现中调用
    
2. 首先通过 System 类的 mapLibraryName() 中获得 mapping 后的 lib 全名，细节见下章节
    
3. 遍历存放 native lib 路径元素数组 nativeLibraryPathElements
    
4. 逐个调用各元素的 findNativeLibrary() 实现去寻找
    
5. 一经找到立即返回，遍历结束仍未发现的话返回 null


## c++打包成so
创建Android.mk文件，
## Android.mk文件的基本概念

Android.mk是一个GNU Makefile文件，用于管理一个或多个模块（Module）的编译。每个模块通常都由一组源文件组成，可以是代码文件（.c、.cpp等）或者资源文件（.xml、.png等）。所有的源文件都会被编译成为一个可执行的二进制文件或者APK文件。

在Android系统中，Android.mk文件通常是用来编译的，它描述了每个模块的依赖关系以及编译选项。
Android.mk 和 Application.mk 不是一样的。它们都是 Android 应用构建系统 (Android NDK) 中的 Makefile 文件，但它们具有不同的目的和组成部分。

1. Android.mk：这个文件用于编译==一个具体的本地模块==。它为构建系统提供如何==编译源代码、链接和组合库文件==等信息。当你需要同时为一个项目编译多个模块时，你可能需要分别为活动源代码文件夹编写多个 Android.mk 文件。通常 Android.mk 包含有：
  - LOCAL_PATH: 指定源码所在的路径。
  - LOCAL_MODULE: 指定输出的模块。
  - LOCAL_SRC_FILES: 指定源代码文件。
  - LOCAL_CFLAGS: 指定 C 编译器标志。
  - LOCAL_LDFLAGS: 指定链接器标志。
  等相关指令和变量。

2. Application.mk：这个文件定义了==整个应用的全局特性==。通常情况下，一个项目只需要使用一个 Application.mk 文件。这个文件用于控制构建配置和属性，例如如何编译本地库、平台兼容性等。这些设置会应用到整个项目中的所有 Android.mk 文件。一些 Application.mk 文件的设置包括：
  - APP_ABI: 指定目标 ABI，例如 armeabi-v7a、arm64-v8a、x86 等。
  - APP_PLATFORM: 表示构建为哪个 Android 操作系统级别。
  - APP_STL: 用于选择STL (Standard Template Library) 配置。
  - APP_MODULES: 指定要构建的模块列表。
  等相关指令和变量。

总之，虽然 Android.mk 和 Application.mk 都是构建 Android 应用的 Makefile 文件，但它们具有不同的目的，分别为编译具体模块和定义全局特性。
```makefile
YourProject
|-- app
    |-- src
    |-- jni
        |-- cpp
            |-- a_module
                |-- Android.mk
            |-- b_module
                |-- Android.mk
        |-- Application.mk

```



## java层和ndk层的通信
### 1.如何通信
#### Java调用jni

##### 静态注册
java中：
c++中：静态注册的：Java_全限定名类名_方法名
##### 动态注册
动态注册的步骤：

1. 利用结构体 JNINativeMethod 数组记录 java 方法与 JNI 函数的对应关系。
2. 实现 JNI_OnLoad 方法，在加载动态库后，执行动态注册。
3. 调用 FindClass 方法，获取 java 对象。
4. 调用 RegisterNatives 方法，传入 java 对象，以及 JNINativeMethod 数组，以及注册数目完成注册。

#### JNI 中调用 Java 方法流程 :​

 ​① 获取 jclass 类型变量 :​

	调用 jclass GetObjectClass(jobject obj) 或 jclass FindClass(const char* name) 方法 , 获取 jclass 类型变量 ;

​② 通过反射获取方法 :​

	调用 jmethodID GetMethodID(jclass clazz, const char* name, const char* sig) 获取 Java 对象方法 ,
	调用 jmethodID GetStaticMethodID(jclass clazz, const char* name, const char* sig) 获取 Java 类的静态方法 ;

​③ 调用方法 :​

	void CallVoidMethod(jobject obj, jmethodID methodID, …) 调用 Java 对象方法 ,
	void CallStaticVoidMethod(jclass clazz, jmethodID methodID, …) 调用 Java 的静态方法 ;


来源：https://blog.51cto.com/u_14202100/5084702

##### 实例：
native调用jni反射

```java
//要被调用的类
public class Stext{  
    public String laila(String test){  
        Log.d("", test);  
        return "Stext java层的,这是native传来的：" + test;  
    }  
  
}
```

```kotlin

//native方法
external fun stringFromJNI(): String  
  
companion object {  
    // Used to load the 'nativeapplication' library on application startup.  
    init {  
        System.loadLibrary("nativeapplication")  
    }  
}


```

```cpp
//函数签名：参数 + 返回值  
static const char *signature = "(Ljava/lang/String;)Ljava/lang/String;";  
  
  
//反射运行class文件  
extern "C" JNIEXPORT jstring JNICALL  
Java_com_shay_nativeapplication_MainActivity_stringFromJNI(  
        JNIEnv* env,  
        jobject /* this */) {  
    std::string hello = " C++操作";  
  
    std::string result =  hello.append("back to JAVA!!!");  
    //获取Stext类信息  
    jclass  jclass1 = env->FindClass("com/shay/nativeapplication/Stext");  
    if(jclass1 == nullptr){  
        return env->NewStringUTF("error no found class");  
    }  
    //获取‘laila‘方法信息  
    jmethodID  methodId = env->GetMethodID(jclass1, "laila", signature);  
  
    //构造函数创建对象  
    jmethodID constructor = env->GetMethodID(jclass1, "<init>", "()V");  
    jobject stext_obj = env->NewObject(jclass1, constructor);  
  
    //调用java函数  
    jstring params = env->NewStringUTF(result.c_str());  
    jstring  stirng = static_cast<jstring>(env->CallObjectMethod(stext_obj, methodId, params));  
    env->DeleteLocalRef(stext_obj);  
    return stirng;  
}

```




### 2.原理分析
JNIEnv* 介绍
JNIEnv，即JNIEnvironment；字面意思就是JNI环境。其实它是一个与线程相关的JNI环境结构体。所以JNIEnv类型实际代表了Java环境。
通过这个JNIEnv*指针，就可以对Java端代码进行操作。
**与线程相关，不同线程的JNIEnv相互独立。 JNIEnv只在当前线程中有效。**
Native方法不能将JNIEnv从一个线程传递到另一个线程中。相同的Java线程对Native方法多次调用时，传递给Native方法的JNIEnv是相同的。但是，一个本地方法可能会被不同的Java线程调用，因此可以接受不同的JNIEnv。

和JNIEnv相比，JavaVM可以在进程中各个线程间共享。理论上一个进程可以有多个JavaVM，但Android只允许一个JavaVM。需要强调在Android SDK中强调了额 " do not cache JNIEnv * "，要用的时候在不同的线程中通过JavaVM * jvm的方法来获取与当前线程相关的JNIEnv *。

    JNIEnv概念 : JNIEnv是一个线程相关的结构体, 该结构体**代表了 Java 在本线程的运行环境。通过JNIEnv可以调用到一系列JNI系统函数。**
    JNIEnv线程相关性： 每个线程中都有一个 JNIEnv 指针。JNIEnv只在其所在线程有效, 它不能在线程之间进行传递。

注意： **在C++创建的子线程中获取JNIEnv，要通过调用JavaVM的AttachCurrentThread函数获得**。在子线程退出时，**要调用JavaVM的DetachCurrentThread函数来释放对应的资源**，否则会出错。

JNIEnv 作用：

    访问Java成员变量和成员方法；
    调用Java构造方法创建Java对象等。

### JNI引用
在JNI中，Java对象引用可以分为==本地引用（Local reference）==和==全局引用（Global reference）==。本地引用在本地范围（如一个JNI函数调用或一个线程）中有效，而全局引用可以在多个范围中使用。

#### I . 全局引用


​##### 1 . 全局引用作用域 :​


​与局部引用对比 :​ 全局引用与局部引用相对应 , 其作用域是全局的 , 局部引用只能在当前方法使用 ;

​空间 :​ 可以 跨方法 , 跨线程使用 ;

​时间 :​ 创建后可以使用 , 手动释放后全局引用失效 ; ( 手动释放前全局可用 )

	env->DeleteGlobalRef()

​##### 2 . 全局引用 内存回收 :​ 全局引用 与 局部引用 均不会被 JVM 自动回收 , 如果内存不足 , JVM 宁可抛出 OOM 异常 , 也不会回收这些内存 ;



##### ​3 . 全局引用相关方法 :​



​① 创建全局引用 :​ NewGlobalRef ;

​② 释放全局引用 :​ DeleteGlobalRef ;

#### 本地引用
==JNI代码创建或使用一个Java对象时==，引用计数器将始终保持在一个较高的水平。在某些情况下，如果JNI代码生成了大量的本地引用而没有及时释放，这将导致内存泄漏问题。
	例如：jobject stext_obj = env->NewObject( ******)
为了防止内存泄漏，可以在JNI代码中使用`DeleteLocalRef`来显式回收本地引用，释放相关资源。

这是一个示范如何使用`DeleteLocalRef`的例子：

```java
JNIEXPORT void JNICALL Java_com_example_NativeClass_nativeMethod(JNIEnv *env, jclass clazz) {
    // 创建一个Java对象
    jobject obj = (*env)->NewObject(env, clazz, methodID);
    
    // 在这里你可以使用该Java对象完成所需的任务

    // 任务完成后，用DeleteLocalRef回收本地引用，释放资源
    (*env)->DeleteLocalRef(env, obj);
}
```
针对这些情况，我们除了手动释放以外，还可以模拟智能指针来自动释放。


# SurfaceView, Surface, SurfaceHolder
![[94ebc5e484c2ab5ffaffe842a2bc199d.png]]
- Surface  
    Handle onto a raw buffer that is being managed by the screen compositor。
    即Surface是保存原始缓存区的句柄，也就是显示的像素数据
- SurfaceView  
    SurfaceView是视图(View)的继承类，这个视图里内嵌了一个专门用于绘制的Surface。你可以控制这个Surface的格式和尺寸。Surfaceview控制这个Surface的绘制位置。我们绘制view不用依赖于view的刷新机制，可以直接执行渲染。

为什么要设计surfaceView？
答：**当渲染的缓冲数据来自外部的其它系统服务或API时——比如系统媒体解码器的音视频数据，或者 Camera API 的相机数据等，这时 UI 渲染的效率要求会变得非常高。**
**开发者有了新的诉求** ：能否有这样一种特殊的视图，它拥有独立的 Surface ，这样就可以脱离现有 Activity 宿主的限制，==在一个独立的线程中进行绘制==。
**由于该视图==不会占用主线程资源==，一方面可以实现复杂而高效的 UI 渲染，另一方面可以及时响应用户其它输入事件。**

Surfaceview低于宿主界面？
宿主一般是上层，一般window是一个surface，其中的view都是使用这个surface。
用来描述 `SurfaceView` 的 `Layer` 或者 `LayerBuffer` 的 `z` 轴位置默认是低于宿主窗口的。与此同时，为了便于最底层的视图可见， `SurfaceView` 在宿主 `Activity` 的窗口上设置了一块透明区域（**挖了一个洞**）。
最终，`SurfaceFlinger` 把所有的 `Layer` 通过用统一流程来绘制和合成对应的 `UI`。



## GLSurfaceView和OpenGL


# SparseArray和ArrayMap
## SparseArray

**Android内部特有的api**

SparseArray中Key为int类型（避免了装箱和拆箱），Value是Object类型，Key和Value分别存放在一个数组内，**Key数组int值是按顺序排列的，查找的时候采用的是二分查找，效率很高。而Value数组的位置和Key数组中的位置是一样的。**

add的时候会进行位移，remove的时候不一定会进行位移，把某个值标记为delete，如果下次有符合的值直接放到该位置，就没有位移了。但是也有缺点，Key只能是int值。最后Android中还扩展了SparseLongArray。

SparseArray总结：

- 其内部主要通过 2 个数组来存储 key 和 value，分别是 int[] 和 Object[]。这也限定了其 key 只能为 int 类型，且 key 不能重复，否则会发生覆盖。
- 一切操作都是基于二分查找算法，将 key 以升序的方法 “紧凑” 的排列在一起，从而提高内存的利用率以及访问的效率。相比较 HashMap 而言，这是典型的时间换空间的策略。
- 删除操作并不是真的删除，而只是标记为 DELETE，以便下次能够直接复用。
## ArrayMap


ArrayMap的Key和Value同HashMap一样都可以存放多种类型，ArrayMap对象的数据存储格式如下：

1.mHashes是一个记录所有key的hashcode值组成的数组，是从小到大的排序方式；采用二分查找法，从mHashes数组中查找值等于hash的key
2.mArray是一个记录着key-value键值对所组成的数组，是mHashes大小的2倍；

在Android Performance Pattern中，官方给出的使用场景为：

	1. item数量小于1000，尤其是插入数据和删除数据不频繁的情况。
	2. Map中包含子Map对象
### ArrayMap和HashMap的区别？

==ArrayMap的存在是为了解决HashMap占用内存大的问题==，它内部使用了一个==int数组用来存储元素的hashcode==，使用了一个Object数组用来存储元素，==两者根据索引对应形成key-value结构==，这样就不用像HashMap那样需要额外的创建Entry对象来存储，减少了内存占用。但是在==数据量比较大时，ArrayMap的性能就会远低于HashMap==，因为 ArrayMap基于二分查找算法来查找元素的，并且数组的插入操作如果不是末尾的话需要挪动数组元素，效率较低。

而HashMap内部基于数组+单向链表+红黑树实现，也是key-value结构， 正如刚才提到的，HashMap每put一个元素都需要创建一个Entry来存放元素，导致它的内存占用会比较大，但是在大数据量的时候，因为HashMap中当出现冲突时，冲突的数据量大于8，就会从单向链表转换成红黑树，而红黑树的插入、删除、查找的时间复杂度为O(logn),相对于ArrayMap的数组而言在插入和删除操作上要快不少，所以数据量上百的情况下，使用HashMap会有更高的效率。

### ArrayMap和SparseArray
主要是：SparseArray有延迟删除的机制，可以有效的提高效率；ArrayMap即时删除，且有减容机制，可以有效的节省空间。

### 如何解决冲突问题？
在ArrayMap中，假设存在冲突的话，并不会像HashMap那样使用单向链表或红黑树来保留这些冲突的元素，而是全部key、value都存储到一个数组当中，然后查找的话通过二分查找进行，这也就是当数据量大时不宜用ArrayMap的原因了。



[相关文档-锁机制](锁.md5)


# Android开源库说明

主要由 davilk、art虚拟机-Framework-Linux内核-第三方开源库（OpenGL，SQLITE）-device虚拟设备库-hardware硬件抽象库。

此后要扩展到Framwork开发，ROM定制，HAL开发，图像渲染开发，或者音视频开发。祝你好运。
（当然该Android文档会继续更新Android开发细分领域之外的内容）