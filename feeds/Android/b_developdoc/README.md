
           
# 1、BmobNewIM SDK 集成

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

