## 简介

Bmob平台为您的移动应用提供了一个完整的后端解决方案，我们提供轻量级的SDK开发包，让开发者以最小的配置和最简单的方式使用Bmob平台提供的服务，进而完全消除开发者编写服务器代码以及维护服务器的操作。

## 快速入门

建议您在阅读本开发文档之前，先阅读我们提供的 [Android快速入门文档](http://docs.bmob.cn/android/faststart/index.html?menukey=fast_start&key=start_android)，便于您后续的开发。

## SDK导入

### AndroidStudio配置

鉴于目前Google官方推荐使用 `Android Studio` 进行Android项目开发，自 `V3.4.2` 开始，Bmob Android SDK 可以使用Gradle来进行包依赖管理，如果你使用Android Studio来进行基于BmobSDK的项目开发，建议你按照如下两个步骤进行：

一、 在`Project`的`build.gradle`文件中添加`Bmob的maven仓库地址`，示例如下：（**注意文字说明部分**）：

```java

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.2.3'
    }
}

allprojects {
    repositories {
        jcenter()
		//Bmob的maven仓库地址，必须填写
        maven { url "https://raw.github.com/bmob/bmob-android-sdk/master" }
    }
}

```

二、 在`app`的`build.gradle`文件中添加`compile依赖文件`,示例如下：（**注意文字说明部分**）：

```java 

apply plugin: 'com.android.application'

android {
    compileSdkVersion 22
    buildToolsVersion '22.0.1'

	//**兼容Android6.0系统所需，如果这句话报错，可将其导入到libs文件夹下面**
    useLibrary 'org.apache.http.legacy'

    defaultConfig {
        applicationId "cn.bmob.android"
        minSdkVersion 14
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    lintOptions{
        abortOnError false
    }
}


dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])

	//以下SDK开发者请根据需要自行选择

    //bmob-sdk：Bmob的android sdk包，包含了Bmob的数据存储、文件等服务，以下是最新的bmob-sdk:
	//3.4.7依赖，其他诸如`org.apache.http.legacy`,如果你需要兼容Android6.0可自行添加
	compile 'cn.bmob.android:bmob-sdk:3.4.7'
	compile 'com.squareup.okhttp3:okhttp:3.2.0'
    compile 'com.squareup.okio:okio:1.7.0'
	//注：3.4.7的SDK将数据的加解密实现转移到了底层so中，请务必导入`libbmob.so`，否则无法使用BmobSDK。

	//3.4.6依赖，每个SDK对应特定版本的okhttp相关包，不可更改
	compile 'cn.bmob.android:bmob-sdk:3.4.6'
	compile 'com.squareup.okhttp:okhttp:2.4.0'
    compile 'com.squareup.okio:okio:1.4.0'
    compile 'com.android.support:support-v4:23.2.1'
	//注:bmob-sdk:3.4.6需要依赖okhttp（2.4.0）、okio（1.4.0），如果需要兼容Android6.0系统，则还需要添加support-v4（23.2.1）及org.apache.http.legacy包，具体可查看下面注释[2]的解释

    //bmob-push：Bmob的推送包
    compile 'cn.bmob.android:bmob-push:0.8'

	//bmob-im：Bmob的即时通讯包，如果想使用即时通讯服务，则添加以下两个包，注意每个版本的im依赖特定版本的bmob-sdk，而bmob-sdk又需要特定的依赖库，具体可以查看下面注释[3]中的对应关系
	compile 'cn.bmob.android:bmob-im:2.0.4'
	compile 'cn.bmob.android:bmob-sdk:3.4.6'
	//注：别忘记导入3.4.6的相关依赖包

	//bmob-sms ：Bmob单独为短信服务提供的包
    compile 'cn.bmob.android:bmob-sms:1.0.1'

}

```

**注：**

**[1]、bmob-sdk：3.4.7依赖以下包：**

- `okhttp3(3.2.0)、okio(1.7.0)`:CDN文件服务的依赖库升级到`okhttp3`，用于文件的上传和下载（`必填`）
- `libbmob.so`                :用来替换数据加解密方式的底层so库（`必填`），在官网下载的SDK解压包的libs目录内

注：由于`PermissionManager权限管理类`需要依赖`support-v4:23.2.1`的jar包,导致开发者认为SDK依赖文件较多，故分离出SDK。开发者如果需要兼容Android6.0系统，可以在下载的SDK的官方Demo的`com.example.bmobexample.permission`包下面查看该类源码。

**[2]、bmob-sdk:3.4.6依赖以下包：**

- `okhttp（2.4.0）、okio（1.4.0）`：CDN文件服务使用okhttp相关包进行文件的上传和下载（`必填`）
- `support-v4（23.2.1）`:用于兼容Android6.0系统，并提供权限管理工具（如果需要兼容Android6.0系统，则需添加此依赖）
- `org.apache.http.legacy`：用于兼容Android6.0系统（如果需要兼容Android6.0系统，则需添加此依赖）

**[3]、每个版本的im依赖特定版本的bmob-sdk：**

- `bmob-im:1.1.8--->bmob-sdk:3.3.5`
- `bmob-im:1.1.9--->bmob-sdk:3.4.3`
- `bmob-im:2.0.1--->bmob-sdk:3.4.6-0304`
- `bmob-im:2.0.2--->bmob-sdk:3.4.6-0304`
- `bmob-im:2.0.3--->bmob-sdk:3.4.6`
- `bmob-im:2.0.4--->bmob-sdk:3.4.6`

	其中`bmob-sdk:3.4.6-0304`是Bmob Android SDK的过渡版本，主要用于NewIM_v2.0.1及v2.0.2。

**[4]、bmob-sms适用于只需要使用Bmob短信功能的开发者，而bmob-sdk内部包含了bmob-sms的短信功能,请不要重复添加。**

**[5]、BmobSDK的官方仓库：[bmob-android-sdk](https://github.com/bmob/bmob-android-sdk)，开发者可到此仓库查看最新发布的各版本SDK，我们会尽量与官网发布的SDK保持同步更新。**

### Eclipse导入

开发者到 [SDK下载中心](http://www.bmob.cn/downloads) 找到对应SDK下载之后，在Eclipse工程的项目根目录中新建`libs`文件夹，将下载的jar包添加到此文件夹即可。

**注：**

1、若配置不成功，则需要额外增加以下步骤：

右键工程根目录，选择`Properties -> Java Build Path -> Libraries`，然后点击`Add External JARs...` 选择指向该libs文件夹下的jar的路径，点击OK即可。

2、BmobSDK_v3.4.7需要依赖`okhttp3（3.2.0）、okio（1.7.0）`及`libbmob.so`库。

3、BmobSDK_v3.4.6需要依赖`okhttp（2.4.0）、okio（1.4.0）`，如果需要兼容Android6.0系统，则还需要添加support-v4（23.2.1）及org.apache.http.legacy依赖包。

## 兼容Android6.0

自`v3.4.6`版本开始，Bmob提供了一些新的方法和工具类来帮助开发者为自己的应用兼容Android6.0系统。

### 配置org.apache.http.legacy

Android6.0版本移除了对Appache的HTTP client的支持，因此，需要添加org.apache.http.legacy.jar包，请参照如下方式添加：

1. Eclipse

	你需要在Eclipse工程的项目根目录中新建libs文件夹，将org.apache.http.legacy.jar包，添加到libs文件夹中

2. AndroidStudio

    你需要在`app`的`build.gradle`文件添加配置信息useLibrary 'org.apache.http.legacy'声明编译时依赖

	android {
		compileSdkVersion 23
		buildToolsVersion "23.0.2"
		useLibrary 'org.apache.http.legacy'
	}

**注：如果在build.gradle文件中`useLibrary 'org.apache.http.legacy'`这句话报错，可将该jar直接放到libs目录下即可。**

### 运行时权限管理

Android6.0中对特定的权限进行了动态授权的方式，需要在运行时用户手动授予，如果用户拒绝后再次申请还可以向用户弹框说明权限的作用，用户点击确认后再去申请。

因此，我们提供了一个权限管理的工具类`PermissionManager(cn.bmob.v3.helper)`，具体使用如下：

**注：在`v3.4.6`的BmobSDK内部集成`PermissionManager`类，自`v3.4.7`以后的SDK内部将不再提供该类，开发者可以在下载的配套官方Demo的`com.example.bmobexample.permission`包下面查看该类源码。**

1.构建`PermissionManager`对象

    PermissionManager helper;
	helper = PermissionManager.with(MainActivity.this)
			//添加权限请求码
            .addRequestCode(MainActivity.REQUEST_CODE_CAMERA)
			//设置权限，可以添加多个权限
            .permissions(Manifest.permission.CAMERA)
			//设置权限监听器
            .setPermissionsListener(new PermissionListener() {

                @Override
                public void onGranted() {
					//当权限被授予时调用
                   Toast.makeText(MainActivity.this, "Camera Permission granted",Toast.LENGTH_LONG).show();
                }

                @Override
                public void onDenied() {
					//用户拒绝该权限时调用
                   Toast.makeText(MainActivity.this, "Camera Permission denied",Toast.LENGTH_LONG).show();
                }

                @Override
                public void onShowRationale(String[] permissions) {
					//当用户拒绝某权限时并点击`不再提醒`的按钮时，下次应用再请求该权限时，需要给出合适的响应（比如,给个展示对话框来解释应用为什么需要该权限）
                    Snackbar.make(btn_camera, "需要相机权限去拍照", Snackbar.LENGTH_INDEFINITE)
                            .setAction("ok", new View.OnClickListener() {
                                @Override
                                public void onClick(View v) {
									//必须调用该`setIsPositive(true)`方法
                                    helper.setIsPositive(true);
                                    helper.request();
                                }
                            }).show();
                }
            })
			//请求权限
			.request();

注：

- `with`方法可以传入Activity或者Fragment；
- `addRequestCode`方法传入请求码，用于区分各种不同的权限申请；
- `permissions`方法传入的是你所要请求的权限，支持可变参数，可以批量申请权限；
- `PermissionListener`接口回调的三个方法：
   - onGranted()会在权限申请通过后被调用；
   - onDenied()在权限申请被拒绝时被调用
   - onShowRationale()方法中你可以弹对话框向用户解释权限的作用，不过记得要调用`setIsPositive(true)`。
- `request`方法用来请求权限申请

2.覆写`onRequestPermissionsResult`方法

	@Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode) {
            case REQUEST_CODE_CAMERA:
                helper.onPermissionResult(permissions, grantResults);
                break;
        }
    }

### Notification变更

Android6.0中，`Notification.setLatestEventInfo()`方法被移除，替代的方案是用Notification.Builder来构建通知，对此SDK提供了`NotificationCompat(cn.bmob.v3.helper)`类来做版本兼容（与`android.support.v4.app`包下的NotificationCompat用法一样）。

参照代码如下：

	NotificationManager notificationManager = (NotificationManager) mContext
			.getSystemService(Context.NOTIFICATION_SERVICE);
	PendingIntent pi = PendingIntent.getActivity(mContext, 0,
			new Intent(MainActivity.this, MainActivity.class), 0);
	NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
			.setTicker("更新啦")
			.setContentTitle("标题")
			.setContentText("内容")
			.setSmallIcon(R.drawable.ic_launcher);
	Notification notification = builder.build();
	notificationManager.notify(0, notification);

## 对象

一个数据对象（APP中创建的BmobObject类的子类）对应于Bmob后台的一个数据表。

### 数据对象

Bmob存储的数据是建立在BmobObject基础上的，所以任何要保存的数据对象必须继承自BmobObject类。BmobObject类本身包含objectId、createdAt、updatedAt、ACL四个默认的属性，objectId是数据的唯一标示，相当于数据库中表的主键，createdAt是数据的创建时间，updatedAt是数据的最后修改时间，ACL是数据的操作权限。

如，你的游戏中使用GameScore表来记录玩家的比分信息，其中表的字段有：score（分数）、playerName（玩家名字）、isPay(是否付费玩家)、pic（玩家头像）属性，那么这个数据对象为如下定义：

```java
//必须要继承自BmobObject类
public class GameScore extends BmobObject{
   
	private String playerName;
	private Integer score;
	private Boolean isPay;
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
	
	public Boolean getIsPay() {
		return isPay;
	}
	
	public void setIsPay(Boolean isPay) {
		this. isPay = isPay;
	}
	
    public BmobFile getPic() {
		return pic;
	}
	
	public void setPic(BmobFile pic) {
		this.pic = pic;
	}
}
```

需要注意的是：

 - JavaBean不需要对objectId、createdAt、updatedAt、ACL四个属性进行定义。
 - 不少开发者会没有注意到createdAt和updatedAt属性中的字母d，写成createAt和updateAt。
 - 尽可能使用Integer、Boolean，而不是int、boolean，也就是选择包装类，而不是使用基本数据类型（这两者的区别大家可以看这篇文章：http://www.cnblogs.com/shenliang123/archive/2011/10/27/2226903.html）

### 特殊对象

为了提供更好的服务，BmobSDK中提供了BmobUser、BmobInstallation、BmobRole三个特殊的BmobObject对象来完成不同的功能，在这里我们统一称为特殊对象。

 - BmobUser对象主要是针对应用中的用户功能而提供的，它对应着web端的User表，使用BmobUser对象可以很方便的在应用中实现用户的注册、登录、邮箱验证等功能，具体的使用方法可查看文档的[`用户管理`](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_用户管理)部分。

 - BmobInstallation对象主要用于应用的安装设备管理中，它对应着web端的Installation表，任何安装了你应用的设备都会在此表中产生一条数据标示该设备。结合Bmob提供的推送功能，还可以实现将自定义的消息推送给不同的设备终端，具体的使用方法可查看文档的[`消息推送`](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_消息推送)部分。

 - BmobRole对象主要用于角色管理，对应用于Web端的Role表，具体的使用方法可查看文档的[`ACL和角色`](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_ACL和角色)部分。

## 数据类型

目前为止，Bmob支持的数据类型：String、Integer、Float、Short、Byte、Double、Character、Boolean、Object、Array。
同时也支持BmobObject、BmobDate、BmobGeoPoint、BmobFile特有的数据类型。

以下为Web端类型与SDK端支持的JAVA类型对应表：

|Web端类型|支持的JAVA类型|说明|
|:---|:---|:---|
|Number	 |Integer、Float、Short、Byte、Double、Character|对应数据库的Number类型|
|Array	 |List|数组类型|
|File  	 |BmobFile|Bmob特有类型，用来标识文件类型|
|GeoPoint|BmobGeoPoint|Bmob特有类型，用来标识地理位置|
|Date    |BmobDate|Bmob特有类型，用来标识日期类型|
|Pointer |特定对象|Bmob特有类型，用来标识指针类型|
|Relation|BmobRelation|Bmob特有类型，用来标识数据关联|

**注：**

**1、`不能使用int、float、short byte、double、character等基本数据类型。`**

## 类名和表名的关系

- Bmob官方推荐类名和表名完全一致的映射使用方式， 即如，上面的GameScore类，它在后台对应的表名也是GameScore（区分大小写）。
- 如果你希望表名和类名并不相同，如表名为T_a_b，而类名还是GameScore，那么你可以使用BmobObject提供的setTableName("表名")的方法，示例代码如下：
```java
//这时候实际操作的表是T_a_b
public class GameScore extends BmobObject{
	private String playerName;
	private Integer score;
	private Boolean isPay;
    private BmobFile pic;
		
	public GameScore() {
		this.setTableName("T_a_b");
	}

	public String getPlayerName() {
		return playerName;
	}
	//其他方法，见上面的代码
}
```
当然了，除了在构造函数中直接调用setTableName方法之外，你还可以在GameScore的实例中动态调用setTableName方法。

### 查询自定义表名的数据

如果您使用了setTableName方法来自定义表名，那么在对该表进行数据查询的时候必须使用以下方法。`需要注意的是查询的结果是JSONArray,需要自行解析JSONArray中的数据`。

```java
/**
 * 查询数据
 */
public void queryData(){
	BmobQuery query = new BmobQuery("T_a_b");
	query.findObjects(this, new FindCallback() {
		
		@Override
		public void onSuccess(JSONArray arg0) {
			//注意：查询的结果是JSONArray,需要自行解析
			showToast("查询成功:"+arg0.length());
		}
		
		@Override
		public void onFailure(int arg0, String arg1) {
			showToast("查询失败:"+arg1);
		}
	});
}
```

**自定义表名情况下的更新、删除数据和普通的更新、删除数据方式一样，没有变化。为方便大家了解学习，我们提供了一个关于自定义表名情况下增删改查数据的Demo，下载地址是：[https://github.com/bmob/bmob-android-demo-dynamic-tablename](https://github.com/bmob/bmob-android-demo-dynamic-tablename)。**

## 添加数据

添加数据使用BmobObject对象的`save`方法，就可以将当前对象的内容保存到Bmob服务端。
例如，你现在要保存一条游戏分数的记录，代码如下：

```java
GameScore gameScore = new GameScore();
//注意：不能调用gameScore.setObjectId("")方法
gameScore.setPlayerName("比目");
gameScore.setScore(89);
gameScore.setIsPay(false);
gameScore.save(mContext, new SaveListener() {
		
	@Override
	public void onSuccess() {
		toast("添加数据成功，返回objectId为："+gameScore.getObjectId() + ”,数据在服务端的创建时间为：“ + gameScore.getCreatedAt());
	}
		
	@Override
	public void onFailure(int code, String arg0) {
		// 添加失败
	}
});
```

运行以上代码，如果添加成功，你可以在Bmob提供的后台的数据浏览中看到类似这样的结果：

```java
objectId: "0c6db13c", score: 89, playerName: "比目", isPay: false,createdAt:"2013-09-27 10:32:54", updatedAt:"2013-09-27 10:32:54"
```

**这里需要注意的是：**
1. 如果服务器端不存在GameScore表，那么系统将自动建表，并插入数据。
2. 如果服务器端已经存在GameScore表，和相应的score、playerName、isPay字段，那么你此时添加的数据和数据类型也应该和服务器端的表结构一致，否则会保存数据失败。
3. 每个BmobObject对象都有几个默认的键(数据列)是不需要开发者指定的，`objectId`是每个保存成功数据的唯一标识符。`createdAt`和`updatedAt`代表每个对象(每条数据)在服务器上创建和最后修改的时间。这些键(数据列)的创建和数据内容是由服务器端自主来完成的。`因此，使用save和insert方法时,不需要调用setObjectId方法，否则会出现提示：“It is a reserved field: objectId(105)”--表明objectId为系统保留字段，不允许修改。`。

## 更新数据

更新一个对象也是非常简单。例如：将GameScore表中objectId为`0c6db13c`的游戏分数修改为77.

```java
GameScore gameScore = new GameScore();
gameScore.setScore(77);
gameScore.update(this, "0c6db13c", new UpdateListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","更新成功：");
	}
		
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","更新失败："+msg);
	}
});
```

**自`V3.4.4`版本开始，SDK提供了另一种方法来更新数据，通过调用`Bmobobject`类中的`setValue（key，value）`方法，只需要传入key及想要更新的值即可**

举例，说明如下：

```java
public class Person extends BmobObject {
	private BmobUser user;	//BmobObject类型
	private BankCard cards;	//Object类型
	private Integer age;	//Integer类型
	private Boolean gender; //Boolean类型
	...
	getter、setter方法
}

其中BankCard类结构如下：

public class BankCard{
	private String cardNumber;
	private String bankName;
	public BankCard(String bankName, String cardNumber){
		this.bankName = bankName;
		this.cardNumber = cardNumber;
	}
	...
	getter、setter方法
}

```

```java 
Person p2=new Person();
//更新BmobObject的值
//	p2.setValue("user", BmobUser.getCurrentUser(this, MyUser.class));
//更新Object对象
p2.setValue("bankCard",new BankCard("农行", "农行账号"));
//更新Object对象的值
//p2.setValue("bankCard.bankName","建行");
//更新Integer类型
//p2.setValue("age",11);
//更新Boolean类型
//p2.setValue("gender", true);
p2.update(this, objectId, new UpdateListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","更新成功：");
	}
		
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","更新失败："+msg);
	}
});

```

**注意：修改数据只能通过objectId来修改，目前不提供查询条件方式的修改方法。**

### 原子计数器

很多应用可能会有计数器功能的需求，比如文章点赞的功能，如果大量用户并发操作，用普通的更新方法操作的话，会存在数据不一致的情况。

为此，Bmob提供了原子计数器来保证原子性的修改某一**数值字段**的值。注意：原子计数器只能对应用于Web后台的Number类型的字段，即JavaBeans数据对象中的Integer对象类型（**不要用int类型**）。

```java
gameScore.increment("score"); // 分数递增1
gameScore.update(this, updateListener);
```

您还可以通过`increment(key, amount)`方法来递增或递减任意幅度的数字

```java
gameScore.increment("score", 5); // 分数递增5
//gameScore.increment("score", -5); // 分数递减5
gameScore.update(this, updateListener);
```

## 删除数据

从服务器删除对象。例如：将GameScore表中objectId为`dd8e6aff28`的数据删除。

```java
GameScore gameScore = new GameScore();
gameScore.setObjectId("dd8e6aff28");
gameScore.delete(this, new DeleteListener() {
		
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","删除成功");
	}
		
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","删除失败："+msg);
	}
});
```

**注意：删除数据只能通过objectId来删除，目前不提供查询条件方式的删除方法。**

### 删除字段的值

你可以在一个对象中删除一个字段的值，通过`remove`操作：

```java
GameScore gameScore = new GameScore();
gameScore.setObjectId("dd8e6aff28");
gameScore.remove("score");	// 删除GameScore对象中的score字段
gameScore.update(this, new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","删除GameScore对象中的score字段成功");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","删除GameScore对象中的score字段失败："+msg);
	}
});
```

## 批量数据操作

在BmobObject对象中提供了三种用于批量操作的方法，分别是`insertBatch`、`updateBatch`、`deleteBatch`,批量添加、更新、删除。

 - `insertBatch`的使用方式如下：

```java
List<BmobObject> persons = new ArrayList<BmobObject>();
for (int i = 0; i < 3; i++) {
	Person person = new Person();
	person.setName("张三 "+i);
	person.setAddress("上海朝阳路"+i+"号");
	person.setGpsAdd(new BmobGeoPoint(112.934755, 24.52065));
	person.setUploadTime(new BmobDate(new Date()));
	List<String> hobbys = new ArrayList<String>();
	hobbys.add("阅读");
	hobbys.add("篮球");
	hobbys.add("唱歌");
	person.setHobby(hobbys);
	person.setBankCard(new BankCard("中国银行", "176672673687545097"+i));
	persons.add(person);
}
new BmobObject().insertBatch(this, persons, new SaveListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("批量添加成功");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("批量添加失败:"+msg);
	}
});
```

 - `updateBatch`的使用方式如下：

```java
List<BmobObject> persons = new ArrayList<BmobObject>();
Person p1 = new Person();
p1.setObjectId("e51d651c22");
p1.setAge(25);
Person p2 = new Person();
p2.setObjectId("3f70a922c4");
p2.setAge(26);
p2.setGender(false);
Person p3 = new Person();
p3.setObjectId("08fdd55765");
p3.setAge(27);

persons.add(p1);
persons.add(p2);
persons.add(p3);

new BmobObject().updateBatch(this, persons, new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("批量更新成功");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("批量更新失败:"+msg);
	}
});
```

 - `deleteBatch`的使用方式如下：

```java
List<BmobObject> persons = new ArrayList<BmobObject>();
Person p1 = new Person();
p1.setObjectId("38ea274d0c");
Person p2 = new Person();
p2.setObjectId("01e29165bc");
Person p3 = new Person();
p3.setObjectId("d8226c4828");

persons.add(p1);
persons.add(p2);
persons.add(p3);

new BmobObject().deleteBatch(this, persons, new DeleteListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("批量删除成功");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("批量删除失败:"+msg);
	}
});
```

注意：

 1. 任何一种批量操作每次只支持最大50条记录的操作。
 2. 批量操作不支持对User表的操作。

## 查询数据

数据的查询可能是每个应用都会频繁使用到的，BmobSDK中提供了`BmobQuery`类，它提供了多样的方法来实现不同条件的查询，同时它的使用也是非常的简单和方便的。

### 查询单条数据

当我们知道某条数据的`objectId`时，就可以根据`objectId`直接获取单条数据对象。例如：查询`objectId`为`a203eba875`的人员信息。

```java
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.getObject(this, "a203eba875", new GetListener<GameScore>() {
		
	@Override
	public void onSuccess(GameScore object) {
		// TODO Auto-generated method stub
		toast("查询成功：");
		//获得playerName的信息
		object.getPlayerName();
		//获得数据的objectId信息
        object.getObjectId();
        //获得createdAt数据创建时间（注意是：createdAt，不是createAt）
		object.getCreatedAt();
	}

	@Override
	public void onFailure(int code, String arg0) {
		// TODO Auto-generated method stub
		toast("查询失败："+arg0);
	}
		
});
```

### 查询多条数据

查询某个数据表中的所有数据是非常简单的查询操作，例如：查询GameScore表中playerName为“比目”的50条数据记录。

```java
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
//查询playerName叫“比目”的数据
query.addWhereEqualTo("playerName", "比目");
//返回50条数据，如果不加上这条语句，默认返回10条数据
query.setLimit(50);
//执行查询方法
query.findObjects(this, new FindListener<GameScore>() {
		@Override
		public void onSuccess(List<GameScore> object) {
			// TODO Auto-generated method stub
			toast("查询成功：共"+object.size()+"条数据。");
			for (GameScore gameScore : object) {
               //获得playerName的信息
			   gameScore.getPlayerName();
			   //获得数据的objectId信息
               gameScore.getObjectId();
               //获得createdAt数据创建时间（注意是：createdAt，不是createAt）
			   gameScore.getCreatedAt();
        	}
		}
		@Override
		public void onError(int code, String msg) {
			// TODO Auto-generated method stub
			toast("查询失败："+msg);
		}
});
```

查询的结果不需要进行任何处理，BmobSDK已经为你封装成相应的JavaBean集合了，你直接使用即可。

**注：**
通过setLimit方法设置返回的记录数量。更多细节可[点击查看分页查询](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_条件查询)一节。


### 查询条件

在查询的使用过程中，基于不同条件的查询是非常常见的，BmobQuery同样也支持不同条件的查询。

#### 比较查询

如果要查询特定键的特定值，可以使用`addWhereEqualTo`方法，如果要过滤掉特定键的值可以使用`addWhereNotEqualTo`方法。

比如需要查询playerName不等于“Barbie”的数据时可以这样写：

```java
query.addWhereNotEqualTo("playerName", "Barbie");
```

当然，你可以在你的查询操作中添加多个约束条件，来查询符合要求的数据。

```java
query.addWhereNotEqualTo("playerName", "Barbie");     //名字不等于Barbie
query.addWhereGreaterThan("score", 60);      		  //条件：分数大于60岁
```

各种不同条件的比较查询：

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

#### 子查询

如果你想查询匹配几个不同值的数据，如：要查询“Barbie”,“Joe”,“Julia”三个人的成绩时，你可以使用`addWhereContainedIn`方法来实现。

```java
String[] names = {"Barbie", "Joe", "Julia"};
query.addWhereContainedIn("playerName", Arrays.asList(names));
```

相反，如果你想查询排除“Barbie”,“Joe”,“Julia”这三个人的其他同学的信息，你可以使用`addWhereNotContainedIn`方法来实现。

```java
String[] names = {"Barbie", "Joe", "Julia"};
query.addWhereNotContainedIn("playerName", Arrays.asList(names));
```

#### 时间查询

`时间查询`比较特殊，我们需要结合`BmobDate`这个类来查询某个指定日期时间前后的数据，这里也给出示例供大家参考：

比如:
如果想查询指定日期之前的数据，则可以使用`addWhereLessThan`或者`addWhereLessThanOrEqualTo`（包含当天）来查询。
如果想查询指定日期之后的数据，则可以使用`addWhereGreaterThan`或`addWhereGreaterThanOrEqualTo`（包含当天）来查询。
如果想查询指定时间当天的数据，则需要使用`复合与查询`来查询，例如，想`查询2015年5月1号当天的Person数据`,示例代码如下：

```java
BmobQuery<Person> query = new BmobQuery<Person>();
List<BmobQuery<Person>> and = new ArrayList<BmobQuery<Person>>();
//大于00：00：00
BmobQuery<Person> q1 = new BmobQuery<Person>();
String start = "2015-05-01 00:00:00";  
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
Date date  = null;
try {
date = sdf.parse(start);
} catch (ParseException e) {
e.printStackTrace();
}  
q1.addWhereGreaterThanOrEqualTo("createdAt",new BmobDate(date));
and.add(q1);
//小于23：59：59
BmobQuery<Person> q2 = new BmobQuery<Person>();
String end = "2015-05-01 23:59:59"; 
SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
Date date1  = null;
try {
date1 = sdf1.parse(end);
} catch (ParseException e) {
e.printStackTrace();
}  
q2.addWhereLessThanOrEqualTo("createdAt",new BmobDate(date1));
and.add(q2);
//添加复合与查询
query.and(and);
```

注：
**由于createdAt、updatedAt是服务器自动生成的时间，在服务器保存的是精确到微秒值的时间，所以，基于时间类型的比较的值要加1秒。**

#### 数组查询

对于字段类型为数组的情况，需要查找字段中的数组值包含有xxx的对象，可以使用`addWhereContainsAll`方法：

比如我想查询有阅读和唱歌爱好的人，可以这样：

```java
BmobQuery<Person> query = new BmobQuery<Person>();
String [] hobby = {"阅读","唱歌"};
query.addWhereContainsAll("hobby", Arrays.asList(hobby));
query.findObjects(this, new FindListener<Person>() {

    @Override
    public void onSuccess(List<Person> object) {
        // TODO Auto-generated method stub
        toast("查询成功：共" + object.size() + "条数据。");
    }

    @Override
    public void onError(int code, String msg) {
        // TODO Auto-generated method stub
        toast("查询失败：" + code);
    }

});
```

#### 模糊查询

对字符串值的模糊查询 比如 查询包含字符串的值，有几种方法。

你可以使用任何正确的正则表达式来检索相匹配的值，使用`addWhereMatches`方法：

```java
query.addWhereMatches(("username", "^[A-Z]\\d");
```

还可以使用如下方法：

```java
//查询username字段的值含有“sm”的数据
query.addWhereContains("username", "sm");

//查询username字段的值是以“sm“字开头的数据
query.whereStartsWith("username", "sm");

// 查询username字段的值是以“ile“字结尾的数据
query.whereEndsWith("username", "ile");
```

#### 列值是否存在

如果你想查询某个列的值存在，那么可以使用`addWhereExists`方法：

```java
//查询username有值的数据
query.addWhereExists("username");
```

如果想查询某个列的值不存在，则可以用`addWhereDoesNotExists`方法

```java
//查询username字段没有值的数据
query.addWhereDoesNotExists("username");
```

#### 分页查询

有时，在数据比较多的情况下，你希望查询出的符合要求的所有数据能按照多少条为一页来显示，这时可以使用`setLimit`方法来限制查询结果的数据条数来进行分页。

默认情况下，Limit的值为`100`，最大有效设置值`1000`（设置的数值超过1000还是视为1000）。

```java
query.setLimit(10); // 限制最多10条数据结果作为一页
```

在数据较多的情况下，在`setLimit`的基础上分页显示数据是比较合理的解决办法。
`setSKip`方法可以做到跳过查询的前多少条数据来实现分页查询的功能。默认情况下Skip的值为10。

```java
query.setSkip(10); // 忽略前10条数据（即第一页数据结果）
```

**大家也可以直接下载我们提供的Demo源码（[https://github.com/bmob/bmob-android-demo-paging](https://github.com/bmob/bmob-android-demo-paging)），查看如何使用分页查询，结合ListView开发下拉刷新查看更多内容的应用。**

#### 排序

对应数据的排序，如数字或字符串，你可以使用升序或降序的方式来控制查询数据的结果顺序：

```java
// 根据score字段升序显示数据
query.order("score");
// 根据score字段降序显示数据
query.order("-score");
// 多个排序字段可以用（，）号分隔
query.order("-score,createdAt");
```
**说明：多个字段排序时，先按第一个字段进行排序，再按第二个字段进行排序，依次进行。**

### 复合查询

#### 与查询(and)

有些查询需要使用到复合“与”的查询条件，例如：你想查询出Person表中年龄在6-29岁之间且姓名以"y"或者"e"结尾的人，那么，可以采用and查询，示例代码如下：

```java
//查询年龄6-29岁之间的人，每一个查询条件都需要New一个BmobQuery对象
//--and条件1
BmobQuery<Person> eq1 = new BmobQuery<Person>();
eq1.addWhereLessThanOrEqualTo("age", 29);//年龄<=29
//--and条件2
BmobQuery<Person> eq2 = new BmobQuery<Person>();
eq2.addWhereGreaterThanOrEqualTo("age", 6);//年龄>=6

//查询姓名以"y"或者"e"结尾的人--这个需要使用到复合或查询（or）
//--and条件3
BmobQuery<Person> eq3 = new BmobQuery<Person>();
eq3.addWhereEndsWith("name", "y");
BmobQuery<Person> eq4 = new BmobQuery<Person>();
eq4.addWhereEndsWith("name", "e");
List<BmobQuery<Person>> queries = new ArrayList<BmobQuery<Person>>();
queries.add(eq3);
queries.add(eq4);
BmobQuery<Person> mainQuery = new BmobQuery<Person>();
BmobQuery<Person> or = mainQuery.or(queries);

//最后组装完整的and条件
List<BmobQuery<Person>> andQuerys = new ArrayList<BmobQuery<Person>>();
andQuerys.add(eq1);
andQuerys.add(eq2);
andQuerys.add(or);
//查询符合整个and条件的人
BmobQuery<Person> query = new BmobQuery<Person>();
query.and(andQuerys);
query.findObjects(this, new FindListener<Person>() {
    @Override
    public void onSuccess(List<Person> object) {
        // TODO Auto-generated method stub
    	toast("查询年龄6-29岁之间，姓名以'y'或者'e'结尾的人个数："+object.size());
    }
    @Override
    public void onError(int code, String msg) {
        // TODO Auto-generated method stub
    	toast("复合与查询失败："+code+",msg:"+msg);
    }
});
```

#### 或查询(or)

有些情况，在查询的时候需要使用到复合的“或”的查询条件。例如，你想查出 Person 表中 age 等于 29 或者 age 等于 6 的人，可以这样：

```java
BmobQuery<Person> eq1 = new BmobQuery<Person>();
eq1.addWhereEqualTo("age", 29);
BmobQuery<Person> eq2 = new BmobQuery<Person>();
eq2.addWhereEqualTo("age", 6);
List<BmobQuery<Person>> queries = new ArrayList<BmobQuery<Person>>();
queries.add(eq1);
queries.add(eq2);
BmobQuery<Person> mainQuery = new BmobQuery<Person>();
mainQuery.or(queries);
mainQuery.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		
	}
});
```

你还可以在此基础上添加更多的约束条件到新创建的 BmobQuery 对象上，表示一个 and 查询操作。

### 查询结果计数

如果你只是想统计满足查询对象的数量，你并不需要获取所有匹配对象的具体数据信息，可以直接使用`count`替代`findObjects`。例如，查询一个特定玩家玩的游戏场数：

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
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("count failure："+msg);
	}
});
```

### 查询指定列

有的时候，一张表的数据列比较多，而我们只想查询返回某些列的数据时，我们可以使用BmobQuery对象提供的`addQueryKeys`方法来实现。如下所示：

```java
//只返回Person表的objectId这列的值
BmobQuery<Person> bmobQuery = new BmobQuery<Person>();
bmobQuery.addQueryKeys("objectId");
bmobQuery.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		toast("查询成功：共" + object.size() + "条数据。");
         //注意：这里的Person对象中只有指定列的数据。
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败：" + msg);
	}
});
```

指定多列时用`,`号分隔每列，如：`addQueryKeys("objectId,name,age")`;

### 统计查询

从`BmobSDKV3.3.6`开始，Bmob为开发者提供了以下关键字或其组合的统计查询操作,分别用于计算`总和、平均值、最大值、最小值`，同时支持分组和过滤条件。

<table>
<tr>
<th> 方法名 </td>
<th> 参数说明 </td>
<th> 方法说明 </td>
</tr>
<tr>
<td> sum </td>
<td> String[] sumKeys（多个列名） </td>
<td> 求某列或多列的和 </td>
</tr>
<tr>
<td>average</td>
<td>String[] aveKeys（多个列名）</td>
<td>求某列或多列的平均值</td>
</tr>
<tr>
<td>max</td>
<td>String[] maxKeys（多个列名）</td>
<td>求某列或多列的最大值</td>
</tr>
<tr>
<td>min</td>
<td>String[] minKeys（多个列名）</td>
<td>求某列或多列的最小值</td>
</tr>
<tr>
<td>groupby</td>
<td>String[] groupKeys（多个列名）</td>
<td>分组</td>
</tr>
<tr>
<td>having</td>
<td>HashMap map(键（String）值(Object)对的形式)</td>
<td>分组的过滤条件</td>
</tr>
<tr>
<td>setHasGroupCount</td>
<td>boolean hasCount</td>
<td>是否返回每个分组的记录数</td>
</tr>
</table>

注：
1、为避免和用户创建的列名称冲突，Bmob约定以上查询返回的字段采用`_(关键字)+首字母大写的列名` 的格式：
例：
计算玩家得分表（GameScore）中列名为score的总和，那么返回的结果集会有一个列名为`_sumScore`，
若设置了setHasGroupCount（true）,则结果集中会返回`_count`。
2、以上方法可自由组合且与之前的查询语句中的where, order, limit, skip等组合一起使用。 
3、因为返回格式不固定，故使用`findStatistics`来专门处理统计查询。

#### 统计查询方法

例如，如果要计算所有玩家的得分总和，那么代码如下：

```java
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.sum(new String[] { "playScore" });
query.findStatistics(this, GameScore.class,new FindStatisticsListener() {

	@Override
	public void onSuccess(Object object) {
		JSONArray ary = (JSONArray) object;
		if(ary!=null){//
			try {
				JSONObject obj = ary.getJSONObject(0);
				int sum = obj.getInt("_sumPlayScore");//_(关键字)+首字母大写的列名
				showToast("游戏总得分：" + sum);
			} catch (JSONException e) {
			}
		}else{
			showToast("查询成功，无数据");
		}
	}

	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("查询出错：code =" + ",msg = " + msg);
	}
});
```

注：`sum方法的参数只能查询Number类型的列名（对应Java的Integer类型）`，即要计算哪个列的值的总和。

查询平均值、最大、最小和上面的求和类似，在这里也一并提示下：

```java
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
//query.average(new String[]{"playScore"});//查询某列的平均值
query.min(new String[]{"playScore"});//查询最小值
//query.max(new String[]{"playScore"});//查询最大值
query.groupby(new String[]{"createdAt"});
query.findStatistics(this, GameScore.class, new FindStatisticsListener() {
			
	@Override
	public void onSuccess(Object result) {
		// TODO Auto-generated method stub
		JSONArray ary = (JSONArray) result;
		if (ary!=null) {
			try {
				JSONObject obj = ary.getJSONObject(0);
//				int playscore = obj.getInt("_avgPlayScore");
				int minscore = obj.getInt("_minPlayScore");
//				int maxscore = obj.getInt("_maxPlayScore");
				String createDate = obj.getString("createdAt");
				showToast("minscore = " + minscore+ ",统计时间 = "+ createDate);
			} catch (JSONException e) {
				e.printStackTrace();
			}
		} else {
			showToast("查询成功，无数据");
		}
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("查询出错：code =" + ",msg = " + msg);
	}
});
```

#### 分组统计

如果你需要对查询结果进行分组，可使用`groupby`方法，支持根据多个列名进行分组。

```java
//我们以创建时间按天和游戏分别统计玩家的得分，并按时间降序
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.sum(new String[] { "playScore", "signScore" });//求多个列的总和
query.groupby(new String[] { "createdAt", "game" });//按照时间和游戏名进行分组
query.order("-createdAt");//降序排列
query.findStatistics(this, GameScore.class,new FindStatisticsListener() {

	@Override
	public void onSuccess(Object object) {
		// TODO Auto-generated method stub
		JSONArray ary = (JSONArray) object;
		if(ary!=null){
			int length = ary.length();
			try {
				for (int i = 0; i < length; i++) {
					JSONObject obj = ary.getJSONObject(i);
					int playscore = obj.getInt("_sumPlayScore");
					int signscore = obj.getInt("_sumSignScore");
					String createDate = obj.getString("createdAt");
					String game = obj.getString("game");
					showToast("游戏总得分：" + playscore + ",签到得分："+ signscore + ",时间 = " + createDate+",game = "+game);
				}
			} catch (JSONException e) {
				e.printStackTrace();
			}
		} else {
			showToast("查询成功，无数据");
		}
	}

	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("查询出错：code =" + ",msg = " + msg);
	}
});
```

有时候，我们需要知道分组统计时每个分组有多少条记录，可使用`setHasGroupCount（true）`,如下：

```java
// 查询创建时间按天统计所有玩家的得分和每一天有多少条玩家的得分记录，并按时间降序:
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.sum(new String[] { "playScore" });    // 统计总得分
query.groupby(new String[] { "createdAt" });// 按照时间分组
query.order("-createdAt");                  // 降序排列
query.setHasGroupCount(true);              // 统计每一天有多少个玩家的得分记录，默认不返回分组个数
query.findStatistics(this, GameScore.class,new FindStatisticsListener() {

	@Override
	public void onSuccess(Object object) {
		// TODO Auto-generated method stub
		JSONArray ary = (JSONArray) object;
		if (ary!=null) {
			int length = ary.length();
			try {
				for (int i = 0; i < length; i++) {
					JSONObject obj = ary.getJSONObject(i);
					int playscore = obj.getInt("_sumPlayScore");
					String createDate = obj.getString("createdAt");
					int count = obj.getInt("_count");//setHasGroupCount设置为true时，返回的结果中含有"_count"字段
					showToast("游戏总得分：" + playscore + ",总共统计了"
							+ count + "条记录,统计时间 = "+ createDate);
				}
			} catch (JSONException e) {
				e.printStackTrace();
			}
		} else {
			showToast("查询成功，无数据");
		}
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("查询出错：code =" + ",msg = " + msg);
	}
});
```

#### 添加过滤条件

如果需要对分组计算后的结果再进行过滤，可使用`having`来继续过滤部分结果。

```java
//我们按游戏名统计所有玩家的总得分，并只返回总得分大于100的记录，并按时间降序
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
query.sum(new String[] {"playScore"});//计算总得分数
query.groupby(new String[] {"game"});//分组条件：按游戏名进行分组
query.order("-createdAt");// 降序排列
HashMap<String, Object> map = new HashMap<String, Object>();
JSONObject js = new JSONObject();
try {
	js.put("$gt", 100);
} catch (JSONException e1) {
}
map.put("_sumPlayScore", js);//过滤条件：总得分数大于100
query.having(map);
query.setLimit(100);
query.findStatistics(this, GameScore.class,new FindStatisticsListener() {

	@Override
	public void onSuccess(Object object) {
		// TODO Auto-generated method stub
		JSONArray ary = (JSONArray) object;
		if(ary!=null){
			int length = ary.length();
			try {
				for (int i = 0; i < length; i++) {
					JSONObject obj = ary.getJSONObject(i);
					int playscore = obj.getInt("_sumPlayScore");//过滤条件的key是什么，返回的数据中就有什么
					String game = obj.getString("game");        //返回的数据中同样包含groupby里面的列名
					showToast("游戏得分：" + playscore + ",游戏名 = "+ game);
				}
			} catch (JSONException e) {
				e.printStackTrace();
			}
		} else {
			showToast("查询成功，无数据");
		}
    }

	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("查询出错：code =" + ",msg = " + msg);
	}
});

