## 消息推送简介
推送通知是让用户及时被通知、和你的应用保持联系的一种非常棒的方式，你可以快速而有效地通知到所有的用户，下面这个教程将会教你如何使用Bmob来推送消息。

## 消息推送快速入门

一、在Bmob官方网站的下载界面中，选择下载[Android推送SDK](http://www.bmob.cn/downloads)，将下载的zip压缩包进行解压，得到`Bmob_Push_v(版本号)_日期.jar`，然后将它放在你项目根目录下的"libs"目录中。

二、在您的应用程序AndroidManifest.xml文件中添加相应的权限：

```xml
	<!--BmobSDK所需的权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!--推送所需的权限-->
    <uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

三、在您的应用程序AndroidManifest.xml文件中注册BmobPush SDK运行所需的推送服务和消息接收器：

```xml
	<service
	    android:label="PushService"
		android:name="cn.bmob.push.lib.service.PushService"
		android:process=":bmobpush"
		android:exported="true">
	     <intent-filter>
	         <action android:name="cn.bmob.push.lib.service.PushService"/>
	     </intent-filter>
	</service>

	<!-- 用于进程保活 -->
    <service
        android:name="cn.bmob.push.lib.service.PushNotifyService"
        android:process=":bmobpush" >
    </service>
	
	<receiver android:name="cn.bmob.push.PushReceiver" >
	    <intent-filter>
	        <!-- 系统启动完成后会调用 -->
	        <action android:name="android.intent.action.BOOT_COMPLETED" />               
	        <!-- 解锁完成后会调用 -->
	        <action android:name="android.intent.action.USER_PRESENT" />
	        <!-- 监听网络连通性 -->
	        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />               
	    </intent-filter>
	</receiver>

	<!-- 第四部中创建的消息接收器，在这里进行注册 -->
	<receiver android:name="your.package.MyPushMessageReceiver">
	     <intent-filter >
	          <action android:name="cn.bmob.push.action.MESSAGE"/>
	     </intent-filter>
	</receiver>

	<!-- 接收心跳和唤醒的广播，要和PushService运行在同个进程 -->
    <receiver
         android:name="cn.bmob.push.PushNotifyReceiver"
         android:process=":bmobpush" >
         <intent-filter>
             <!-- 接收心跳广播的action -->
             <action android:name="cn.bmob.push.action.HEARTBEAT" />
             <!-- 接收唤醒广播的action -->
             <action android:name="cn.bmob.push.action.NOTIFY" />
         </intent-filter>
    </receiver>
```

四、在你的应用程序中创建一个消息接收器。

Push消息通过`action=cn.bmob.push.action.MESSAGE`的Intent把数据发送给客户端`your.package.MyPushMessageReceiver`,消息格式由应用自己决定，PushService只负责把服务器下发的消息以字符串格式透传给客户端。

`your.package.MyPushMessageReceiver`的代码示例如下：

```java
public class MyPushMessageReceiver extends BroadcastReceiver{

	@Override
	public void onReceive(Context context, Intent intent) {
		// TODO Auto-generated method stub
		if(intent.getAction().equals(PushConstants.ACTION_MESSAGE)){
			Log.d("bmob", "客户端收到推送内容："+intent.getStringExtra("msg"));
		}
	}
	
}
```

五、启动推送服务

在你的应用程序主Activity中调用如下方法：

```java
    // 初始化BmobSDK
    Bmob.initialize(this, "你的AppKey");
	// 使用推送服务时的初始化操作
	BmobInstallation.getCurrentInstallation().save();
	// 启动推送服务
	BmobPush.startWork(this);
```
代码中的"你的Application Id"就是你在Bmob后台中创建的应用程序的Application Id，如果你不知道这是什么，可以参考[快速入门文档](http://docs.bmob.cn/android/faststart/index.html?menukey=fast_start&key=start_android "Android快速入门")中的注册Bmob账号部分。


六、在web端进行推送设置

在应用面板-->消息推送-->推送设置界面中填写包名进行保存。
![](image/setting.png)

七、在web端推送消息

完成以上步骤后，你可以运行应用程序，从web推送一条消息给客户端。
![](image/pushmsg.png)


在后台推送消息给Android和iOS两个平台的时候，有一些需要注意的：
1、由于Android和iOS的提送机制不同，iOS要经过APNS，Android的推送完全是走Bmob的长连接服务，为兼容这个问题，如果你选择发送格式为“json”格式时，需要添加APNS兼容头部（见下面json的aps部分），推送内容格式如下：

```
{
	"aps": {
	"sound": "cheering.caf", 
	"alert": "这个是通知栏上显示的内容", 
	"badge": 0 
	}, 
	"xx" : "json的key-value对，你可以根据情况添加更多的，客户端进行解析获取", 
}
```

其中，sound是iOS接收时的声音，badge是iOS通知栏的累计消息数。

2、如果你选择发送格式为“text”时，推送内容为“推送消息测试。。。。”，Bmob会自动添加aps部分发送给APNS，，相当于自动生成如下的json格式的推送内容：

```
{
	"aps": {
		"alert": "推送消息测试。。。。", 
	}
}
```
同时，也会发送给Android端，相当于自动生成如下的json格式的推送内容：

```
{
	"alert" : "推送消息测试。。。。", 
}
```

3、如果只是发送给Android端，大家可以自定义json格式的数据。

4、由于iOS的APNS的推送的大小是有限制的，默认最多256bytes，因此,如果你需要跨平台互通的话，需注意推送的内容不要太长。

5、想要更多了解Bmob的推送格式的朋友，如即时聊天，可以查看我们在问答社区中的回答：[http://wenda.bmob.cn//?/question/204](http://wenda.bmob.cn//?/question/204)

八、源码下载

为了更好的让开发者朋友正确的集成和使用Bmob推送功能，我们还提供了一个关于Bmob推送功能的简单Demo以供大家参考，有需要的朋友可以到如下地址进行源码的下载。[https://github.com/bmob/bmob-android-demo-push](https://github.com/bmob/bmob-android-demo-push),
[0.9版本或以上的Demo](https://github.com/bmob/NewPushDemo)

## 消息推送的视频教程和Demo

Bmob官方为大家准备了消息推送的视频教程，有需要的朋友可以移步浏览视频教程：

客户端推送消息：[http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html](http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html)

集成BmobPushSDK：[http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html](http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html)


## 其他相关说明

以上文档仅仅介绍了如何实现消息的一次性推送，如果你还需要用到其他的推送方法，如组播、广播等，还需要详细阅读下面的相关知识。

### 安装消息推送服务
每一个Bmob的App被安装在用户的设备上后，如果要使用消息推送功能，Bmob SDK会自动生成一个Installation对象。Installation对象包含了推送所需要的所有信息。举例：一个棒球的App，你可以让用户订阅感兴趣的棒球队，然后及时将这个球队的消息推送给用户 。
您可以使用 BmobSDK，通过 **BmobInstallation** 对象进行一系列操作，就像你存储和获取其他的普通对象一样，比如BmobObject对象。

BmobInstallation对象有几个系统默认的特殊字段来帮助你进行设备定位等管理：
- **channels** : 当前这个设备订阅的渠道名称数组
- **timeZone** : 设备所在位置的时区， 如Asia/Shanghai，这个会在每个BmobInstallation对象更新时同步（只读）
- **deviceType** : 设备的的类型, 值为："ios" 或 "android" (只读)
- **installationId** : Bmob使用的设备唯一号 (只读)

#### 保存 installation

使用消息推送前，首先需要保存设备信息。

```java
BmobInstallation.getCurrentInstallation().save();
```

#### 自定义Installation表

开发者如果想要为设备信息表增加其他属性，则可以通过继承BmobInstallation类的方式来完成，用来定制更通用的推送。

举例：

```java
public class MyBmobInstallation extends BmobInstallation {

	/**  
	 * 用户id-这样可以将设备与用户之间进行绑定
	 */  
	private String uid;
	
	public MyBmobInstallation(Context context) {
		super(context);
	}

	public String getUid() {
		return uid;
	}

	public void setUid(String uid) {
		this.uid = uid;
	}
	
}
```

那么如何更新增加的`uid`字段的值呢？

**具体思路：先将当前设备查询出来，之后调用`update`方法更新该值**

示例如下：

```java
BmobQuery<MyBmobInstallation> query = new BmobQuery<MyBmobInstallation>();
query.addWhereEqualTo("installationId", BmobInstallation.getInstallationId(this));
query.findObjects(this, new FindListener<MyBmobInstallation>() {
	
	@Override
	public void onSuccess(List<MyBmobInstallation> object) {
		// TODO Auto-generated method stub
		if(object.size() > 0){
			MyBmobInstallation mbi = object.get(0);
			mbi.setUid("用户id");
			mbi.update(context,new UpdateListener() {
				
				@Override
				public void onSuccess() {
					// TODO Auto-generated method stub
					Log.i("bmob","设备信息更新成功");
				}
				
				@Override
				public void onFailure(int code, String msg) {
					// TODO Auto-generated method stub
					Log.i("bmob","设备信息更新失败:"+msg);
				}
			});
		}else{
		}
	}
	
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
	}
});

