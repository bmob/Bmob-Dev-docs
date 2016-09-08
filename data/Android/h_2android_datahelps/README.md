
## 对象

Q:为什么我修改表中的某个Number类型的字段，其他Number类型的都变为0呢？
A:继承自BmobObject的类不要用int类型，用Integer。

---

Q:定义类名必须和表中的名一致？
A:类名和表名一致，表内字段名和类变量名一致。

---

Q:插入一条数据之后怎么获得该数据的id
A:

```
GameScore gameScore = new GameScore();
//注意：不能调用gameScore.setObjectId("")方法
gameScore.setPlayerName("比目");
gameScore.setScore(89);
gameScore.setIsPay(false);
gameScore.save(mContext, new SaveListener() {

@Override
public void onSuccess() {
    toast("添加数据成功，返回objectId为："+gameScore.getObjectId() + ”,数据在服务端的创建时间为：“ + gameScore.getCreatedAt());
}

@Override
public void onFailure(int code, String arg0) {
    // 添加失败
}
});
```
请看代码，成功后gameScore使用getObjectId()就可以获取objectId了。


---
Q:success方法中获取的数据，用全局变量接收，但是在方法外就接受不到，变量为空
A:请先理解同步和异步的概念，回调中的onsuccess是异步方法，是不能用全局变量接收的，可以直接在onsuccess方法中做ui层面的更新

---

Q:缓存路径能指定吗？
A:不可以

---

Q:我在User表中增加了一个Number类型的字段，设置为以1自增，但几天过去了，里面的值并未自动增加，这个自增是怎样自增的，还要去哪里设置吗？
A:添加字段的时候选择num类型，有一个自增的checkbox和初始值的input，填一下就可以了

---

Q:场景是多个客户端共同操作同一个数据表，更新该表的一个字段的值，如何做到一个客户端更新时锁定该数据表，操作结束时解锁？
A:目前没有该功能，只有Number类型可以使用原子计算器达到该效果

---

Q:一次查询多条数据 算是调用了几次API？
A:一次调用算一次

---

Q:请问每条数据的objectId都是唯一的吗，我的意思是，假设有个user表，有userName和level两个属性。如果两个对象的这两个属性都相同，其objectId是否就相同？ 
A:objectId是每一条数据的唯一标示，不会出现重复的。

---

Q:String 数据类型最大可支持多大数据？
A:最大为16M

---

Q:求问怎么获取上传数据后生成的objectid
A:保存数据成功后，你的BmobObject对象就有objectId了的。
或者你按条件查询得到的数据对象中也是包含objectId的。

---

Q:数据查询必须要objectId吗？在用户数据表中，在无法获取用户objectId的情况下，如何查询一个用户是否存在，是否可以通过其他字段查询？
A:单条查询必须使用ObjectId，多条查询时可以添加条件来进行查询。

---

## 查询

Q:查询成功，但是list只能在onSuccess方法中使用,如何在本类中的其他地方使用？

A:网路请求都是异步独立线程的，你用`handler`把数据传递出来就可以。

---

Q:如果不知道objectId，是否可以通过表中的元素获得数据？

A添加数据的时候，`onSuccess`中可以得到objectId。也可以通过条件查询得到对应的objectId的。

---

Q:在Activity关闭的时候如何动态关闭查询？
A:查询都是一次性的，并不需要取消

---
Q:bmob怎么查询一列数据并合并相同数据？
A:可以使用统计查询中的groupby

---

Q:bmob的数据库操作方法save insert这些都有开线程吗
A:bmob sdk提供的操作都是在线程中运行的，对外都是提供异步的回调方法，其回调方法，比如onsuccess和onFailure等是可以再UI线程中运行的，开发者不需要额外再开线程。

---

Q:Bmob查询数据结束标志
A:在查询的回调方法中的onSuccess或onFailure触发时都是代表这个查询结束。

---
Q:Bmob能否进行多表查询？
A:无法用一条语句查多张表，只能单独一个一个查

---

Q:怎么设置缓存 让缓存数据在listView显示
A:可以使用缓存查询，具体可以查看官方文档

---

Q:怎么通过BmobUser的一个属性列来获取其他列的信息
A:使用bmob的查询功能，查询Username 等于名称的用户信息即可，使用的是addWhereEqualTo方法

---

Q:我的应用想只查询最新上传的一组数据，请问该如何操作呢
A:可以根据时间来设置条件，再进行查询

---

