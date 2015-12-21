#Android SDK 开发指南

如果您还没有安装 Bmob SDK ，请前往快速入门指南，让我们的SDK已嵌入到您的项目中并在Eclipse中运行。
## 简介
Bmob平台为您的移动应用提供了一个完整的后端解决方案。我们的目标是完全消除开发者需要编写服务器代码或维护服务器的操作。 我们提供的SDK开发包也是非常轻量级的，目的是让开发者做到最小的配置和使用最简单的方式来使用Bmob平台提供的服务。

## 应用程序
在Bmob平台注册后，您创建的每个应用程序都有其自己的应用程序ID,在您的应用程序中将凭此ID进行Bmob SDK的使用。您的Bmob账户可以创建容纳多个应用程序，这是非常方便和有用的。即使您只有一个应用程序，也可以不是不同的版本进行测试和生产。

## 安装Bmob SDK
一、在你的项目根目录下创建"libs"目录，将下载的BmobSDK文件放入该目录下。

二、在您的应用程序中添加相应的权限：
```xml
< uses-permission android:name="android.permission.INTERNET" />
< uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
< uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
< uses-permission android:name="android.permission.READ_PHONE_STATE"/>
< uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
< uses-permission android:name="android.permission.READ_LOGS"/>

```
```xml
< ?xml version="1.0" encoding="utf-8"?>
    < manifest xmlns:android="http://schemas.android.com/apk/res/android"
    	package="cn.bmob.example"
    	android:versionCode="1"
    	android:versionName="1.0" >

    < uses-sdk android:minSdkVersion="8" android:targetSdkVersion="17" />

    < uses-permission android:name="android.permission.INTERNET" />
    < uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    < uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    < uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    < uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    < uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    < uses-permission android:name="android.permission.READ_LOGS" />

    < application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        < activity
            android:name="cn.bmob.example.MainActivity"
            android:screenOrientation="portrait"
            android:label="@string/app_name" >
            
                < action android:name="android.intent.action.MAIN" />
                < category android:name="android.intent.category.LAUNCHER" />
            
        
        < activity
            android:name=".CreateActivity"
            android:screenOrientation="portrait">
        < activity
            android:name=".DeleteActivity"
            android:screenOrientation="portrait">
        < activity
            android:name=".UpdateActivity"
            android:screenOrientation="portrait">
        < activity
            android:name=".FindActivity"
            android:screenOrientation="portrait">
    < /application>
< /manifest>
```
三、在你的应用程序启动的Activity的onCreate()方法中初始化Bmob功能。代码如下所示：
```java
package com.bomb.example;
import cn.bmob.Bmob;
import android.app.Activity;
import android.os.Bundle;
public class MainActivity extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
	     // TODO Auto-generated method stub
	    super.onCreate(savedInstanceState);
	     // 初始化 Bmob SDK
	    // 使用时请将第二个参数Application ID替换成你在Bmob服务器端创建的Application ID
	    Bmob.initialize(this, "Your Application ID");
	}
}
```
## 对象
Bmob存储的数据是建立在BmobObject基础上的，所以任何要保存的数据对象必须继承自BmobObject。例如，游戏中可能会用到的分数对象GameScore,它可能包含score、playerName、cheatMode等属性。
```java
public class GameScore extends BmobObject{
	private String playerName;
	private Integer score;
	private Boolean cheatMode;
        private BmobFile pic;
		
	public String getPlayerName() {
		return playerName;
	}
	public void setPlayerName(String playerName) {
		this.playerName = playerName;
	}
	public Integer getScore() {
		return score;
	}
	public void setScore(Integer score) {
		this.score = score;
	}
	public Boolean getCheatMode() {
		return cheatMode;
	}
	public void setCheatMode(Boolean cheatMode) {
		this.cheatMode = cheatMode;
	}
        public BmobFile getPic() {
		return pic;
	}
	public void setPic(BmobFile pic) {
		this.pic = pic;
	}
}
```
### 保存对象
例如，您现在要保存一条游戏分数的记录，可以这样做：
```java
GameScore gameScore = new GameScore();
gameScore.setPlayerName("Barbie");
gameScore.setScore(89);
gameScore.setCheatMode(false);
gameScore.insertObject(mContext, new InsertListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		// 保存成功
	}
		
	@Override
	public void onFailure(String arg0) {
		// TODO Auto-generated method stub
		// 保存失败
	}
});
```
运行完以上代码后，数据即可保存到服务器端。为了确认数据是否真的已经保存成功，你可以在Bmob服务器端你的应用程序的数据浏览项目中查看。你应该看到类似这样的结果：
```java
objectId: "0c6db13c", score: 89, playerName: "Barbie", cheatMode: false,createdAt:"2013-09-27 10:32:54", updatedAt:"2013-09-27 10:32:54"
```
这里需要注意几点：
    