```

注：

**不能调用`save`方法保存，因为之前调用BmobInstallation.getCurrentInstallation(this).save()方法已经将该设备信息保存到设备表中。**

### 订阅频道和退订

#### 订阅频道

订阅频道可使用 **subscribe** 方法

```java
BmobInstallation installation = BmobInstallation.getCurrentInstallation();
installation.subscribe("Giants");
installation.subscribe("Mets");
installation.save();
```

注：`V3.4.3`版本的Bmob SDK对频道订阅增加去重操作，也就是说：即使你调用subscribe方法订阅了多个相同的频道，Bmob只会记录一个频道。


#### 退订频道

退订频道可使用 **unsubscribe** 方法

```java
BmobInstallation installation = BmobInstallation.getCurrentInstallation();
installation.unsubscribe("Giants");
installation.save();
```

### 广播推送消息
在客户端实现推送消息的功能，通过 **BmobPushManager** 对象来完成，比如给所有设备推送一条消息，如下：
```java
BmobPushManager bmobPush = new BmobPushManager();
bmobPush.pushMessageAll("Hello Bmob.");
```

### 组播推送消息
发送消息给订阅了Giants频道的用户
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
List<String> channels = new ArrayList<String>();
channels.add("Giants");
		
query.addWhereEqualTo("channels", channels);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
同时发送消息给多个频道时，可以将其他频道添加在channels中。

### 多播推送消息
#### 推送给不活跃的用户
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereLessThan("updatedAt", new BmobDate(new Date()));
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

#### 根据查询条件做推送
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("score", 80);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
请注意，where 条件查询的都是 installations 表。这里是假设 installations 表存储了 score 的Number属性，你可以像查询普通对象一样构造where查询

#### 根据平台做推送
给Android平台的终端推送
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceType", "android");
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
给IOS平台的终端推送
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceType", "ios");
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

#### 根据地理位置信息做推送
```java
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereWithinRadians("location", new BmobGeoPoint(112.934755, 24.52065), 1.0);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
上面的例子假设 installation 表中有个 location 属性是 GeoPoint 类型，我们就可以根据地理信息位置做推送。

### 点播推送消息
发送给Android单个客户端
```java
String installationId = "客户端installationId";
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("installationId", installationId);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
发送给iOS单个客户端
```java
String deviceToken = "客户端deviceToken";
BmobPushManager bmobPush = new BmobPushManager();
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceToken", deviceToken);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

