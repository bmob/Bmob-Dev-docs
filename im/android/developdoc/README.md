
## Android即时通讯服务

为解决旧版IM的一些问题，比如：`无法自定义消息类型，消息发送受限于BmobPushSDK，API设计不够合理规范且不易扩展，聊天消息出现接收延迟或丢失的问题等`，自`V2.0.0`开始,`BmobNewIM`采用全新架构（不兼容旧版IM），设计更加合理规范，API更加简单易用，扩展性强。**强烈建议开发者使用新版IM**。

新版IM具备以下特点：

- **与用户系统解耦**
  任何终端用户只需要提供一个唯一标识自己的`objectId`就可以加入聊天，不再局限于Bmob的用户系统
- **支持多账号登录、跨平台**
  支持`单个设备多个账号`登录，支持与iOS互通聊天
- **支持多种格式的富媒体消息**
  支持`文本`、`图片`、`音频`和`地理位置`等多种格式的富媒体消息，功能更加丰富
- **允许开发者自定义消息**
  支持开发者`自定义消息类型`，方便开发者扩展本业务逻辑相关的消息
- **API设计更加合理规范**
  全新的架构设计，API更加简单易用，较旧版进一步降低开发者使用成本

### Android旧版IM文档

为了和新版IM进行区分，我们将`V2.0.0`之前的版本统称为`旧版IM`，由于我们已不再对其进行维护，故将其开源，想要`浏览旧版IM源码、Demo及开发文档`，可移步我们的GitHub地址：[bmob-android-im-sdk](https://github.com/bmob/bmob-android-im-sdk)。


**以下介绍文档均指新版IM**

### Android-NewIM快速入门

请按照以下步骤完成NewIM的集成工作：

#### 下载NewIMSDK及官方Demo

下载[BmobNewIMSDK](http://www.bmob.cn/site/sdk#android_im_sdk_tab)：其包含NewIMSDK（相关libs包和values文件夹）以及官方Android studio工程；

也可移步Github地址[ bmob-newim-demo ](https://github.com/bodismile/bmob-newim-demo)Fork该项目。

注：

1、新版IMDemo只提供Android Studio版本。

2、解压之后的文件夹内容如下：

`libs(包含三个jar文件)`-这三个jar需要全部复制进工程中才可以使用新版IM

`values(bmob_im_notification_strings.xml)`-用于通知栏显示

`NewIM_V2.0.x_Demo`-官方发布的示例Demo,一个完整即时通讯Demo，包含陌生人聊天及基于好友之间的聊天（v2.0.4以后版本demo提供）。

***注：libs和values文件夹下面的都需要复制到应用中，否则将无法正常使用IM服务。***

#### 配置AndroidManifest.xml

1、 添加Bmob_APP_KEY：

```xml
   <meta-data
	    android:name="Bmob_APP_KEY"
	    android:value="Bmob平台的Application ID" />
```
***注：Bmob_APP_KEY 必须填写，否则无法正常使用IM服务。**

2、 添加权限

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

 3、 添加Service、receiver标签：

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
    android:name="cn.bmob.newim.core.service.BmobImService"
    android:process=":bmobcore" />
   //v2.0.4版本增加service-用于进程保活
  <service
    android:name="cn.bmob.newim.core.service.NotifyService"
    android:process=":bmobcore" />
  <service android:name="cn.bmob.newim.core.service.ReConnectService" />
  <service android:name="cn.bmob.newim.core.service.HeartBeatService" />

```

#### 注册消息接收器

- 如果你使用的是`NewIM_V2.0.2以后(包含v2.0.2)`的SDK版本,那么你需要自定义消息接收器继承自`BmobIMMessageHandler`来处理服务器发来的消息和离线消息。

```xml

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

别忘记在Application的onCreate方法中注册这个`DemoMessageHandler`：

```xml

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

- 如果你使用的SDK版本是`NewIM_V2.0.1`,那么你需要在应用中创建一个广播消息接收器，用于接收服务器发来的消息。

```xml

public class MessageReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(final Context context, Intent intent) {
        if(intent!=null){
            final MessageEvent event =(MessageEvent)intent.getSerializableExtra("event");
            //开发者可以在这里发应用通知
    }
}

```

同样，别忘记在`AndroidManifest.xml`中注册这个receiver

```xml
<receiver
    android:name="程序包名.MessageReceiver"
    android:enabled="true">
    <intent-filter>
        <action android:name="cn.bmob.im.action.MESSAGE"/>
    </intent-filter>
</receiver>

```

#### 初始化

在Application的onCreate方法中调用`BmobIM.init(context)`

```xml
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

1. 初始化方法包含了BmobSDK的初始化步骤，故无需再初始化BmobSDK
2. 在初始化的时候，最好做下判断：只有主进程运行的时候才开始初始化，避免资源浪费。

#### 服务器连接

- 连接服务器：

```xml
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

注：

调用`connect`方法，需要传入一个唯一的用户标示`clientId`，Demo使用的是Bmob的用户登录系统。

- 断开连接：

```xml
BmobIM.getInstance().disConnect();

```

注：调用`disConnect`方法，客户端会断开与服务器之间的连接，再次聊天需要重新调用`connect`方法完成与服务器之间的连接。

- 监听服务器连接状态

调用`setOnConnectStatusChangeListener`方法即可监听到当前长链接的连接状态

```java
 BmobIM.getInstance().setOnConnectStatusChangeListener(new ConnectStatusChangeListener() {
            @Override
            public void onChange(ConnectionStatus status) {
                Logger.i("" + status.getMsg());
            }
        });

```
### 会话

#### 创建会话

新版IM采用会话（`BmobIMConversation`）管理消息(`BmobIMMessage`)的方式，即消息的查询、发送和删除等操作均在指定会话下进行，因此需要获取指定会话信息并创建会话实例。

目前创建会话有两种创建方式：

1、 创建`暂态会话`-该会话只提供消息发送功能，不可使用其他查询，删除等API，`不会自动创建会话`到本地DB中。一般用于`自定义消息的发送`，比如，添加好友的请求，在对方还没有同意的情况下，你并不希望在自己的会话列表中显示该会话。

```java

//开启私聊会话，可设置会话是否保存到自己的本地会话表中
startPrivateConversation(BmobIMUserInfo info, boolean isTransient,ConversationListener listener)

参数：
info：与谁聊天，就填谁的信息
isTransient：true-不保存，false-保存

注：v2.0.4版本的NewIM开始提供此种方式创建暂态会话

```
2、创建`常态会话`-该会话提供消息查询、发送、删除等功能，`SDK内部自动创建该会话`。

```java

//开启私聊会话，默认会保存该会话到自己的本地会话表中
startPrivateConversation(BmobIMUserInfo info, ConversationListener listener)

```

示例如下：

```xml
//如果需要更新用户资料，开发者只需要传新的info进去就可以了
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

说明：

`BmobIMUserInfo`类，是用户信息类，有三个属性需要开发者关注下：userId(用户id，唯一)，name(用户名)，avatar（用户头像）。

#### 查询全部会话

```
BmobIM.getInstance().loadAllConversation()

```
#### 查询未读消息

- 查询指定会话下的未读消息数量：

```
BmobIM.getInstance().getUnReadCount(String conversationId)

```
- 查询全部会话的全部未读消息数量：

```
BmobIM.getInstance().getUnReadCount.getAllUnReadCount()

```

#### 删除指定会话

```
//提供两种方式删除会话
BmobIM.getInstance().deleteConversation(BmobIMConversation c);
BmobIM.getInstance().deleteConversation(String conversationId);

```

#### 清空会话

```

BmobIM.getInstance().clearAllConversation();

```

#### 更新会话标题、会话图标及用户信息

由于新版IM并不包含与用户有关的逻辑，只负责存储用户信息并对外提供更新等方法,用来操作本地的用户信息。

在与人单聊时，需要更新会话标题和会话图标及用户信息，可调用如下方法在`DemoMessageHandler`的全局消息接收器中进行统一更新。

```
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

### 消息

消息(`BmobIMMessage`)是所有消息的基类，新版IM目前支持的消息类型有如下：

- 文本消息：`BmobIMTextMessage`
- 文件消息：`BmobIMFileMessage`，由此衍生的有如下消息类型:
  - 图像消息：`BmobIMImageMessage`
  - 音频消息：`BmobIMAudioMessage`
  - 视频消息：`BmobIMVideoMessage`
- 地理位置消息：`BmobIMLocationMessage`

新版IM采用会话（`BmobIMConversation`）管理消息(`BmobIMMessage`)的方式，即消息的查询、发送和删除等操作均在指定会话下进行，因此需要通过以下两个步骤来获取指定会话信息并创建会话实例。

1、 开启私聊

```
//如果需要更新用户资料，开发者只需要传新的info进去就可以了
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

**注：如果不调用BmobIMConversation的`obtain`方法是无法控制消息发送等操作的，会报`client disconnect`的错误。**

#### 查询聊天记录

```
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

#### 删除聊天记录

删除消息不同于删除会话，会直接清空本地的消息记录数据。

```
//删除指定聊天消息
c.deleteMessage(BmobIMMessage msg)

//删除一条或多条聊天消息
c.deleteBatchMessage(List<BmobIMMessage> msgs)

//清空该会话下的聊天消息，允许保留会话（可选）
c.clearMessage(boolean isKeepConversion,MessageListener listener)

注：isKeepConversion 表示是否保留该会话消息。

```

#### 更新指定会话的所有消息为已读状态

可以在`ChatActivity`的聊天页面的`onDestory`方法中调用如下方法`更新该会话的的所有消息为已读状态`：

```
//更新此会话的所有消息为已读状态
c.updateLocalCache();

```

#### 消息发送

##### 文本消息

文本消息可以是纯文本，也可以是包含表情的文本消息，通过`BmobIMTextMessage`的`setContent`方法设置内容来构建`BmobIMTextMessage`实例，再调用`BmobIMConversation`的`sendMessage`方法发送。

```
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

##### 图像消息

图片可以是通过系统拍照或本地相册中获取的本地图片地址，也可以使用网络上某个有效的图片地址。然后构造一个`BmobIMImageMessage`对象，再调用`BmobIMConversation`的`sendMessage`方法发送。

###### 发送本地图片

使用系统拍照功能或从本地相册中获取到本地图片地址(`localPath`)，然后调用构造方法`BmobIMImageMessage（String localPath）`来创建`BmobIMImageMessage`实例。

```xml
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

###### 发送远程图片URL

例如，从微博或QQ中获取到某个图片地址，然后调用BmobIMImageMessage的`setRemoteUrl`方法设置远程图片URL来创建`BmobIMImageMessage`实例。

```
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
##### 语音消息

语音可以是通过录制音频得到的本地音频地址，也可以使用网络上某个有效的音频地址。然后构造一个`BmobIMAudioMessage`对象，再调用`BmobIMConversation`的`sendMessage`方法发送。

###### 发送本地音频文件：

```xml
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

###### 发送远程语音URL地址

同样的，语音消息也支持发送远程语音URL地址：

```
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

##### 地理位置消息

地理位置可以通过任意地图SDK获取到经纬度，详细地址等信息，然后调用`BmobIMLocationMessage(String address,double latitude,double longitude)`构造方法构建`BmobIMLocationMessage`实例，再调用`BmobIMConversation`的`sendMessage`方法发送。：

```xml
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

#### 消息接收

##### 自定义消息接收器

- 如果你使用的是`NewIM_V2.0.4以后(包含v2.0.4)`的SDK版本,那么不仅可以使用`BmobIMMessageHandler`方式来注册全局的消息接收器，还可以使用`MessageListHandler`为单个页面注册消息接收器，具体步骤如下：

1. 在`Activity/Fragment`中实现`MessageListHandler`接口；
2. 在`onResume`方法中添加页面消息监听器：`BmobIM.getInstance().addMessageListHandler(this)`；
3. 在`onPause`方法中移除页面消息监听器：`BmobIM.getInstance().removeMessageListHandler(this)`；
4. 在`MessageListHandler`接口的`onMessageReceive`方法中做相关的操作。

具体示例可查看NewIMDemo中的`ChatActivity`类：

```xml
@Override
public void onMessageReceive(List<MessageEvent> list) {
    //当注册页面消息监听时候，有消息（包含离线消息）到来时会回调该方法
    for (int i=0;i<list.size();i++){
		//do something...
    }
}

```

- 如果你使用的是`NewIM_V2.0.2以后(包含v2.0.2)`的SDK版本,那么只需要自定义消息接收器继承自`BmobIMMessageHandler`来处理服务器发来的消息和离线消息。

```xml

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

```xml

public class BmobIMApplication extends Application{

    @Override
    public void onCreate() {
        super.onCreate();
		//注册消息接收器
        BmobIM.registerDefaultMessageHandler(new DemoMessageHandler(this));
    }
}
```

- 如果你使用的SDK版本是`NewIM_V2.0.1`,那么你需要在应用中创建一个`BroadcastReceiver`广播消息接收器，用于接收服务器发来的消息。

```xml
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

```

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

##### 应用内消息接收

`V2.0.1`的SDK内部集成EventBus库（`V2.0.2`SDK内部不再集成EventBus，开发者可以自行使用新版EventBus）来进行应用内消息的分发，故在应用内需要接收消息的地方注册和解注册EventBus即可。

SDK内部有两种事件：`MessageEvent（聊天消息）`、`OfflineMessageEvent(离线消息)`。

1、注册EventBus

```
EventBus.getDefault().register(this);

```
2、解注册EventBus

```
EventBus.getDefault().unregister(this);

```

3、处理聊天消息

```
/**聊天消息接收事件
 * @param event
 */
public void onEventMainThread(MessageEvent event){
    //处理聊天消息
}

```

4、处理离线消息

```

/**离线消息接收事件
 * @param event
 */
public void onEventMainThread(OfflineMessageEvent event){
    //处理离线消息
}

```

##### 应用外通知栏提醒

SDK新增`BmobNotificationManager`类，并提供如下两个方法供开发者展示通知栏:

1. **多个用户的多条消息合并成一条通知：有XX个联系人发来了XX条消息**
                           
```xml
/**显示通知：多个用户的多条消息合并显示一条通知
 * @param event 某个消息事件：包含消息、会话及发送用户的信息
 * @param intent 跳转intent
 */
 BmobNotificationManager.getInstance(context).showNotification(MessageEvent event,Intent pendingIntent);

```
2. **自定义通知消息：始终只有一条通知，新消息覆盖旧消息**

```xml
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


#### 暂态消息

SDK在`BmobIMMessage`类中新增`isTransient`属性来标识该条消息是否自动保存到`聊天对象`的本地DB中。

- 设置为`true`,表明为暂态消息，那么这条消息`并不会保存到聊天对象的本地db中`，SDK只负责发送和接收。
- 设置为`false`,表明不为暂态消息，SDK会`自动保存该类型的消息到指定会话的数据库`中。

#### 自定义消息

##### 设置额外信息

有些时候，开发者需要在发送消息时携带一些额外信息，比如`发送方的设备类型、图片的拍摄地点或者音频的来源`等，那么开发者可以通过 `BmobIMExtraMessage.extraMap`属性来解决。

例如：

```
BmobIMAudioMessage audio =new BmobIMAudioMessage();
image.setRemoteUrl("远程音频地址");
//设置音频文件的来源
Map<String,Object> map =new HashMap<>();
map.put("from", "优酷");
audio.setExtraMap(map);
c.sendMessage(audio, listener);

```

**任何继承`BmobIMExtraMessage`类的消息均支持设置额外信息。**


##### 创建新的消息类型

如果设置额外信息无法满足开发者的需求，那么开发者也可以自定义自己的消息类型，步骤如下：

1. 继承自`BmobIMExtraMessage`类;
2. 重写`getMsgType`方法，填写自定义的消息类型;
3. 重写`isTransient`方法。

以下为添加好友请求的消息类：

```xml
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

自定义消息的发送如下：

```xml
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

### 用户管理

`NewIM`SDK只是`即时通讯的消息收发渠道,本身并不提供用户体系`。开发者可使用BmobSDK提供的用户管理方面功能，也可使用开发者自己的用户体系。

`NewIM`SDK内部会`自动创建本地用户表`,并对外提供方法供开发者调用来操作本地用户表。开发者只需要调用`updateUserInfo`方法即可更新本地用户信息。

#### BmobIMUserInfo介绍

`NewIM`SDK中用户的实体类为`BmobIMUserInfo`,其有四个属性，开发者只需要关心后三个即可：

- `id` 本地数据库用户表的id值 开发者无需关心
- `userId` `用户的id`，唯一标识（NewIMDemo中是用的是BmobUser的objectId）
- `name` `用户名` （NewIMDemo中是用的是BmobUser的username）
- `avatar` `用户头像`

#### 获取本地用户信息

```
//获取指定uid的用户信息
BmobIM.getInstance().getUserInfo(String uid)

```
#### 更新本地用户信息

使用如下方法更新本地用户信息：

```

//更新单一用户信息
BmobIM.getInstance().updateUserInfo(BmobIMUserInfo info)

//批量更新本地用户信息
BmobIM.getInstance().updateBatchUserInfo(List<BmobIMUserInfo> list)

```

以下两种情况需要更新用户信息：

1. `当注册或登录成功后，需要更新下当前用户的信息到本地数据库的用户表中`;
2. `当接收到某人(例如A)的消息时候，同样需要更新A的用户信息到本地用户表中，否则在会话界面将默认显示的是用户的userId，也就是Demo中的BmobUser的objectId值`。

### 好友管理

`NewIM`SDK中并`没有集成好友管理`相关的功能，为了方便开发者建立基于好友之间的聊天模式，在`v2.0.4`的`NewIMDemo`中使用BmobSDK新建了`Friend`表来增加了好友管理功能，包括`添加好友`、`删除好友`、`获取好友列表`、`发送添加好友的请求`及`同意某人的添加好友的请求`等。

```
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

#### 获取好友列表

以下摘自`UserModel(cn.bmob.imdemo.model)`类：

```
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


#### 删除好友

以下摘自`UserModel(cn.bmob.imdemo.model)`类：

```
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

#### 添加好友

Demo中创建了一个`NewFriend`的本地数据库类用来存储所有的添加好友请求

```
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
Demo中创建了一个`AddFriendMessage`类来展示如何发送自定义的添加好友请求的消息

```
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

```
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

##### 发送添加好友的请求

以下摘自`UserInfoActivity(cn.bmob.imdemo.ui)`类：

```
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
##### 发送同意添加好友的请求

以下摘自`NewFriendHolder(cn.bmob.imdemo.adapter)`类：

```
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

##### 接收并处理好友相关的请求

以下摘自`DemoMessageHandler(cn.bmob.imdemo)`类：

```
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

##### 添加到Friend表中

以下摘自`DemoMessageHandler(cn.bmob.imdemo)`类：

```
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

## iOS即时通讯服务

### 下载iOS_Demo

[点击这里下载demo（iOS）源码](https://github.com/pzee/BmobIM-Demo)

Bmob即时聊天demo包含了一个完整的即时通讯的App，功能包括：

1. 支持好友管理功能，包括添加好友、删除好友、获取好友列表，也可以与你已有的用户系统完全解耦；
2. 支持的消息类型：纯文本,语音,图片,自定义消息
3. 支持会话的本地化存储；



### iOS界面效果

#### iOS联系人页面

![](image/IMG_0613.png)


#### iOS会话页面

![](image/IMG_0614.png)

#### iOS聊天页面

![](image/IMG_0615.png)

#### iOS搜索联系人页面

![](image/IMG_0616.png)

#### iOS设置页面

![](image/IMG_0612.png)

### iOSIM快速入门

#### 下载安装BmobIMSDK
1. 通过官网下载新的BmobIMSDK
2. 新建项目将BmobIMSDK 复制到新的项目工程
3. 具体可以参照[Demo](https://github.com/pzee/BmobIM-Demo)

#### 配置相关服务
如果需要用到推送服务，所以需要在管理后台上传对应Bundle ID的p12文件，请勿加密，
详细详细请参照：http://docs.bmob.cn/iospush/index.html?menukey=otherdoc&key=iospush

#### 引入相关的库文件
如果项目中已包含BmobSDK数据服务SDK的话，可以不添加新的框架，如果没有则需添加SystemConfiguration.framework、CoreFoundation.framework、Foundation.framework、CFNetwork.framwork、CoreGraphics.framework、sqlite3.tbd

#### 初始化
在你的XCode工程中的AppDelegate.m文件中,引入相关的头文件， 填入申请的授权Key（SDK使用的是应用密钥里的Application ID），示例如下：

`#import <BmobIMSDK/BmobIMSDK.h>`

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    //这里使用的是BmobSDK提供的用户系统，所以需要初始化BmobSDK
    [Bmob registerWithAppKey:@"Application ID"];
    
    self.sharedIM = [BmobIM sharedBmobIM];
    
    [self.sharedIM registerWithAppKey:@"Application ID"];
    
    [UIApplication sharedApplication].statusBarStyle = UIStatusBarStyleLightContent;
    
    BmobUser *user = [BmobUser getCurrentUser];
    //如果有用户了，不需要推送服务来推送消息的话，可以直接连接服务器
    if (user) {
        self.userId = user.objectId;
        [self connectToServer];
    }else{
    	//如果用户还未登录，则监听对应的通知，再进行处理
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(userLogin:) name:@"Login" object:nil];
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(userLogout:) name:@"Logout" object:nil];
    }
    
    self.sharedIM.delegate = self;
    
    
    
    
    return YES;
}
```

如果需要推送服务的话，可以在其相关代理那里设置调用 [self.sharedIM setupDeviceToken:@"xxxx"]的方法后，在连接服务器

```
-(void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(nonnull NSError *)error{
    BmobUser *user = [BmobUser getCurrentUser];
    if (user) {
        [self connectToServer];
    }
    
}
```

```
-(void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken{
		BmobUser *user = [BmobUser getCurrentUser];
	if (user) {
		//开发者自己将deviceToken转成字符串
		NSString *string = [[NSString alloc] initWithData:deviceToken encoding:NSUTF8StringEncoding];
		self.token = [[[string stringByReplacingOccurrencesOfString:@" " withString:@""] stringByReplacingOccurrencesOfString:@"<" withString:@""] stringByReplacingOccurrencesOfString:@">" withString:@""];
		[self connectToServer];   		
	}
}
```

登录的对应处理是

```
-(void)userLogin:(NSNotification *)noti{
    NSString *userId = noti.object;
    self.userId = userId;
    [self connectToServer];
}
```

退出登录的时候需要断开连接

```
-(void)userLogout:(NSNotification *)noti{
    [self.sharedIM disconnect];
}
```

连接服务器

```
-(void)connectToServer{
    [self.sharedIM setupBelongId:self.userId];
    [self.sharedIM setupDeviceToken:self.token];
    [self.sharedIM connect];
}
```

在应用进入前台或者后台的时候可以重新进行连接或者断开连接

进入前台

```
- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.
   
    if (self.userId && self.userId.length > 0) {
        [self connectToServer];
    }
}

```

进入后台

```
- (void)applicationDidEnterBackground:(UIApplication *)application {
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
    if ([self.sharedIM isConnected]) {
        [self.sharedIM disconnect];
    }
```

**需要说明的是，连接服务器建立一次即可，开发者自己控制连接服务器的时机。建立连接之前必须设置appKey和belongId，不然会抛出异常**

#### 注册推送

可以在函数 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 添加推送的相关代码

```

   if (IS_iOS8) {
        //iOS8推送
        UIMutableUserNotificationCategory*categorys = [[UIMutableUserNotificationCategory alloc]init];
        categorys.identifier=@"BmobIMDemo";
        UIUserNotificationSettings*userNotifiSetting = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeAlert|UIUserNotificationTypeBadge|UIUserNotificationTypeSound)
                                                                                         categories:[NSSet setWithObjects:categorys,nil]];
        [[UIApplication sharedApplication]registerUserNotificationSettings:userNotifiSetting];
        [[UIApplication sharedApplication]registerForRemoteNotifications];
    }else{
        [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    }

```

#### 接收消息

在初始化的部分，我们已经设置了self.sharedIM的delegate，我们只需要在对应的代理函数那里处理相关的应用逻辑即可。
在应用连接服务器的时候，SDK会主动的获取离线消息，并保存到本地数据库里面。在这里，开发者可能需要知道哪些人发来了消息，并且去下载相关人物信息。

```
-(void)didGetOfflineMessagesWithIM:(BmobIM *)im{
    //获取哪些人的消息还未读
    NSArray *objectIds = [self.sharedIM allConversationUsersIds];
    if (objectIds && objectIds.count > 0) {
        //Demo里面的方法去查找服务器相关人物的信息
        [UserService loadUsersWithUserIds:objectIds completion:^(NSArray *array, NSError *error) {
            if (array && array.count > 0) {
                //保存到本地数据库
                [self.sharedIM saveUserInfos:array];
                //发新用户的通知
                [[NSNotificationCenter defaultCenter] postNotificationName:kNewMessageFromer object:nil];
            }
        }];
    }
}
```

另一方面，已经连接到服务器上了，就可以收到别人发送过来的消息，这是需要在另一个方法处理

```
-(void)didRecieveMessage:(BmobIMMessage *)message withIM:(BmobIM *)im{
	//查看本地有无这个用户的信息
	BmobIMUserInfo *userInfo = [self.sharedIM userInfoWithUserId:message.fromId];
	if (!userInfo) {
		  //如果没有则去下载
	    [UserService loadUserWithUserId:message.fromId completion:^(BmobIMUserInfo *result, NSError *error) {
	        if (result) {
	        	//保存到本地数据库
	            [self.sharedIM saveUserInfo:result];
	            //发新用户的通知
	            [[NSNotificationCenter defaultCenter] postNotificationName:kNewMessageFromer object:nil];
	        }
	        //发接收到新信息的通知
	        [[NSNotificationCenter defaultCenter] postNotificationName:kNewMessagesNotifacation object:message];
	    }];
	}else{
		  //发接收到新信息的通知
	    [[NSNotificationCenter defaultCenter] postNotificationName:kNewMessagesNotifacation object:message];
	}
	}
```



#### 用户管理

##### 注册

当用户还未进行注册的时候可以通过调用方法来进行简单的注册

```
	BmobUser *user = [[BmobUser alloc] init];
	user.username = self.usernameTextField.text;
	user.password = self.passwordTextField.text;
	    
	    
	[user signUpInBackgroundWithBlock:^(BOOL isSuccessful, NSError *error) {
	    if (isSuccessful) {
	        [[NSNotificationCenter defaultCenter] postNotificationName:@"Login" object:user.objectId];
	        [self dismissViewControllerAnimated:YES completion:nil];
	    }else{
	        [self showInfomation:error.description];
	    }
	}];
```

##### 登录
用户已经注册了，想要在此应用登录的话，可以直接调用登录的方法

```
	[self showLoading];
	[BmobUser loginWithUsernameInBackground:self.usernameTextField.text password:self.passwordTextField.text block:^(BmobUser *user, NSError *error) {
	    if (user) {
	        [self hideLoading];
	        [[NSNotificationCenter defaultCenter] postNotificationName:@"Login" object:user.objectId];
	        [self dismissViewControllerAnimated:YES completion:nil];
	    }else{
	        [self showInfomation:error.description];
	    }
	}];
```

##### 获取好友列表

```
-(void)loadUserFriends{
    [UserService friendsWithCompletion:^(NSArray *array, NSError *error) {
        if (error) {
            [self showInfomation:error.localizedDescription];
        }else{
            BmobUser *loginUser = [BmobUser getCurrentUser];
            NSMutableArray *result  = [NSMutableArray array];
            for (BmobObject *obj in array) {
               
                BmobUser *friend = nil;
                if ([[(BmobUser *)[obj objectForKey:@"user"] objectId] isEqualToString:loginUser.objectId]) {
                    friend = [obj objectForKey:@"friendUser"];
                }else{
                    friend = [obj objectForKey:@"user"];
                }
                BmobIMUserInfo *info = [BmobIMUserInfo userInfoWithBmobUser:friend];
                
                [result addObject:info];
            }
            if (result && result.count > 0) {
                [self.userArray setArray:result];
                [self.tableView reloadData];
                
            }
            
        }
    }];
}
```

##### 添加好友

```
-(void)addFriend{
    [UserService addFriendNoticeWithUserId:self.userInfo.userId completion:^(BOOL isSuccessful, NSError *error) {
        if (error) {
            [self showInfomation:error.localizedDescription];
        }else{
            [self showInfomation:@"已发送添加好友请求"];
        }
    }];
}
```

#### 消息发送

通过IMSDK，用户可以与陌生人聊天，也可以与好友聊天，这个由开发者自由控制。当需要发起聊天的时候，需要建立起一个BmobIMConversation对象来进行管理，SDK提供了方法来快速构建BmobIMConversation对象。

```
//
BmobIMConversation *conversation = [BmobIMConversation conversationWithId:self.userInfo.userId conversationType:BmobIMConversationTypeSingle];
conversation.conversationTitle = self.userInfo.name;
```
##### 查看聊天记录

进入聊天页面是，开发者可能需要让用户查看最近的聊天记录，这个时候可以通过BmobIMConversation 类提供的

`-(NSArray *)queryMessagesWithMessage:(BmobIMMessage *)message  limit:(int)limit;`来处理这个需求。

例如，加载第一页数据的时候，只需要将message设置为nil，即可获取到

```
-(void)loadMessageRecords{
	NSArray *array = [self.conversation queryMessagesWithMessage:nil limit:10];
	    
	if (array && array.count > 0) {
		//排序
		NSArray *result = [array sortedArrayUsingComparator:^NSComparisonResult(BmobIMMessage *obj1, BmobIMMessage *obj2) {
			if (obj1.updatedTime > obj2.updatedTime) {
			    return NSOrderedDescending;
			}else if(obj1.updatedTime <  obj2.updatedTime) {
			    return NSOrderedAscending;
			}else{
			    return NSOrderedSame;
			}
		    
		}];
		[self.messagesArray setArray:result];
		[self.tableView reloadData];
		    
		[self.tableView scrollToRowAtIndexPath:[NSIndexPath indexPathForRow:self.messagesArray.count-1 inSection:0] atScrollPosition:UITableViewScrollPositionBottom animated:YES];
	}
}
```

加载之前的历史消息记录，可以通过将message参数设置为时间为最后一条消息即可

```
-(void)loadMoreRecords{
    if (!self.finished) {
        self.page ++;
        [self.freshControl beginRefreshing];
        
        if (self.messagesArray.count <= 0) {
            [self.freshControl endRefreshing];
            return;
        }
        BmobIMMessage *msg = [self.messagesArray firstObject];
        
        NSArray *array = [self.conversation queryMessagesWithMessage:msg limit:10];
        
        if (array && array.count > 0) {
            NSMutableArray *messages = [NSMutableArray arrayWithArray:self.messagesArray];
            [messages addObjectsFromArray:array];
            //排序
            NSArray *result = [messages sortedArrayUsingComparator:^NSComparisonResult(BmobIMMessage *obj1, BmobIMMessage *obj2) {
                if (obj1.updatedTime > obj2.updatedTime) {
                    return NSOrderedDescending;
                }else if(obj1.updatedTime <  obj2.updatedTime) {
                    return NSOrderedAscending;
                }else{
                    return NSOrderedSame;
                }
                
            }];
            [self.messagesArray setArray:result];
            [self.tableView reloadData];
        }else{
            self.finished = YES;
            [self showInfomation:@"没有更多的历史消息"];
        }
        
    }else{
        [self showInfomation:@"没有更多的历史消息"];
    }
    
    [self.freshControl endRefreshing];
}
```

##### 发送消息

**发送文本消息**

```
-(void)sendTextWithTextField:(UITextField *)textField{
    if (textField.text.length == 0) {
        [self showInfomation:@"请输入内容"];
    }else{
        //创建BmobIMTextMessage对象
        BmobIMTextMessage *message = [BmobIMTextMessage messageWithText:textField.text attributes:nil];
		 //聊天类型设置为单聊
        message.conversationType =  BmobIMConversationTypeSingle;
        message.createdTime = (uint64_t)([[NSDate date] timeIntervalSince1970] * 1000);
        message.updatedTime = message.createdTime;
        [self.messagesArray addObject:message];
        [self.tableView reloadData];
        self.bottomView.textField.text = nil;
        [self.tableView scrollToRowAtIndexPath:[NSIndexPath indexPathForRow:self.messagesArray.count-1 inSection:0] atScrollPosition:UITableViewScrollPositionBottom animated:YES];
        __weak typeof(self)weakSelf = self;
        [self.conversation sendMessage:message completion:^(BOOL isSuccessful, NSError *error) {
            [weakSelf.tableView reloadRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:self.messagesArray.count-1 inSection:0]] withRowAnimation:UITableViewRowAnimationFade];
        
        }];
        
    }
}
```

**发送图片**

发送图片消息的流程是通过把拍照或者图片的内容，转成Data，然后通过Bmob文件上传接口来上传文件，获取到相关的信息（文件地址之类的），再构造对应的BmobIMImageMessage对象来发送消息。

Demo封装了一个直接传入image就能发送图片的方法

```
+(void)uploadImage:(UIImage *)image
        completion:(uploadBlock)block
          progress:(BmobProgressBlock)progressBlock;