1. 在运行以上代码时，如果服务器端您创建的应用程序中已经存在GameScore数据表和相应的score、playerName、cheatMode字段，那么您此时添加的数据和数据类型也应该和服务器端的表结构一致，否则将保存数据失败。
    
2. 如果服务器端不存在GameScore数据表，那么Bmob将根据您第一次(也就是运行的以上代码)保存的GameSocre对象在服务器为您创建此数据表和插入相应数据。
    
3. 每个BmobObject对象有几个默认的键(数据列)是不需要开发者指定的，`objectId`是为每个保存成功数据的唯一标识符。`createdAt`和`updatedAt`代表每个对象(每条数据)在服务器上创建和最后修改的时间。这些键(数据列)的创建和数据内容是由服务器端来完成的。

### 更新对象
更新一个对象也是非常简单。例如：
```java
GameScore gameScore = new GameScore();
gameScore.setScore(77);
gameScore.updateObject(this, "0c6db13c", new UpdateListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("更新成功：");
	}
		
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("更新失败："+msg);
	}
});
```
### 删除对象
从服务器删除对象：
```java
GameScore gameScore = new GameScore();
gameScore.setObjectId("dd8e6aff28");
gameScore.deleteObject(this, new DeleteListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("删除成功");
	}
		
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("删除失败："+msg);
	}
});
```
### 数据类型
目前为止，我们支持的数据类型有String、Int、Boolean、以及BmobObject对象类型。同时Bmob也支持java.util.Date、byte[]、JSONObject、JSONArray数据类型。 您可以在JSONArray对象中嵌套JSONObject对象存储在一个BmobObject中。

我们不建议存储较大的二进制数据，如图像或文件不应使用BmobObject的byte[]字段类型。BmobObjects 的大小不应超过1280字节。如果需要存储较大的文件类型如图像、文件、音乐,可以使用BmobFile对象来存储，具体使用方法可见BmobFile指南部分。

关于处理数据的更多信息，可查看开发指南的[数据安全][1]部分。

## 查询

### 基本查询

如果在你知道某条数据的objectId的情况下，可以使用BmobQuery检索到一个完整的BmobObject：
```java
BmobQuery<GameScore> bmobQuery = new BmobQuery<GameScore>();
bmobQuery.getObject(this, "a203eba875", new GetListener<GameScore>() {
		
	@Override
	public void onSuccess(GameScore object) {
		// TODO Auto-generated method stub
		toast("查询成功：");
	}

	@Override
	public void onFailure(String arg0) {
		// TODO Auto-generated method stub
		toast("查询失败："+arg0);
	}
		
});
```
同时BmobQuery还提供了不同的方法来查询不同条件的数据。

