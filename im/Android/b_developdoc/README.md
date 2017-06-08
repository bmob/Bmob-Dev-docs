## 1、BmobOldIM SDK 回顾

Android BmobIM SDK v2.0.0之前的版本统称为[BmobOldIM SDK](https://github.com/bmob/bmob-android-im-sdk) ，BmobOldIM SDK已经开源但不再进行维护，请开发者集成Android BmobNewIM SDK进行开发。

| BmobOldIM SDK 问题     | 
|--------------------------------|
| 无法自定义消息类型		             | 
| 消息发送受限于BmobPushSDK		    | 
| API设计不够合理规范且不易扩展	    | 
| 聊天消息出现接收延迟或丢失         | 

## 2、BmobNewIM SDK 介绍

Android BmobIM SDK v2.0.0开始的版本统称为[BmobNewIM SDK](https://github.com/chaozhouzhang/bmob-newim-demo)，BmobNewIM SDK采用全新架构，设计更加合理规范，API更加简单易用，扩展性强，但不兼容 Android BmobOldIM SDK。

- 请确保您在使用BmobNewIM SDK之前已经了解文档，如有疑问请加IM使用交流群咨询：[182897507]()。

### 2.1、Android BmobNewIM SDK 特点及其描述

| Android BmobNewIM SDK 特点     | 描述          |
|------------------------------|--------------------------------|
| 与用户系统解耦| 终端用户聊天的唯一标识是objectId，不再受限于Bmob的用户系统|
| 支持多账号登录、跨平台  |  支持单个设备多个账号登录，支持与iOS互通聊天      | 
| 支持多种格式的富媒体消息| 支持文本、图片、音频和地理位置等多种格式的富媒体消息|
| 允许开发者自定义消息   | 支持开发者自定义消息类型，方便开发者扩展本业务逻辑|                          
| API设计更加合理规范   | 全新的架构设计，API更加简单易用，较BmobOldIM SDK 进一步降低开发者使用成本|  

### 2.2、自IM SDK v2.0.5版本开始提供aar格式远程发布包，可以自动集成并结合Data SDK进行开发

| IM SDK aar格式所包含文件     | Data SDK 版本          |
|------------------------------|--------------------------------|
| BmobNewIM_(版本号)_(发布日期).jar| BmobNewIM的核心SDK|
| androidasync_2.1.6.jar| 用于协议通讯|

### 2.3、IM SDK和Data SDK的版本对应关系

IM SDK 使用Data SDK的BmobFile用于图片、语音等文件消息的发送，因此必须导入特定版本的BmobSDK。

| IM SDK 版本     | Data SDK 版本          |
|------------------------------|--------------------------------|
| bmob-im:1.1.8| bmob-sdk:3.3.5|
| bmob-im:1.1.9| bmob-sdk:3.4.3|         
| bmob-im:2.0.1| bmob-sdk:3.4.6-0304|  
| bmob-im:2.0.2| bmob-sdk:3.4.6-0304|
| bmob-im:2.0.3| bmob-sdk:3.4.6|
| bmob-im:2.0.4| bmob-sdk:3.4.6| 
| bmob-im:2.0.5| bmob-sdk:3.4.7-aar|
| bmob-im:2.0.6| bmob-sdk:3.5.5| 
           
## 3、BmobNewIM SDK 集成

### 3.1、手动集成

#### 3.1.1、下载Android BmobNewIM SDK开发包及其Demo


| 下载平台     | 下载地址          |
|------------------------------|--------------------------------|
| Bmob基于BmobNewIM SDK v2.0.6 的Demo| [bmob-newim-demo](http://www.bmob.cn/site/sdk#android_im_sdk_tab)|
| Github基于BmobNewIM SDK v2.0.5 的Demo |[bmob-newim-demo ](https://github.com/bodismile/bmob-newim-demo)| 
| Github基于BmobNewIM SDK v2.0.6 的Demo|[bmob-newim-demo](https://github.com/chaozhouzhang/bmob-newim-demo)|

#### 3.1.2、解压Android BmobNewIM SDK开发包

| 文件     | 使用          |
|------------------------------|--------------------------------|
| libs| 外部依赖库，拷贝于工程的libs文件夹；自Data SDK v3.4.7开始，加入libbmob.so文件；自Data SDK v3.5.2开始，加入libBmobStat.so文件。|
| NewIM_V2.x.x_Demo|开发示例，开发环境是Android Studio，功能是陌生人以及好友聊天|

#### 3.1.3、在app下的build.gradle文件中设置jni依赖库的目录，设置后点击Sync Now同步配置

```gradle
android {
    sourceSets {
        main.jniLibs.srcDirs = ['libs']
   }
}
```

### 3.2、自动集成

#### 3.2.1、在Project下的build.gradle文件中添加Bmob的maven仓库地址
```gradle
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.3.0'
    }
}

allprojects {
    repositories {
        jcenter()
        //Bmob的maven仓库地址，必须填写
        maven { url "https://raw.github.com/bmob/bmob-android-sdk/master" }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

#### 3.2.2、在app下的build.gradle文件中添加dependencies外部依赖库，添加后点击Sync Now同步配置


```gradle
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
		//bmob-im：特定版本的bmob-im依赖特定版本的bmob-sdk
		compile 'cn.bmob.android:bmob-im:2.0.6@aar'
		compile 'cn.bmob.android:bmob-sdk:3.5.5'
	}
```


### 3.3、配置AndroidManifest.xml

#### 3.3.1、 添加Bmob_APP_KEY

```xml
   <meta-data
	    android:name="Bmob_APP_KEY"
	    android:value="Bmob平台的Application ID" />
```

#### 3.3.2、 添加权限
请注意在Android 6.0版本开始某些权限需要动态获取，详情请看Android Developwers官方文档，[android-6.0-changes](http://developer.android.com/intl/zh-cn/about/versions/marshmallow/android-6.0-changes.html)和[android-7.0-changes](https://developer.android.google.cn/about/versions/nougat/android-7.0-changes.html)。



```xml
    <!--网络权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- 监听网络的变化 -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <!-- 设备休眠 -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- sd卡存储-->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <!--摄像头-->
    <uses-permission android:name="android.permission.CAMERA" />
    <!--录音-->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <!--通知震动-->
    <uses-permission android:name="android.permission.VIBRATE" />

```
#### 3.3.3、 添加service、receiver标签：

```xml
  <receiver android:name="cn.bmob.newim.core.ConnectChangeReceiver" >
	    <intent-filter>
	        <action android:name="cn.bmob.action.RECONNECT" />
	        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
	        <action android:name="android.intent.action.BOOT_COMPLETED" />
	        <action android:name="android.intent.action.USER_PRESENT" />
	    </intent-filter>
  </receiver>
  <service
    android:name="cn.bmob.newim.core.service.BmobIMService"
    android:process=":bmobcore" />
  <service
    android:name="cn.bmob.newim.core.service.NotifyService"
    android:process=":bmobcore" />
  <service android:name="cn.bmob.newim.core.service.ReConnectService" />
  <service android:name="cn.bmob.newim.core.service.HeartBeatService" />
```

**注：自v2.0.5版本开始，将原来的`BmobImService`名称更换为`BmobIMService`，请务必修改，否则将无法正常使用IM服务。**

### 3.4、代码配置
#### 3.4.1、注册消息接收器

##### 3.4.1.1、如果你使用的是`NewIM_V2.0.2`及以后的版本

1、请自定义消息接收器继承自`BmobIMMessageHandler`来处理服务器发来的消息和离线消息。

```java
public class DemoMessageHandler extends BmobIMMessageHandler{

    @Override
    public void onMessageReceive(final MessageEvent event) {
        //当接收到服务器发来的消息时，此方法被调用
    }

    @Override
    public void onOfflineReceive(final OfflineMessageEvent event) {
        //每次调用connect方法时会查询一次离线消息，如果有，此方法会被调用
    }
}

```

2、在Application的onCreate方法中注册这个`DemoMessageHandler`。

```java

public class BmobIMApplication extends Application{

    @Override
    public void onCreate() {
        super.onCreate();
		//NewIM初始化
		BmobIM.init(this);
		//注册消息接收器
        BmobIM.registerDefaultMessageHandler(new DemoMessageHandler(this));
    }
}
```

##### 3.4.1.2、如果你使用的SDK版本是`NewIM_V2.0.1`

1、请创建一个广播消息接收器，用于接收服务器发来的消息。

```java

public class MessageReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(final Context context, Intent intent) {
        if(intent!=null){
            final MessageEvent event =(MessageEvent)intent.getSerializableExtra("event");
            //开发者可以在这里发应用通知
    }
}

```

2、在`AndroidManifest.xml`中注册此receiver。

```xml
<receiver
    android:name="程序包名.MessageReceiver"
    android:enabled="true">
    <intent-filter>
        <action android:name="cn.bmob.im.action.MESSAGE"/>
    </intent-filter>
</receiver>

```

#### 3.4.2、初始化BmobNewIM SDK

在Application的onCreate方法中调用`BmobIM.init(context)`。

```java
public class BmobIMApplication extends Application{

    @Override
    public void onCreate() {
        super.onCreate();
        //只有主进程运行的时候才需要初始化
        if (getApplicationInfo().packageName.equals(getMyProcessName())){
            //im初始化
            BmobIM.init(this);
            //注册消息接收器
            BmobIM.registerDefaultMessageHandler(new DemoMessageHandler(this));
        }
    }

	/**
     * 获取当前运行的进程名
     * @return
     */
    public static String getMyProcessName() {
        try {
            File file = new File("/proc/" + android.os.Process.myPid() + "/" + "cmdline");
            BufferedReader mBufferedReader = new BufferedReader(new FileReader(file));
            String processName = mBufferedReader.readLine().trim();
            mBufferedReader.close();
            return processName;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

}


```

注：

1. 初始化方法包含了BmobSDK的初始化步骤，故无需再初始化BmobSDK。
2. 在初始化的时候，最好做下判断：只有主进程运行的时候才开始初始化，避免资源浪费。

## 4、BmobNewIM SDK 使用
### 4.1、服务器连接

#### 4.1.1、连接

调用`connect`方法，需要传入唯一用户标示`clientId`，Demo使用的是Bmob的用户登录系统`objectId`。

```java
User user = BmobUser.getCurrentUser(context,User.class);
BmobIM.connect(user.getObjectId(), new ConnectListener() {
    @Override
    public void done(String uid, BmobException e) {
        if (e == null) {
            Logger.i("connect success");
        } else {
            Logger.e(e.getErrorCode() + "/" + e.getMessage());
        }
    }
});
```

#### 4.1.2、断开连接：

调用`disConnect`方法，客户端会断开与服务器之间的连接，再次聊天需要重新调用`connect`方法完成与服务器之间的连接。
```java
BmobIM.getInstance().disConnect();

```
#### 4.1.3、监听服务器连接状态

调用`setOnConnectStatusChangeListener`方法即可监听到当前长链接的连接状态。

```java
 BmobIM.getInstance().setOnConnectStatusChangeListener(new ConnectStatusChangeListener() {
            @Override
            public void onChange(ConnectionStatus status) {
                Logger.i("" + status.getMsg());
            }
        });
```
### 4.2、会话

#### 4.2.1、创建会话

BmobNewIM SDK 采用会话（`BmobIMConversation`）管理消息(`BmobIMMessage`)的方式，即消息的查询、发送和删除等操作均在指定会话下进行，因此需要获取指定会话信息并创建会话实例。目前创建会话有两种创建方式，分别是`暂态会话`和`常态会话`。

##### 4.2.1.1、暂态消息

BmobNewIM SDK在`BmobIMMessage`类中新增`isTransient`属性来标识该条消息是否自动保存到`聊天对象`的本地DB中。

- 设置为`true`,表明为暂态消息，那么这条消息`并不会保存到聊天对象的本地db中`，SDK只负责发送和接收。
- 设置为`false`,表明不为暂态消息，SDK会`自动保存该类型的消息到指定会话的数据库`中。


##### 4.2.1.2、创建暂态会话

该会话只提供消息发送功能，不可使用其他查询，删除等API，`不会自动创建会话`到本地DB中。一般用于`自定义消息的发送`，比如，添加好友的请求，在对方还没有同意的情况下，你并不希望在自己的会话列表中显示该会话。`v2.0.4`版本的NewIM开始提供此种方式创建暂态会话

```java
//开启私聊会话，isTransient可设置是否保存该会话到自己的本地会话表中
startPrivateConversation(BmobIMUserInfo info, boolean isTransient,ConversationListener listener)
```

##### 4.2.1.3、创建常态会话

该会话提供消息查询、发送、删除等功能，SDK内部自动创建该会话。

```java
//开启私聊会话，默认会保存该会话到自己的本地会话表中
startPrivateConversation(BmobIMUserInfo info, ConversationListener listener)
```

##### 4.2.1.3、创建会话示例
`BmobIMUserInfo`类，是用户信息类，有三个属性需要开发者关注下：userId(用户唯一id)，name(用户名)，avatar（用户头像）。

```java
//如果需要更新用户资料，开发者只需要传新的info进去就可以
BmobIM.getInstance().startPrivateConversation(BmobIMUserInfo info, new ConversationListener() {
    @Override
    public void done(BmobIMConversation c, BmobException e) {
        if(e==null){
			//在此跳转到聊天页面
            Bundle bundle = new Bundle();
            bundle.putSerializable("c", c);
            startActivity(ChatActivity.class, bundle, false);
        }else{
            toast(e.getMessage()+"("+e.getErrorCode()+")");
        }
    }
});
```

#### 4.2.2、查询全部会话

```
BmobIM.getInstance().loadAllConversation()
```
#### 4.2.3、查询会话的未读消息数量

##### 4.2.3.1、查询指定会话下的未读消息数量

```
BmobIM.getInstance().getUnReadCount(String conversationId)
```
##### 4.2.3.2、查询全部会话的全部未读消息数

```
BmobIM.getInstance().getUnReadCount.getAllUnReadCount()
```

#### 4.2.4、删除指定会话

```
//提供两种方式删除会话
BmobIM.getInstance().deleteConversation(BmobIMConversation c);
BmobIM.getInstance().deleteConversation(String conversationId);
```

#### 4.2.5、清空全部会话

```
BmobIM.getInstance().clearAllConversation();
```

#### 4.2.6、更新会话标题、会话图标及用户信息

由于BmobNewIM SDK并不包含与用户有关的逻辑，只负责存储用户信息并对外提供更新等方法,用来操作本地的用户信息。

在与人单聊时，需要更新会话标题和会话图标及用户信息，可调用如下方法在`DemoMessageHandler`的全局消息接收器中进行统一更新。

```java
/**更新用户资料和会话资料
 * @param event
 * @param listener
 */
public void updateUserInfo(MessageEvent event,final UpdateCacheListener listener){
    final BmobIMConversation conversation=event.getConversation();
    final BmobIMUserInfo info =event.getFromUserInfo();
	final BmobIMMessage msg =event.getMessage();
    String username =info.getName();
    String title =conversation.getConversationTitle();
    //sdk内部，将新会话的会话标题用objectId表示，因此需要比对用户名和会话标题--单聊，后续会根据会话类型进行判断
    if(!username.equals(title)) {
        UserModel.getInstance().queryUserInfo(info.getUserId(), new QueryUserListener() {
            @Override
            public void done(User s, BmobException e) {
                if(e==null){
                    String name =s.getUsername();
                    String avatar = s.getAvatar();
                    conversation.setConversationIcon(avatar);
                    conversation.setConversationTitle(name);
                    info.setName(name);
                    info.setAvatar(avatar);
                    //更新用户资料
                    BmobIM.getInstance().updateUserInfo(info);
                   //更新会话资料-如果消息是暂态消息，则不更新会话资料
                    if(!msg.isTransient()){
                        BmobIM.getInstance().updateConversation(conversation);
                    }
                }else{
                    Logger.e(e);
                }
                listener.done(null);
            }
        });
    }else{
        listener.internalDone(null);
    }
}
```

### 4.3、消息

消息(`BmobIMMessage`)是所有消息的基类，以下BmobNewIM SDK目前支持的消息类型。

| 消息类型     | 消息类名          |
|------------------------------|--------------------------------|
| 文本| BmobIMTextMessage|
| 文件|BmobIMFileMessage|         
| 图像| BmobIMImageMessage|  
| 音频| BmobIMAudioMessage|
| 视频| BmobIMVideoMessage|
| 地理位置| BmobIMLocationMessage| 

#### 4.3.1、获取指定会话信息并创建会话实例
BmobNewIM SDK采用会话（`BmobIMConversation`）管理消息(`BmobIMMessage`)的方式，即消息的查询、发送和删除等操作均在指定会话下进行，因此需要通过以下两个步骤来获取指定会话信息并创建会话实例。

1、 开启私聊

```java
//如果需要更新用户资料，开发者只需要传新的info进去就可以
BmobIM.getInstance().startPrivateConversation(BmobImUserInfo info, new ConversationListener() {
    @Override
    public void done(BmobIMConversation c, BmobException e) {
        if(e==null){
			//在此跳转到聊天页面
            Bundle bundle = new Bundle();
            bundle.putSerializable("c", c);
            startActivity(ChatActivity.class, bundle, false);
        }else{
            toast(e.getMessage()+"("+e.getErrorCode()+")");
        }
    }
});

```
2、 创建会话实例

使用`BmobIMConversation.obtain(BmobIMClient client,BmobIMConversation conversation)`方法传入`BmobIMClient和BmobIMConversation的各自实例`就可以创建一个用于控制消息查询、发送和删除的会话实例。

```
BmobIMConversation c;
//在聊天页面的onCreate方法中，通过如下方法创建新的会话实例,这个obtain方法才是真正创建一个管理消息发送的会话
c=BmobIMConversation.obtain(BmobIMClient.getInstance(),(BmobIMConversation)getBundle().getSerializable("c"));

```

创建完成后，就可以使用这个会话实例`c`对消息进行各种操作啦，以下操作中的`c`都指的是该会话实例。

注：
如果不调用BmobIMConversation的`obtain`方法是无法控制消息发送等操作的，会报`client disconnect`的错误。


#### 4.3.2、查询指定会话的聊天记录

```java
//首次加载，可设置msg为null，
//下拉刷新的时候，可用消息表的第一个msg作为刷新的起始时间点，默认按照消息时间的降序排列，limit由开发者控制
c.queryMessages(msg, limit, new MessagesQueryListener() {
            @Override
            public void done(List<BmobIMMessage> list, BmobException e) {
                sw_refresh.setRefreshing(false);
                if (e == null) {
                    if (null != list && list.size() > 0) {
                        adapter.addMessages(list);
                        adapter.notifyDataSetChanged();
                        layoutManager.scrollToPositionWithOffset(list.size() - 1, 0);
                    }
                } else {
                    toast(e.getMessage() + "(" + e.getErrorCode() + ")");
                }
            }
        });

```

#### 4.3.3、删除指定会话的聊天记录

删除消息不同于删除会话，会直接清空本地的消息记录数据。

```java
//删除指定聊天消息
c.deleteMessage(BmobIMMessage msg)

//删除一条或多条聊天消息
c.deleteBatchMessage(List<BmobIMMessage> msgs)

//清空该会话下的聊天消息，允许保留会话（可选）
c.clearMessage(boolean isKeepConversion,MessageListener listener)

注：isKeepConversion 表示是否保留该会话消息。

```

#### 4.3.4、更新指定会话的所有消息为已读状态

可以在`ChatActivity`的聊天页面的`onDestory`方法中调用如下方法`更新该会话的的所有消息为已读状态`：

```
//更新此会话的所有消息为已读状态
c.updateLocalCache();

```

### 4.4、消息发送

#### 4.4.1、文本消息

文本消息可以是纯文本，也可以是包含表情的文本消息，通过`BmobIMTextMessage`的`setContent`方法设置内容来构建`BmobIMTextMessage`实例，再调用`BmobIMConversation`的`sendMessage`方法发送。

```java
BmobIMTextMessage msg =new BmobIMTextMessage();
msg.setContent(text);
//可随意设置额外信息
Map<String,Object> map =new HashMap<>();
map.put("level", "1");
msg.setExtraMap(map);
c.sendMessage(msg, new MessageSendListener() {
    @Override
    public void onStart(BmobIMMessage msg) {
        super.onStart(msg);
		scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```

#### 4.4.2、图片消息

图片可以是通过系统拍照或本地相册中获取的本地图片地址，也可以使用网络上某个有效的图片地址。然后构造一个`BmobIMImageMessage`对象，再调用`BmobIMConversation`的`sendMessage`方法发送。

##### 4.4.2.1、发送本地图片

使用系统拍照功能或从本地相册中获取到本地图片地址(`localPath`)，然后调用构造方法`BmobIMImageMessage（String localPath）`来创建`BmobIMImageMessage`实例。

```java
BmobIMImageMessage image =new BmobIMImageMessage(localPath);
c.sendMessage(image, new MessageSendListener() {

	@Override
    public void onProgress(int value) {
        super.onProgress(value);
        //文件类型的消息才有进度值:do something
        Logger.i("onProgress："+value);
    }

    @Override
    public void onStart(BmobIMMessage msg) {
        scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```

##### 4.4.2.2、发送远程图片URL

例如，从微博或QQ中获取到某个图片地址，然后调用BmobIMImageMessage的`setRemoteUrl`方法设置远程图片URL来创建`BmobIMImageMessage`实例。

```java
BmobIMImageMessage image =new BmobIMImageMessage();
image.setRemoteUrl("http://img.lakalaec.com/ad/57ab6dc2-43f2-4087-81e2-b5ab5681642d.jpg");
c.sendMessage(image, new MessageSendListener() {

	@Override
    public void onProgress(int value) {
        super.onProgress(value);
        //文件类型的消息才有进度值
        Logger.i("onProgress："+value);
    }

    @Override
    public void onStart(BmobIMMessage msg) {
        scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```
#### 4.4.3、语音消息

语音可以是通过录制音频得到的本地音频地址，也可以使用网络上某个有效的音频地址。然后构造一个`BmobIMAudioMessage`对象，再调用`BmobIMConversation`的`sendMessage`方法发送。

##### 4.4.3.1、发送本地音频文件：

```java
BmobIMAudioMessage image =new BmobIMAudioMessage(localPath);
c.sendMessage(image, new MessageSendListener() {

	@Override
    public void onProgress(int value) {
        super.onProgress(value);
        //文件类型的消息才有进度值:do something
        Logger.i("onProgress："+value);
    }

    @Override
    public void onStart(BmobIMMessage msg) {
        scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```

##### 4.4.3.2、发送远程语音URL地址

同样的，语音消息也支持发送远程语音URL地址：

```java
BmobIMAudioMessage image =new BmobIMAudioMessage();
image.setRemoteUrl("远程语音地址");
c.sendMessage(image, new MessageSendListener() {

	@Override
    public void onProgress(int value) {
        super.onProgress(value);
        //文件类型的消息才有进度值
        Logger.i("onProgress："+value);
    }

    @Override
    public void onStart(BmobIMMessage msg) {
        scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```

#### 4.4.4、地理位置消息

地理位置可以通过任意地图SDK获取到经纬度，详细地址等信息，然后调用`BmobIMLocationMessage(String address,double latitude,double longitude)`构造方法构建`BmobIMLocationMessage`实例，再调用`BmobIMConversation`的`sendMessage`方法发送。：

```java
BmobIMLocationMessage location =new BmobIMLocationMessage("广州番禺区",23.5,112.0);
c.sendMessage(location,  new MessageSendListener() {

    @Override
    public void onStart(BmobIMMessage msg) {
        scrollToBottom();
        adapter.addMessage(msg);
        adapter.notifyDataSetChanged();
    }

    @Override
    public void done(BmobIMMessage msg, BmobException e) {
        scrollToBottom();
        adapter.notifyDataSetChanged();
        edit_msg.setText("");
        if (e != null) {
            toast(e.getMessage());
        }
    }
});

```

### 4.5、消息接收

#### 4.5.1、自定义消息接收器

##### 4.5.1.1、`NewIM_V2.0.4及以后`的NewBmobIM SDK版本

如果你使用的是`NewIM_V2.0.4以后(包含v2.0.4)`的SDK版本,那么不仅可以使用`BmobIMMessageHandler`方式来注册全局的消息接收器，还可以使用`MessageListHandler`为单个页面注册消息接收器，具体步骤如下：

1. 在`Activity/Fragment`中实现`MessageListHandler`接口；
2. 在`onResume`方法中添加页面消息监听器：`BmobIM.getInstance().addMessageListHandler(this)`；
3. 在`onPause`方法中移除页面消息监听器：`BmobIM.getInstance().removeMessageListHandler(this)`；
4. 在`MessageListHandler`接口的`onMessageReceive`方法中做相关的操作。

具体示例可查看NewIMDemo中的`ChatActivity`类：

```java
@Override
public void onMessageReceive(List<MessageEvent> list) {
    //当注册页面消息监听时候，有消息（包含离线消息）到来时会回调该方法
    for (int i=0;i<list.size();i++){
		//do something...
    }
}

```
##### 4.5.1.2、`NewIM_V2.0.2及以后`的NewBmobIM SDK版本

如果你使用的是`NewIM_V2.0.2以后(包含v2.0.2)`的SDK版本,那么只需要自定义消息接收器继承自`BmobIMMessageHandler`来处理服务器发来的消息和离线消息。

```java

public class DemoMessageHandler extends BmobIMMessageHandler{

    private Context context;
    public DemoMessageHandler(Context context) {
        this.context = context;
    }

    @Override
    public void onMessageReceive(final MessageEvent event) {
        //当接收到服务器发来的消息时，此方法被调用
		//可以统一在此检测更新会话及用户信息
        UserModel.getInstance().updateUserInfo(event, new UpdateCacheListener() {
            @Override
            public void done(BmobException e) {
                BmobIMMessage msg = event.getMessage();
				//用户自定义的消息类型，其类型值均为0
                if(BmobIMMessageType.getMessageTypeValue(msg.getMsgType())==0){
                    //自行处理自定义消息类型
                    Logger.i(msg.getMsgType() + "," + msg.getContent() + "," + msg.getExtra());
                }else{//SDK内部内部支持的消息类型
                    if (BmobNotificationManager.getInstance(context).isShowNotification()){
						//如果需要显示通知栏，可以使用BmobNotificationManager类提供的方法，也可以自己写通知栏显示方法
                   }else{//直接发送消息事件
                        Logger.i("当前处于应用内，发送event");
                        EventBus.getDefault().post(event);
                    }
                }
            }
        });
    }

    @Override
    public void onOfflineReceive(final OfflineMessageEvent event) {
        //每次调用connect方法时会查询一次离线消息，如果有，此方法会被调用
		Map<String,List<MessageEvent>> map =event.getEventMap();
        Logger.i("离线消息属于"+map.size()+"个用户");
        for (Map.Entry<String, List<MessageEvent>> entry : map.entrySet()) {
            List<MessageEvent> list =entry.getValue();
            //挨个检测离线用户信息是否需要更新
            UserModel.getInstance().updateUserInfo(list.get(0), new UpdateCacheListener() {
                @Override
                public void done(BmobException e) {
                    EventBus.getDefault().post(event);
                }
            });
        }
    }
}

```

同样，别忘记在Application的onCreate方法中注册这个`DemoMessageHandler`：

```java

public class BmobIMApplication extends Application{

    @Override
    public void onCreate() {
        super.onCreate();
		//注册消息接收器
        BmobIM.registerDefaultMessageHandler(new DemoMessageHandler(this));
    }
}
```
##### 4.5.1.3、`NewIM_V2.0.1`的NewBmobIM SDK版本

如果你使用的SDK版本是`NewIM_V2.0.1`,那么你需要在应用中创建一个`BroadcastReceiver`广播消息接收器，用于接收服务器发来的消息。

	public class MessageReceiver extends BroadcastReceiver {
	
	    @Override
	    public void onReceive(final Context context, Intent intent) {
	        if(intent!=null){
	            final MessageEvent event =(MessageEvent)intent.getSerializableExtra("event");
				//可以统一在此检测更新会话及用户信息
		        UserModel.getInstance().updateUserInfo(event, new UpdateCacheListener() {
		            @Override
		            public void done(BmobException e) {
		                BmobIMMessage msg = event.getMessage();
						//用户自定义的消息类型，其类型值均为0
		                if(BmobIMMessageType.getMessageTypeValue(msg.getMsgType())==0){
		                    //自行处理自定义消息类型
		                    Logger.i(msg.getMsgType() + "," + msg.getContent() + "," + msg.getExtra());
		                }else{//SDK内部内部支持的消息类型
		                    if (BmobNotificationManager.getInstance(context).isShowNotification()){
								//如果需要显示通知栏，可以使用BmobNotificationManager类提供的方法，也可以自己写通知栏显示方法
		                   }else{//直接发送消息事件
		                        Logger.i("当前处于应用内，发送event");
		                        EventBus.getDefault().post(event);
		                    }
		                }
		            }
		        });
	    }
	}


别忘记在`AndroidManifest.xml`中注册这个receiver

```xml
<receiver
    android:name="程序包名.MessageReceiver"
    android:enabled="true">
    <intent-filter>
        <action android:name="cn.bmob.im.action.MESSAGE"/>
    </intent-filter>
</receiver>

```

#### 4.5.2、应用内消息接收

`V2.0.1`的SDK内部集成EventBus库（`V2.0.2`SDK内部不再集成EventBus，开发者可以自行使用新版EventBus）来进行应用内消息的分发，故在应用内需要接收消息的地方注册和解注册EventBus即可。

SDK内部有两种事件：`MessageEvent（聊天消息）`、`OfflineMessageEvent(离线消息)`。

1、注册EventBus

```
EventBus.getDefault().register(this);

```
2、解注册EventBus

```java
EventBus.getDefault().unregister(this);

```

3、处理聊天消息

```java
/**聊天消息接收事件
 * @param event
 */
public void onEventMainThread(MessageEvent event){
    //处理聊天消息
}

```

4、处理离线消息

```java

/**离线消息接收事件
 * @param event
 */
public void onEventMainThread(OfflineMessageEvent event){
    //处理离线消息
}

```

#### 4.5.3、应用外通知栏提醒

SDK新增`BmobNotificationManager`类，并提供如下两个方法供开发者展示通知栏:

1. **多个用户的多条消息合并成一条通知：有XX个联系人发来了XX条消息**
                           
```java
/**显示通知：多个用户的多条消息合并显示一条通知
 * @param event 某个消息事件：包含消息、会话及发送用户的信息
 * @param intent 跳转intent
 */
 BmobNotificationManager.getInstance(context).showNotification(MessageEvent event,Intent pendingIntent);

```
2. **自定义通知消息：始终只有一条通知，新消息覆盖旧消息**

```java
 /**显示通知
 * @param largerIcon 通知栏图标 开发者可传应用图标，也可以将聊天头像转成bitmap
 * @param title 标题
 * @param content 内容
 * @param ticker 状态栏上显示的内容
 * @param intent 跳转的intent
 */
BmobNotificationManager.getInstance(context).showNotification(Bitmap largerIcon,String title, String content, String ticker,Intent intent);
```

**注：为了使SDK能够区分当前应用是否退出，开发者需进行以下几个步骤：**

1、在会话和聊天的Activity类实现'ObseverListener'监听器；

2、在`onResume`方法中调用`BmobNotificationManager.getInstance(context).addObserver(this)`方法添加观察者；
   在`onPause`方法中调用`BmobNotificationManager.getInstance(context).removeObserver(this)`方法移除观察者

3、在主Activity的`onDestroy`方法中调用`BmobNotificationManager.getInstance(context).clearObserver()`清空观察者。

### 4.6、自定义消息

#### 4.6.1、设置额外信息

有些时候，开发者需要在发送消息时携带一些额外信息，例如`发送方的设备类型、图片的拍摄地点或者音频的来源`等，那么开发者可以通过 `BmobIMExtraMessage.extraMap`属性来解决，任何继承`BmobIMExtraMessage`类的消息均支持设置额外信息。

```
	BmobIMAudioMessage audio =new BmobIMAudioMessage();
	image.setRemoteUrl("远程音频地址");
	//设置音频文件的来源
	Map<String,Object> map =new HashMap<>();
	map.put("from", "优酷");
	audio.setExtraMap(map);
	c.sendMessage(audio, listener);
```

#### 4.6.2、创建新的消息类型

如果设置额外信息无法满足开发者的需求，那么开发者也可以自定义自己的消息类型。
##### 4.6.2.1、创建自定义消息

1. 继承自`BmobIMExtraMessage`类；
2. 重写`getMsgType`方法，填写自定义的消息类型；
3. 重写`isTransient`方法。

```
	public class AddFriendMessage extends BmobIMExtraMessage{
	
	    @Override
	    public String getMsgType() {
	        return "add";
	    }
	
	    @Override
	    public boolean isTransient() {
	        //设置为true,表明为暂态消息，那么这条消息并不会保存到对方的本地db中
	        //设置为false,则会保存到对方指定会话的本地数据库中
	        return true;
	    }
	
	    public AddFriendMessage(){}
	
	}
```

##### 4.6.2.2、发送自定义消息

```
	//启动一个会话，如果isTransient设置为true,则不会创建在本地会话表中创建该会话，
	//设置isTransient设置为false,则会在本地数据库的会话列表中先创建（如果没有）与该用户的会话信息，且将用户信息存储到本地的用户表中
	BmobIMConversation c = BmobIM.getInstance().startPrivateConversation(info, true,null);
	//这个obtain方法才是真正创建一个管理消息发送的会话
	BmobIMConversation conversation = BmobIMConversation.obtain(BmobIMClient.getInstance(), c);
	AddFriendMessage msg =new AddFriendMessage();
	User currentUser = BmobUser.getCurrentUser(this,User.class);
	msg.setContent("很高兴认识你，可以加个好友吗?");//给对方的一个留言信息
	Map<String,Object> map =new HashMap<>();
	map.put("name", currentUser.getUsername());//发送者姓名，这里只是举个例子，其实可以不需要传发送者的信息过去
	map.put("avatar",currentUser.getAvatar());//发送者的头像
	map.put("uid",currentUser.getObjectId());//发送者的uid
	msg.setExtraMap(map);
	conversation.sendMessage(msg, new MessageSendListener() {
	    @Override
	    public void done(BmobIMMessage msg, BmobException e) {
	        if (e == null) {//发送成功
	            toast("好友请求发送成功，等待验证");
	        } else {//发送失败
	            toast("发送失败:" + e.getMessage());
	        }
	    }
	});
```

### 4.7、用户管理

BmobNewIM SDK只是即时通讯的消息收发渠道，本身并不提供用户体系。开发者可使用BmobSDK提供的用户管理方面功能，也可使用开发者自己的用户体系。

BmobNewIM SDK内部会自动创建本地用户表，并对外提供方法供开发者调用来操作本地用户表。开发者只需要调用`updateUserInfo`方法即可更新本地用户信息。

#### 4.7.1、BmobIMUserInfo介绍

BmobNewIM SDK中用户的实体类为`BmobIMUserInfo`，其有四个属性，开发者只需要关心后三个即可。

| 属性名     | 属性含义          |
|------------------------------|--------------------------------|
| id | 本地数据库用户表的id值，开发者无需关心 |
| userId| 用户唯一id（Demo中用的是BmobUser的objectId）|         
| name| 用户名 （Demo中是用的是BmobUser的username）|  
| avatar| 用户头像|

#### 4.7.2、更新本地用户信息

以下两种情况需要更新用户信息：
1. 当注册或登录成功后，需要更新下当前用户的信息到本地数据库的用户表中，这样才能通过getUserInfo方法获取到本地的用户信息。
2. 当接收到某人消息的时候，同样需要更新A的用户信息到本地用户表中，否则在会话界面将默认显示的是用户的userId，也就是Demo中的BmobUser的objectId值。

##### 4.7.2.1、更新单一本地用户信息
```
BmobIM.getInstance().updateUserInfo(BmobIMUserInfo info)
```
##### 4.7.2.2、批量更新本地用户信息
```
BmobIM.getInstance().updateBatchUserInfo(List<BmobIMUserInfo> list)
```

#### 4.7.3、获取本地用户信息

BmobNewIM SDK内部会自动创建了一个本地数据库用来存储用户信息，开发者需要先调用`updateUserInfo`更新用户信息到本地数据库中，才能通过`getUserInfo(uid)`获取到本地用户信息。

```
BmobIM.getInstance().getUserInfo(String uid)
```

### 4.8、好友管理

BmobNewIM SDK中并没有集成好友管理相关的功能，为了方便开发者建立基于好友之间的聊天模式，在`v2.0.4`版本开始的Demo中使用Data SDK新建了`Friend`表来进行好友管理。

```java
/**好友表
 * @author smile
 * @project Friend
 * @date 2016-04-26
 */
public class Friend extends BmobObject{

	//用户
    private User user;
	//好友
    private User friendUser;

    //getter setter...
}
```

#### 4.8.1、获取好友列表

以下摘自`UserModel(cn.bmob.imdemo.model)`类：

```java
/**
 * 查询好友
 * @param listener
 */
public void queryFriends(final FindListener<Friend> listener){
    BmobQuery<Friend> query = new BmobQuery<>();
    User user =BmobUser.getCurrentUser(getContext(), User.class);
    query.addWhereEqualTo("user", user);
    query.include("friendUser");
    query.order("-updatedAt");
    query.findObjects(getContext(), new FindListener<Friend>() {
        @Override
        public void onSuccess(List<Friend> list) {
            if (list != null && list.size() > 0) {
                listener.onSuccess(list);
            } else {
                listener.onError(0, "暂无联系人");
            }
        }

        @Override
        public void onError(int i, String s) {
            listener.onError(i, s);
        }
    });
}

```


#### 4.8.2、删除好友

以下摘自`UserModel(cn.bmob.imdemo.model)`类：

```java
/**
 * 删除好友
 * @param f
 * @param listener
 */
public void deleteFriend(Friend f,DeleteListener listener){
    Friend friend =new Friend();
    friend.delete(getContext(),f.getObjectId(),listener);
}

```

#### 4.8.3、添加好友

Demo中创建了一个`NewFriend`的本地数据库类用来存储所有的添加好友请求。

```java
/**本地的好友请求表
 * @author :smile
 * @project:NewFriend
 * @date :2016-04-26-17:28
 */
public class NewFriend implements java.io.Serializable {

    private Long id;
	//用户uid
    private String uid;
	//留言消息
    private String msg;
	//用户名
    private String name;
	//头像
    private String avatar;
	//状态：未读、已读、已添加、已拒绝等
    private Integer status;
	//请求时间
    private Long time;

    //getter setter...

}

```
Demo中创建了一个`AddFriendMessage`类来展示如何发送自定义的添加好友请求的消息。

```java
/**添加好友请求-自定义消息类型
 * @author :smile
 * @project:AddFriendMessage
 * @date :2016-01-30-17:28
 */
public class AddFriendMessage extends BmobIMExtraMessage{

    public AddFriendMessage(){}

    @Override
    public String getMsgType() {
		//自定义一个`add`的消息类型
        return "add";
    }

    @Override
    public boolean isTransient() {
        //设置为true,表明为暂态消息，那么这条消息并不会保存到本地db中，SDK只负责发送出去
        //设置为false,则会保存到指定会话的数据库中
        return true;
    }
	
	...

}

```

Demo中创建了一个`AgreeAddFriendMessage`类来展示如何发送自定义的同意添加好友请求的消息，并在对方的本地会话表中新增消息类型。

```java
/**同意添加好友请求-仅仅只用于发送同意添加好友的消息
 * @author smile
 * @project AgreeAddFriendMessage
 * @date 2016-03-04-10:41
 */
public class AgreeAddFriendMessage extends BmobIMExtraMessage{

    //以下均是从extra里面抽离出来的字段，方便获取
    private String uid;//最初的发送方
    private Long time;
    private String msg;//用于通知栏显示的内容

    @Override
    public String getMsgType() {
        return "agree";
    }

    @Override
    public boolean isTransient() {
        //如果需要在对方的会话表中新增一条该类型的消息，则设置为false，表明是非暂态会话
        //此处将同意添加好友的请求设置为false，为了演示怎样向会话表和消息表中新增一个类型，在对方的会话列表中增加`我通过了你的好友验证请求，我们可以开始聊天了!`这样的类型
        return false;
    }

    //getter setter...

    ...
}

```

#### 4.8.4、发送添加好友的请求

以下摘自`UserInfoActivity(cn.bmob.imdemo.ui)`类：

```java
/**
 * 发送添加好友的请求
 */
private void sendAddFriendMessage(){
    //启动一个暂态会话，也就是isTransient为true,表明该会话仅执行发送消息的操作，不会保存会话和消息到本地数据库中，
    BmobIMConversation c = BmobIM.getInstance().startPrivateConversation(info, true,null);
    //这个obtain方法才是真正创建一个管理消息发送的会话
    BmobIMConversation conversation = BmobIMConversation.obtain(BmobIMClient.getInstance(), c);
	//新建一个添加好友的自定义消息实体
    AddFriendMessage msg =new AddFriendMessage();
    User currentUser = BmobUser.getCurrentUser(this,User.class);
    msg.setContent("很高兴认识你，可以加个好友吗?");//给对方的一个留言信息
    Map<String,Object> map =new HashMap<>();
    map.put("name", currentUser.getUsername());//发送者姓名，这里只是举个例子，其实可以不需要传发送者的信息过去
    map.put("avatar",currentUser.getAvatar());//发送者的头像
    map.put("uid",currentUser.getObjectId());//发送者的uid
    msg.setExtraMap(map);
    conversation.sendMessage(msg, new MessageSendListener() {
        @Override
        public void done(BmobIMMessage msg, BmobException e) {
            if (e == null) {//发送成功
                toast("好友请求发送成功，等待验证");
            } else {//发送失败
                toast("发送失败:" + e.getMessage());
            }
        }
    });
}

```
#### 4.8.5、发送同意添加好友的请求

以下摘自`NewFriendHolder(cn.bmob.imdemo.adapter)`类：

```java
/**
 * 发送同意添加好友的请求
 */
private void sendAgreeAddFriendMessage(final NewFriend add,final SaveListener listener){
	//发给谁，就填谁的用户信息
    BmobIMUserInfo info = new BmobIMUserInfo(add.getUid(), add.getName(), add.getAvatar());
    //启动一个暂态会话，也就是isTransient为true,表明该会话仅执行发送消息的操作，不会保存会话和消息到本地数据库中，
    BmobIMConversation c = BmobIM.getInstance().startPrivateConversation(info,true,null);
    //这个obtain方法才是真正创建一个管理消息发送的会话
    BmobIMConversation conversation = BmobIMConversation.obtain(BmobIMClient.getInstance(),c);
    //而AgreeAddFriendMessage的isTransient设置为false，表明我希望在对方的会话数据库中保存该类型的消息
    AgreeAddFriendMessage msg =new AgreeAddFriendMessage();
    User currentUser = BmobUser.getCurrentUser(getContext(), User.class);
    msg.setContent("我通过了你的好友验证请求，我们可以开始聊天了!");//---这句话是直接存储到对方的消息表中的
    Map<String,Object> map =new HashMap<>();
    map.put("msg",currentUser.getUsername()+"同意添加你为好友");//显示在通知栏上面的内容
    map.put("uid",add.getUid());//发送者的uid-方便请求添加的发送方找到该条添加好友的请求
    map.put("time", add.getTime());//添加好友的请求时间
    msg.setExtraMap(map);
    conversation.sendMessage(msg, new MessageSendListener() {
        @Override
        public void done(BmobIMMessage msg, BmobException e){
            if (e == null) {//发送成功
                //修改本地的好友请求记录
                NewFriendManager.getInstance(getContext()).updateNewFriend(add.getUid(),add.getTime(),Config.STATUS_VERIFIED);
                listener.onSuccess();
            } else {//发送失败
                listener.onFailure(e.getErrorCode(),e.getMessage());
            }
        }
    });
}

```

#### 4.8.6、接收并处理好友相关的请求

以下摘自`DemoMessageHandler(cn.bmob.imdemo)`类：

```java
/**
 * 处理自定义消息类型:用户自定义的消息类型，其类型值均为0
 * @param msg
 */
private void processCustomMessage(BmobIMMessage msg,BmobIMUserInfo info){
    String type =msg.getMsgType();
    //发送页面刷新的广播
    EventBus.getDefault().post(new RefreshEvent());
    //处理消息
    if(type.equals("add")){//接收到的添加好友的请求
        NewFriend friend = AddFriendMessage.convert(msg);
        //本地好友请求表做下校验，本地没有的才允许显示通知栏--有可能离线消息会有些重复
        long id = NewFriendManager.getInstance(context).insertOrUpdateNewFriend(friend);
        if(id>0){
            showAddNotify(friend);
        }
    }else if(type.equals("agree")){//接收到的对方同意添加自己为好友,此时需要做的事情：1、添加对方为好友，2、显示通知
        AgreeAddFriendMessage agree = AgreeAddFriendMessage.convert(msg);
        addFriend(agree.getFromId());//添加消息的发送方为好友
        //这里应该也需要做下校验--来检测下是否已经同意过该好友请求，我这里省略了
        showAgreeNotify(info,agree);
    }else{
        Toast.makeText(context,"接收到的自定义消息："+msg.getMsgType() + "," + msg.getContent() + "," + msg.getExtra(),Toast.LENGTH_SHORT).show();
    }
}

```

#### 4.8.7、添加到Friend表中

以下摘自`DemoMessageHandler(cn.bmob.imdemo)`类：

```java
/**
 * 添加对方为自己的好友
 * @param uid
 */
private void addFriend(String uid){
    User user =new User();
    user.setObjectId(uid);
	//添加到Friend表中
    UserModel.getInstance().agreeAddFriend(user, new SaveListener() {
        @Override
        public void onSuccess() {
            Log.i("bmob", "onSuccess");
        }

        @Override
        public void onFailure(int i, String s) {
            Log.i("bmob", "onFailure:"+s+"-"+i);
        }
    });
}
```

## 5、BmobNewIM SDK 混淆
```

# 不混淆im sdk
-keep class cn.bmob.newim.**{*;}
-dontwarn cn.bmob.newim.**
# 不混淆greenDao类
-dontwarn de.greenrobot.dao.**
-keep class de.greenrobot.dao.** { *;}
-keepclassmembers class * extends de.greenrobot.dao.AbstractDao {
    public static java.lang.String TABLENAME;
}
-keep class **$Properties
# 不混淆async
-dontwarn com.koushikdutta.async.**
-keep class com.koushikdutta.async.** { *;}
```