```
然后可以直接调用方法来发送图片，并在当前页显示出来

```
[MessageService uploadImage:resizeImage completion:^(BmobIMImageMessage *message, NSError *error) {
        if (!error) {
            [self.messagesArray addObject:message];
            [self scrollToBottom];
             __weak typeof(self)weakSelf = self;
            
            [self.conversation sendMessage:message completion:^(BOOL isSuccessful, NSError *error) {
                [weakSelf reloadLastRow];
            }];
        }else{
            [self showInfomation:error.localizedDescription];
        }
    } progress:^(CGFloat progress) {
        [self showProgress:progress];
    }];

```

**发送语音文件**

发送语音消息的流程是把录音下载的Data转成AMR格式，保存在本地，然后通过Bmob文件上传接口来上传文件，获取到相关的信息（文件地址之类的），再构造对应的BmobIMAudioMessage对象来发送消息。

Demo封装了一个直接传入NSData就能发送语音文件的方法

```
+(void)uploadAudio:(NSData *)data
                          duration:(CGFloat)duration
                        completion:(uploadBlock)block
                          progress:(BmobProgressBlock)progressBlock
```

直接调用这个方法就能发送语音文件

```
[MessageService uploadAudio:data
                           duration:duration
                         completion:^(BmobIMAudioMessage *message, NSError *error) {
                             if (!error) {
                               
                                 [self.messagesArray addObject:message];
                                 [self scrollToBottom];
                                 __weak typeof(self)weakSelf = self;
                                 [self.conversation sendMessage:message completion:^(BOOL isSuccessful, NSError *error) {
                                     [weakSelf reloadLastRow];
                                 }];
                             }
                         } progress:nil];