使用BmobQuery时，先创建一个BmobQuery对象，然后添加不同的条件，使用`findObjects`方法结合`FindListener`回调接口来查询与条件匹配的BmobObjects数据。例如，查询指定人员的信息，使用`addWhereEqualTo`方法来添加条件值：
```java
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.addWhereEqualTo("playerName", "Barbie");
query.findObjects(this, new FindListener<Person>() {
		@Override
		public void onSuccess(List<GameScore> object) {
			// TODO Auto-generated method stub
			toast("查询成功：共"+object.size()+"条数据。");
			for (GameScore gameScore : object) {
				Log.d(TAG, "~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ");
				Log.d(TAG, "ObjectId = "+gameScore.getObjectId());
				Log.d(TAG, "PlayerName = "+gameScore.getPlayerName());
				Log.d(TAG, "Score = "+gameScore.getScore());
				Log.d(TAG, "CheatMode = "+gameScore.getCheatMode());
				Log.d(TAG, "CreatedAt = "+gameScore.getCreatedAt());
				Log.d(TAG, "UpdatedAt = "+gameScore.getUpdatedAt());
			}
		}
		@Override
		public void onError(String msg) {
			// TODO Auto-generated method stub
			toast("查询失败："+msg);
		}
});
```
### 查询条件
如果要过滤掉特定键的值可以使用`addWhereNotEqualTo`方法。比如需要查询playerName不等于“Barbie”的数据时可以这样写：
```java
query.addWhereNotEqualTo("playerName", "Barbie");
```
当然，你可以在你的查询操作中添加多个约束条件，来查询符合要求的数据。
```java
query.addWhereNotEqualTo("playerName", "Barbie");     //名字不等于Barbie
query.addWhereGreaterThan("score", 60);      //条件：分数大于60岁
```
有些时候，在数据比较多的情况下，您希望只查询符合要求的多少条数据即可，这时可以使用`setLimit`方法来限制查询结果的数据条数。默认情况下Limit的值为100.
```java
query.setLimit(10); // 限制最多10个结果
```
在数据较多的情况下，分页显示数据是比较合理的解决办法，`setKip`方法可以做到跳过首次查询的多少条数据来实现分页的功能。
```java
query.setSkip(10); // 忽略前10个
```
对应数据的排序，如数字或字符串，你可以使用升序或降序的方式来控制查询数据的结果顺序：
```java
// 根据score字段升序显示数据
query.order("score");
// 根据score字段降序显示数据
query.order("-score");
// 多个排序字段可以用（，）号分隔
query.order("-score,createdAt");
```
各种不同的比较查询：
```java
// 分数 < 50
query.addWhereLessThan("score", 50);
//分数 <= 50
query.addWhereLessThanOrEqualTo("score", 50);
//分数 > 50
query.addWhereGreaterThan("score", 50);
//分数 >= 50
query.addWhereGreaterThanOrEqualTo("score", 50);
```
如果你想查询匹配几个不同值的数据，如：要查询“Barbie”,“Joe”,“Julia”三个人的成绩时，你可以使用`addWhereContainedIn`方法来实现。
```java
String[] names = {"Barbie", "Joe", "Julia"};
query.addWhereContainedIn("playerName", Arrays.asList(names));
```
相反，你想查询排除“Barbie”,“Joe”,“Julia”这三个人的其他同学的信息，你可以使用`addWhereNotContainedIn`方法来实现。
```java
String[] names = {"Barbie", "Joe", "Julia"};
query.addWhereNotContainedIn("playerName", Arrays.asList(names));
```
### 查询条件
查询包含字符串的值，有几种方法。你可以使用任何正确的正则表达式来检索相匹配的值，使用`addWhereMatches`方法：
```java
// 比较name字段的值是以大写字母和数字开头
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.addWhereMatches("name", "^[A-Z]\\d");
query.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
                // 查询结果，符合条件的值，如“A4zhang“
		toast("查询成功：共"+object.size()+"条数据。");
	}
	@Override
	public void onError(String msg) {
		// TODO Auto-generated method stub
		toast("查询失败："+msg);
	}
});
```
查询字符串中包含“XX“内容，可用如下方法：
```java
// 查询playerName字段的值中包含“小“字的数据
BmobQuery<GameSauce> query = new BmobQuery<GameSauce>();
query.addWhereContains("playerName", "小");
```
查询字符串是以“XX“开始的数据，可使用如下方法：
```java
// 查询playerName字段的值是以“张“字开头的数据
BmobQuery<GameSauce> query = new BmobQuery<GameSauce>();
query.addWhereStartsWith("playerName", "张");
```
查询字符串是以“XX“结束的数据，可使用如下方法：
```java
// 查询playerName字段的值是以“西“字结尾的数据
BmobQuery<GameSauce> query = new BmobQuery<GameSauce>();
query.addWhereEndsWith("playerName", "西");
```
### 查询对象个数
如果你只是想统计有多少个对象满足查询，你并不需要获取所有匹配的对象，可以直接使用`count`替代`findObjects`。例如，查询一个特定玩家玩了多少场游戏：
```java
BmobQuery<GameSauce> query = new BmobQuery<GameSauce>();
query.addWhereEqualTo("playerName", "Barbie");
query.count(this, GameSauce.class, new CountListener() {
	@Override
	public void onSuccess(int count) {
		// TODO Auto-generated method stub
		toast("Barbie has played" + count + "games");
	}
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("count failure："+msg);
	}
});
```
对于超过1000个对象的查询，这种计数请求可能会出现请求超时。它可能会遇到超时错误或者返回一个近似的值。因此，请仔细设计你的应用架构来避免依赖这种计数查询。

## 文件

### 文件对象
BmobFile可以让你的应用程序将文件存储到服务器中，比如常见的文件类型图像文件、影像文件、音乐文件和任何其他二进制数据都可以使用。（单个文件大小不可超过10M）

