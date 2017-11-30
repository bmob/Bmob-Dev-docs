
           
# 1、BmobFeedsSDK 集成

### 1.1、将以下文件放进app下的libs文件夹
| 文件     |
|------------------------------|
| BmobFeeds_vx.x.x_xxxxxx.aar|
| BmobFeedsSDK_vx.x.x_xxxxxx.aar|


### 1.2、在project下的build.gradle文件中配置
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.3'
    }
}

allprojects {
    repositories {
        jcenter()
        flatDir {
            dirs 'libs'
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```

### 1.3、在app下的build.gradle文件中设置jni依赖库的目录，设置后点击Sync Now同步配置
```gradle
dependencies {
		compile (name:'BmobFeeds_v1.0.2_171121',ext:'aar')
		compile (name:'BmobFeedsSDK_v1.0.1_171122',ext:'aar')
    	compile 'com.github.bumptech.glide:glide:3.7.0'
    	compile 'com.google.code.gson:gson:2.8.2'
}
```

### 1.4、 配置AndroidManifest.xml
```xml
<meta-data
    android:name="Bmob_APP_KEY"
    android:value="Bmob平台的Application ID" />
```

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

```xml
<activity
    android:name="cn.bmob.feeds.ui.activity.InfoActivity">
</activity>
```

### 1.5、继承FeedsApp并在AndroidManifest.xml中配置


继承FeedsApp：
```java
public class App extends FeedsApp {
    @Override
    public void onCreate() {
        super.onCreate();
    }
}
```

在在AndroidManifest.xml中配置：
```xml
<application     
    
    ...
    
    android:name=".App"
    
    ...
    
    > 

	...
	   	
</application>

```



# 2、BmobFeedsSDK的使用

### 2.1、添加FeedsFragment

例如：
Example2Activity：

```
public class Example2Activity extends AppCompatActivity {
    private FeedsFragment mFeedsFragment;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example_2);
        mFeedsFragment = new FeedsFragment();
        replace(R.id.container, mFeedsFragment);
    }

    /**
     * @param container
     * @param fragment
     */
    public void replace(int container, Fragment fragment) {
        FragmentManager manager = getSupportFragmentManager();
        FragmentTransaction transaction = manager.beginTransaction();
        transaction.replace(container, fragment);
        transaction.commit();
    }
}
```

activity_example_2.xml：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">


    <FrameLayout
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_above="@id/bottom_navigation"
        android:layout_below="@id/toolbar"></FrameLayout>
</LinearLayout>
```

### 2.2、自定义FeedsFragment属性
| 方法     |	备注	|
|------------------------------|----------------|
| setTabBackgroundColor|	设置顶部导航栏的背景颜色，一般与应用的主题颜色一致|
| setTabTextSelectedColor|	设置顶部导航栏的标题选中颜色|
|setTabTextUnselectedColor|设置顶部导航栏的标题未选中颜色|
|setTabTextSize|设置顶部导航栏的标题大小|




# 3、广告呈现形式
## 3.1、开屏引导页广告

### 3.1.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:orientation="vertical">


    <cn.bmob.feeds.tuia.FeedsSplashView
        android:id="@+id/fsv"
        android:layout_width="match_parent"
        app:countTime ="5"
        android:layout_height="match_parent">
    </cn.bmob.feeds.tuia.FeedsSplashView>
</LinearLayout>
```

| 属性名     |	含义	|
|-----------|----------------|
| countTime |	设置开屏广告的展示时间，此时间过后，开屏广告自动消失|


### 3.1.2、代码配置
```java
public class SplashActivity extends BaseActivity {
    @BindView(R.id.fsv)
    FeedsSplashView mFsv;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);
        ButterKnife.bind(this);
        mFsv.setTargetIntent(this,new Intent(mContext,MainActivity.class));
        mFsv.setCountTime(6);
        mFsv.loadFeeds();
    }


    @Override
    protected void onDestroy() {
        if (mFsv!=null){
            mFsv.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
| 	setTargetIntent |	设置开屏广告的展示时间过后，需要跳转的页面|
|	setCountTime|设置开屏广告的展示时间，此时间过后，开屏广告自动消失|
|	loadFeeds|加载广告图片|
|	destroy|销毁资源，在声明周期onDestroy时调用|


## 3.2、插屏广告，也叫弹窗广告
### 3.2.1、代码配置
```java
public class InterstitialActivity extends BaseActivity {

    private FeedsInterstitialView mFeedsInterstitialView;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_interstitial);
        mFeedsInterstitialView = new FeedsInterstitialView(this);
        mFeedsInterstitialView.loadFeeds();
    }

    @Override
    protected void onDestroy() {
        if (mFeedsInterstitialView != null) {
            mFeedsInterstitialView.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告图片|
|destroy|销毁资源，在声明周期onDestroy时调用|
## 3.3、浮标位广告，也叫悬浮框广告
### 3.3.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
             android:orientation="vertical"
             android:layout_width="match_parent"
             android:layout_height="match_parent">

    <cn.bmob.feeds.tuia.FeedsDobberView
        android:layout_width="60dp"
        android:id="@+id/fdv"
        android:layout_gravity="center|right"
        android:layout_height="60dp">
    </cn.bmob.feeds.tuia.FeedsDobberView>

</FrameLayout>
```

### 3.3.2、代码配置
```java
public class DobberActivity extends BaseActivity {
    @BindView(R.id.fdv)
    FeedsDobberView mFdv;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dobber);
        ButterKnife.bind(this);
        mFdv.loadFeeds();
    }


    @Override
    protected void onDestroy() {
        if (mFdv!=null){
            mFdv.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告图片|
|destroy|销毁资源，在声明周期onDestroy时调用|


## 3.4、Banner位广告，也叫横幅位广告
### 3.4.1、单张Banner位
#### 3.4.1.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:app="http://schemas.android.com/apk/res-auto"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">

    <cn.bmob.feeds.act.banner.single.FeedsBannerSingleView
        android:id="@+id/banner_top"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        app:size="Large">

    </cn.bmob.feeds.act.banner.single.FeedsBannerSingleView>


    <cn.bmob.feeds.act.banner.single.FeedsBannerSingleView
        android:id="@+id/banner_tail"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        app:size="Small">

    </cn.bmob.feeds.act.banner.single.FeedsBannerSingleView>
</RelativeLayout>
```
| 属性名     |	含义	|
|-----------|----------------|
| size |	设置Banner高度的大小，Small较矮，Large较高，默认为Small|

#### 3.4.1.2、代码配置
```java
public class BannerSingleActivity extends AppCompatActivity {

    @BindView(R.id.banner_top)
    FeedsBannerSingleView mBannerTop;
    @BindView(R.id.banner_tail)
    FeedsBannerSingleView mBannerTail;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_banner_single);
        ButterKnife.bind(this);
        mBannerTail.loadFeeds();
        mBannerTop.loadFeeds();
    }

    @Override
    protected void onDestroy() {
        if (mBannerTop!=null){
            mBannerTop.destroy();
        }
        if (mBannerTail!=null){
            mBannerTail.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告图片|
|destroy|销毁资源，在声明周期onDestroy时调用|