```

**发送其他文件消息**

当开发者需要发送其他类型的文件消息的时候，先要继承BmobIMFileMessage类，通过BmobSDK提供的文件上传接口，把文件上传至服务器上，获取到url，再去创建对应的对象。
这里的流程可以参照发送图片，发送语音的相关源代码。当然了，开发者也必须指定其msgType属性，好让应用可以根据这个属性来显示相应的视图。


**发送位置**

```
BmobIMLocationMessage *message = [BmobIMLocationMessage messageWithAddress:@"广州大学城" attributes:@{KEY_METADATA:@{KEY_LATITUDE:@(23.039),KEY_LONGITUDE:@(113.388)}}];
    message.conversationType =  BmobIMConversationTypeSingle;
    message.createdTime = (uint64_t)([[NSDate date] timeIntervalSince1970] * 1000);
    message.updatedTime = message.createdTime;
    [self.messagesArray addObject:message];
    [self scrollToBottom];
    
    __weak typeof(self)weakSelf = self;
   
    [self.conversation sendMessage:message completion:^(BOOL isSuccessful, NSError *error) {
        [weakSelf reloadLastRow];
    }];
```

**发送自定义消息**

开发者也可以发送自定义消息(需继承BmobIMMessage)，例如添加好友的请求，当前聊天对象正在输入之类的消息，这种暂态的消息是不需要存储在数据库的，需要设置extra{KEY_IS_TRANSIENT:@(YES),...}。其他额外信息开发者可以自由设置

例如发送添加好友通知，这种消息是暂时的，不需要保存到数据库里面，可以这样构造

```
    BmobIMMessage *message = [[BmobIMMessage alloc] init];
    message.msgType = @"notice";
    message.conversationType = BmobIMConversationTypeSingle;
    message.extra = @{KEY_IS_TRANSIENT:@(YES)};
    message.content = @"添加好友";
    [self.conversation sendMessage:message completion:^(BOOL isSuccessful, NSError *error) {
        NSLog(@"error %@",error.localizedDescription);
    }];
```
##### 接收消息

在上一个大章节 接收消息 那里，我们已经把接收到的消息用通知的形式广播出来，我们只需要在聊天页面添加观察者来监听通知就行了。
在Demo里在函数`- (void)viewDidLoad `

```
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(receiveMessage:) name:kNewMessagesNotifacation object:nil];
```

在`-(void)receiveMessage:(NSNotification *)noti`进行对应的处理

```
-(void)receiveMessage:(NSNotification *)noti{
    BmobIMMessage *message = noti.object;
    //如果是消息来源是当前聊天用户，就将其加载到内存里并显示出来
    if ([message.fromId isEqualToString:self.conversation.conversationId]) {
        [self.messagesArray addObject:message];
        [self.tableView reloadData];
        [self.tableView scrollToRowAtIndexPath:[NSIndexPath indexPathForRow:self.messagesArray.count-1 inSection:0] atScrollPosition:UITableViewScrollPositionBottom animated:YES];
    }
    
}
```