在这个例子中，我们将一个图片文件保存到服务器端：
```java
String picPath = "sdcard/temp.jpg";
final BmobFile bmobFile = new BmobFile(GameSauce.class, new File(picPath));
bmobFile.upload(this, new UploadFileListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("上传文件成功:"+bmobFile.getFileUrl());
                // 文件上传成功后，可将文件对象保存到数据表中
		final GameSauce gs = new GameSauce();
		gs.setPlayerName("rohs");
		gs.setPic(bmobFile);
		gs.insertObject(MainActivity.this, new InsertListener() {
			@Override
			public void onSuccess() {
				// TODO Auto-generated method stub
				toast("创建数据成功："+gs.getObjectId());
			}
			@Override
			public void onFailure(String msg) {
				// TODO Auto-generated method stub
				toast("创建数据失败："+msg);
			}
		});
	}
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("上传文件失败："+msg);
	}
});
```
查询数据时获取文件对象
```java
bmobQuery.findObjects(this, new FindListener<GameSauce>() {
	@Override
	public void onSuccess(List<GameSauce> object) {
		// TODO Auto-generated method stub
		toast("查询成功：共"+object.size()+"条数据。");
		for (Person person : object) {
		       if(person.getPic() != null){
                                  //文件名称
                                person.getPic().getFilename();
                                //文件下载地址
				person.getPic().getFileUrl();
			}
		}
	}
	@Override
	public void onError(String msg) {
		// TODO Auto-generated method stub
		toast("查询失败："+msg);
	}
});
```

## 用户

用户是一个应用程序的核心。对于个人开发者来说，能够让自己的应用程序积累到多的用户，就能给自己带来更多的创作动力。因此Bmob提供了一个专门的用户类，BmobUser来自动处理用户账户管理所需的功能。

有了这个类，您就可以在您的应用程序中添加用户账户功能。

BmobUser是一个BmobObject的子类，它继承了BmobObject所有的方法具有BmobObject相同的功能。不同的是，BmobUser增加了一些特定的关于用户账户相关的功能。

### 属性
BmobUser除了从BmobObject继承的属性外，还有几个特定的属性：
username: 用户的用户名`（必需）`。
password: 用户的密码`（必需）`。
email: 用户的电子邮件地址`（可选）`。

### 注册
你的应用程序会做的第一件事可能是要求用户注册。下面的代码是一个典型的注册过程：
```java
BmobUser bu = new BmobUser();
bu.setUsername("sendi");
bu.setPassword("123456");
bu.setEmail("sendi@163.com");
bu.signUp(this, new InsertListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("注册成功:");
	}
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("注册失败:"+msg);
	}
});
```
在注册过程中，服务器会进行注册用户信息的检查，以确保注册的用户名和电子邮件地址是独一无二的。此外，对于用户的密码，你可以在应用程序中进行相应的加密处理后提交。

如果注册不成功，你可以查看返回的错误对象。最有可能的情况是，用户名或电子邮件已经被另一个用户注册。这种情况您可以提示用户，要求他们尝试使用不同的用户名进行注册。

你也可以要求用户使用Email做为用户名注册，这样做的好处是，你在提交信息的时候可以将输入的“用户名“默认设置为用户的Email地址，以后在用户忘记密码的情况下可以使用Bmob提供重置密码功能。

### 登录
当用户注册成功后，您需要让他们以后能够登录到他们的账户使用应用。要做到这一点，你可以使用BmobUser类的login方法。
```java
BmobUser bu2 = new BmobUser();
bu2.setUsername("lucky");
bu2.setPassword("123456");
bu2.login(this, new InsertListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("登陆成功:");
	}
	@Override
	public void onFailure(String msg) {
		// TODO Auto-generated method stub
		toast("登陆失败:"+msg);
	}
});
```
### 当前用户
如果用户在每次打开你的应用程序时都要登录，这将会直接影响到你应用的用户体验。为了避免这种情况，你可以使用缓存的CurrentUser对象。

每当你注册成功或是第一次登录成功，都会在本地磁盘中又一个缓存的用户对象，这样，你可以通过获取这个缓存的用户对象来进行登录：
```java
BmobUser bmobUser = BmobUser.getCurrentUser(this);
if(bmobUser != null){
	// 允许用户使用应用
}else{
	//缓存用户对象为空时， 可打开用户注册界面…
}
```
当然，你也可以使用如下方法清除缓存用户对象：
```java
	BmobUser.logOut(this);   //清除缓存用户对象
	BmobUser currentUser = BmobUser.getCurrentUser(this); // 现在的currentUser是null了
```
### 重置密码
这是一个事实，一旦你引入了一个密码系统，那么肯定会有用户忘记密码的情况。对于这种情况，我们提供了一种方法，让用户安全地重置起密码。