```

### 缓存查询

缓存查询通常是将查询结果缓存在磁盘上。
当用户的设备处于离线状态时，就可以从缓存中获取数据来显示。
或者在应用界面刚刚启动，从网络获取数据还未得到结果时，先使用缓存数据来显示。
这样可以让用户不必在按下某个按钮后进行枯燥的等待。
默认的查询操作是没有启用缓存的，开发者可以使用`setCachePolicy`方法来启用缓存功能。
例如：优先从缓存获取数据，如果获取失败再从网络获取数据。

```java
bmobQuery.setCachePolicy(CachePolicy.CACHE_ELSE_NETWORK);	// 先从缓存获取数据，如果没有，再从网络获取。
bmobQuery.findObjects(this, new FindListener<Person>() {
	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		toast("查询成功：共"+object.size()+"条数据。");
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败："+msg);
	}
});
```

#### 缓存策略

Bmob SDK提供了几种不同的缓存策略，以适应不同应用场景的需求：

- `IGNORE_CACHE`     :只从网络获取数据，且不会将数据缓存在本地，这是默认的缓存策略。
- `CACHE_ONLY`        :只从缓存读取数据，如果缓存没有数据会导致一个BmobException,可以忽略不处理这个BmobException.
- `NETWORK_ONLY`      :只从网络获取数据，同时会在本地缓存数据。
- `NETWORK_ELSE_CACHE`:先从网络读取数据，如果没有，再从缓存中获取。
- `CACHE_ELSE_NETWORK`:先从缓存读取数据，如果没有，再从网络获取。
- `CACHE_THEN_NETWORK`:先从缓存取数据，无论结果如何都会再次从网络获取数据。也就是说会产生2次调用。

**建议的做法：**
**第一次进入应用的时候，设置其查询的缓存策略为`CACHE_ELSE_NETWORK`,当用户执行上拉或者下拉刷新操作时，设置查询的缓存策略为`NETWORK_ELSE_CACHE`。**

#### 缓存方法

如果需要操作缓存内容，可以使用BmobQuery提供的方法做如下操作：

- 检查是否存在当前查询条件的缓存数据

```java
boolean isInCache = query.hasCachedResult(context,Class<?> clazz);
```

**注：缓存和查询条件有关，此方法必须放在所有的查询条件（where、limit、order、skip、include等）都设置完之后，否则会得不到缓存数据。**

- 清除当前查询的缓存数据

```java
query.clearCachedResult(context,Class<?> clazz);
```

- 清除所有查询结果的缓存数据

```java
BmobQuery.clearAllCachedResults(this);
```

- 设置缓存的最长时间（以毫秒为单位）

```java
query.setMaxCacheAge(TimeUnit.DAYS.toMillis(1));//此表示缓存一天
```

示例如下：

```java
BmobQuery<Person> query	 = new BmobQuery<Person>();
query.addWhereEqualTo("age", 25);
query.setLimit(10);
query.order("createdAt");
//判断是否有缓存，该方法必须放在查询条件（如果有的话）都设置完之后再来调用才有效，就像这里一样。
boolean isCache = query.hasCachedResult(context,Person.class);
if(isCache){--此为举个例子，并不一定按这种方式来设置缓存策略
	query.setCachePolicy(CachePolicy.CACHE_ELSE_NETWORK);	// 如果有缓存的话，则设置策略为CACHE_ELSE_NETWORK
}else{
	query.setCachePolicy(CachePolicy.NETWORK_ELSE_CACHE);	// 如果没有缓存的话，则设置策略为NETWORK_ELSE_CACHE
}
query.findObjects(this, new FindListener<Person>() {

	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		Log.i("smile","查询个数："+object.size())
	}

	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("smile","查询失败："+object.size())
	}
});
```

**注：**
**1、只有当缓存查询的条件一模一样时才会获取到缓存到本地的缓存数据。**
**2、设置的默认的最大缓存时长为5小时。**

### BQL查询

`Bmob Query Language`（简称 BQL） 是 Bmob 自 `BmobSDKV3.3.7` 版本开始，为查询 API 定制的一套类似 SQL 查询语法的子集和变种，主要目的是降低大家学习 Bmob 查询API 的成本，可以使用传统的 SQL 语法来查询 Bmob 应用内的数据。 

具体的 BQL 语法，请参考 [Bmob Query Language 详细指南](/bql/index.html?menukey=otherdoc&key=bql)。

#### 基本BQL查询

可以通过以下方法来进行SQL查询：
例如：需要查询所有的游戏得分记录

```java
String bql ="select * from GameScore";//查询所有的游戏得分记录
new BmobQuery<GameScore>().doSQLQuery(context,bql,new SQLQueryListener<GameScore>(){
			
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		if(e ==null){
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list!=null && list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据返回");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```

上面的示例也等价于(`此方法自BmobV3.3.8版本提供`)：

```java
//查询所有的游戏得分记录
String bql ="select * from GameScore";
BmobQuery<GameScore> query=new BmobQuery<GameScore>();
//设置查询的SQL语句
query.setSQL(bql);
query.doSQLQuery(context,new SQLQueryListener<GameScore>(){
			
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		if(e ==null){
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list!=null && list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据返回");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```


如果需要查询个数，则可以这样：

```java
String bql = "select count(*),* from GameScore";//查询GameScore表中总记录数并返回所有记录信息
new BmobQuery<GameScore>().doSQLQuery(context,bql, new SQLQueryListener<GameScore>(){
			
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			int count = result.getCount();//这里得到符合条件的记录数
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```

注：当查询的表为系统表（目前系统表有User、Installation、Role）时，需要带上下划线 `_`。

比如，你想查询的是用户`smile`的信息，则：

```sql
select * from _User where username = smile
```

#### 统计BQL查询

由于统计查询的结果是不定的，故BQL提供了另外一种查询方法来进行统计查询，可以使用`doStatisticQuery`方法来进行：

```java
//按照姓名分组求和,并将结果按时间降序排列
String bql = "select sum(playScore) from GameScore group by name order by -createdAt";
new BmobQuery<GameScore>().doStatisticQuery(context, bql,new StatisticQueryListener(){
	
	@Override
	public void done(Object result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			JSONArray ary = (JSONArray) result;
			if(ary!=null){//开发者需要根据返回结果自行解析数据
				...
			}else{
				showToast("查询成功，无数据");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```

#### 占位符查询

在更多的时候，一个查询语句中间会有很多的值是可变值，为此，我们也提供了类似 Java JDBC 里的 `PreparedStatement` 使用占位符查询的语法结构。

##### 普通查询

```java
String bql="select * from GameScore where player = ? and game = ?";//查询玩家1的地铁跑酷的GameScore信息
new BmobQuery<GameScore>().doSQLQuery(context, bql,new SQLQueryListener<GameScore>(){
			
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list!=null && list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据返回");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
},"玩家1","地铁跑酷");
```

最后的可变参数 `玩家1` 和 `地铁跑酷` 会自动替换查询语句中的问号位置（按照问号的先后出现顺序）。

上面的示例也等价于如下代码（`此方法自BmobV3.3.8版本提供`）：

```java
String bql="select * from GameScore where player = ? and game = ?";
BmobQuery<GameScore> query=new BmobQuery<GameScore>();
//设置SQL语句
query.setSQL(bql);
//设置占位符参数
query.setPreparedParams(new Object[]{"玩家1","地铁跑酷"});
query.doSQLQuery(context,new SQLQueryListener<GameScore>(){
			
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list!=null && list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据返回");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```

##### 内置函数

对于包含`内置函数`的占位符查询，比较特殊，请使用`Bmob Query Language 详细指南`中的`内置函数`中[占位符查询用到的内置函数](/bql/index.html?menukey=otherdoc&key=bql#index_占位符查询用到的内置函数)列出的形式进行查询操作：

举例：我想查询当前用户在2015年5月12日之后，在特定地理位置附近的游戏记录，可以这样：

```java 
String sql = "select * from GameScore where createdAt > date(?) and player = pointer(?,?) and gps near geopoint(?,?)";
new BmobQuery<GameScore>().doSQLQuery(this, sql,new SQLQueryListener<GameScore>(){
	
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			List<GameScore> list = (List<GameScore>) result.getResults();
			if(list!=null && list.size()>0){
				...
			}else{
				Log.i("smile", "查询成功，无数据返回");
			}
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
},"2015-05-12 00:00:00","_User",user.getObjectId(),112.934755,24.52065);
```

**注**
**1、我们更推荐使用占位符语法，理论上会降低 BQL 转换的性能开销；**
**2、最后的可变参数会自动替换查询语句中的问号位置（按照问号的先后出现顺序），有多少个问号，最后的可变参数就应该有多少个；**
**3、同样的，统计查询也支持占位符,只需要将`doSQLQuery`替换成`doStatisticQuery`方法即可；**
**4、只有查询条件`where``limit`子句支持占位符查询，和统计查询有关的`group by`、`order by`、`having`等字句是不支持占位符的。**

例如：
`正确`查询：

```java
//按照游戏名进行分组并获取总得分数大于200的统计信息，同时统计各分组的记录数
String bql = "select sum(playScore),count(*) from GameScore group by game having _sumPlayScore>200";
new BmobQuery<GameScore>().doStatisticQuery(this, bql,new StatisticQueryListener(){
	
	@Override
	public void done(Object result, BmobException e) {
		// TODO Auto-generated method stub
		...
	}
});
```

`错误`查询：

```java
String bql = "select sum(playScore),count(*) from GameScore group by ? having ?";
new BmobQuery<GameScore>().doStatisticQuery(this, bql,new StatisticQueryListener(){
	
	@Override
	public void done(Object result, BmobException e) {
		// TODO Auto-generated method stub
		...
	}
},"game","_sumPlayScore>200");
```

#### BQL缓存查询

BQL查询同步支持`缓存查询`，只需要调用BmobQuery的`setCachePolicy`方法设置缓存策略即可，`建议使用如下方式进行BQL缓存查询`：

```java
String sql = "select * from GameScore order by playScore,signScore desc";
BmobQuery<GameScore> query = new BmobQuery<GameScore>();
//设置sql语句
query.setSQL(sql);
//判断此查询本地是否存在缓存数据
boolean isCache = query.hasCachedResult(this,GameScore.class);
if(isCache){
	query.setCachePolicy(CachePolicy.CACHE_ELSE_NETWORK);	// 如果有缓存的话，则设置策略为CACHE_ELSE_NETWORK
}else{
	query.setCachePolicy(CachePolicy.NETWORK_ELSE_CACHE);	// 如果没有缓存的话，则设置策略为NETWORK_ELSE_CACHE
}
query.doSQLQuery(this,new SQLQueryListener<GameScore>(){
	
	@Override
	public void done(BmobQueryResult<GameScore> result, BmobException e) {
		// TODO Auto-generated method stub
		if(e ==null){
			Log.i("smile", "查询到："+result.getResults().size()+"符合条件的数据");
		}else{
			Log.i("smile", "错误码："+e.getErrorCode()+"，错误描述："+e.getMessage());
		}
	}
});
```

**注：**
**doSQLQuery目前有三种查询方式进行SQL查询，分别是：**
**1、doSQLQuery（Context context,SQLQueryListener<T> listener)**
**2、doSQLQuery（Context context, String bql, SQLQueryListener<T> listener)----基本BQL查询**
**3、doSQLQuery（Context context, String bql, SQLQueryListener<T> listener,Object... params)----占位符查询**
**只有`第一种查询方式`才能和`query.hasCachedResult(context,class)`或者`query.clearCachedResult(context,class)`并列使用。**
**建议使用`第一种查询方式`进行BQL缓存查询。**

## 数组
对于数组类型数据，BmobSDK提供了3种操作来原子性地修改一个数组字段的值：

 - **add、addAll** 在一个数组字段的后面添加一些指定的对象(包装在一个数组内)
 - **addUnique、addAllUnique** 只会在原本数组字段中没有这些对象的情形下才会添加入数组，插入数组的位置随机
 - **removeAll** 从一个数组字段的值内移除指定数组中的所有对象

举例子：

```java
public class Person extends BmobObject {
	private List<String> hobbys;		// 爱好-对应服务端Array类型：String类型的集合
	private List<BankCard> cards;	    // 银行卡-对应服务端Array类型:Object类型的集合
	...
	getter、setter方法
}

其中BankCard类结构如下：
public class BankCard{
	private String cardNumber;
	private String bankName;
	public BankCard(String bankName, String cardNumber){
		this.bankName = bankName;
		this.cardNumber = cardNumber;
	}
	...
	getter、setter方法
}
```

### 添加数组数据

给`Person`对象中的数组类型字段添加数据,有以下两种方式：

#### 使用add、addAll添加

```java
Person p = new Person();
p.setObjectId("d32143db92");
//添加String类型的数组
p.add("hobbys", "唱歌");	                            // 添加单个String
//p.addAll("hobbys", Arrays.asList("游泳", "看书"));	// 添加多个String
//添加Object类型的数组
p.add("cards",new BankCard("工行卡", "工行卡账号"))   //添加单个Object
List<BankCard> cards =new ArrayList<BankCard>();
for(int i=0;i<2;i++){
	cards.add(new BankCard("建行卡"+i, "建行卡账号"+i));
}
//p.addAll("cards", cards);						    //添加多个Object值
p.save(this, new SaveListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","保存成功");
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","保存失败："+msg);
	}
});
```

** 注：此类方法不管这些数据之前是否已添加过，都会再次添加。**

#### 使用addUnique、addAllUnique添加

```java
Person p = new Person();
//添加String类型的数组
p.addUnique("hobbys", "唱歌");	                            // 添加单个String
//p.addAllUnique("hobbys", Arrays.asList("游泳", "看书"));	// 添加多个String
//添加Object类型的数组
p.addUnique("cards",new BankCard("工行卡", "工行卡账号"))     //添加单个Object
List<BankCard> cards =new ArrayList<BankCard>();
for(int i=0;i<2;i++){
	cards.add(new BankCard("建行卡"+i, "建行卡账号"+i));
}
//p.addAllUnique("cards", cards);						    //添加多个Object
p.update(this, "d32143db92",new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","添加爱好成功");
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","添加爱好失败："+msg);
	}
});
```

** 注： 只有在这些数据之前未添加过的情况下才会被添加。**

### 更新数组数据

数组更新比较特殊，自`V3.4.4`版本开始提供`BmobObject`的`setValue`方法来更新数组，例：

```java
Person p2 = new Person();
//更新String类型数组中的值
p2.setValue("hobbys.0","爬山");                             //将hobbys中第一个位置的爱好（上面添加成功的唱歌）修改为爬山
//更新Object类型数组中的某个位置的对象值(0对应集合中第一个元素)
p2.setValue("cards.0", new BankCard("中行", "中行卡号"));    //将cards中第一个位置银行卡修改为指定BankCard对象
//更新Object类型数组中指定对象的指定字段的值
//	p2.setValue("cards.0.bankName", "农行卡");				//将cards中第一个位置的银行卡名称修改为农行卡
//	p2.setValue("cards.1.cardNumber", "农行卡账号");			//将cards中第二个位置的银行卡账号修改为农行卡账号
p2.update(this, objectId, new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","更新成功：" + p2.getUpdatedAt());
	}

	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","更新失败：" + msg);
	}
});
```

### 删除数组数据

同理我们也可以使用removeAll从数组字段中移除某些值：

```java
Person p = new Person();
p.removeAll("hobby", Arrays.asList("阅读","唱歌","游泳"));
p.update(this, new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("bmob","从hobby字段中移除阅读、唱歌、游泳成功");
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","从hobby字段中移除阅读、唱歌、游泳失败："+msg);
	}
});
```

### 查询数组数据

对于字段类型为数组的情况，可以以数组字段中包含有xxx的数据为条件进行查询：

```java
BmobQuery<Person> query = new BmobQuery<Person>();
String [] hobby = {"阅读","唱歌"};
query.addWhereContainsAll("hobby", Arrays.asList(hobby));
query.findObjects(this, new FindListener<Person>() {

	@Override
	public void onSuccess(List<Person> object) {
		// TODO Auto-generated method stub
		Log.i("bmob","查询成功：共" + object.size() + "条数据。");
	}

	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","查询失败：" + code);
	}
	
});
```

## 数据关联性

### 关联关系描述

在程序设计中，不同类型的数据之间可能存在某种关系。
比如：帖子和作者的关系，一篇帖子只属于某个作者，这是`一对一的关系`。
比如：帖子和评论的关系，一条评论只属于某一篇帖子，而一篇帖子对应有很多条评论，这是`一对多的关系`。
比如：学生和课程的关系，一个学生可以选择很多课程，一个课程也可以被很多学生所选择，这是`多对多的关系`。

Bmob提供了`Pointer（一对一、一对多）`和`Relation（多对多）`两种数据类型来解决这种业务需求。

#### 关联关系案例详解
由于关联关系讲解起来比较复杂，以下用一个简单的案例来说明在Bmob中是如何使用关联关系的。

场景：**用户发表帖子，同时又可对帖子进行评论留言。**

在这个场景中涉及到三个表：用户表（`_User`）、帖子表（`Post`）、评论表（`Comment`）,以下是各个表的字段：

`_User`字段如下：
<table>
<tr><th> 字段 </td><th>类型</th><th>  含义</th></tr>
<tr><td> objectId </td><td>  String</td><td>  用户ID</td></tr>
<tr><td> username </td><td>  String</td><td>  用户名(可以既发帖子又发评论)</td></tr>
<tr><td> age </td><td>  Integer</td><td>  年龄</td></tr>
</table>
----------

`Post`字段如下：
<table>
<tr><th> 字段 </td><th>  含义</th><th>  类型</th></tr>
<tr><td> objectId </td><td>  String</td><td> 帖子ID </td></tr>
<tr><td> title </td><td>  String</td><td> 帖子标题 </td></tr>
<tr><td> content </td><td>  String</td><td> 帖子内容 </td></tr>
<tr><td> author </td><td>  Pointer<_User></td><td> 帖子作者 </td></tr>
<tr><td> likes </td><td>  Relation<_User></td><td> 喜欢帖子的读者 </td></tr>
</table>
----------

`Comment`字段如下：
<table>
<tr><th> 字段 </td><th>  含义</th><th>  类型</th></tr>
<tr><td> objectId </td><td>  String</td><td> 评论ID </td></tr>
<tr><td> content </td><td>  String</td><td> 评论内容 </td></tr>
<tr><td> post </td><td>   Pointer<Post></td><td> 评论对应的帖子 </td></tr>
<tr><td> author </td><td>  Pointer<_User></td><td> 评论该帖子的人 </td></tr>
</table>
----------

#### Web端创建关联字段
如果你需要在Web端创建上述表的话，那么当选择的字段类型为`Pointer或Relation`时，会提示你选择该字段所指向或关联的数据表。

如下图所示：

![图1 创建关联字段](image/createline.png)

#### 创建数据对象

```java
public class MyUser extends BmobUser {
	
	private Integer age;//为用户表新增一个age字段，注意其必须为`Integer`类型，而不是int

	//自行实现getter和setter方法
}

```

**1、扩展BmobUser的时，不需要再加上`objectId、username、password、createAt、updateAt`等系统字段，因为BmobUser中已经实现了，如果再次声明的话，会导致编译性的错误。**

**2、类名可以自定义，这个跟其他表的命名方式有所不同。**

```java

public class Post extends BmobObject {

	private String title;//帖子标题

	private String content;// 帖子内容

	private MyUser author;//帖子的发布者，这里体现的是一对一的关系，该帖子属于某个用户

	private BmobFile image;//帖子图片

	private BmobRelation likes;//多对多关系：用于存储喜欢该帖子的所有用户

	//自行实现getter和setter方法
	
}

```

```java

public class Comment extends BmobObject {

	private String content;//评论内容  

	private MyUser user;//评论的用户，Pointer类型，一对一关系
	
	private Post post; //所评论的帖子，这里体现的是一对多的关系，一个评论只能属于一个微博

	//自行实现getter和setter方法
}

```

**注：**

**1、类名要和数据表名保持一致。**

**2、MyUser属性对应为Pointer的指针类型。**

以下举例均假定A用户已注册并登陆

![图1](image/userA.png)

### 一对一关系

**用户发表帖子，一篇帖子也只能属于某个用户，那么帖子和用户之间的关系是`一对一关系`，建议使用`Pointer`类型来表示。**

`Pointer`本质上可以看成是我们将一个指向某条记录的指针记录下来，我们查询时可以通过该指针来获得其指向的关联对象。

用户A写了一篇帖子，需要在`Post`表中生成一条记录，并将该帖子关联到用户A这条记录，表明该帖子是A所发表的。

示例如下：

#### 添加一对一关联

```java
MyUser user = BmobUser.getCurrentUser(this, MyUser.class);
// 创建帖子信息
Post post = new Post();
post.setContent(content);
//添加一对一关联
post.setAuthor(user);
post.save(this, new SaveListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		...
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		...
	}
});

```

添加成功后，在后台的`Post`表中，你就会看到有一条记录生成，并且该帖子的`author`字段的值指向了`_User`表中的`用户A`这条记录。

![图1](image/post1.png)

#### 查询一对一关联
如果想查询`用户A`(当前用户)所发表的所有帖子，那么可以这样：

```java
MyUser user = BmobUser.getCurrentUser(this, MyUser.class);
BmobQuery<Post> query = new BmobQuery<Post>();
query.addWhereEqualTo("author", user);	// 查询当前用户的所有帖子
query.order("-updatedAt");
query.include("author");// 希望在查询帖子信息的同时也把发布人的信息查询出来
query.findObjects(this, new FindListener<Post>() {
	@Override
	public void onSuccess(List<Post> object) {
		// TODO Auto-generated method stub
		...
	}
	
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败:"+msg);
	}
});

```

**注：如果想查询某个用户所发表的所有帖子，则将该用户查询出来，然后使用上述代码查询指定用户所发表的所有帖子。**

#### 更新一对一关联

如果希望将刚刚这条帖子的作者修改成`用户B`,示例：

```java
Post p = new Post();
//构造用户B，如果你知道用户B的objectId的话，可以使用这种方式进行关联，如果不知道的话，你需要将用户B查询出来
// 这里假设已知用户B的objectId为aJyG2224
MyUser userB =new MyUser();
userB.setObjectId("aJyG2224");
p.setAuthor(userB);//重新设置帖子作者
p.update(WeiboListActivity.this, "ESIt3334", new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		
	}
});

```

修改成功后，在后台可查看到这个帖子的作者已经变更为用户B

![图1](image/postupdate.png)

#### 删除一对一关联
如果你想和`ESIt3334`这个帖子解除关联关系，可以这样：

```java
Post p = new Post();
p.remove("author");
p.update(WeiboListActivity.this, "ESIt3334", new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		...
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		
	}
});

```

删除成功后，在后台的`Post`表中，你就会看到`ESIt3334`这个帖子的`author`字段的值已经被置空了。

![图1](image/postdelete.png)

### 一对多关系
**一条评论只能属于某一篇帖子，一篇帖子可以有很多用户对其进行评论，那么帖子和评论之间的关系就是`一对多关系`，推荐使用`pointer`类型来表示**。

因为使用方法和上面的一对一关联基本相同，只是查询一对多关联的时候有些区别，故只举添加和查询两个例子：


#### 添加一对多关联
将评论和微博进行关联，并同时和当前用户进行关联，表明是当前用户对该帖子进行评论，示例如下：

```java
MyUser user = BmobUser.getCurrentUser(this, MyUser.class);
Post post = new Post();
post.setObjectId("ESIt3334");
final Comment comment = new Comment();
comment.setContent(content);
comment.setPost(post);
comment.setUser(user);
comment.save(this, new SaveListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("life","评论发表成功");
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("life","评论失败");
	}
});

```

#### 查询一对多关联

我想`查询出某个帖子（objectId为ESIt3334）的所有评论,同时将该评论的作者的信息也查询出来`，那么可以使用`addWhereEqualTo`方法进行查询：

```java
BmobQuery<Comment> query = new BmobQuery<Comment>();
//用此方式可以构造一个BmobPointer对象。只需要设置objectId就行
Post post = new Post();
post.setObjectId("ESIt3334");
query.addWhereEqualTo("post",new BmobPointer(post));		
//希望同时查询该评论的发布者的信息，以及该帖子的作者的信息，这里用到上面`include`的并列对象查询和内嵌对象的查询
query.include("user,post.author");
query.findObjects(this, new FindListener<Comment>() {
	
	@Override
	public void onSuccess(List<Comment> object) {
		// TODO Auto-generated method stub
		...
	}
	
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		...
	}
});

```

注：`addWhereEqualTo`对`BmobPonter`类型的一对多的关联查询是`BmobSDKV3.3.8`开始支持的，因此使用时，请更新SDK版本。


### 多对多关系

**一个帖子可以被很多用户所喜欢，一个用户也可能会喜欢很多帖子，那么可以使用`Relation`类型来表示这种`多对多关联关系`**。

`Relation`本质上可以理解为其存储的是一个对象，而这个对象中存储的是多个指向其它记录的指针。

#### 添加多对多关联

```java
MyUser user = BmobUser.getCurrentUser(this, MyUser.class);
Post post = new Post();
post.setObjectId("ESIt3334");
//将当前用户添加到Post表中的likes字段值中，表明当前用户喜欢该帖子
BmobRelation relation = new BmobRelation();
//将当前用户添加到多对多关联中
relation.add(user);
//多对多关联指向`post`的`likes`字段
post.setLikes(relation);
post.update(this, new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("life","多对多关联添加成功");
	}
	
	@Override
	public void onFailure(int arg0, String arg1) {
		// TODO Auto-generated method stub
		Log.i("life","多对多关联添加失败");
	}
});

```
添加成功后，在后台的`Post`表中就能查看到`likes`字段已经生成并对应到了`_User`

![图1](image/relation.png)

点击红框中的`关联关系`按钮展开后，可查看刚才所添加的喜欢该帖子的用户A：

![图1](image/likes.png)


#### 查询多对多关联

如果希望`查询喜欢该帖子（objectId为ESIt3334）的所有用户`,那么就需要用到`addWhereRelatedTo`方法进行多对多关联查询。

示例代码：

```java
// 查询喜欢这个帖子的所有用户，因此查询的是用户表
BmobQuery<MyUser> query = new BmobQuery<MyUser>();
Post post = new Post();
post.setObjectId("ESIt3334");
//likes是Post表中的字段，用来存储所有喜欢该帖子的用户
query.addWhereRelatedTo("likes", new BmobPointer(post));	
query.findObjects(this, new FindListener<MyUser>() {
	
	@Override
	public void onSuccess(List<MyUser> object) {
		// TODO Auto-generated method stub
		Log.i("life", "查询个数："+object.size());
	}
	
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("life", "查询失败："+code+"-"+msg);
	}
});

```

#### 修改多对多关联

如果`用户B也喜欢该帖子（objectId为ESIt3334）`，此时需要为该帖子(Post)的`likes`字段多添加一个用户,示例如下：

```java
Post post = new Post();
post.setObjectId("ESIt3334");
//将用户B添加到Post表中的likes字段值中，表明用户B喜欢该帖子
BmobRelation relation = new BmobRelation();
//构造用户B
MyUser user = new MyUser();
user.setObjectId("aJyG2224");
//将用户B添加到多对多关联中
relation.add(user);
//多对多关联指向`post`的`likes`字段
post.setLikes(relation);
post.update(this, new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("life","用户B和该帖子关联成功");
	}
	
	@Override
	public void onFailure(int arg0, String arg1) {
		// TODO Auto-generated method stub
		Log.i("life","用户B和该帖子关联失败");
	}
});

```

修改成功后，你在点击该帖子的`likes`字段下面的`关联关系`按钮展开后，可查看刚才所添加的喜欢该帖子的用户B：

![图1](image/updaterelation.png)


#### 删除多对多关联


如果`想对该帖子进行取消喜欢的操作`，此时，需要删除之前的多对多关联，具体代码：

```java
Post post = new Post();
post.setObjectId("83ce274594");
MyUser user = BmobUser.getCurrentUser(this, MyUser.class);
BmobRelation relation = new BmobRelation();
relation.remove(user);
post.setLikes(relation);
post.update(this, new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("life", "关联关系删除成功");
	}
	
	@Override
	public void onFailure(int arg0, String arg1) {
		// TODO Auto-generated method stub
		Log.i("life", "关联关系删除失败："+arg0+"-"+arg1);
	}
});

```

### include用法


在某些情况下，你想在一个查询内获取`Pointer`类型的关联对象。

比如上述示例中，如果希望在查询帖子信息的同时也把该帖子的作者的信息查询出来，可以使用`include`方法

```java
query.include("author");
```

你可以使用`,`号(逗号)操作符来`include并列查询`两个对象

比如，查询评论表的同时将该评论用户的信息和所评论的帖子信息也一并查询出来（因为Comment表有两个`Pointer类型`的字段），那么可以这样做：

```java
query.include("user,post");
```

但不能如下的做法：

```java
query.include("user");
query.include("post");
```

你同时还可以使用 `. `号（英语句号）操作符来进行`include中的内嵌对象查询`


比如，你想在查询评论信息的同时将该评论`Comment`对应的帖子`post`以及该帖子的作者信息`author`一并查询出来，你可以这样做：

```java
query.include("post.author");
```

另外，include还可以指定返回的字段：

```java
query.include("post[likes].author[username|email]");
```
其中，post和author都是Pointer类型，post指向的表只返回likes字段，author指向的表只返回username和email字段。


**注：include的查询对象只能为BmobPointer类型，而不能是BmobRelation类型。**


### 内部查询


如果你在查询某个对象列表时，它们的某个字段是BmobObject类型，并且这个BmobObject匹配一个不同的查询，这种情况下可使用`addWhereMatchesQuery`方法。

请注意，默认的 limit 限制 100 也同样作用在内部查询上。因此如果是大规模的数据查询，你可能需要仔细构造你的查询对象来获取想要的行为。

例如：**查询带有图片的帖子的评论列表**:

```java
BmobQuery<Comment> query = new BmobQuery<Comment>();
BmobQuery<Post> innerQuery = new BmobQuery<Post>();
innerQuery.addWhereExists("image", true);
// 第一个参数为评论表中的帖子字段名post
// 第二个参数为Post字段的表名，也可以直接用"Post"字符串的形式
// 第三个参数为内部查询条件
query.addWhereMatchesQuery("post", "Post", innerQuery);
query.findObjects(this, new FindListener<Comment>() {
	@Override
	public void onSuccess(List<Comment> object) {
		// TODO Auto-generated method stub
		toast("查询成功:");
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败:"+msg);
	}
});
```

反之，不想匹配某个子查询，你可以使用`addWhereDoesNotMatchQuery`方法。 

比如**查询不带图片的帖子的评论列表**：

```java
BmobQuery<Comment> query = new BmobQuery<Comment>();
BmobQuery<Post> innerQuery = new BmobQuery<Post>();
innerQuery.addWhereExists("image", true);
// 第一个参数为评论表中的帖子字段名post
// 第二个参数为Post字段的表名，也可以直接用"Post"字符串的形式
// 第三个参数为内部查询条件
query.addWhereDoesNotMatchQuery("post", "Post", innerQuery);
query.findObjects(this, new FindListener<Comment>() {
	@Override
	public void onSuccess(List<Comment> object) {
		// TODO Auto-generated method stub
		toast("查询成功:");
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败:"+msg);
	}
});
```

**注：**

`当查询的表为系统表（目前系统表有User、Installation、Role）时，需要带上下划线 `_`。`

比如，你想查询出用户`smile`和`smile`好友的所有帖子，则可以这样：

```sql

BmobQuery<User> innerQuery = new BmobQuery<User>();
String[] friendIds={"ssss","aaaa"};//好友的objectId数组
innerQuery.addWhereContainedIn("objectId", Arrays.asList(friendIds));
//查询帖子
BmobQuery<Post> query = new BmobQuery<Post>();
`query.addWhereMatchesQuery("author", "_User", innerQuery);`
query.findObjects(this, new FindListener<Post>() {
	@Override
	public void onSuccess(List<Post> object) {
		// TODO Auto-generated method stub
		toast("查询成功:");
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败:"+msg);
	}
});
```

## 用户管理

用户是一个应用程序的核心。对于个人开发者来说，自己的应用程序积累到越多的用户，就会给自己带来越强的创作动力。因此Bmob提供了一个专门的用户类——BmobUser来自动处理用户账户管理所需的功能。

有了这个类，你就可以在你的应用程序中添加用户账户功能。

BmobUser是BmobObject的一个子类，它继承了BmobObject所有的方法，具有BmobObject相同的功能。不同的是，BmobUser增加了一些特定的关于用户账户管理相关的功能。

### 属性
BmobUser除了从BmobObject继承的属性外，还有几个特定的属性：
username: 用户的用户名`（必需）`。
password: 用户的密码`（必需）`。
email: 用户的电子邮件地址`（可选）`。
emailVerified:邮箱认证状态`（可选）`。
mobilePhoneNumber：手机号码`（可选）`。
mobilePhoneNumberVerified：手机号码的认证状态`（可选）`。

### 扩展用户类

很多时候，你的用户表还会有很多其他字段，如性别、年龄、头像等。那么，你需要对BmobUser类进行扩展，添加一些新的属性。示例代码如下所示：

```java
public class MyUser extends BmobUser {
	
	private Boolean sex;
	private String nick;
	private Integer age;

	public boolean getSex() {
		return this.sex;
	}

	public void setSex(boolean sex) {
		this.sex = sex;
	}

	public String getNick() {
		return this.nick;
	}

	public void setNick(String nick) {
		this.nick = nick;
	}

	public Integer getAge() {
		return age;
	}

	public void setAge(Integer age) {
		this.age = age;
	}
}
```

更多代码实现大家可以下载SDK，在里面的`BmobExample`中查找`MyUser`类，参考它的用法。

### 创建用户

创建用户对象如下：
```java
BmobUser user = new BmobUser();
```

### 注册

你的应用程序可能会要求用户注册。下面的代码是一个典型的注册过程：
```java
BmobUser bu = new BmobUser();
bu.setUsername("sendi");
bu.setPassword("123456");
bu.setEmail("sendi@163.com");
//注意：不能用save方法进行注册
bu.signUp(context, new SaveListener() {
	@Override
	public void onSuccess() {
		toast("注册成功:");
		//通过BmobUser.getCurrentUser(context)方法获取登录成功后的本地用户信息
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("注册失败:"+msg);
	}
});
```

在注册过程中，服务器会对注册用户信息进行检查，以确保注册的用户名和电子邮件地址是独一无二的。此外，对于用户的密码，你可以在应用程序中进行相应的加密处理后提交。

如果注册不成功，你可以查看返回的错误对象。最有可能的情况是，用户名或电子邮件已经被另一个用户注册。这种情况你可以提示用户，要求他们尝试使用不同的用户名进行注册。

你也可以要求用户使用Email做为用户名注册，这样做的好处是，你在提交信息的时候可以将输入的“用户名“默认设置为用户的Email地址，以后在用户忘记密码的情况下可以使用Bmob提供重置密码功能。

**注：**

- 有些时候你可能需要在用户注册时发送一封验证邮件，以确认用户邮箱的真实性。这时，你只需要登录自己的应用管理后台，在应用设置->邮件设置（下图）中把“邮箱验证”功能打开，Bmob云后端就会在注册时自动发动一封验证给用户。

![](image/email_verify.png)

- username字段是大小写敏感的字段，如果你希望应用的用户名不区分大小写，请在注册和登录时进行大小写的统一转换。

### 登录

当用户注册成功后，您需要让他们以后能够用注册的用户名登录到他们的账户使用应用。要做到这一点，你可以使用BmobUser类的login方法。
```java
BmobUser bu2 = new BmobUser();
bu2.setUsername("lucky");
bu2.setPassword("123456");
bu2.login(context, new SaveListener() {
	@Override
	public void onSuccess() {
		toast("登录成功:");
		//通过BmobUser user = BmobUser.getCurrentUser(context)获取登录成功后的本地用户信息
		//如果是自定义用户对象MyUser，可通过MyUser user = BmobUser.getCurrentUser(context,MyUser.class)获取自定义用户信息
	}
	@Override
	public void onFailure(int code, String msg) {
		toast("登录失败:"+msg);
	}
});
```

也可使用如下方式完成`用户名+密码`的登录：

```java
BmobUser.loginByAccount(context, "username", "用户密码", new LogInListener<MyUser>() {
			
			@Override
			public void done(MyUser user, BmobException e) {
				if(user!=null){
					Log.i("smile","用户登陆成功");
				}
			}
		});

```

### 当前用户

如果用户在每次打开你的应用程序时都要登录，这将会直接影响到你应用的用户体验。为了避免这种情况，你可以使用缓存的CurrentUser对象。

每当你应用的用户注册成功或是第一次登录成功，都会在本地磁盘中有一个缓存的用户对象，这样，你可以通过获取这个缓存的用户对象来进行登录：
```java
BmobUser bmobUser = BmobUser.getCurrentUser(context);
if(bmobUser != null){
	// 允许用户使用应用
}else{
	//缓存用户对象为空时， 可打开用户注册界面…
}
```

在扩展了用户类的情况下获取当前登录用户，可以使用如下的示例代码（`MyUser`类可参看上面）：

```java
MyUser userInfo = BmobUser.getCurrentUser(context,MyUser.class);

```

**自`V3.4.5`版本开始，SDK新增了`getObjectByKey(context,key)`方法从本地缓存中获取当前登陆用户某一列的值。其中`key`为用户表的指定列名。**

```java 
//BmobUser中的特定属性
String username = (String) BmobUser.getObjectByKey(context, "username");
//MyUser中的扩展属性
Integer age = (Integer) BmobUser.getObjectByKey(context, "age");
Boolean sex = (Boolean) BmobUser.getObjectByKey(context, "sex");
```

### 更新用户

很多情况下你可能需要修改用户信息，比如你的应用具备修改个人资料的功能，Bmob提供的用户更新方式有两种写法：

第一种：`新建一个用户对象，并调用update(context,objectId,updateListener)方法来更新（推荐使用）`，示例：

```java
BmobUser newUser = new BmobUser();
newUser.setEmail("xxx@163.com");
BmobUser bmobUser = BmobUser.getCurrentUser(context);
newUser.update(context,bmobUser.getObjectId(),new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("更新用户信息成功:");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("更新用户信息失败:" + msg);
	}
});
```

第二种：获取本地的用户对象，并调用update（context,updateListener）方法来更新（`不推荐使用`），示例：

```java
BmobUser bmobUser = BmobUser.getCurrentUser(context);
// 修改用户的邮箱为xxx@163.com
bmobUser.setEmail("xxx@163.com");
bmobUser.update(context,new UpdateListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("更新用户信息成功:");
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("更新用户信息失败:" + msg);
	}
});
```


**1、开发者在进行用户更新操作的时候，推荐使用`第一种`方式来进行用户的更新操作,因为此方法只会更新你提交的用户信息（比如只会向服务器提交当前用户的email值），而不会将本地存储的用户信息也提交到后台更新。**

**2、在更新用户信息时，如果用户邮箱有变更并且在管理后台打开了邮箱验证选项的话，Bmob云后端同样会自动发一封邮件验证信息给用户。**

### 查询用户
查询用户和查询普通对象一样，只需指定BmobUser类即可，如下：
```java
BmobQuery<BmobUser> query = new BmobQuery<BmobUser>();
query.addWhereEqualTo("username", "lucky");
query.findObjects(context, new FindListener<BmobUser>() {
	@Override
	public void onSuccess(List<BmobUser> object) {
		// TODO Auto-generated method stub
		toast("查询用户成功："+object.size());
	}
	@Override
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询用户失败："+msg);
	}
});
```
浏览器中查看用户表

User表是一个特殊的表，专门存储BmobUser对象。在浏览器端，你会看到一个User表旁边有一个小人的图标。

![](image/create_table.png)

### 退出登录
退出登录非常简单，可以使用如下的代码：

```java
BmobUser.logOut(context);   //清除缓存用户对象
BmobUser currentUser = BmobUser.getCurrentUser(context); // 现在的currentUser是null了
```

### 密码修改
自`V3.4.3`版本开始，SDK为开发者提供了直接修改当前用户登录密码的方法，只需要传入旧密码和新密码，然后调用`BmobUser`提供的静态方法`updateCurrentUserPassword`即可，以下是示例：

```java
BmobUser.updateCurrentUserPassword(context, "旧密码", "新密码", new UpdateListener() {
			
			@Override
			public void onSuccess() {
				// TODO Auto-generated method stub
				Log.i("smile", "密码修改成功，可以用新密码进行登录啦");
			}
			
			@Override
			public void onFailure(int code, String msg) {
				// TODO Auto-generated method stub
				Log.i("smile", "密码修改失败："+msg+"("+code+")");
			}
		});

```

### 邮箱

#### 邮箱登录
新增`邮箱+密码`登录方式,可以通过`loginByAccount`方法来操作：

```java 
BmobUser.loginByAccount(context, account, password, new LogInListener<MyUser>() {
			
			@Override
			public void done(MyUser user, BmobException e) {
				// TODO Auto-generated method stub
				if(user!=null){
					Log.i("smile","用户登陆成功");
				}
			}
		});

```

#### 邮箱验证
设置邮件验证是一个可选的应用设置, 这样可以对已经确认过邮件的用户提供一部分保留的体验，邮件验证功能会在用户(User)对象中加入emailVerified字段, 当一个用户的邮件被新添加或者修改过的话，emailVerified会被默认设为false，如果应用设置中开启了邮箱认证功能，Bmob会对用户填写的邮箱发送一个链接, 这个链接可以把emailVerified设置为 true.

emailVerified 字段有 3 种状态可以考虑：

 - true : 用户可以点击邮件中的链接通过Bmob来验证地址，一个用户永远不会在新创建这个值的时候显示emailVerified为true。
 - false : 用户(User)对象最后一次被刷新的时候, 用户并没有确认过他的邮箱地址, 如果你看到emailVerified为false的话，你可以考虑刷新用户(User)对象。
 - missing : 用户(User)对象已经被创建，但应用设置并没有开启邮件验证功能； 或者用户(User)对象没有email邮箱。

##### 请求验证Email
发送给用户的邮箱验证邮件会在一周内失效，可以通过调用 `requestEmailVerify` 来强制重新发送：
```java
final String email = "xxx@qq.com";
BmobUser.requestEmailVerify(context, email, new EmailVerifyListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("请求验证邮件成功，请到" + email + "邮箱中进行激活。");
	}
	@Override
	public void onFailure(int code, String e) {
		// TODO Auto-generated method stub
		toast("请求验证邮件失败:" + e);
	}
});
```

#### 邮箱重置密码
开发者只需要求用户输入注册时的电子邮件地址即可：
```java
final String email = "xxx@163.com";
BmobUser.resetPasswordByEmail(context, email, new ResetPasswordListener() {
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("重置密码请求成功，请到" + email + "邮箱进行密码重置操作");
	}
	@Override
	public void onFailure(int code, String e) {
		// TODO Auto-generated method stub
		toast("重置密码失败:" + e);
	}
});
```

邮箱重置密码的流程如下：

1. 用户输入他们的电子邮件，请求重置自己的密码。
2. Bmob向他们的邮箱发送一封包含特殊的密码重置链接的电子邮件。
3. 用户根据向导点击重置密码连接，打开一个特殊的Bmob页面，根据提示他们可以输入一个新的密码。
4. 用户的密码已被重置为新输入的密码。


### 手机号相关功能

#### 手机号码登录

在手机号码被验证后，用户可以使用该手机号码进行登录操作。

手机号码登录包括两种方式：`手机号码＋密码`、`手机号码＋短信验证码`。

##### 手机号码+密码

```java 
BmobUser.loginByAccount(context, "11位手机号码", "用户密码", new LogInListener<MyUser>() {
			
	@Override
	public void done(MyUser user, BmobException e) {
		// TODO Auto-generated method stub
		if(user!=null){
			Log.i("smile","用户登陆成功");
		}
	}
});

```


##### 手机号码+短信验证码

先请求登录的短信验证码：

```java
BmobSMS.requestSMSCode(context, "11位手机号码","模板名称", new RequestSMSCodeListener() {
			
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//验证码发送成功
			Log.i("smile", "短信id："+smsId);//用于后续的查询本次短信发送状态
		}
	}
});

```

最后调用`loginBySMSCode`方法进行手机号码登录:

```java
BmobUser.loginBySMSCode(context, "11位手机号码", code, new LogInListener<MyUser>() {
				
		@Override
		public void done(MyUser user, BmobException e) {
			// TODO Auto-generated method stub
			if(user!=null){
				Log.i("smile","用户登陆成功");
			}
		}
	});
}

```

#### 手机号码一键注册或登录

Bmob同样支持手机号码一键注册或登录，以下是一键登录的流程：


1、请求登录操作的短信验证码：

```java
BmobSMS.requestSMSCode(context, "11位手机号码","模板名称", new RequestSMSCodeListener() {
			
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//验证码发送成功
			Log.i("smile", "短信id："+smsId);//用于查询本次短信发送详情
		}
	}
	});

```

2、用户收到短信验证码之后，就可以调用`signOrLoginByMobilePhone`方法来实现一键登录:

```java
BmobUser.signOrLoginByMobilePhone(context, "11位手机号码", "验证码", new LogInListener<MyUser>() {
				
	@Override
	public void done(MyUser user, BmobException e) {
		// TODO Auto-generated method stub
		if(user!=null){
			Log.i("smile","用户登陆成功");
		}
	}
});

```

如果，你想在一键注册或登录的同时保存其他字段的数据的时，你可以使用`signOrLogin`方法（此方法`V3.4.3`版本提供）。

比如，你想在手机号码注册或登录的同时，设置用户名及登录密码等信息，那么具体示例如下：

```java 

MyUser user = new MyUser();
user.setMobilePhoneNumber("11位手机号码");//设置手机号码（必填）
user.setUsername(xxx);                  //设置用户名，如果没有传用户名，则默认为手机号码
user.setPassword(xxx);                  //设置用户密码
user.setAge(18);	                    //设置额外信息：此处为年龄
user.signOrLogin(context, "验证码", new SaveListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		toast("注册或登录成功");
		Log.i("smile", ""+user.getUsername()+"-"+user.getAge()+"-"+user.getObjectId());
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("错误码："+code+",错误原因："+msg);
	}
});

```

#### 绑定手机号码
如果已有用户系统，需要为用户绑定手机号，那么官方推荐的绑定流程如下：

第一步、先发送短信验证码并验证验证码的有效性,即调用`requestSMSCode`发送短信验证码，调用`verifySmsCode`来验证有效性。

第二步、在验证成功之后更新当前用户的`MobilePhoneNumber`和`MobilePhoneNumberVerified`两个字段，具体绑定示例如下：

```java 
User user =new User();
user.setMobilePhoneNumber(phone);
user.setMobilePhoneNumberVerified(true);
User cur = BmobUser.getCurrentUser(context,User.class);
user.update(context, cur.getObjectId(),new UpdateListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		Log.i("smile","手机号码绑定成功");
	}
	
	@Override
	public void onFailure(int arg0, String arg1) {
		// TODO Auto-generated method stub
		Log.i("smile","手机号码绑定失败："+arg0+"-"+arg1);
	}
});

```

#### 手机号码重置密码
Bmob自`V3.3.9`版本开始引入了短信验证系统，如果用户已经验证过手机号码或者使用过手机号码注册或登录过，也可以通过手机号码来重置用户密码，以下是官方建议使用的重置流程：

1、请求重置密码操作的短信验证码：

```java
BmobSMS.requestSMSCode(context, "11位手机号码","模板名称", new RequestSMSCodeListener() {
			
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//验证码发送成功
			Log.i("smile", "短信id："+smsId);//用于查询本次短信发送详情
		}
	}
	});

```

2、用户收到重置密码的验证码之后，就可以调用`resetPasswordBySMSCode`方法来实现密码重置:

```java
BmobUser.resetPasswordBySMSCode(context, code,"1234567", new ResetPasswordByCodeListener() {
				
	@Override
	public void done(BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){
			Log.i("smile", "密码重置成功");
		}else{
			Log.i("smile", "重置失败：code ="+ex.getErrorCode()+",msg = "+ex.getLocalizedMessage());
		}
	}
});

```

重置成功以后，用户就可以使用新密码登陆了。

**注：**

**1、请开发者按照官方推荐的操作流程来完成重置密码操作。也就是说，开发者在进行重置密码操作时，无需调用`verifySmsCode`接口去验证该验证码的有效性。**

**2、验证码只能使用一次，一旦该验证码被使用就会失效，那么再拿失效的验证码去调用重置密码接口，一定会报`207-验证码错误`。因为重置密码接口已经包含验证码的有效性验证。**

#### 手机号码验证

##### 请求发送短信验证码

Bmob自`V3.3.9`版本开始引入了短信验证系统，可通过`requestSMSCode`方式请求发送短信验证码：

```java
BmobSMS.requestSMSCode(context, "11位手机号码", "模板名称",new RequestSMSCodeListener() {
			
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//验证码发送成功
			Log.i("smile", "短信id："+smsId);//用于查询本次短信发送详情
		}
	}
});

```

短信默认模板：

```java 

	您的验证码是`%smscode%`，有效期为`%ttl%`分钟。您正在使用`%appname%`的验证码。【比目科技】

```


**注：**

1、`模板名称`：模板名称需要开发者在应用的管理后台进行短信模板的添加工作，具体：`短信服务`->`短信模板`,之后点击创建即可。


具体请看下图：

![](image/sms.png)


2、只有审核通过之后的自定义短信模板才可以被使用，如果自定义的短信模板其状态显示`审核中`或者`审核失败`,再调用该方法则会以`默认模板`来发送验证码。

**3、开发者提交短信验证码模板时需注意以下几点：**

**1）、模板中不能有【】和 [] ，否则审核不通过；**

**2）、如果你提交的短信模板无法发送，则有可能包含一些敏感监控词，具体可去Github下载  [短信关键字监控参考文档](https://github.com/bmob/bmob-public-docs/blob/master/%E7%9F%AD%E4%BF%A1%E5%85%B3%E9%94%AE%E5%AD%97%E7%9B%91%E6%8E%A7%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3.doc)  来查看提交内容是否合法。**

**3）、一天一个应用给同一手机号发送的短信不能超过10条，否则会报`10010`错误，其他错误码可查看  [短信功能相关错误码](http://docs.bmob.cn/errorcode/index.html?menukey=otherdoc&key=errorcode#index_短信功能相关错误码) 。**


##### 验证验证码

通过`verifySmsCode`方式可验证该短信验证码：

```java
BmobSMS.verifySmsCode(context,"11位手机号码", "验证码", new VerifySMSCodeListener() {
			
	@Override
	public void done(BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//短信验证码已验证成功
			Log.i("smile", "验证通过");
		}else{
			Log.i("smile", "验证失败：code ="+ex.getErrorCode()+",msg = "+ex.getLocalizedMessage());
		}
	}
});

```

验证成功后，用户的`mobilePhoneVerified`属性会自动变为`true`。

##### 查询短信发送状态

通过`querySmsState`方式可查询指定`smsId`的发送状态：

```java

BmobSMS.querySmsState(context, smsId, new QuerySMSStateListener() {
			
			@Override
			public void done(SmsState state, BmobException ex) {
				// TODO Auto-generated method stub
				if(ex==null){
					Log.i("smile","短信状态："+state.getSmsState()+",验证状态："+state.getVerifyState());
				}
			}
		});

```

注：`SmsState`包含两种状态：

1、`smsState`（短信状态）   :SUCCESS（发送成功）、FAIL（发送失败）、SENDING(发送中)。

2、`verifyState`（验证状态）:true(已验证)、false(未验证)。


**注意事项：**

**关于短信条数的计算规则如下:**

1. 实际计算的短信字数 = 模板的内容或自定义短信的内容字数 + 6。加上6是因为默认的签名【比目科技】占了6个字。
2. 实际计算的短信字数在70个字以下算1条。
3. 实际计算的短信字数超过70字的以67字为一条来计算的。也就是135个字数是计算为3条的。
4. 计算得到的短信条数在本条短信发送成功后将会从你的账户剩余的短信条数中扣除。

**短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条。**


#### 短信验证案例

为了方便大家，官方提供了一个短信验证的demo：[https://github.com/bmob/bmob_android_demo_sms](https://github.com/bmob/bmob_android_demo_sms) 。

此案例包含了：`用户名/邮箱/手机号码+密码登录`、`手机号码一键注册登录`、`绑定手机号`以及`通过手机号重置用户密码`。

### 第三方账号登陆

Bmob提供了非常简单的方法来实现第三方账号登陆的功能，目前支持`新浪微博`、`QQ账号`、`微信账号`的登陆。

自`BmobV3.3.9`版本开始，为了与第三方开放平台的SDK解藕，Bmob使用了全新的第三方账号登录方式，之前的微博和qq登录方式的API已删除。

#### 应用场景

第三方账号登陆目前适应以下两种应用场景：

`一、没有Bmob账号，希望使用第三方账号一键注册或登陆Bmob账号`

如果开发者希望用户使用第三方平台的账号注册或登录Bmob的用户体系，则推荐的步骤如下：

1、第三方平台授权，开发者需自行根据第三方平台文档提出的授权方法完成账号授权并得到授权信息

2、调用Bmob提供的`loginWithAuthData（BmobV3.3.9版本提供）`方法，并自行构造`BmobThirdUserAuth（第三方授权信息）`对象，调用成功后，在Bmob的User表中会产生一条记录。


`二、已有Bmob账号，希望与第三方账号进行关联`

如果已使用Bmob的用户体系（假设用户A已登录），希望和第三方平台进行关联，则推荐的步骤如下：

1、第三方平台授权，开发者需自行根据第三方平台文档提出的授权方法完成账号授权并得到授权信息

2、调用`associateWithAuthData`方法，并自行构造`BmobThirdUserAuth(第三方授权信息)`对象，调用成功后，你就会在后台的用户A的authData这个字段下面看到提交的授权信息。


#### 相关文档

为了方便开发者完成授权，现整理各个平台的需要查阅的文档：

##### 微博登陆相关文档

1、[移动客户端接入文档](http://open.weibo.com/wiki/%E7%A7%BB%E5%8A%A8%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%A5%E5%85%A5)：此文档请着重查阅其中的`SDK接入流程`。

2、[新浪微博AndroidSDK快速入门](https://github.com/sinaweibosdk/weibo_android_sdk)，请详细查看`README`文档,其介绍了完整的集成流程。

3、[新浪微博常见问题](https://github.com/sinaweibosdk/weibo_android_sdk/blob/master/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%20FAQ.md#5-%E5%85%B3%E4%BA%8E%E6%8E%88%E6%9D%83)：在新浪微博授权过程中出现问题，请查看此文档，一般出现频率较高的错误有：

`sso package and sign error`- 平台上填写的包名和签名不正确。请仔细检查，一般最需要检查的是`签名`，签名需要使用微博提供的获取签名的工具[（app_signatures.apk）](https://github.com/sinaweibosdk/weibo_android_sdk/blob/master/app_signatures.apk)。

`redirect_uri_mismatch`     - 请确保你在weibo平台上填写的授权回调地址与代码中写的授权回调地址(RedirectURI)一样。

##### QQ登陆相关文档

1、如何使用SDK，请参见 [腾讯开放平台Android_SDK使用说明](http://wiki.open.qq.com/wiki/mobile/Android_SDK%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)。

2、如何调用具体API，请参见 [API调用说明](http://wiki.open.qq.com/wiki/Android_API%E8%B0%83%E7%94%A8%E8%AF%B4%E6%98%8E)。

3、常见问题汇总，请参见[问题汇总说明](http://bbs.open.qq.com/thread-6159767-1-1.html)。

##### 微信登陆相关文档

1、[Android接入指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&lang=zh_CN&token=a6350e5290b2fee66bf0a98f02d7ddc7a655ddce)：这里主要介绍的是微信sdk的集成步骤

2、[微信登陆开发指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&lang=zh_CN&token=a6350e5290b2fee66bf0a98f02d7ddc7a655ddce)：在`移动应用开发`->`微信登录功能`->`移动应用微信登录开发指南`。主要介绍微信OAuth2.0授权登录的流程。


**注：**

三者当中较麻烦的是微信登陆的授权，因此，在此简单说明下可能遇见的问题：

**1、在微信登陆过程中出现了问题，请仔细注意以下几点：**

1）、需要在微信开放平台上填写你的`应用信息、包名和签名`；
2）、微信登陆不同于QQ或微博登陆，应用需要`提交微信官方审核`，只有审核通过后才能打开授权界面；
3）、建议`导出正式签名的apk来测试`，不建议直接run debug版本的apk；
4）、检查`签名是否正确`、检查`传递的参数是否正确`。

**2、微信登陆的测试相对比较麻烦，如果开发者希望直接能够进行调试，建议使用微信官方demo中的`debug keystore`**。具体使用步骤：

1)、Eclipse中选择`Window->Preferences->Android->Build`;

2)、在Build页有个`Custom debug keystore`选项,然后点击`Browse`,选择微信官方demo中的`debug keystore`文件即可。

之后重新运行应用时会使用该`debug keystore`文件对应用进行Debug签名。

`不要忘了在微信后台重新填写通过微信签名工具获得的该调试应用的签名`。


#### 第三方账号一键注册或登录

假设你已通过上述提供的文档完成相应平台的授权并得到对应的授权信息，则可以这样来完成一键注册或登陆操作：

```java
	BmobThirdUserAuth authInfo = new BmobThirdUserAuth(snsType,accessToken, expiresIn,userId);
	BmobUser.loginWithAuthData(context, authInfo, new OtherLoginListener() {
	
		@Override
		public void onSuccess(JSONObject userAuth) {
			// TODO Auto-generated method stub
			...
		}
		
		@Override
		public void onFailure(int code, String msg) {
			// TODO Auto-generated method stub
			Log.i("smile","第三方登陆失败："+msg);
		}
	
	});			

```

注：

`BmobThirdUserAuth`的各参数解释：

1、`snsType`:只能是三种取值中的一种：`weibo、qq、weixin`

2、`accessToken`：接口调用凭证

3、`expiresIn`：access_token的有效时间

4、`userId`:用户身份的唯一标识，对应微博授权信息中的`uid`,对应qq和微信授权信息中的`openid`


#### 关联第三方账号

##### 账号关联

```java
	BmobThirdUserAuth authInfo = new BmobThirdUserAuth(snsType,accessToken, expiresIn, userId);
	BmobUser.associateWithAuthData(context, authInfo, new UpdateListener() {
		
		@Override
		public void onSuccess() {
			Log.i("smile","关联成功");
		}
		
		@Override
		public void onFailure(int code, String msg) {
			Log.i("smile","关联失败：code =" + code + ",msg = " + msg);
		}
	});

```

##### 解除关联

```java
	BmobUser.dissociateAuthData(context,snsType,new UpdateListener() {

		@Override
		public void onSuccess() {
			// TODO Auto-generated method stub
			Log.i("smile","取消"+type+"关联成功");
		}
	
		@Override
		public void onFailure(int code, String msg) {
			// TODO Auto-generated method stub
			if (code == 208) {// 208错误指的是没有绑定相应账户的授权信息
				Log.i("smile","你没有关联该账号");
			} else {
				Log.i("smile","取消"+type+"关联失败：code =" + code + ",msg = " + msg);
			}
		}
});

```

#### 第三方登录的案例源码

具体案例可参考我们Github上的demo：[https://github.com/bmob/bmob-android-demo-thirdpartylogin](https://github.com/bmob/bmob-android-demo-thirdpartylogin) ,这个源码包含了第三方登录的源码和登录之后如何获取用户基本信息的部分。


## 文件管理

`BmobFile`可以让你的应用程序将文件存储到服务器中，常见的文件类型都可以实现存储：比如图像文件、影像文件、音乐文件和任何其他二进制数据。

**注：**

1、以下均为SDK对文件进行操作的方法，如果你想在Web端对文件进行操作，请查看我们的帮助文档：[http://docs.bmob.cn/helps/index.html?menukey=otherdoc&key=helps#index_如何在Web后台上传文件（如图片）](http://docs.bmob.cn/helps/index.html?menukey=otherdoc&key=helps#index_如何在Web后台上传文件（如图片）)

2、自 `BmobSDKv3.4.6` 开始，文件服务需要注意以下几个方面：

- **SDK内部集成CDN文件服务，删除`BmobProFile`的相关代码，并不再提供新旧文件管理的功能，但上传的方法名不变**；

- **新增了文件下载`(download)`和批量删除CDN文件`(deleteBatch)`的方法**；

- **2016年7月,旧版SDK中的新旧文件管理的上传方法将停止服务，之前通过旧版SDK中的新旧文件管理上传的文件仍可下载，请大家及时更新SDK**；

- **之前使用了`BmobProFile中`的`upload`方法上传的文件，开发者可以直接在文件的url地址后面增加："?t=2&a="+ 你的accessKey，那么拼接后的文件是可以直接用来访问并下载的。**；

```xml	
	举个例子：

	如果之前通过新版文件管理的上传方法得到的文件url地址：
	http://newfile.codenow.cn:8080/a272a1aac5274f7085f140de9db94635.png，
	
	那么签名后的可访问的文件地址为：
	http://newfile.codenow.cn:8080/a272a1aac5274f7085f140de9db94635.png?t=2&a=你的accessKey。
```
- **无法查看accessKey**。因为已经废除新旧文件管理功能，所以在开发者管理后台的设置-->应用密钥中已无法查看accessKey，而之前开发者所使用的accessKey继续有效。

3、CDN文件服务需要`okhttp-2.4.0、okio-1.4.0`及`WAKE_LOCK`权限，请导入okhttp相关jar包并在`AndroidManifest.xml`类的`manifest`标签下添加如下权限，否则会造成调用上传/下载文件的方法无反应。

```xml
	<!--保持CPU 运转，屏幕和键盘灯有可能是关闭的,用于文件上传和下载 -->
	<uses-permission android:name="android.permission.WAKE_LOCK" />
```

### 创建文件对象

创建文件对象方式如下：

```java
String picPath = "sdcard/temp.jpg";
BmobFile bmobFile = new BmobFile(new File(picPath));
```

### 上传单一文件

文件分片上传的方法非常简单，示例代码如下：

```java
String picPath = "sdcard/temp.jpg";
BmobFile bmobFile = new BmobFile(new File(picPath));
bmobFile.uploadblock(context, new UploadFileListener() {
	
	@Override
	public void onSuccess() {
		//bmobFile.getFileUrl(context)--返回的上传文件的完整地址
		toast("上传文件成功:" + bmobFile.getFileUrl(context));
	}
	
	@Override
	public void onProgress(Integer value) {
		// 返回的上传进度（百分比）
	}
	
	@Override
	public void onFailure(int code, String msg) {
		toast("上传文件失败：" + msg);
	}
});
```


#### 设置文件分片上传时每片大小

自`BmobSDKv3.4.6`开始,新增`BmobConfig`类，允许开发者设置`查询超时时间`及`文件分片上传时的每片大小`。建议在`Application`类的`onCreate`方法中调用。

示例代码如下:

```java

public class BmobApplication extends Application {

	@Override
	public void onCreate() {
		super.onCreate();
		//设置BmobConfig
		BmobConfig config =new BmobConfig.Builder()
		//请求超时时间（单位为秒）：默认15s
		.setConnectTimeout(30)
		//文件分片上传时每片的大小（单位字节），默认512*1024
		.setBlockSize(500*1024)
		.build();
		Bmob.getInstance().initConfig(config);
	}
}

```

### 批量上传文件

自`BmobSDKv3.2.7`开始,新增批量上传文件的方法；

自`BmobSDKv3.4.6`开始,文件批量上传的静态方法由`Bmob`转移至`BmobFile`类,建议调用`BmobFile.uploadBatch`方法。

示例代码如下：

```java
//详细示例可查看BmobExample工程中BmobFileActivity类
String filePath_mp3 = "/mnt/sdcard/testbmob/test1.png";
String filePath_lrc = "/mnt/sdcard/testbmob/test2.png";
final String[] filePaths = new String[2];
filePaths[0] = filePath_mp3;
filePaths[1] = filePath_lrc;
BmobFile.uploadBatch(context, filePaths, new UploadBatchListener() {
			
	@Override
	public void onSuccess(List<BmobFile> files,List<String> urls) {
		//1、files-上传完成后的BmobFile集合，是为了方便大家对其上传后的数据进行操作，例如你可以将该文件保存到表中
		//2、urls-上传文件的完整url地址
		if(urls.size()==filePaths.length){//如果数量相等，则代表文件全部上传完成
			//do something
		}
	}
	
	@Override
	public void onError(int statuscode, String errormsg) {
		ShowToast("错误码"+statuscode +",错误描述："+errormsg);
	}

	@Override
	public void onProgress(int curIndex, int curPercent, int total,int totalPercent) {
		//1、curIndex--表示当前第几个文件正在上传
		//2、curPercent--表示当前上传文件的进度值（百分比）
		//3、total--表示总的上传文件数
		//4、totalPercent--表示总的上传进度（百分比）
	}
});
```

**注：**

**1、有多少个文件上传，onSuccess方法就会执行多少次;**

**2、通过onSuccess回调方法中的files或urls集合的大小与上传的总文件个数比较，如果一样，则表示全部文件上传成功。**


### 下载文件

自`BmobSDKv3.4.6`版本,SDK提供了文件的下载方法`download`，并且允许开发者设置下载文件的存储路径。

**注：下载方法并不局限于下载通过BmobSDK上传的文件，也就是说只要提供一个文件url地址，也可以调用下载方法的。**

下载文件的步骤：

1、先获取BmobFile对象实例，可以是查询数据时返回的BmobFile，也可以自行构建BmobFile对象：

- 通过查询数据时返回的BmobFile，示例代码如下：

```java

bmobQuery.findObjects(context, new FindListener<GameScore>() {
	@Override
	public void onSuccess(List<GameScore> object) {
		for (GameScore gameScore : object) {
				BmobFile bmobfile = gameScore.getPic();
		       if(file!= null){
					//调用bmobfile.download方法
	           }
			}
		}
	}
	@Override
	public void onError(int code, String msg) {
		toast("查询失败："+msg);
	}
});

```

- 通过如下构造方法构造BmobFile对象：

需求：如果你想下载一个远程图片地址，那么就需要使用下面的构造方法构造一个BmobFile对象（其中group可为空）

```java
/**  
 * @param fileName 文件名(必填)
 * @param group 组名（选填）
 * @param url  完整url地址（必填）
 * 注：必须要有文件名和文件的完整url地址，group可为空
 */
public BmobFile(String fileName,String group,String url){
	this.filename = fileName;
	this.group=group;
	this.url = url;
}

```

示例代码如下：

```java

BmobFile bmobfile =new BmobFile("xxx.png","","http://bmob-cdn-2.b0.upaiyun.com/2016/04/12/58eeed852a7542cb964600c6cc0cd2d6.png")；

```

2、然后调用`bmobfile.download`方法下载文件:

有两种下载方法：

 - `download(Context context,DownloadFileListener listener)`：此方法会将文件下载到当前应用的默认缓存目录中，以getFilename()得到的值为文件名

 - `download(Context context,File savePath, DownloadFileListener listener)`：此方法允许开发者指定文件存储目录和文件名


示例代码如下：

```java
private void downloadFile(BmobFile file){
	//允许设置下载文件的存储路径，默认下载文件的目录为：context.getApplicationContext().getCacheDir()+"/bmob/"
	File saveFile = new File(Environment.getExternalStorageDirectory(), file.getFilename());
	file.download(context,saveFile, new DownloadFileListener() {
		
		@Override
		public void onStart() {
			toast("开始下载...");
		}
		
		@Override
		public void onSuccess(String savePath) {
			toast("下载成功,保存路径:"+savePath);
		}
		
		@Override
		public void onProgress(Integer value, long newworkSpeed) {
			Log.i("bmob","下载进度："+value+","+newworkSpeed);
		}
		
		@Override
		public void onFailure(int code, String msg) {
			toast("下载失败："+code+","+msg);
		}
	});
}


```
### 删除文件

`BmobSDKv3.4.6`中删除文件的接口，`只能删除通过CDN文件服务（v3.4.6开始采用CDN文件服务）上传的文件`。不兼容之前的新旧文件管理，但使用方法不变。

示例代码如下：

```java
BmobFile file = new BmobFile();
file.setUrl(url);//此url是上传文件成功之后通过bmobFile.getUrl()方法获取的。
file.delete(context, new DeleteListener() {
	
	@Override
	public void onSuccess() {
		// TODO Auto-generated method stub
		showToast("文件删除成功");
	}
	
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		showToast("文件删除失败："+code+",msg = "+msg);
	}
});

```

### 批量删除文件

自 `BmobSDKv3.4.6` 版本，SDK提供了文件的批量删除接口`deleteBatch，且只能删除通过CDN文件服务（v3.4.6开始采用CDN文件服务）上传的文件`。

示例代码如下：

```java
//此url必须是上传文件成功之后通过bmobFile.getUrl()方法获取的。
String[] urls =new String[]{url};
BmobFile.deleteBatch(this, urls, new DeleteBatchListener() {
	
	@Override
	public void done(String[] failUrls, BmobException e) {
		if(e==null){
			toast("全部删除成功");
		}else{
			if(failUrls!=null){
				toast("删除失败个数："+failUrls.length+","+e.toString());
			}else{
				toast("全部文件删除失败："+e.getErrorCode()+","+e.toString());
			}
		}
	}
});

```
为方便大家理解文件服务的使用，Bmob提供了一个文件上传的案例和源码，大家可以到[示例和教程中查看和下载](http://docs.bmob.cn/android/example/index.html?menukey=example_teach_doc&key=example_teach_android#index_上传文件案例教程)。

**注：**

**1、文件的批量上传是BmobSDK_v3.2.7版本才提供的功能，如需使用，请更新版本;**
**2、文件的下载和批量删除是BmobSDK_v3.4.6才提供的功能，如需使用，请更新版本。**

## 数据实时同步

**数据实时同步是一个超酷的功能！**

SDK可以实现对数据表或行的监听，当这个表或者行的数据发生变化时，Bmob会立即将变化的信息告知SDK。
这种服务非常适合做游戏开发（如，开发斗地主游戏，三个人同时监听一行数据的变化，任何一个人出牌都会将数据写入到这行数据中，其他人也就立即知道了）、群聊（一群人监听某个表的变化，任何人发言都会将数据写入到这个表中，其他人也可以立即知道了）等实时性要求很高的场景中。

为方便大家快速了解数据的实时同步服务，我们提供了一个简单的应用实例（ [https://github.com/bmob/bmob-android-demo-realtime-data](https://github.com/bmob/bmob-android-demo-realtime-data) ）供大家参考。

### 开始连接
使用数据实时功能，首先需要创建`BmobRealTimeData`对象,然后调用`start`方法连接服务器。
```java
BmobRealTimeData rtd = new BmobRealTimeData();
rtd.start(this, new ValueEventListener() {
	@Override
	public void onDataChange(JSONObject data) {
		// TODO Auto-generated method stub
		Log.d("bmob", "("+data.optString("action")+")"+"数据："+data);
	}
	
	@Override
	public void onConnectCompleted() {
		// TODO Auto-generated method stub
		Log.d("bmob", "连接成功:"+rtd.isConnected());
	}
});
```

`start`方法中的`ValueEventListener`参数用于监听连接成功和数据变化的回调。当有数据变化时会通过onDataChange回调方法反馈到客户端。开发者只需要处理得到的data就可以了。

**注：**

**1、监听器不支持UI线程，在监听回调中请不要直接操作UI；**

**2、如果你要监听User、Installation等系统表的话，表名前需要加上“_”，例如：_User**


### 监听数据
在BmobRealTimeData对象连接成功后，就可以进行数据的监听了。BmobSDK提供了监听表和行的方法如下：
```java
// 监听表更新
rtd.subTableUpdate(tableName);
// 监听表删除
rtd.subTableDelete(tableName);
// 监听行更新
rtd.subRowUpdate(tableName, objectId);
// 监听行删除
rtd.subRowDelete(tableName, objectId);
```
其中`tableName`为要监听的数据表名，`objectId`为要监听的数据行Id,
通常比较保险的做法是在`BmobRealTimeData`对象的连接状态为`true`的情况下进行监听，代码如下：
```java
if(rtd.isConnected()){
	// 监听表更新
	rtd.subTableUpdate(tableName);
}
```

### 取消监听
当开发者想取消监听某个行为是，可使用下面的方法：

```java
// 取消监听表更新
rtd.unsubTableUpdate(testTableName);
// 取消监听表删除
rtd.unsubTableDelete(testTableName);
// 取消监听行更新
rtd.unsubRowUpdate(testTableName, objectId);
// 取消监听行删除
rtd.unsubRowDelete(testTableName, objectId);
```


## ACL和角色

数据安全是软件系统中最重要的组成部分，为了更好的保护应用数据的安全，Bmob在软件架构层面提供了应用层次、表层次、ACL（Access Control List：访问控制列表）、角色管理（Role）四种不同粒度的权限控制的方式，确保用户数据的安全（详细请查看[Bmob数据与安全页面](http://docs.bmob.cn/datasafety/index.html?menukey=otherdoc&key=datasafety)，了解Bmob如何保护数据安全）。

其中，最灵活的方法是通过ACL和角色，它的思路是每一条数据有一个用户和角色的列表，以及这些用户和角色拥有什么样的许可权限。

大多数应用程序需要对不同的数据进行灵活的访问和控制，这就可以使用Bmob提供的ACL模式来实现。例如：

- 对于私有数据，读写权限可以只局限于数据的所有者。
- 对于一个论坛，会员和版主有写的权限，一般的游客只有读的权限。
- 对于日志数据只有开发者才能够访问，ACL可以拒绝所有的访问权限。
- 属于一个被授权的用户或者开发者所创建的数据，可以有公共的读的权限，但是写入权限仅限于管理者角色。
- 一个用户发送给另外一个用户的消息，可以只给这些用户赋予读写的权限。


### 默认访问权限
在没有显示指定的情况下，每一个BmobObject(表)中的ACL(列)属性的默认值是所有人可读可写的。在客户端想要修改这个权限设置，只需要简单调用BmobACL的setPublicReadAccess方法和setPublicWriteAccess方法，即：

```java
BmobACL aCL = new BmobACL();

aCL.setPublicReadAccess(true);

aCL.setPublicWriteAccess(true);
```

注意：可读可写是默认的权限，不需要写额外的代码。

### 指定用户的访问权限
假如你想实现一个分享日志类的应用时，这可能会需要针对不同的日志设定不同的访问权限。比如，公开的日志，发布者有更改和修改的权限，其他用户只有读的权限，那么可用如下代码实现：

```java
Blog blog = new Blog();
blog.setTitle("论电影的七个元素");
blog.setContent("这是blog的具体内容");

BmobACL acl = new BmobACL();    //创建一个ACL对象
acl.setPublicReadAccess(true);	// 设置所有人可读的权限
acl.setWriteAccess(BmobUser.getCurrentUser(this), true);   // 设置当前用户可写的权限

blog.setACL(acl);    //设置这条数据的ACL信息
blog.save(this, new SaveListener() {
	@Override
	public void onSuccess() {
		//添加成功
	}
	
	@Override
	public void onFailure(int code, String msg) {
		//添加失败
	}
});
```
有时，用户想发表一篇不公开的日志，这种情况只有发布者才对这篇日志拥有读写权限，相应的代码如下：
```java
Blog blog = new Blog();
blog.setTitle("一个人的秘密");
blog.setContent("这是blog的具体内容");

BmobACL acl = new BmobACL();  //创建ACL对象
acl.setReadAccess(BmobUser.getCurrentUser(this), true); // 设置当前用户可写的权限
acl.setWriteAccess(BmobUser.getCurrentUser(this), true); // 设置当前用户可写的权限

blog.setACL(acl);    //设置这条数据的ACL信息
blog.save(this, new SaveListener() {
	@Override
	public void onSuccess() {
		//添加成功
	}
	
	@Override
	public void onFailure(int code, String msg) {
		//添加失败
	}
});
```

### 角色管理
上面的指定用户访问权限虽然很方便，但是对于有些应用可能会有一定的局限性。比如一家公司的工资系统，员工和公司的出纳们只拥有工资的读权限，而公司的人事和老板才拥有全部的读写权限。要实现这种功能，你也可以通过设置每个用户的ACL权限来实现，如下：
```java

//创建公司某用户的工资对象
WageInfo wageinfo = new WageInfo();
wageinfo.setWage(100000);   

//这里创建四个用户对象，分别为老板、人事小张、出纳小谢和自己
BmobUser boss;
BmobUser hr_zhang;
BmobUser cashier_xie;
BmobUser me;

//创建ACL对象
BmobACL acl = new BmobACL();

//设置四个用户读的权限
acl.setReadAccess(boos, true);    
acl.setReadAccess(hr_zhang, true);
acl.setReadAccess(cashier_xie, true);
acl.setReadAccess(me, true);

//设置老板和人事小张对这个工资的写权限
acl.setWriteAccess(boss, true);
acl.setWriteAccess(hr_zhang, true);

//设置工资对象的ACL
wageinfo.setACL(acl);
wageinfo.save(this, new SaveListener() {
	@Override
	public void onSuccess() {
		//保存成功
	}
	
	@Override
	public void onFailure(int code, String msg) {
		//保存失败
	}
});
```
但是，一个公司的人事、出纳和员工不仅仅只有一个人，同时还会有离职、调换岗位以及新员工加入等问题存在。如果用上面的代码对公司的每个人进行一一设置的话是不现实的，既麻烦也很难维护。针对这个问题，我们可以利用BmobRole来解决。我们只需要对用户进行分类，每个分类赋予不同的权限。如下代码实现：

```java
//创建公司某用户的工资对象
WageInfo wageinfo = new WageInfo();
wageinfo.setWage(100000);

//这里创建四个用户对象，分别为老板、人事小张、出纳小谢和自己
BmobUser boss;
BmobUser hr_zhang;
BmobUser hr_luo;
BmobUser cashier_xie;
BmobUser me;

//创建HR和Cashier两个用户角色（这里为了举例BmobRole的使用，将这段代码写在这里，正常情况下放在员工管理界面会更合适）
BmobRole hr = new BmobRole("HR");
BmobRole cashier = new BmobRole("Cashier");

//将hr_zhang和hr_luo归属到hr角色中
hr.getUsers().add(hr_zhang);
hr.getUsers().add(hr_luo);
//保存到云端角色表中（web端可以查看Role表）
hr.save(this);  

//将cashier_xie归属到cashier角色中
cashier.getUsers().add(cashier_xie);
//保存到云端角色表中（web端可以查看Role表）
cashier.save(this);

//创建ACL对象
BmobACL acl = new BmobACL();
acl.setReadAccess(boos, true); // 假设老板只有一个, 设置读权限
acl.setReadAccess(me, true); // 给自己设置读权限
acl.setRoleReadAccess(hr, true); // 给hr角色设置读权限
acl.setRoleReadAccess(cashier, true); // 给cashier角色设置读权限

acl.setWriteAccess(boss, true); // 设置老板拥有写权限
acl.setRoleWriteAccess(hr, true); // 设置ht角色拥有写权限

//设置工资对象的ACL
wageinfo.setACL(acl);
wageinfo.save(this);
```

需要说明一点的是，Web端的Role表也具有ACL的列，你可以将角色管理的权限赋予某些用户。


### 角色之间的从属关系
下面我们来说一下角色与角色之间的从属关系。用一个例子来说明下：一个互联网企业有移动部门，部门中有不同的小组，如Android开发组和IOS开发组。每个小组只拥有自己小组的代码读写权限，但这两个小组同时拥有核心库代码的读权限。
```java
//创建MobileDep（移动研发部）、AndroidTeam（android开发组）和iOSTeam（ios开发组）三个角色
BmobRole mobileDep = new BmobRole("MobileDep");
BmobRole androidTeam = new BmobRole("AndroidTeam");
BmobRole iosTeam = new BmobRole("iOSTeam");

//保存AndroidTeam和iosTeam角色到云端
androidTeam.save(this);
iosTeam.save(this);

//将androidTeam和iosTeam两种角色添加到移动部门角色中
mobileDep.getRoles().add(androidTeam);
mobileDep.getRoles().add(iosTeam);
mobileDep.save(this);

// 假设创建三个代码数据对象
Code coreCode = new Code();
Code androidCode = new Code();
Code iosCode = new Code();

//......此处省略一些具体的属性设定

coreCode.save(this);
androidCode.save(this);
iosCode.save(this);

//设置androidTeam角色对androidCode对象的读和写的权限
androidCode.setRoleReadAccess(androidTeam, true);
androidCode.setRoleWriteAccess(androidTeam, true);

//设置iosTeam角色对iosCode对象的读和写的权限
iosCode.setRoleReadAccess(iosTeam, true);
iosCode.setRoleWriteAccess(iosTeam, true);

//设置mobileDep角色可以对coreCode对象进行读操作
coreCode.setRoleReadAccess(mobileDep);

```

### ACL案例源码

我们为大家提供一个ACL相关的案例源码，大家可以点击下载：[https://github.com/bmob/bmob-android-demo-acl](https://github.com/bmob/bmob-android-demo-acl)

## 地理位置

Bmob允许用户根据地球的经度和纬度坐标进行基于地理位置的信息查询。通过在BmobObject的查询中添加一个BmobGeoPoint的对象查询，你就可以实现轻松查找出离当前用户最接近的信息或地点的功能。

为了方便大家查看文档，这里创建一个Person类：

```java
public class Person extends BmobObject{
    private BmobGeoPoint gpsAdd;

    public BmobGeoPoint getGpsAdd() {
        return gpsAdd;
    }
    public void setGpsAdd(BmobGeoPoint gpsAdd) {
        this.gpsAdd = gpsAdd;
    }
}

```

### 创建地理位置对象

首先需要创建一个BmobGeoPoint对象。例如，创建一个东经116.39727786183357度，北纬39.913768382429105度的BmobGeoPoint对象：
```java
BmobGeoPoint point = new BmobGeoPoint(116.39727786183357, 39.913768382429105);
```

### 查询地理位置信息

现在，你可以测试找出最接近某个点的信息了（**数据表要有一定的地理坐标对象的数据，GeoPoint字段类型**）。查询使用BmobQuery对象的`addWhereNear`方法进行操作：
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
	public void onError(int code, String msg) {
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
	public void onError(int code, String msg) {
		// TODO Auto-generated method stub
		toast("查询失败：" + msg);
	}
});
```

**注意事项**

1. **每个BmobObject数据对象中`只能`有一个BmobGeoPoint对象**。

2. 地理位置的点不能超过规定的范围。`纬度的范围`应该是在`-90.0到90.0`之间。`经度的范围`应该是在`-180.0到180.0`之间。如果您添加的经纬度超出了以上范围，将导致程序错误。


## 云端逻辑

很多时候，单纯的前端代码是不能完成全部事情的，一些重要和复杂的业务逻辑还是希望能够在服务端中执行。比如：对比较大量的比赛数据进行排序，对某个网站进行资料采集和处理，获取用户的IP信息，等等。Bmob不仅提供了云端存储，还开放了云端的业务逻辑代码功能，也就是云端逻辑。

相关云端逻辑的使用，大家可以参考开发文档： [http://docs.bmob.cn/cloudcode/developdoc/index.html?menukey=develop_doc&key=develop_cloudcode](http://docs.bmob.cn/cloudcode/developdoc/index.html?menukey=develop_doc&key=develop_cloudcode)

云端逻辑的执行有多种方法：

- 定时任务（直接在Web管理后台中设定）； 
- RestApi调用（参考开发文档：[http://docs.bmob.cn/restful/developdoc/index.html?menukey=develop_doc&key=develop_restful#index_云端逻辑](http://docs.bmob.cn/restful/developdoc/index.html?menukey=develop_doc&key=develop_restful#index_云端逻辑)）； 
- SDK调用。

其中，在SDK中调用云端逻辑的方法如下：
```java
AsyncCustomEndpoints ace = new AsyncCustomEndpoints();
//第一个参数是上下文对象，第二个参数是云端逻辑的方法名称，第三个参数是上传到云端逻辑的参数列表（JSONObject cloudCodeParams），第四个参数是回调类
ace.callEndpoint(context, cloudCodeName, params, new CloudCodeListener() {
	@Override
	public void onSuccess(Object object) {
		// TODO Auto-generated method stub
		toast("云端usertest方法返回:" + object.toString());
	}
	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		toast("访问云端usertest方法失败:" + msg);
	}
});
```

## 短信服务

除了与用户相关的包括一键注册，手机号码登录等操作外，Bmob 还推出了单独的短信验证码服务。 在实际的应用中，开发者希望能够通过短信验证的方式来与用户进行某些重要操作的确认，你就可以在用户验证过手机号码的前提下，使用 Bmob 提供的短信验证码服务(`Bmob SMS SDK`)。

每个 Bmob 帐户有 30 条免费额度的短信数量用于测试，超过需要购买短信条数才能继续使用。

默认使用 【比目科技】 作为签名，可以在控制台进行修改。

下面是使用方法：

### SMS初始化

此短信SDK可单独使用，调用如下方法完成应用的初始化：

```java

BmobSMS.initialize(context,Bmob Application ID);

```

### 请求发送自定义的短信内容

**使用场景**

 此方法允许开发者使用 Bmob 的短信功能灵活发送自定义的短信内容到用户的手机上，可运用于多种场景下，比如 `发送祝福短信、推送消息、广告、通知`等

**使用前准备**

开发者必须在Bmob后台提交身份证信息（`Bmob后台->账户管理->身份验证`），并经`Bmob官方审核`通过后才可以使用。

请不要发送任何敏感内容，一经发现，Bmob 有权立刻停止您对该接口的使用权。

**调用示例**

```java

SimpleDateFormat format =new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String sendTime = format.format(new Date());
BmobSMS.requestSMS(context, number, "审核通过后的短信内容",sendTime,new RequestSMSCodeListener() {
	
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//
			Log.i("bmob","短信发送成功，短信id："+smsId);//用于查询本次短信发送详情
		}else{
			Log.i("bmob","errorCode = "+ex.getErrorCode()+",errorMsg = "+ex.getLocalizedMessage());
		}
	}
});

```

注：

1、如果需要立即发送，sendTime设置为null;

2、sendTime的格式为 `yyyy-MM-dd HH:mm:ss`,如果sendTime的格式不正确或者是这个时间是过去的时间，那么短信会立即发送;

3、smsId可用于查询该条短信的发送状态。


### 请求发送短信验证码

通过`requestSMSCode`方式给绑定手机号的该用户发送指定短信模板的短信验证码：

```java

BmobSMS.requestSMSCode(context, "11位手机号码", "模板名称",new RequestSMSCodeListener() {
			
	@Override
	public void done(Integer smsId,BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//验证码发送成功
			Log.i("bmob", "短信id："+smsId);//用于查询本次短信发送详情
		}
	}
});

```

短信默认模板：

```java 

	您的验证码是`%smscode%`，有效期为`%ttl%`分钟。您正在使用`%appname%`的验证码。【比目科技】

```

**注：**

1、`模板名称`：模板名称需要开发者在应用的管理后台进行短信模板的添加工作，具体：`短信服务`->`短信模板`,之后点击创建即可。


具体请看下图：

![](image/sms.png)


2、只有审核通过之后的自定义短信模板才可以被使用，如果自定义的短信模板其状态显示`审核中`或者`审核失败`,再调用该方法则会以`默认模板`来发送验证码。

**3、开发者提交短信验证码模板时需注意以下几点：**

**1）、模板中不能有【】和 [] ，否则审核不通过；**

**2）、如果你提交的短信模板无法发送，则有可能包含一些敏感监控词，具体可去Github下载  [短信关键字监控参考文档](https://github.com/bmob/bmob-public-docs/blob/master/%E7%9F%AD%E4%BF%A1%E5%85%B3%E9%94%AE%E5%AD%97%E7%9B%91%E6%8E%A7%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3.doc) 来查看提交内容是否合法。**

**3）、一天一个应用给同一手机号发送的短信不能超过10条，否则会报`10010`错误，其他错误码可查看：[短信功能相关错误码](http://docs.bmob.cn/errorcode/index.html?menukey=otherdoc&key=errorcode#index_短信功能相关错误码) 。**

### 验证验证码

通过`verifySmsCode`方式可验证该短信验证码：

```java

BmobSMS.verifySmsCode(context,"11位手机号码", "验证码", new VerifySMSCodeListener() {
			
	@Override
	public void done(BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){//短信验证码已验证成功
			Log.i("bmob", "验证通过");
		}else{
			Log.i("bmob", "验证失败：code ="+ex.getErrorCode()+",msg = "+ex.getLocalizedMessage());
		}
	}
});

```

### 查询短信发送状态

通过`querySmsState`方式可查询指定`smsId`的发送状态：

```java

BmobSMS.querySmsState(context, smsId, new QuerySMSStateListener() {
			
			@Override
			public void done(SmsState state, BmobException ex) {
				// TODO Auto-generated method stub
				if(ex==null){
					Log.i("bmob","短信状态："+state.getSmsState()+",验证状态："+state.getVerifyState());
				}
			}
		});

```

注：`SmsState`包含两种状态：

1、`smsState`（短信状态）   :SUCCESS（发送成功）、FAIL（发送失败）、SENDING(发送中)。

2、`verifyState`（验证状态）:true(已验证)、false(未验证)。


**注意事项：**

**关于短信条数的计算规则如下:**

1. 实际计算的短信字数 = 模板的内容或自定义短信的内容字数 + 5。加上5是因为默认的签名【云验证】占了5个字。
2. 实际计算的短信字数在70个字以下算1条。
3. 实际计算的短信字数超过70字的以67字为一条来计算的。也就是135个字数是计算为3条的。
4. 计算得到的短信条数在本条短信发送成功后将会从你的账户剩余的短信条数中扣除。

**短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条。**

## 消息推送
### 消息推送简介
推送通知是让用户及时被通知、和你的应用保持联系的一种非常棒的方式，你可以快速而有效地通知到所有的用户，下面这个教程将会教你如何使用Bmob来推送消息。

### 消息推送快速入门

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
		android:process="cn.bmob.push"
		android:exported="true">
	     <intent-filter>
	         <action android:name="cn.bmob.push.lib.service.PushService"/>
	     </intent-filter>
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
	BmobInstallation.getCurrentInstallation(this).save();
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

为了更好的让开发者朋友正确的集成和使用Bmob推送功能，我们还提供了一个关于Bmob推送功能的简单Demo以供大家参考，有需要的朋友可以到如下地址进行源码的下载。[https://github.com/bmob/bmob-android-demo-push](https://github.com/bmob/bmob-android-demo-push)

### 消息推送的视频教程和Demo

Bmob官方为大家准备了消息推送的视频教程，有需要的朋友可以移步浏览视频教程：

客户端推送消息：[http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html](http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html)

集成BmobPushSDK：[http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html](http://v.youku.com/v_show/id_XNzQ4ODczOTc2.html)


### 其他相关说明

以上文档仅仅介绍了如何实现消息的一次性推送，如果你还需要用到其他的推送方法，如组播、广播等，还需要详细阅读下面的相关知识。

#### 安装消息推送服务
每一个Bmob的App被安装在用户的设备上后，如果要使用消息推送功能，Bmob SDK会自动生成一个Installation对象。Installation对象包含了推送所需要的所有信息。举例：一个棒球的App，你可以让用户订阅感兴趣的棒球队，然后及时将这个球队的消息推送给用户 。
您可以使用 BmobSDK，通过 **BmobInstallation** 对象进行一系列操作，就像你存储和获取其他的普通对象一样，比如BmobObject对象。

BmobInstallation对象有几个系统默认的特殊字段来帮助你进行设备定位等管理：
- **channels** : 当前这个设备订阅的渠道名称数组
- **timeZone** : 设备所在位置的时区， 如Asia/Shanghai，这个会在每个BmobInstallation对象更新时同步（只读）
- **deviceType** : 设备的的类型, 值为："ios" 或 "android" (只读)
- **installationId** : Bmob使用的设备唯一号 (只读)

##### 保存 installation

使用消息推送前，首先需要保存设备信息。

```java
BmobInstallation.getCurrentInstallation(this).save();
```

##### 自定义Installation表

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

#### 订阅频道和退订

##### 订阅频道

订阅频道可使用 **subscribe** 方法

```java
BmobInstallation installation = BmobInstallation.getCurrentInstallation(this);
installation.subscribe("Giants");
installation.subscribe("Mets");
installation.save();
```

注：`V3.4.3`版本的Bmob SDK对频道订阅增加去重操作，也就是说：即使你调用subscribe方法订阅了多个相同的频道，Bmob只会记录一个频道。


##### 退订频道

退订频道可使用 **unsubscribe** 方法

```java
BmobInstallation installation = BmobInstallation.getCurrentInstallation(this);
installation.unsubscribe("Giants");
installation.save();
```

#### 广播推送消息
在客户端实现推送消息的功能，通过 **BmobPushManager** 对象来完成，比如给所有设备推送一条消息，如下：
```java
BmobPushManager bmobPush = new BmobPushManager(this);
bmobPush.pushMessageAll("Hello Bmob.");
```

#### 组播推送消息
发送消息给订阅了Giants频道的用户
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
List<String> channels = new ArrayList<String>();
channels.add("Giants");
		
query.addWhereEqualTo("channels", channels);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
同时发送消息给多个频道时，可以将其他频道添加在channels中。

#### 多播推送消息
##### 推送给不活跃的用户
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereLessThan("updatedAt", new BmobDate(new Date()));
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

##### 根据查询条件做推送
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("score", 80);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
请注意，where 条件查询的都是 installations 表。这里是假设 installations 表存储了 score 的Number属性，你可以像查询普通对象一样构造where查询

##### 根据平台做推送
给Android平台的终端推送
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceType", "android");
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
给IOS平台的终端推送
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceType", "ios");
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

##### 根据地理位置信息做推送
```java
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereWithinRadians("location", new BmobGeoPoint(112.934755, 24.52065), 1.0);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
上面的例子假设 installation 表中有个 location 属性是 GeoPoint 类型，我们就可以根据地理信息位置做推送。

#### 点播推送消息
发送给Android单个客户端
```java
String installationId = "客户端installationId";
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("installationId", installationId);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```
发送给iOS单个客户端
```java
String deviceToken = "客户端deviceToken";
BmobPushManager bmobPush = new BmobPushManager(this);
BmobQuery<BmobInstallation> query = BmobInstallation.getQuery();
query.addWhereEqualTo("deviceToken", deviceToken);
bmobPush.setQuery(query);
bmobPush.pushMessage("消息内容");
```

## 应用安全

请大家在使用Bmob开发应用程序之前，仔细阅读“数据与安全”的文档：[http://docs.bmob.cn/datasafety/index.html?menukey=otherdoc&key=datasafety](http://docs.bmob.cn/datasafety/index.html?menukey=otherdoc&key=datasafety)

## 开源案例

为方便大家更好的理解Bmob SDK，我们特意为大家提供了一些源码，大家可以下载之后，**嵌入Bmob的Application Id**，就可以打包运行了。

**阅读源码是一种良好的习惯！！**

* 即时聊天案例源码：[https://github.com/bmob/BmobIMSDK4Android](https://github.com/bmob/BmobIMSDK4Android)

* 图文社区案例源码：[https://git.oschina.net/v7/Wonderful](https://git.oschina.net/v7/Wonderful)  这个案例是猿圈媛圈开发团队提供的。

* 校园小菜案例源码：[https://github.com/bmob/Shop](https://github.com/bmob/Shop) 这个案例是湖工大的朋友提供的。

* 社交分享案例源码：[https://github.com/bmob/bmob-android-social-share](https://github.com/bmob/bmob-android-social-share) 这个是金刚锁开发者提供的

* 第三方登录案例源码：[https://github.com/bmob/bmob-android-demo-thirdpartylogin](https://github.com/bmob/bmob-android-demo-thirdpartylogin)  包含第三方登录和登录后获取用户信息的源码

## 其他功能

### 获取服务器时间

在Bmob对象中提供了一个静态方法，用于获取服务器时间。
```java
Bmob.getServerTime(context, new GetServerTimeListener() {
	@Override
	public void onSuccess(long time) {
		// TODO Auto-generated method stub
		SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm");
		String times = formatter.format(new Date(time * 1000L));
		Log.i("bmob","当前服务器时间为:" + times);
	}

	@Override
	public void onFailure(int code, String msg) {
		// TODO Auto-generated method stub
		Log.i("bmob","获取服务器时间失败:" + msg);
	}
});
```

### 自动更新组件

Bmob为大家提供了应用的自动更新组件，使用这个组件可以快速方便实现应用的自动升级功能。
详细的使用操作可以参考文档：[自动更新组件文档](http://docs.bmob.cn/android/autoupdate/index.html?menukey=otherdoc&key=autoupdate)

### 表结构

自`V3.4.2`版本开始，SDK提供了`获取表结构信息`方法,具体示例如下：

#### 获取特定表的结构

```java
Bmob.getTableSchema(context,"待查询的表名", new GetTableSchemaListener() {
			
	@Override
	public void done(BmobTableSchema schema, BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null){
			Log.i("bmob", "获取指定表的表结构信息成功："+schema.getClassName()+"-"+schema.getFields().toString());
		}else{
			Log.i("bmob", "获取指定表的表结构信息失败:" + ex.getLocalizedMessage()+"("+ex.getErrorCode()+")");
		}
	}
});

```

#### 获取所有表的结构
```java 

Bmob.getAllTableSchema(context, new GetAllTableSchemaListener() {
			
	@Override
	public void done(List<BmobTableSchema> schemas, BmobException ex) {
		// TODO Auto-generated method stub
		if(ex==null && schemas!=null && schemas.size()>0){
			Log.i("bmob", "获取所有表结构信息成功");
		}else{
			Log.i("bmob","获取所有表结构信息失败："+ex.getLocalizedMessage()+"("+ex.getErrorCode()+")");
		}
	}
});

```

#### 返回数据说明
注：`BmobTableSchema`参数说明：

其中

`className`：表示表名

`fields`   ： 是Map<String, Map<String,Object>>类型，里面包含了对应表的所有列的属性，

其fields内部结构如下：

>{"列1":Map,"列2":Map, ...}

而Map的结构为：

>{"type":"typeName","targetClass":"tableName"}

其中 `type` 指的是该列的类型， 而 `targetClass` 指的是指向的表名，只有在 type 为 Pointer 或者 Relation 时才有值。

具体json格式如下,仅供参考：

```java
 {
	className: "Post", 
	fields: {
	  ACL: {
	    type: "Object"
	  }, 
	  author: {
	    targetClass: "_User", 
	    type: "Pointer"
	  }, 
	  content: {
	    type: "String"
	  }, 
	  createdAt: {
	    type: "Date"
	  }, 
	  objectId: {
	    type: "String"
	  }, 
	  updatedAt: {
	    type: "Date"
	  }
	}
 }

```

## 错误码列表

**Android SDK的错误码都是以`9`开头的，其他错误码请点击查看：[RestAPI错误码列表](http://docs.bmob.cn/errorcode/index.html?menukey=otherdoc&key=errorcode)。**

**9001**

内容：AppKey is Null, Please initialize BmobSDK.

含义：Application Id为空，请初始化。

**9002**

内容：Parse data error

含义：解析返回数据出错

**9003**

内容：upload file error

含义：上传文件出错

**9004**

内容：upload file failure

含义：文件上传失败

**9005**

内容：A batch operation can not be more than 50

含义：批量操作只支持最多50条

**9006**

内容：objectId is null

含义：objectId为空

**9007**

内容：BmobFile File size must be less than 10M.

含义：文件大小超过10M

**9008**

内容：BmobFile File does not exist.

含义：上传文件不存在

**9009**

内容：No cache data.

含义：没有缓存数据

**9010**

内容：The network is not normal.(Time out)

含义：网络超时

**9011**

内容：BmobUser does not support batch operations.

含义：BmobUser类不支持批量操作

**9012**

内容：context is null.

含义：上下文为空

**9013**

内容： BmobObject Object names(database table name) format is not correct.

含义：BmobObject（数据表名称）格式不正确

**9014**

含义：第三方账号授权失败

**9015**

含义：其他错误均返回此code


**9016**

内容：The network is not available,please check your network!

含义：无网络连接，请检查您的手机网络。

**9017**

含义：与第三方登录有关的错误，具体请看对应的错误描述

**9018**

含义：参数不能为空


**9019**

含义：格式不正确：手机号码、邮箱地址、验证码

## 混淆打包

使用了BmobSDK的应用在混淆过程中，需注意以下几点：

1、`不要混淆BmobSDK的代码`。因为Bmob Android SDK本身进行了代码混淆；

2、任何继承自`BmobObject、BmobUser`的JavaBean及`在上述JavaBean中定义的Object属性类`都不要混淆

3、如果使用了`support-v4`、`okhttp相关包`及`org.apache.http.legacy.jar`包均不要混淆。

具体可参考BmobExample中proguard-project.txt的代码：

```xml

-ignorewarnings

# 这里根据具体的SDK版本修改
-libraryjars libs/bmob_v3.0.9beta.jar

-keepattributes Signature

# 不混淆BmobSDK
-keep class cn.bmob.v3.** {*;}

# 保证继承自BmobObject、BmobUser类的JavaBean不被混淆
-keep class * extends cn.bmob.v3.BmobObject {
    *;
}
# 也可逐个填写
-keep class com.example.bmobexample.bean.BankCard{*;}
-keep class com.example.bmobexample.bean.GameScore{*;}
-keep class com.example.bmobexample.bean.MyUser{*;}
-keep class com.example.bmobexample.bean.Person{*;}

-keep class com.example.bmobexample.file.Movie{*;}
-keep class com.example.bmobexample.file.Song{*;}

-keep class com.example.bmobexample.relation.Post{*;}
-keep class com.example.bmobexample.relation.Comment{*;}

# 如果你使用了okhttp、okio的包，请添加以下混淆代码
-dontwarn com.squareup.okhttp.**
-keep class com.squareup.okhttp.** { *;}
-keep interface com.squareup.okhttp.** { *; }
-dontwarn okio.**

# 如果你使用了support v4包，请添加如下混淆代码
-dontwarn android.support.v4.** 
-keep class android.support.v4.** { *; }
-keep interface android.support.v4.app.** { *; }
-keep public class * extends android.support.v4.**
-keep public class * extends android.app.Fragment

# 如果你需要兼容6.0系统，请不要混淆org.apache.http.legacy.jar 
 -dontwarn android.net.compatibility.**
 -dontwarn android.net.http.**
 -dontwarn com.android.internal.http.multipart.**
 -dontwarn org.apache.commons.**
 -dontwarn org.apache.http.**
 -keep class android.net.compatibility.**{*;}
 -keep class android.net.http.**{*;}
 -keep class com.android.internal.http.multipart.**{*;}
 -keep class org.apache.commons.**{*;}
 -keep class org.apache.http.**{*;}

```






