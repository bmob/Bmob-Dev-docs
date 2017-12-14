
           
# 1、BmobFeedsSDK 集成

### 1.1、将下载后的SDK的以下文件放进app下的libs文件夹
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
        //广告SDK配置
        flatDir {
            dirs 'libs'
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```

### 1.3、在app下的build.gradle文件中设置依赖，设置后点击Sync Now同步配置
```gradle
dependencies {
		compile (name:'BmobFeeds_vx.x.x_xxxxxx',ext:'aar')
		compile (name:'BmobFeedsSDK_vx.x.x_xxxxxx',ext:'aar')
    	compile 'com.github.bumptech.glide:glide:3.7.0'
    	compile 'com.google.code.gson:gson:2.8.2'
}
```

### 1.4、 配置AndroidManifest.xml
```xml
<meta-data
    android:name="Bmob_APP_KEY"
    android:value="此出替换为Bmob平台的Application ID" />
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
## 2.1、信息流广告（SDK嵌入形式）
### 2.1.1、添加FeedsFragment

例如：
FlowSdkActivity：
```
/**
 * Created on 17/11/22 10:29
 * 嵌入在SDK中的信息流广告，开发者只需要加载SDK中的FeedsFragment即可
 * 此页面为只嵌入FeedsFragment的案例，嵌入多个fragment的案例请参考MainActivity
 * @author zhangchaozhou
 */

public class FlowSdkActivity extends AppCompatActivity {
    private FeedsFragment mFeedsFragment;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_flow_sdk);

        //新建信息流fragment对象
        mFeedsFragment = new FeedsFragment();
        //显示信息流fragment
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

    @Override
    public void onBackPressed() {
        //由于信息流fragment中包含了网页，需要实现返回上一页网页功能，
        //所以开发者需要在嵌入信息流fragment的activity中重载返回事件
        if (mFeedsFragment.canGoBack()) {
            mFeedsFragment.goBack();
        } else {
            finish();
        }
    }
}
```

activity_flow_sdk.xml：
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


### 2.2.2、自定义FeedsFragment属性
| 方法     |	备注	|
|------------------------------|----------------|
| setTabBackgroundColor|	设置顶部导航栏的背景颜色，一般与应用的主题颜色一致|
| setTabTextSelectedColor|	设置顶部导航栏的标题选中颜色|
|setTabTextUnselectedColor|设置顶部导航栏的标题未选中颜色|
|setTabTextSize|设置顶部导航栏的标题大小|

### 2.2.3、配置返回键的相关操作
```java
    @Override
    public void onBackPressed() {
        //由于信息流fragment中包含了网页，需要实现返回上一页网页功能，
        //所以开发者需要在嵌入信息流fragment的activity中重载返回事件
        if (mFeedsFragment.canGoBack()) {
            mFeedsFragment.goBack();
        } else {
            finish();
        }
    }
```



## 2.2、信息流广告（开发者请求形式）/自定义广告

### 2.2.1、请求自定义广告
| ActRequest方法     |	含义	|
|-----------|----------------|
|loadFeeds(ActRequest.OnActListener onActListener)|请求自定义广告，开发者可根据返回的Act属性自行设计UI展示|

参见示例：FlowDeveloperActivity.java
```java
		ActRequest mActRequest = new ActRequest(this);
        //开发者自己的数据获取之后嵌入一条信息流广告：
        mActRequest.loadFeeds(new ActRequest.OnActListener() {
            @Override
            public void onError(int code, String error) {
                Logger.e("获取广告出错：" + code + "-" + error);
                //在广告获取结果出来后再添加开发者的数据，以防两条广告连续出现
                mItems.addAll(items);
                //无论获取广告成功还是失败都应该刷新列表数据
                refresh();
            }

            @Override
            public void onSuccess(Act act) {
                Item item = new Item();
                item.setAct(act);
                //在广告获取结果出来后再添加开发者的数据，以防两条广告连续出现
                mItems.addAll(items);
                mItems.add(item);
                //无论获取广告成功还是失败都应该刷新列表数据
                refresh();
            }
        });
```


### 2.2.2、展示自定义广告
| 返回Act的属性     |	含义	|
|-----------|----------------|
|url|点击后需要跳转到InfoActivity页面的网址|
|title|点击后需要跳转到InfoActivity页面的标题|
|images|当前信息流广告需要展示的图片|
|descript|当前信息流广告的解释，可以不展示|

参见示例：FlowDeveloperAdapter.java
```java
        Item item = mItems.get(position);
        if (getItemViewType(position) == TYPE_FLOW_AD) {
            //信息流广告的布局，开发者可以自己定义布局的展现形式
            flowAdHolder.mLlImagesMulti.removeAllViews();
            flowAdHolder.mLlLabels.removeAllViews();
            final Act act = item.getAct();

            flowAdHolder.mTvTitle.setText(act.getTitle().trim());

            List<String> images = act.getImages();
            //处理信息流广告的images属性
            if (images != null) {
                if (images.size() == 1) {
                    //如果images是一张展示大图
                    flowAdHolder.mIvSingle.setVisibility(View.VISIBLE);
                    flowAdHolder.mLlImagesMulti.setVisibility(View.GONE);
                    Glide.with(mContext).load(images.get(0)).fitCenter().thumbnail(0.1f).diskCacheStrategy(DiskCacheStrategy.ALL).into(flowAdHolder.mIvSingle);
                } else {
                    //如果images是多张展示小图
                    flowAdHolder.mLlImagesMulti.removeAllViews();
                    flowAdHolder.mLlImagesMulti.setVisibility(View.VISIBLE);
                    flowAdHolder.mIvSingle.setVisibility(View.GONE);
                    for (int i = 0; i < images.size(); i++) {
                        ImageView imageView = new ImageView(mContext);
                        LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.MATCH_PARENT);
                        layoutParams.weight = 1;
                        layoutParams.setMargins(2, 0, 2, 0);
                        layoutParams.gravity = Gravity.CENTER_VERTICAL;
                        Glide.with(mContext).load(images.get(i)).centerCrop().thumbnail(0.1f).diskCacheStrategy(DiskCacheStrategy.ALL).into(imageView);
                        flowAdHolder.mLlImagesMulti.addView(imageView, layoutParams);
                    }
                }
            }


            List<Label> labels = act.getLabels();
            //处理信息流广告的标签属性
            if (labels != null) {
                for (int i = 0; i < labels.size(); i++) {
                    TextView tv = new TextView(mContext);
                    tv.setId(i);
                    LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
                    layoutParams.gravity = Gravity.CENTER_VERTICAL;
                    layoutParams.setMargins(0, 0, 6, 0);

                    tv.setText(labels.get(i).getText());
                    GradientDrawable gd = new GradientDrawable();
                    gd.setColor(ContextCompat.getColor(mContext, android.R.color.transparent));
                    gd.setCornerRadius(15);


                    try {
                        tv.setTextColor(Color.parseColor(labels.get(i).getColor()));
                        gd.setStroke(1, Color.parseColor(labels.get(i).getColor()));
                    } catch (Exception e) {
                        tv.setTextColor(Color.parseColor("#" + labels.get(i).getColor()));
                        gd.setStroke(1, Color.parseColor("#" + labels.get(i).getColor()));
                    }
                    tv.setPadding(6, 0, 6, 0);
                    tv.setBackgroundDrawable(gd);
                    tv.setTextSize(12);
                    flowAdHolder.mLlLabels.addView(tv, layoutParams);
                }
            }


            //处理信息流广告的来源属性
            TextView tv = new TextView(mContext);
            LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
            layoutParams.gravity = Gravity.CENTER_VERTICAL;
            layoutParams.setMargins(0, 0, 16, 0);
            tv.setText(act.getSource());
            tv.setTextSize(12);
            tv.setPadding(16, 0, 16, 0);
            flowAdHolder.mLlLabels.addView(tv, layoutParams);


            //点击信息流广告跳转到InfoActivity加载广告url
            flowAdHolder.mLlFeed.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    //加载广告网页的界面，必须在清单文件中注册，并使用此界面加载网页广告，否则打开的网页并不能计入广告收益中！
                    InfoActivity.loadFeeds(mContext, act.getUrl(), act.getTitle());
                }
            });
        } else {
            //开发者自己列表属性的布局
            contentHolder.mTvContent.setText(item.getContent() + " - " + position);
        }
```
### 2.2.3、跳转自定义广告

| 跳转到广告页面方法     |	含义|
|-----------|----------------|
|InfoActivity.loadFeeds(Context context,String url);|加载广告，其中url是自定义方法获取到Url地址，请务必使用此方法加载地址，否则将视为网页加载，价格将视为网页价格。|
|InfoActivity.loadFeeds(Context context,String url,String title);|其中title为广告返回的Act的title属性值。|

参见示例：FlowDeveloperAdapter.java
```java
			flowAdHolder.mLlFeed.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    InfoActivity.loadFeeds(mContext,act.getUrl(),act.getTitle());
                }
            });
```






## 2.3、开屏引导页广告

### 2.3.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:orientation="vertical">


    <cn.bmob.feeds.act.FeedsSplashView
        android:id="@+id/fsv"
        android:layout_width="match_parent"
        app:countTime ="5"
        android:layout_height="match_parent">
    </cn.bmob.feeds.act.FeedsSplashView>
</LinearLayout>
```

| 属性名     |	含义	|
|-----------|----------------|
| countTime |	设置开屏广告的展示时间，此时间过后，开屏广告自动消失|


### 2.3.2、代码配置
```java
/**
 * Created on 17/11/28 15:38
 * 开屏广告
 *
 * @author zhangchaozhou
 */

public class SplashActivity extends BaseActivity {
    @BindView(R.id.fsv)
    FeedsSplashView mFsv;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);
        ButterKnife.bind(this);
        //设置跳转的Intent
        mFsv.setTargetIntent(this, new Intent(mContext, MainActivity.class));
        //设置加载时间，单位为秒数，
        //首次加载不会出现开屏广告，但是同样会加载相同时间，再进入目标界面
        //所以请在此界面设置开发者自己的欢迎页面背景，请参考界面R.layout.activity_splash
        mFsv.setCountTime(6);
        //开始加载开屏广告
        mFsv.loadFeeds();
    }


    @Override
    protected void onDestroy() {
        //释放资源
        if (mFsv != null) {
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
|	loadFeeds|加载广告|
|	destroy|销毁资源，在声明周期onDestroy时调用|


## 2.4、插屏广告，也叫弹窗广告
### 2.4.1、代码配置
```java
/**
 * Created on 17/11/29 08:49
 * 插屏广告
 *
 * @author zhangchaozhou
 */

public class InterstitialActivity extends BaseActivity {
    private FeedsInterstitialView mFeedsInterstitialView;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_interstitial);

        //创建对象
        mFeedsInterstitialView = new FeedsInterstitialView(this);
        //加载广告，第一次加载不会出现广告，此时出现的是开发者自己的界面内容
        mFeedsInterstitialView.loadFeeds();
    }

    @Override
    protected void onDestroy() {
        //释放资源
        if (mFeedsInterstitialView != null) {
            mFeedsInterstitialView.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告|
|destroy|销毁资源，在声明周期onDestroy时调用|
## 2.5、浮标位广告，也叫悬浮框广告
### 2.5.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
             android:orientation="vertical"
             android:layout_width="match_parent"
             android:layout_height="match_parent">

    <cn.bmob.feeds.act.FeedsDobberView
        android:layout_width="60dp"
        android:id="@+id/fdv"
        android:layout_gravity="center|right"
        android:layout_height="60dp">

    </cn.bmob.feeds.act.FeedsDobberView>


</FrameLayout>
```

### 2.5.2、代码配置
```java
/**
 * Created on 17/11/29 09:48
 * 浮标广告
 *
 * @author zhangchaozhou
 */

public class DobberActivity extends BaseActivity {
    @BindView(R.id.fdv)
    FeedsDobberView mFdv;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dobber);
        ButterKnife.bind(this);
        //加载广告
        mFdv.loadFeeds();
    }


    @Override
    protected void onDestroy() {
        //释放资源
        if (mFdv != null) {
            mFdv.destroy();
        }
        super.onDestroy();
    }
}
```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告|
|destroy|销毁资源，在声明周期onDestroy时调用|


## 2.6、Banner位广告，也叫横幅位广告
### 2.6.1、单张Banner位
#### 2.6.1.1、页面引用
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

#### 2.6.1.2、代码配置
```java
/**
 * Created on 17/11/30 09:30
 * 单条横幅广告
 *
 * @author zhangchaozhou
 */

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
        //加载广告
        mBannerTail.loadFeeds();
        mBannerTop.loadFeeds();
    }

    @Override
    protected void onDestroy() {
        //释放资源
        if (mBannerTop != null) {
            mBannerTop.destroy();
        }
        if (mBannerTail != null) {
            mBannerTail.destroy();
        }
        super.onDestroy();
    }
}

```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告|
|destroy|销毁资源，在声明周期onDestroy时调用|


### 2.6.2、多张Banner位轮播

#### 2.6.2.1、页面引用
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">

    <cn.bmob.feeds.act.banner.multi.FeedsBannerMultiView
        android:layout_width="match_parent"
        android:id="@+id/banner"
        android:layout_height="140dp">

    </cn.bmob.feeds.act.banner.multi.FeedsBannerMultiView>
</LinearLayout>
```
#### 2.6.2.2、代码配置
```java
/**
 * Created on 17/11/29 10:37
 * 横幅多条轮播广告
 *
 * @author zhangchaozhou
 */

public class BannerMultiActivity extends BaseActivity {
    @BindView(R.id.banner)
    FeedsBannerMultiView mFeedsBannerMultiView;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_banner_multi);
        ButterKnife.bind(this);
        //加载广告
        mFeedsBannerMultiView.loadFeeds();
    }

    @Override
    protected void onDestroy() {
        //释放资源
        if (mFeedsBannerMultiView != null) {
            mFeedsBannerMultiView.destory();
        }
        super.onDestroy();
    }
}

```
| 方法     |	含义	|
|-----------|----------------|
|loadFeeds|加载广告|
|destroy|销毁资源，在声明周期onDestroy时调用|

## 2.7、推送广告
### 2.7.1、代码配置
```java
                //推送通知广告，如果后台没有相关广告不会进行推送通知
                FeedsPush feedsPush = new FeedsPush(mContext);
                //设置小图标，若没有设置则默认显示SDK中的小图标
                feedsPush.setSmallIcon(R.mipmap.ic_launcher);
                feedsPush.loadFeeds();
```
| 方法     |	含义	|
|-----------|----------------|
|setSmallIcon(Integer id)|设置推送显示的小图标，没有设置则默认显示SDK中的小图标|
|loadFeeds|加载广告|