Q:查询中findListener中的onerror方法不执行,两次测试均是数据库无该数据的，但是程序均不执行onerror方法
A:没有数据不代表查询出错，当没有数据符合你的查询条件时，就会返回空，此时还是执行onSuccess的

---

Q:想问问查询的时候排序有多个关键字怎么解决？
A:数据服务的文档中有的，在开发文档的查询数据->查询条件->排序那里：

排序

对应数据的排序，如数字或字符串，你可以使用升序或降序的方式来控制查询数据的结果顺序：

// 根据score字段升序显示数据
query.order("score");
// 根据score字段降序显示数据
query.order("-score");
// 多个排序字段可以用（，）号分隔
query.order("-score,createdAt");
说明：多个字段排序时，先按第一个字段进行排序，再按第二个字段进行排序，依次进行。

---

## 数据关联

Q:我有个Relation字段，想用它来记录喜欢这篇文章的用户，我该怎么添加里面的数据呢？
A:这个问题请看 [数据关联](http://docs.bmob.cn/data/Android/b_developdoc/doc/index.html#数据关联性) 相关文档。

---

Q:Relation字段 能否像pointer一样在查询的时候一并把关联的记录也查询下来，不需要二次查询
A:目前并没有这个功能，建议使用pointer

---

Q:activity是一个表，里面有一个BmobPointer org指向BmobUser，
activity.getOrg.getObjectId总是为空呢？
A:查询的时候应该没有用includ查询进去

---

Q:查询数据时，表中的一个字段是pointer字段，如何将这个pointer字段的一个属性作为查询匹配条件？
A:
```
BmobQuery<Comment> query = new BmobQuery<Comment>();
BmobQuery<Post> innerQuery = new BmobQuery<Post>();
innerQuery.addWhereExists("image", true);
// 第一个参数为评论表中的帖子字段名post
// 第二个参数为Post字段的表名，也可以直接用"Post"字符串的形式
// 第三个参数为内部查询条件
query.addWhereMatchesQuery("post", "Post", innerQuery);
query.findObjects(this, new FindListener<Comment>() {
@Override
public void onSuccess(List<Comment> object) {
    // TODO Auto-generated method stub
    toast("查询成功:");
}
@Override
public void onError(int code, String msg) {
    // TODO Auto-generated method stub
    toast("查询失败:"+msg);
}
});
```
如上，就是使用内部查询就可以实现你的需求了，只需要将查询条件和表名换成你需要的就可以了～

---

Q:如果一个用户要收藏一个产品应该怎么定义对象
A:可以使用pointer类型

---

Q:删除关联关系
我现在有评论类如下：
```
public class Comment extends BmobObject {
private String comment;
private Found found;
private Lost lost;
private MyUser user;
}
```
如果我想删除Lost类或者Found类的某条数据
其对应的Comment会被同时删除吗？
如果不能应该怎么实现同时删除？
A:不能同时删除，对于不同数据表的数据只能分别执行删除操作。

---

Q:查询时 include 两个Pointer字段后会把前一个的数据冲掉
对表 AaaaEntity 查询时 include 两个Pointer字段后会把前一个的数据冲掉,如:
bmobQuery.include("userAuthor");
bmobQuery.include("xxxxEntity");

则:
aaaaEntity.getUserAuthor.getName()返回空

如果去掉后一行
bmobQuery.include("userAuthor");
//bmobQuery.include("xxxxEntity");
或
移到后面：
bmobQuery.include("xxxxEntity");
bmobQuery.include("userAuthor");

aaaaEntity.getUserAuthor.getName() 就能正常返回值

A:include的用法在文档里面已经说明了的(http://docs.bmob.cn/android/de ... clude用法),想include多个就这样用：query.include("x1,x2");

---

## 用户管理

Q:打开了邮箱验证功能，注册成功后未验证也能登录成功？
A:Bmob SDK中，邮箱的验证和用户的注册登录是异步的关系，也就是说，即使用户没有点击邮箱验证功能，也是一样可以登录成功的。如果需要限制用户的登录或者只能查看到登录后的部分功能，可以使用`BmobUser.getEmailVerified`。

---

Q:Bmob如何实现用户登录之后获取数据读写权限，以及如何实现登出操作的？
A:用户登录之后，我们会把获取到的用户信息保存在本地文件中，你可以通过`BmobUser.getCurrentUser`方法获取对应的值，当调用 `logout`方法之后，这些缓存的数据就会清除。如果不调用`logout`方法，下次重新打开这个应用，还是可以通过`BmobUser.getCurrentUser`方法获得上次登陆的用户信息，从而判断是否登陆过。

---

Q:BmobUser.logOut(getApplicationContext()); //清除缓存用户对象
这个只是针对本地缓存，看了源码，只是对本地清除，没有真正向服务端注销登录的账号，请问是怎么去处理这个问题
A:BmobSDK中的BmobUser登录，只是登录成功后缓存用户信息到本地。服务端并没有记录用户的登录状态，所以退出登录并不需要向服务器注销。

---

Q:Bmob支持第三方登录吗？怎么做？
A:支持，官方的文档上有介绍。


---

Q:为什么邮箱验证还没去验证却可以登录
A:是可以的，邮箱验证那个字段需要开发者根据需求自行决定要不要使用

---

Q:登录时异常退出MyUser declares multiple JSON fields named mobilePhoneNumber
A:MyUser定义了一个Bmob的系统字段呀，你可以看看用户管理那里的文档，里面有介绍说明BmobUser的特有属性，以下摘抄自文档：

BmobUser除了从BmobObject继承的属性外，还有几个特定的属性：
username: 用户的用户名（必需）。
password: 用户的密码（必需）。
email: 用户的电子邮件地址（可选）。
emailVerified:邮箱认证状态（可选）。
mobilePhoneNumber：手机号码（可选）。
mobilePhoneNumberVerified：手机号码的认证状态（可选）。

---

Q:第三方登录之后该怎么获得User的objectId呢？
A:登陆成功之后，系统就会给你生成一个objectId的，你正常的通过 user.getObjectId() 就可以得到了

---

Q:如何修改user表中其他用户的数据
A:直接在web控制台修改或者使用masterkey

---

Q:修改数据提示User cannot be altered without sessionToken Error.
A:这种情况一般都是没有进行用户登录就对用户信息进行更新导致的

---

Q:更新用户update时失败，9012错误
A:9012是context is null,没传上下文对象

---

Q:用了getobjectId方法为什么还是显示objectId cant't be empty。
```
public void setsj(View v){
final User setUser = new User();
List<String> a=new ArrayList<String>();
a.add("5cd431f659");
a.add("4c2184e8ea");
setUser.setFriends(a);
setUser.update(this, setUser.getObjectId(), new UpdateListener() {

    @Override
    public void onSuccess() {
        // TODO Auto-generated method stub
        Log.i("bmob", "更新成功：");
        toast("更新成功");
    }

    @Override
    public void onFailure(int code, String msg) {
        // TODO Auto-generated method stub
        Log.i("bmob","更新失败："+msg);
        toast("更新失败");
    }
});
}

```
这段代码是通过点击一个button然后更新我写好的数据，可一直显示更新失败，查看logcat显示objectId cant't be empty。可是我有用setUser.getObjectId()啊，为什么还是说ID为空？
A:你的user并不是通过登录得到的，而是自己生成的，并没有objectid，需要从服务器上获取的数据才有objectid

---

Q:更新了用户信息后 服务器都更新了数据 但是本地缓存用户没有更新
A:更新用户信息后需要从新登陆，本地用户信息才会更新。

---

Q:登录后在个人资料中上传头像在用户表，并且要头像和用户要对应。
A:上传图片成功后，将BmobFile对象更新到当前用户的头像字段中即可。

---

Q:在数据下_User 下把email添加后为啥 emailVerified Boolean 这一栏显示的是false 是什么原因呢
A:该字段需要注册用户点击了验证邮件才会主动设置为true

---

## 数据实时功能

Q:如何实现Bmob服务端向Android应用发送实时通知？如某个数据过高需要提醒APP用户
A:以使用实时数据监听功能来实现

---

Q:登录之后，怎么获取用户的信息并显示出来
A:登录成功之后就可以通过getCurrentUser方法获取本地用户信息

---

## ACL和角色

Q:例如我已经有一个角色叫Chief，我怎么用代码给它添加成员？直接new BmobRole("Chief")然后再getUsers().add然后再save?
但这个代码不应该是新建一个角色吗？
如果是用BmobQuery<BmobRole>获取的话获取失败，返回101错误
A:

```
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
```

---

Q:是否可以针一行数据的某一个字段控制读写权限 ？
是否可以针一行数据的某一个字段控制读写权限 ？例如，UserA 发了一条说说，这条说说只有UserA可以写，其它用户可以读，但是其中有一个点赞计数字段，所有用户都可以对这条说说点赞，点赞后，这个点赞计数字段值就加1。
A:没有针对一个字段控制读写的，如果需要，可以将这些需要控制的另外建一个表，使用pointer字段指向该表来获取

---

## 地理位置

Q:基于地理位置的查询是根据什么排序的
A:是按照距离从近到远来进行排序的

---

## 自动更新

Q:自动更新生成的表和文档上的不一致
A:没有调用initAppversion方法

---

Q:自动更新，如何实现只获取是否有新版本，不弹出对话框。
A:目前SDK中暂没有这个功能，后续版本会考虑添加

---

## 文件

Q:Bmob如何实现储存和传输图片？
A:通过`BmobFile`类上传图片，上传成功之后，会返回一个`BmobFile`，你从这个`BmobFile`可以得到文件上传之后的url，把这个url保存到你的对应表中。下载的时候，先查询数据表得到url，然后下载这个图片就可以了。 

---

Q:Bmob如何将整批图片下载在本地呢？
A:首先先查询，得到全部数据，从而得到图片的url列表，再用一些下载文件的代码把图片批量下载下来。

---

Q:BmobFile类最多可以保存多少张图片？
A:`BmobFile`类只能保存一张图片，你可以用`BmobFile`上传图片，得到图片的url，保存的字段用string或者array。

---

Q:怎么让表的某个字段包含多张图片？
A:用array来存储文件的url

---

Q:能把json文件放在bmob里，并配置一个专门的地址，然后APP通过这路径下载或者读取吗？
A:用文件服务实现即可。

---

Q:怎么通过objectID获得文件的下载路径？
A:

```
// 根据objectId查询数据
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

// 假设GameScore对象中有一列是BmobFile类型的icon
// 如下取出查询到的数据中的BmobFile类型，得到下载地址
BmobFile icon = object.getIcon();
String url = icon.getFileUrl();

}

@Override
public void onFailure(int code, String arg0) {
// TODO Auto-generated method stub
toast("查询失败："+arg0);
}

});

```

---

Q:为什么最新的SDK里面的BmobFile没有loadImageThumbnail方法
A:最新的SDK已经将图像处理的接口去掉了，需要用户自己在本地处理


---

Q:怎么去在批量上完图片之后，取得对应的缩略图呢？
A:缩略图功能已经取消，请在客户端进行图片的处理

---

Q:9015your uploading task is canceled. 安卓文件上传无响应
A:调用了bmobfile.cancel()方法会出错该提示。

---

Q:表中有一列数据为BmobFile类型 ，数据为.TXT格式 ，怎么将文件下载下来
A:查询这一行数据，在结果中从这一列对应的BmobFile对象中用getFileUrl()得到文件的地址，再进行下载。

---
Q:listview显示Bmob上的图片怎么实现？
A:图片上传后会返回相应url给客户端的，可以在表中，在需要显示的时候利用url将图片下载下来进行显示

---

Q:我通过软件上传了文件到bmob后端云。怎么获得该文件url。
A:上传后会有url返回

---
Q:bmob可以通过客户端下载文件吗？
A:上传到Bmob之后就会返回文件的地址，至于怎么下载，由你来决定。

---
Q:为什么我上传的文件(图片)已经删除了，但是通过url仍然可以访问到图片呢？
A:CDN缓存的，过一段时间就会完全删除

---

## 其它问题

Q:Cause: com.android.dex.DexException: Multiple dex files define Landroid/support/v4/accessibilityservice/AccessibilityServiceInfoCompat$AccessibilityServiceInfoVersionImpl;
Error:Execution failed for task ':app:transformClassesWithDexForDebug'.
A:Cause: com.android.dex.DexException: Multiple dex files define Landroid/support/v4/accessibilityservice/AccessibilityServiceInfoCompat$AccessibilityServiceInfoVersionImpl;
Error:Execution failed for task ':app:transformClassesWithDexForDebug'.

这是重复导包造成的，你用的v4和v7包重复了，你需要去掉报错的那个模块

---

Q:我使用BmobUser.login接口,返回onFailure的参数code分别对应什么情况?有说明文档么?
A:[android 错误码列表](http://docs.bmob.cn/data/Android/g_errorcode/doc/index.html)

---

Q:App_ID is not setted出现什么原因？
A:初始化BmobSDK时需要传入你自己应用的ApplicationID

---

Q:Duplicate files copied in APK META-INF/maven/com.squareup.okhttp/okhttp/pom.xml
A:导了重复的包

---

Q:bomb_AndroidSDK_V3.4.7_0518 中文乱码
A:SDK的demo的编码格式是gbk,换下格式就行

---

Q:android Bmobquery 开两个线程 分别查两个不一样的表，返回数据有问题
A:sdk中的很多方法本身就是在子线程中执行的，开发者没必要开子线程(创建子线程和线程池管理SDK都封装好了)。

---

Q:查询出了回调函数，List就变空了
A:请先理解下同步和异步的概念，bmob目前的接口提供的都是异步回调函数，建议在onsucess/onFailure中进行ui操作

---

Q:Bmob的各个SDK可以使用Application的Context来初始化吗
A:可以的。最好是用Application的Context来初始化。

---

Q:bmob的jar包和volley包冲突怎么回事
A:bmob的jar里面包含了volley,无需再次导入

---

Q:移动端和WEB端能用BMOB作为云端，实现数据共享么？
A:可以的，使用同一个app id来进行操作即可

---

Q:真机运行时控制台输出 Error:warning: Ignoring InnerClasses attribute for an anonymous inner class
A:可以试试在你的app的build.gradle的android标签下添加如下：
```
lintOptions {
ignoreWarnings true
}
```

---

Q:把应用装到手机很慢，一直在Gradle Build
A:如果你确定你的应用所需的jar包已经下载完了，可以将gradle设置成离线模式

---

Q:BmobSDK能导入源码开发编译吗
A:BmobSDK目前并未开源

---

Q:3.4.7 sdk java.lang.UnsatisfiedLinkError 怎么破
java.lang.UnsatisfiedLinkError: Couldn't load bmob from loader dalvik.system.PathClassLoader[DexPathList[[zip file "/data/app/top.kiuber.sharemy-1.apk"],nativeLibraryDirectories=[/data/app-lib/top.kiuber.sharemy-1, /vendor/lib, /system/lib]]]: findLibrary returned null
A:详情移步到博客http://www.kiuber.top/2016/05/24/android-studio-add-so/
问题已经被攻克，原因是so库文件未导入。
解决方法：在project视图下，在main文件夹内新建jniLibs文件夹，把对应so库文件夹及文件复制到jniLibs文件内，然后在MainActivity.java文件

---

Q:邮箱验证用哪个SDK
A:使用数据服务SDK即可

---

Q:Android studio连接Bmob时报错
```
java.lang.UnsatisfiedLinkError: com.android.tools.fd.runtime.IncrementalClassLoader$DelegateClassLoader[DexPathList[[dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-support-annotations-23.4.0_eff36cb3dd5776bcc7dfe63d3c4af3d7d0b02909-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_9-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_8-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_7-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_6-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_5-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_4-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_3-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_2-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_1-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-slice_0-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-okio-1.7.0_16f89fb230458d29c309937f6ab11ce75258c504-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-okhttp-3.2.0_8f755226a0726d7921fa90d83c674c16a1bd0ee3-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-internal_impl-23.4.0_2c4831db21059d6465959fb999a28d5a6fe10599-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-com.android.support-support-vector-drawable-23.4.0_eb28b4ae1a0615e1130648d3b547db30e6e89fd0-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-com.android.support-support-v4-23.4.0_c594c96eba293bbb78cda22a0502566240fb4409-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-com.android.support-appcompat-v7-23.4.0_39e8b9d21669eb9eb3df764bcd49eb0facc75e07-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-com.android.support-animated-vector-drawable-23.4.0_613291d2784b41eebf3800d518847e90b2efa55b-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-bmob-sdk-3.4.7_3bb8e20fe85419a97fea506c0c8a8a8fe18f45d5-classes.dex", dex file "/data/data/com.example.administrator.bmob2/files/instant-run/dex/slice-bmob-push-0.9_35e71e7e49d7888481221634e134fec14816f381-classes.dex"],nativeLibraryDirectories=[/data/app/com.example.administrator.bmob2-2/lib/arm, /vendor/lib, /system/lib]]] couldn't find "libbmob.so
```
A:使用aar格式的SDK，这样就不用导入so库

---
Q:sdk怎么和Retrofit这些库一起使用呢

A:出现这个问题的原因是retrofit依赖的okhttp和sdk的远程aar包中的okhttp重复导致的，将会导致编译不过，解决方式有：<br>
1 下载Retrofit的jar包，采用本地依赖的方式；<br>
2 compile Retrofit的配置加下exclude，把重复的okhttp除去，如下：<br>
```
compile ('com.squareup.retrofit2:retrofit:2.1.0'){
        exclude group : 'com.squareup.okhttp3'
    }
```

---