重置密码的流程很简单，开发者只要求用户输入注册的电子邮件地址即可：
```java
final String email = "xxx@163.com";
BmobUser.resetPassword(this, email, new ResetPasswordListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("重置密码请求成功，请到" + email + "邮箱进行密码重置操作");
	}
	@Override
	public void onFailure(String e) {
		// TODO Auto-generated method stub
		toast("重置密码失败:" + e);
	}
});
```
密码重置流程如下：

1. 用户输入他们的电子邮件，请求重置自己的密码。
2. Bmob向他们的邮箱发送一封包含特殊的密码重置连接的电子邮件。
3. 用户根据向导点击重置密码连接，打开一个特殊的Bmob页面，让他们输入一个新的密码。
4. 用户的密码已被重置为新输入的密码。

### 查询用户
查询用户和查询普通对象一样，只需指定BmobUser类即可，如下：
```java
BmobQuery<BmobUser> query = new BmobQuery<BmobUser>();
query.addWhereEqualTo("username", "lucky");
query.findObjects(this, new FindListener<BmobUser>() {
	@Override
	public void onSuccess(List<BmobUser> object) {
		// TODO Auto-generated method stub
		toast("查询用户成功："+object.size());
	}
	@Override
	public void onError(String msg) {
		// TODO Auto-generated method stub
		toast("查询用户失败："+msg);
	}
});
```
浏览器中查看用户表

User表是一个特殊的表，专门存储BmobUser对象。在浏览器端，你会看到一个User表旁边有一个小人的图标。

![](http://static.bmob.cn/new/images/create_table.png)

## 地理位置

Bmob允许用户根据地球的经度和纬度坐标进行基于地理位置的信息查询。你可以在BmobObject的查询中添加一个BmobGeoPoint的对象查询。您可以实现轻松查找出离当前用户最接近的信息或地点的功能。

### 地理位置对象
首先需要创建一个BmobGeoPoint对象。例如，创建一个北纬39.913768382429105度-东经116.39727786183357度的BmobGeoPoint对象：
```java
BmobGeoPoint point = new BmobGeoPoint(39.913768382429105, 116.39727786183357);
```
### 地理位置查询
现在，你的数据表中有了一定的地理坐标对象的数据，这样可以测试找出最接近某个点的信息了。你可以使用BmobQuery对象的`addWhereNear`方法来这样做：
```java
BmobQuery<Person> bmobQuery = new BmobQuery<Person>();
bmobQuery.addWhereNear("gpsAdd", new BmobGeoPoint(112.934755, 24.52065));
bmobQuery.setLimit(10);    //获取最接近用户地点的10条数据
bmobQuery.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		toast("查询成功：共" + object.size() + "条数据。");
	}
	@Override
	public void onError(String msg) {
		// TODO Auto-generated method stub
		toast("查询失败：" + msg);
	}
});
```

要限制查询指定距离范围的数据可以使用`addWhereWithinKilometers`、`addWhereWithinMiles`或`addWhereWithinRadians`方法。

要查询一个矩形范围内的信息可以使用`addWhereWithinGeoBox`来实现：
```java
BmobGeoPoint southwestOfSF = new BmobGeoPoint(116.10675, 39.711669);
BmobGeoPoint northeastOfSF = new BmobGeoPoint(116.627623, 40.143687);
BmobQuery<Person> query = new BmobQuery<Person>();
query.addWhereWithinGeoBox("gpsAdd", southwestOfSF, northeastOfSF);
query.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		toast("查询成功：共" + object.size() + "条数据。");
	}
	@Override
	public void onError(String msg) {
		// TODO Auto-generated method stub
		toast("查询失败：" + msg);
	}
});
```
### 注意事项
目前有几个需要注意的地方：

1. 每个BmobObject数据对象中`只能`有一个BmobGeoPoint对象。

2. 地理位置的点不能超过规定的范围。`纬度的范围`应该是在`-90.0到90.0`之间。`经度的范围`应该是在`-180.0到180.0`之间。如果您添加的经纬度超出了以上范围，将导致程序错误。

有关详细信息，请查看我们提供的API文档。


  [1]: http://www.bmob.cn/docs/getdatasafety