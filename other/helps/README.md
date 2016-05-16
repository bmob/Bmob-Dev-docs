## 平台常见问题

### Bmob有哪些优势？

1. 在很大程度上加快了用户产品的开发速度；对后端的支持让用户有更多的时间关注用户体验方面的设计。   
2. 为用户节省了服务端人员的配备和服务器租借，节省了很多成本。    
3. 在游戏方面，Bmob云端数据库保存玩家游戏数据，操作非常简单，用Api可以直接操作云端数据库。

### Bmob有后台介绍视频吗？

感谢Bmob用户为我们的后台录制了视频。    
视频地址：[使用 Bmob 搭建我们的第一个后端云 APP](http://www.jikexueyuan.com/course/2208.html)

其它的基础视频：[点击查看入门教程](http://pan.baidu.com/s/1nhPkq)

### Bmob支持多少用户同时在线存储查询？（并发）

一个APP支持10w+读并发，5w+写并发

### 服务器端运行在什么环境下？ 

北京BGP c机房 

### 你们的平台稳定吗？

Bmob采用南北双线，多路分流的方式，将服务器部署在国内外主流的大型服务器提供商中。部署时采用7层负载均衡技术，确保每个节点机房都能够承受大量的并发请求。而每个应用之间采用`Docker`容器虚拟化，确保应用之间的安全隔离性。自2013年7月创立至今，团队积累了大量的运维和服务经验，确保平台的稳定服务。 

### 数据放在云端安全吗？

- 首先，数据在传输过程中采用了`requestId` + `timestamp` + `Application Key`的一次性对称加密算法和服务端主动防御的技术，确保数据在传输过程中能够不被`Fiddler`、`Wireshark`等抓包工具恶意抓取进行分析破坏。
- 应用之间除采用Docker虚拟化之外，系统还定期/实时做了3级容灾备份，确保数据的可用性。
- 在软件架构层面，Bmob提供了应用层次、表层次、ACL、角色、IP白名单、签名等多种安全控制方式。如果你想更深入了解Bmob的安全架构，可以详细查看我们的[数据安全文档](http://docs.bmob.cn/datasafety/index.html?menukey=otherdoc&key=datasafety)。

### Bmob支持国外数据访问吗？

根据用户反馈，东南亚跟北美那边的访问速度还是可以的。欧洲那边的话，就我们的数据来看那边的访问量不是很高，但是可以访问。

### 你们的SDK会不会有广告？

没有，我们不做广告业务，SDK是非常纯净的，不会恶意嵌入广告的。


### 你们支持什么平台？

- Android、iOS和WP三种主流的移动操作系统平台
- Cocos2d-x和Unity两种主流的游戏引擎
- Js支持HTML5移动开发
- C#、php、Java支持PC端开发
- Restful开放API接口（可使用任何语言开发）

### 不同SDK的数据是否打通

当然！本质上，所有的SDK都是基于Restful开发，数据是完全打通的。

### 我想迁移数据到Bmob,但是user表如何迁移呢？

调用restapi的注册接口来插入数据就行

### Bmob怎么用做HTML5的数据管理后台?

你可以用js sdk来开发对应的html5页面，开发好之后联系我们客服，我们帮你把h5页面放到你的bmob子域名中去，给用户访问。

### 如何联系Bmob技术和商务

- 技术客服QQ：[2093289624](http://wpa.qq.com/msgrd?v=3&uin=2093289624&site=qq&menu=yes)
- 商务QQ：[2499654572](http://wpa.qq.com/msgrd?v=3&uin=2499654572&site=qq&menu=yes)
- 商务合作邮件：partner@bmob.cn
- 技术沙龙邮件：event@bmob.cn


## Web开发者后台相关问题

### 如何在Web后台上传文件（如图片）

Bmob提供了一种非常简单的文件上传的方法：

- 在Web后台中点击进入应用程序的控制面板中，如下图所示，选择需要用到文件的表，然后点击“添加一列”按钮，这时，弹出一个“添加新的表字段”的对话框。在这个对话框中，请输入字段名称，选择字段类型（**注意：请选择File类型**）。

![](image/addfile_1.png)

- 现在，你就可以快速上传文件了：点击“添加一行”按钮，在File字段中点击“Upload File”就可以直接上传文件。如下图所示。如果想要上传更多的文件，可以重复第二步操作。

![](image/addfile_2.png)

### 为什么导入CSV数据之后是乱码

请先将导入的数据编码转换为“UTF-8无BOM格式编码”之后再上传（转换为UTF-8编码的一个简单方法是：用[Notepad++](http://notepad-plus-plus.org/)打开要导入的CSV文件，然后点击“格式->以UTF-8无BOM格式编码”菜单）。

### 为什么导出的CSV数据显示乱码

导出的文件请以“UTF-8无BOM格式编码”格式打开查看(可使用[Notepad++](http://notepad-plus-plus.org/)打开CSV文件，然后点击“格式->以UTF-8无BOM格式编码”菜单)，如果用excel直接打开可能出现中文乱码！

### 能提供一个CSV文件参考下吗

[点击这里下载CSV文件模版](http://static.bmob.cn/new/developmentdoc/appdemo/bmobtest.csv)

使用方法：Web后台->创建应用->创建表->导入数据->选择这个CSV文件

### 能直接在Web后台上传文件吗

可以，先在表中创建需要File类型的字段，然后新增一条记录就可以直接在Web端上传文件了。

### 我如何修改Bmob上的表名、列名？

不能修改，考虑发布后的APP会由于修改表名和列名而造成无法使用的问题。开发过程中可以通过删除再创建达到目的。

### 导出数据表中的数据时可以自定义字段吗？

可以的。开发者后台->数据浏览->更多->导出数据，可以选择导出需要的列。

![](image/export.png)

### 能通过在控制台添加Relation的数据吗？

可以，你可以点击relation字段，进去之后添加数据

### 如何获取_User表中的password？

Bmob没有提供直接获取密码的方法。如果直接可以获取密码的话，会存在安全隐患的。如果非要这样做，你可以新增一个字段，记录明文的密码。

### Bmob中支持的String最大容量是多少？

4M

### 能不能设置主键？

可以，存在重复值会留下最早创建的记录，其它的删除。
![](image/primarykey.png)

## RestApi相关问题

### 如何在线测试RestApi

使用Chrome浏览器的Postman插件就可以进行调试了。[点击链接Postman下载地址](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?utm_source=chrome-ntp-icon)。

界面效果[点击这里查看](http://docs.bmob.cn/restful/faststart/index.html?menukey=fast_start&key=start_restful#index_RestAPI调试工具)。

### Postman发起数据请求没有反应

首先先检查本地网络，通常是因为本地网络或者Postman没有成功发出数据请求，其次可以打开[https://api.bmob.cn/](https://api.bmob.cn/)查看是否能打开进行测试。

### 其他语言用RestApi开发遇到请求security的错误

请查找相关语言访问HTTPS的配置问题。
如PHP用CURL开发时，需要添加如下脚本：

```java
curl_setopt($c, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($c, CURLOPT_SSL_VERIFYHOST, FALSE);
```


### 能提供Java调用RestApi的示例代码吗

点击下面的链接可以看到Java调用RestApi的示例代码：

[http://wenda.bmob.cn//?/question/51](http://wenda.bmob.cn//?/question/51)

[http://wenda.bmob.cn/?/question/859](http://wenda.bmob.cn/?/question/859)

### 关于where条件的问题

有开发者提出用PostMan请求的时候没有问题，但是用Java请求构造了where查询条件的时候有错（请求的格式大致如， `https://api.bmob.cn/1/classes/Footballer?limit=20&where={"location": {"$nearSphere": {"__type": "GeoPoint","latitude": 32.31735060,"longitude": 118.32457035 }}}` ）。

这个问题是因为特殊字符导致的问题，大家可以参考以下的解决方案：

[http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function](http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function)

顺便说一句，PHP中的话，可以直接用`http_build_query`方法构造请求参数。

### Restapi 有 IM 吗？

restapi可以发送推送信息，也可以通过get的方式获取聊天内容，但没有开放长连接服务，也就是说用restapi可以实现im功能，但方法只能通过定时器+get数据的方式。

### 哪里可以看到错误码

[点击这里](http://docs.bmob.cn/errorcode/index.html?menukey=otherdoc&key=errorcode)可以查看RestApi的错误码列表。

## Android相关问题

### 支持同步数据上传吗

不支持阻塞主线程同步上传数据的方法！

### SDK请求时占用内存大吗

如果只是数据服务的话，占用内存非常小。如果涉及图片服务，需要视图片大小而定内存占用情况。

### 文件能不能使用批量操作

当然可以。

### 定义类名必须和表中的名一致？

类名和表名一致，表内字段名和类变量名一致。

### Bmob如何实现储存和传输图片？

通过`BmobProFile`类上传图片，上传成功之后，会返回一个`BmobFile`，你从这个`BmobFile`可以得到文件上传之后的url，把这个url保存到你的对应表中。下载的时候，先查询数据表得到url，然后下载这个图片就可以了。 

### Bmob如何将整批图片下载在本地呢？

首先先查询，得到全部数据，从而得到图片的url列表，再用一些下载文件的代码把图片批量下载下来。

### BmobFile类最多可以保存多少张图片？

`BmobFile`类只能保存一张图片，你可以用`BmobFile`或者`BmobProFile`类上传图片，得到图片的url，保存的字段用string或者array。

### 怎么让表的某个字段包含多张图片？

用array来存储文件的url

### 查询单条数据的时候，只能通过objectId来查询么？ 

如果确定是只有一个的，条件查询也可以。

### 如果不知道objectId，是否可以通过表中的元素获得数据？

添加数据的时候，`onSuccess`中可以得到objectId。也可以通过条件查询得到对应的objectId的。

### 注册和登录的流程是怎样开发的

注册成功之后，服务器会返回`sessionToken`（标识用户登录成功的会话信息）给`BmobUser`对象，这时即可立即显示登录后台的界面，同步在后台调用登录接口进行登录操作。

### 登录踢人、改密码踢人相关

一处登录其他地方下线以及改密码的问题请看如下伪代码：
![](image/pseudocode.png)

### 打开了邮箱验证功能，注册成功后未验证也能登录成功？

Bmob SDK中，邮箱的验证和用户的注册登录是异步的关系，也就是说，即使用户没有点击邮箱验证功能，也是一样可以登录成功的。如果需要限制用户的登录或者只能查看到登录后的部分功能，可以使用`BmobUser.getEmailVerified`。

### Bmob如何实现用户登录之后获取数据读写权限，以及如何实现登出操作的？

用户登录之后，我们会把获取到的用户信息保存在本地文件中，你可以通过`BmobUser.getCurrentUser`方法获取对应的值，当调用 `logout`方法之后，这些缓存的数据就会清除。如果不调用`logout`方法，下次重新打开这个应用，还是可以通过`BmobUser.getCurrentUser`方法获得上次登陆的用户信息，从而判断是否登陆过。

### 我有个Relation字段，想用它来记录喜欢这篇文章的用户，我该怎么添加里面的数据呢？

这个问题请看 [数据关联](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_%E6%95%B0%E6%8D%AE%E5%85%B3%E8%81%94) 相关文档。

### Bmob数据库的pointer和我自己使用外建字段的区别？

pointer的好处是可以在查询的时候一并把关联的记录也查询下来，不需要二次查询。让查询的速度更快


### 新版文件管理上传文件大小限制，免费用户单文件50M/每应用，但是我上传的文件是28M，却提示我超过最大限制，请问这是为什么？

web开发者后台的文件服务中，有一个 文件大小限制 的功能，你可以在上面修改的。

### 查询成功，但是list只能在onSuccess方法中使用,如何在本类中的其他地方使用？

网路请求都是异步独立线程的，你用`handler`把数据传递出来就可以。

### Bmob怎么设计赞和踩功能？

利用原子计数器   
很多应用可能会有计数器功能的需求，比如文章点赞的功能，如果大量用户并发操作，用普通的更新方法操作的话，会存在数据不一致的情况。
详情请查看[原子计数器](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_%E5%8E%9F%E5%AD%90%E8%AE%A1%E6%95%B0%E5%99%A8)。

### 手机中安装两个包含bmob push sdk的app，那么这时另一个包含bmobpush sdk的app会报错。
解决方法：
删除androidMainfest.xml中
```<permission android:protectionLevel="normal" android:name="cn.bmob.permission.push"></permission>```
这一句。

其实这一句完全可以不加，也可以正常使用，亲测2个app推送正常，且不报错，关于原因请百度android permission相关知识（如果找不到再找客服吧~）

### 为什么我修改表中的某个Number类型的字段，其他Number类型的都变为0呢？

继承自BmobObject的类不要用int类型，用Integer。

## iOS相关问题

### 支持什么编译器

Bmob完全支持iOS 64bit/32bit的真机和模拟器调试。

### 请问有对应的swift，开发方法吗？
Swift项目中使用BmobSDK可以看这个文档：[Swift项目中使用BmobSDK](http://docs.bmob.cn/swift/index.html?menukey=otherdoc&key=swift)

### 哪里可以看到错误码

请查看iOS开发文档中的[错误代码列表](http://docs.bmob.cn/ios/developdoc/index.html?menukey=develop_doc&key=develop_ios#index_错误码列表)。

### 注册和登录的流程是怎样开发的

见Android相关部分。

### 打开了邮箱验证功能，注册成功后未验证也能登录成功？

Bmob SDK中，邮箱的验证和用户的注册登录是异步的关系，也就是说，即使用户没有点击邮箱验证功能，也是一样可以登录成功的。如果需要限制用户的登录或者只能查看到登录后的部分功能，可以使用`[[[BmobUser getCurrentUser] objectForKey:@"emailVerified"] boolValue]`方法。

### Bmob单张图片如何上传到bmob数据库？

用`BmobProFile`类的上传方法上传之后，在`onSuccess`中你会得到`BmobFile`或者这个文件的url，再把这个`BmobFile`或者url插入到`BmobObject`中，保存就可以了。

### 怎么获取relation中的数据? 

假设你有一个帖子(Post)类和一个系统默认的用户(User)类, 而每一个帖子(Post)都可以被不同的用户(User)所喜欢。 如果帖子(Post)类下面有一个Key名为likes，且是 Relation 类型, 存储了喜欢这个帖子(Post)的用户(User)。那么你可以找到喜欢过同一个指定的帖子(Post)的所有用户：

	BmobQuery *bquery = [BmobQuery queryForUser];
	[bquery orderByDescending:@"updatedAt"];
	BmobObject *obj = [BmobObject objectWithoutDatatWithClassName:@"Post" objectId:@"a1419df47a"];
	[bquery whereObjectKey:@"likes" relatedTo:obj];
	[bquery findObjectsInBackgroundWithBlock:^(NSArray *array, NSError *error) {
	}];

### 如果每个用户都有写入权限，安全应该怎么做？ 

可以设置ACL，详情请查看文档[ACL和角色](http://docs.bmob.cn/ios/developdoc/index.html?menukey=develop_doc&key=develop_ios#index_ACL%E5%92%8C%E8%A7%92%E8%89%B2)。

### 注册的时候如何给User表自定义的字段插值？
 
有个`BmobUser`类用来操作用户相关的数据  
	BmobUser *bUser = [[BmobUser alloc] init];  
	[bUser setUserName:@"小明"];  
	[bUser setPassword:@"123456"];  
	//age 为自定义  
	[bUser setObject:@18 forKey:@"age"];  
	[bUser signUpInBackground];

### 有iOS 点赞功能的demo吗？

有的，[Bmob点赞案例](https://github.com/limaofuyuanzhang/BmobLikeDemo) 实现的用户注册、用户登录、发贴、显示所有帖子资料以及对帖子进行点赞的功能 。

## 云端逻辑相关问题

### 使用云端逻辑需要掌握什么语言?

`Javascript`，因为云端逻辑是用`Nodejs`部署架构的。

### 云端逻辑能做什么事情?

云端逻辑的推出是为了给大家解决更多后端业务逻辑的问题，让大家尽可能把更多的业务逻辑挪到云端，实现更快的更新迭代。目前，云端逻辑除可以自由操作云端数据库外，还提供了`邮件模块`、`HTTP模块`和`事件模块`等，方便大家自由发挥。

### 云端逻辑能上传文件吗?

暂不支持。

### 如何调试云端逻辑?

- Bmob在Web端（当前云端逻辑的下边）为大家提供最简单的云端逻辑的调试工具。
- 云端逻辑本地化调试工具：[https://github.com/bmob/bmob-cloud-tool](https://github.com/bmob/bmob-cloud-tool) 。

## 推送服务相关问题

### 推送服务采用的协议是什么

`Websocket`

### 会不会限制推送消息的数量

没有限制！

推送的用户数量没有限制，每天推送的消息条数也没有限制，所有都没有限制。

### 服务器能支撑的长连接有多大

Bmob的推送服务器是耗内存型的，保持1个长连接占用<10KB的内存，64GB的内存能够支撑600万用户的长连接。

### Android推送收不到消息

1. 手机是否连入网络   
2. 包名（应用包名，看配置文件）是否正确填写在web后台中  
 
如果还是不能接收到推送，请检查：  

3. 手机是否有bmob的推送后台在运行  
4. 后台的Installation表有没有该手机对应的设备信息

### iOS推送接收不到消息

iOS的推送都是用apns。你确认是否操作了几点：  
1. 检查推送的代码是否写错;   
2. 真机操作；  
3. Bmob后台上传了未加密的p12证书；   
4. Bmob数据后台的Installation表是否可以看到对应数据。  
5. push token是否保存到服务器了

### 推送的耗电和耗流量情况怎样

以下说到的，不考虑推送的内容部分。推送内容的多少是由开发者决定的。

另外，实测电量、流量消耗，与网络状况相关比较大。

所以这里的数据是理论平均值：流量消耗 50K/天，电量消耗 60mAh/天。

### 可以推送富文本到客户端吗

不直接支持文件的推送，但可以通过推送 url 来实现。

即先推送文件下载 url，到客户端触发逻辑来通过 url 下载文件。

### iOS在服务端如何推送有声音和Badge提示

需要开发者自己定义JSON格式，格式如下：
```java
{
	"alert" : "You got your emails.",
	"badge" : 9,
	"sound" : "bingbong.aiff"
}
```

## 即时聊天相关问题

### IM的开源地址是什么？

Android：[bmob-android-im-sdk](https://github.com/bmob/bmob-android-im-sdk)   
   iOS : [bmob-iOS-im-sdk](https://github.com/bmob/bmob-iOS-im-sdk)

### 为什么我的手机接收不到信息

请先在Web后台配置包名或者证书。

详情查看IM常见问题部分：[IM常见问题](http://docs.bmob.cn/im/faststart/index.html?menukey=fast_start&key=start_im#index_%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E8%A7%A3%E7%AD%94)

### 消息推送的时候我没开启网络，在推送一段时间后再开启网络，会收到消息吗？

在断网开始的一分钟内发的消息是接收不到的，因为心跳包的默认时间是一分钟，这一分钟内，服务器不认为该链接是断开的，所以消息不会保存成离线消息。

### Bmob可以做群聊吗？

可以利用[数据实时同步](http://docs.bmob.cn/android/developdoc/index.html?menukey=develop_doc&key=develop_android#index_%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%90%8C%E6%AD%A5)来实现群聊。

思路可以这样：

1. 创建一个群聊表，包括 群组id信息 发送人信息 聊天信息，这里可以把所有的聊天内容放进去;  
2. 创建一个群组信息表，包括 群组用户列表 创建时间 群组名称等;  
3. 创建一个群组信息变更表，包括 群组id信息，用一个字段记录有消息新增;

当有人在群组中发起聊天时，首先先往群聊表中新增一条记录，然后往群组信息表更表中更新一下记录。所有群组的人都监听自己群组对应的群组信息表更表的几行。 

### 为什么发送位置的时候定位不了

1. 如果你是直接用demo里面的bin目录下的apk的话，是不存在这个问题的。
2. 如果你是下载demo之后直接运行的话，需要去重新去百度地图官网申请key,因为demo里面的可以是和我的eclipse绑定在一起的，相信做过百度地图开发的知道这是为什么。

## 短信服务相关问题

### 请问我要怎么购买?可以提供发票吗？

登录开发者后台-->专项服务-->短信服务 里面进行自由购买。
需要发票报销的可以联系客服开具发票。

### 为什么有时候收不到短信？

请检查你是否短时间内给同一个手机号码发送了多次短信，短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条。

### 短信服务的签名【云验证】这几个字可以换成我们自己定义的吗？

Bmob的短信服务不支持大家修改签名，也就是短信里面的“【云验证】”几个字。并不是说我们不想给大家修改这个签名，而是：运营商是通过这个签名来识别通道，如果一个通道里面包含各种各样不同的【***】信息的话，会让这个通道变成共享通道。这样做直接带来的不好就是：前期看似速度不错，当随着用户数增多，后期有可能被认定多客户信息混发或某个用户的恶意行为导致整个通道被监管监控，限制流速甚至会端口关停。
 
### 提交短信验证码模板时需注意什么？

1. 模板中不能有【】和 [] ，否则审核不通过；  
2. 如果你提交的短信模板无法发送，则有可能包含一些敏感监控词，具体可去Github下载 [短信关键字监控参考文档](https://github.com/bmob/bmob-public-docs/blob/master/%E7%9F%AD%E4%BF%A1%E5%85%B3%E9%94%AE%E5%AD%97%E7%9B%91%E6%8E%A7%E5%8F%82%E8%80%83%E6%96%87%E6%A1%A3.doc) 来查看提交内容是否合法。  
3. 一天一个应用给同一手机号发送的短信不能超过10条，否则会报10010错误，其他错误码可查看 [短信功能相关错误码](http://docs.bmob.cn/errorcode/index.html?menukey=otherdoc&key=errorcode#index_%E7%9F%AD%E4%BF%A1%E5%8A%9F%E8%83%BD%E7%9B%B8%E5%85%B3%E9%94%99%E8%AF%AF%E7%A0%81) 。

## 支付服务相关问题

### 请问你们的支付sdk怎么收费？

渠道费和公司税共收取5%。其中渠道费是支付宝/微信收取的费用。

### 为什么我支付了0.01，后台看到只有0.00？

请勿担心，在Bmob财务管理平台订单管理处，金额从小数点后第三位开始不显示，比如支付了0.01元实收0.00，其实是0.0095。

### 有哪些其它需要注意的事项？

支付问题：[点击查看其它注意事项](http://docs.bmob.cn/androidpay/index.html?menukey=fast_start&key=start_android_pay#index_%E5%85%B6%E4%BB%96)

## APP官网相关问题

### APP官网的一些案例

[http://easytouch.bmob.cn/](http://easytouch.bmob.cn/)

[http://xiaocai.bmob.cn/](http://xiaocai.bmob.cn/)

[http://multimomo.bmob.cn/](http://multimomo.bmob.cn/)

### 不使用SDK也可以创建APP官网吗

是的。APP官网是我们为开发者提供的扩展服务，与SDK、云数据库、文件服务等其他业务没有任何联系，完全是耦合的关系。

使用APP官网的流程如下：注册进入官网->创建应用->点击“应用官网”->输入你想要的域名->选择模版->编辑保存。

### 官网修改后怎么没有立即生效

我们采用生成静态文件的方式，生成和同步到泛域名服务器需要30秒的时间，请耐心等待。

### 能使用其他域名吗

可以。但需要提交一些材料给我们（具体材料需要先联系我们的客服QQ），提交到工信部进行备案。

### 怎么知道我的域名是否备案

登录网站 [http://www.miitbeian.gov.cn/publish/query/indexFirst.action ](http://www.miitbeian.gov.cn/publish/query/indexFirst.action ) 按操作查询即可

