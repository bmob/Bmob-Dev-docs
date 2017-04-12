v3.5.4 2017年04月12日
1. 修复若干bug；
2. SDK内部改进。
注:此次更新开发者不用修改之前的常用代码。

v3.5.3 2016年11月09日
1. 增加了对新的短信服务的支持；
2. 修复了旧版本使用推送调用BmobInstallation的save()方法导致的内存吃紧问题。
注:此次更新开发者不用修改之前的常用代码。

v3.5.2 2016年10月27日
1. 集成了统计SDK功能,开发者不用额外集成；
2. 修复了一键注册登录的用户扩展字段信息没有保存到本地的bug；
3. 对文件上传到又拍云回调成功但返回的path为"null"的低概率情况加了判断并提示重传；
4. 修改了BmobQuery等部分细节代码；
注:此次更新开发者不用修改之前的常用代码 

v3.5.1 2016年07月20日
1. 新增对数据迁移的支持。

v3.5.0 2016年06月27日
1. 使用`Rx+okhttp3`全面重构BmobSDK，所有方法均额外提供Observable形式调用；
2. Context参数变化：除初始化方法外，其余方法均不再需要传递Context参数
3. 回调函数变化：
	1)、所有回调函数的onSuccess、onFailure方法统一调整为done(T t,BmobException e)形式；
	2）、合并以下回调函数：
		GetListener<T>->QueryListener<T>
		GetCallbackGetCallback->QueryListener<JSONObject>
		FindCallback->QueryListener<JSONArray>
		DeleteListener、EmailVerifyListener、ResetPasswordByCodeListener、ResetPasswordByEmailListener、VerifySMSCodeListener->UpdateListener
		GetServerTimeListener->QueryListener<Long>
		OtherLoginListener-->LogInListener<JSONObject>
		StatisticQueryListener、FindStatisticsListener->QueryListener<JSONArray>
		GetTableSchemaListener->QueryListener<BmobTableSchema>
		GetAllTableSchemaListener->QueryListListener<BmobTableSchema>
		QuerySMSStateListener->QueryListener<BmobSmsState>
		RequestSMSCodeListener->QueryListener<Integer>

2. 新增`BmobBatch`批量操作类，`支持批量添加、批量更新、批量删除的同步提交`，且批量添加的请求返回objectId字段；
3. 修复由于手机时间与服务器时间相差较大引起的调用文件的upload方法返回`401 signature error`错误；
4. 解决`AndroidStudio`运行BmobSDK后出现`Ignoring InnerClasses attribute for an anonymous inner class`的警告；
5. 同步更新`BmobPush_v1.0.0`推送SDK。

v3.4.7  2016年5月27日
1. 文件服务的依赖库升级到okhttp3;
2. 修复调用文件的download方法有时成功有时却提示`file download error(9015)`的错误;
3. 修复连接网络不稳定的wifi进行文件上传操作时出现`RuntimeException: json 解析出错`的问题;
4. 数据加解密改用底层so库实现，进一步保证数据安全;
5. 将权限管理类PermissionManager分离出SDK以减少依赖关系;
6. 修复部分机型出现的z.so无法加载的问题;
7. 同步更新BmobPush_v0.9的SDK。

v3.4.6   2016年4月20日
附加：
1. [关于旧版本自动更新组件出现解析包出错问题的解决方法](http://docs.bmob.cn/data/Android/e_autoupdate/doc/index.html#常见问题)；
2. 修复v3.4.6_0413版本中自动更新组件出现的AndroidRuntimeException问题；
3. 增加文件服务的AppKey和Wake_Lock权限检测。
新增：
1. 新增CDN文件服务，废弃原来的新旧文件服务，但上传方法名不变；
2. 新增文件下载download方法，允许设定文件的下载目录；
3. 新增文件批量删除接口（只针对于通过CDN文件服务上传的文件）；
4. 新增BmobConfig类，允许开发者设置查询超时时间及文件上传时的每片大小；
5. 全面兼容Android6.0系统，并增加权限管理工具类(PermissionManager)，方便开发者对权限进行控制和管理。
转移：
文件的批量上传的静态uploadBatch方法由Bmob类转移至BmobFile类。
修复：
1. 修复调用add、addUnique、remove、removeAll、increment、setValue等方法成功后本地用户信息未及时更新的问题；
2. 修复自动更新组件中target_size填成英文导致应用奔溃的问题；
3. 修复部分机型出现的缓存查询失效的问题；
4. 修复同时发送多个查询请求时会出现多次初始化的问题；
5. 修复由手机系统时间的修改而导致的sdk time error的问题（需要在应用启动页面的onCreate方法中调用Bmob.getInstance().synchronizeTime(context)方法）。

v3.4.5   2015年11月11日
1. 修复特殊网络情况下出现400错误的问题；
2. 新增getObjectByKey方法获取当前登陆用户的某一列的值；
3. setValue方法支持java基本数据类型；
4. 强制更新模式下隐藏右上角关闭按钮。

v3.4.4   2015年9月30日
1. 新增数组更新方式，并同步支持所有类型的字段更新；
2. 自动更新组件回调方法中添加更新错误提示

v3.4.3   2015年8月20日
1. 新增修改当前用户密码的方法；
2. 修复V3.4.3_0820版本中一键注册或登录方法出错的问题；
3. 推送的频道订阅增加去重操作；
4. 使用okhttp优化网络框架，提升网络效率。

v3.4.2   2015年7月27日
1. 新增获取表结构信息的方法 

v3.4.1   2015年7月10日
1. 短信服务允许自定义短信内容； 
2. 新版文件服务新增获取文件地址的方法；
3. 新版文件服务新增文件删除功能；
4. 新旧文件服务同步兼容BmobFile对象；
5. 优化新版文件上传与下载的进度提示。

v3.4.0   2015年6月16日
1. 短信验证统一采用BmobSMS类； 
2. 修复其他bug。

v3.3.9   2015年6月12日
1. 新增短信验证API； 
2. 用户登录新增多种登录方式：邮箱+密码、手机号码+密码、手机号码+短信验证码； 
3. 修改第三方账号登录方式，并新增对微信登录的支持；
4. 修复自动更新组件多次点击忽略版本的更新按钮报错的bug。

v3.3.8   2015年5月21日
1. addWhereEqualTo方法支持一对多关联关系查询；
2. 缓存查询方法允许单独使用； 
3. 修复BQL查询设置缓存策略后查询出错的问题。

v3.3.7   2015年5月13日
1. 新增SQL语句查询，让查询更简单；
2. 解决新版文件管理处理本地缩略图时出现OOM的问题； 
3. 解决新版文件管理提交缩略图任务时modelId无效的问题。

v3.3.6   2015年4月27日
1. 新增统计查询方法，可查询总和、平均值、最大值、最小值并支持分组和添加过滤条件。 

v3.3.5   2015年4月08日
1. 自动更新组件中修复自动更新方式下无法强制用户更新应用的问题；
2. 自动更新组件中支持监听对话框按键操作；
3. BmobPush同步更新到V0.6版本。

v3.3.4   2015年3月10日
1. 修复自动更新组件的自动更新方式下“忽略该版”按钮选中再取消后无法再次出现版本更新提示的问题；
2. 自动更新组件中允许下载已上传到应用市场的apk文件（须填写应用市场下载地址android_url）;
3. 自动更新组件中支持对更新内容根据分隔符“；”进行文字排版;
4. 修复某些特定情况下调用BmobUser的update方法后无法获取最近更新的用户信息;
5. 修复新版文件管理中开启URL签名认证后无法获取签名地址的问题。

v3.3.3   2015年2月11日
1. 修复第三方登陆成功后无法获取本地用户信息的问题；
2. 修复设置缓存策略后无法获取本地缓存信息的问题；
3. 修复调用云端代码（callEndpoint）方法的成功回调的返回值中含有“results”的问题；
4. 新版文件管理中对本地缩略图的处理方法新增压缩质量的参数。

v3.3.2   2015年1月27日
1. 整体优化新版文件管理的代码结构和处理流程；
2. 修复自动更新的初始化方法无法自动创建AppVersion表的问题。

v3.3.1   2015年1月21日
1. 修复新版SDK由于初始化方法未成功返回而导致的sdk time error、internal error等错误；
2. 修复调用BmobUser的update方法成功之后再调用getCurrentUser方法无法获取已更新信息的问题。

v3.3.0   2015年1月19日
1. 新增数据加密功能，保障数据安全，强烈建议更新SDK；
2. 新增文件删除功能；
3. BmobProFile类增加URL签名方法；
4. 修复BmobProFile类获取本地缩略图的方法出错的问题；

v3.2.9   2015年1月12日
1. 新增新版文件管理BmobProFile类，提供了单一上传、批量上传、下载文件、生成缩略图等功能。

v3.2.8   2015年1月05日
1. 修改单一文件upload方法报NullPointerException错误的问题；
2. 修改调用loadImageThumbnail来加载缩略图报can not draw recycle bitmap错误的问题。

v3.2.7   2014年12月09日
1. 新增安全认证功能，增加SDK安全性；
2. 新增批量上传文件的功能；
3. 为单一文件上传补充进度提醒（onProgress）方法；
4. 解决第三方登陆成功之后无法修改用户信息的问题；
5. 支持复合与查询方式。

v3.2.6   2014年11月03日
1. 修改Bmob的应用Application ID的本地存储方式。

v3.2.5   2014年10月27日
1. 修复SDK调用云端代码出现ArrayIndexOutOfBoundsException(对应的错误码为9015)的问题；
2. 修复自动更新组件在切换网络之后出现的解析包错误的问题，增加target_size必填项；
3. 更新BmobpushSDK到V0.5，优化推送服务频繁重启的问题，建议使用此版本推送服务。

v3.2.4   2014年10月13日
1. 修改调用addWhereWithinGeoBox（查询指定位置的某矩形范围内）和addWhereWithinRadians（查询指定半径范围内）方法查询数据时出错的bug；
2. 修改调用BmobObject和BmobUser的update和delete方法出错的bug；
3. 第三方账号登录增加OnCancel回调方法；
4. 修改批量插入带pointer类型的数据时出现上传后的pointer类型的字段变成BmobObject类型的bug；
5. 更新BmobpushSDK到V0.4，优化导致推送延迟或无法接收到推送的问题。

v3.2.3   2014年9月18日
1. 第三方账号登陆增加QQ、新浪微博关联与取消关联的方法；
2. 自动更新组件新增初始化创建AppVersion表，不再需要手动创建；
3. 自动更新组件新增强制更新和忽略版本更新功能；
4. 修复调用BmobUser的update(context)方法和BmobObject的save(context)方法报错的bug；
5. 修复其他bug。 

v3.2.2   2014年9月15日
1. 修改V3.2.1版本文件上传出错的问题； 
2. 更新Bmobpush到V0.3，增加推送服务稳定性；
3. 修复其他bug。 

v3.2.1   2014年9月02日
1.	增加删除字段功能
2. 增加对数组字段的增删改查功能
3. 去除insertObject、updateObject等过期方法
4. 修改云端代码返回Json数据时带反斜杠的问题
5. 修复其他Bug 

v3.2.0   2014年8月08日
1. 增加数据实时功能

v3.1.9   2014年8月05日
1.	修复文件分片上传失败无限重试的bug
2.	增加新浪微博、手机QQ的第三方账号登陆

v3.1.8   2014年7月23日
1. 修复文件上传失败的bug
2. 更新BmobPushSDK到0.2beta

v3.1.7_Beta   2014年7月17日
1.	增加BmobQuery对象getObject时include、addQueryKeys等操作的支持

v3.1.6_Beta   2014年7月14日
1. 修复Pointer类型创建错误的bug
2. 修复自动更新点击通知栏崩溃的bug
3. 增加推送JSONObject数据的支持
4. 修复注册用户成功后在onSuccess回调中获取本地用户为null的bug

v3.1.5_Beta   2014年6月28日
1.	修复初始化设备表失败的bug
2.	恢复NETWORK_ELSE_CACHE查询缓存策略

v3.1.4_Beta   2014年6月19日
1. 修复findObjects查询无缓存数据时的错误回调
2. 修复BmobUser类中Number类型字段增量更新无效的bug
3. 修复创建数据对象中包含指针类型为BmobUser时创建失败的bug
4. 修复查询对象多继承时，表名获取错误的bug
5. 修复第一次保存Installation信息失败的bug
6. 增加应用自动更新功能

v3.1.3_Beta   2014年6月10日
1. 修复同一字段的and查询操作
2. 增加用户注册成功后的缓存操作
3. 修复同一对象中多个Pointer类型的include查询失效的bug
4. BmobObject对象添加setTableName方法，可自定义表名
5. 增加对老版本创建、查询数据方式的支持

v3.1.2_Beta   2014年6月04日
1. 修复Context为null时的bug
2. 修复在onSuccess方法中出现异常时调用onFailure的bug
3. BmobObject实现Serializable支持序列化

v3.1.1_Beta   2014年6月03日
1. SaveListener替代InsertListener
2. 修复无网络情况下崩溃的bug

v3.1.0_Beta   2014年5月27日
1. 修复同一个对象不能自增多列的bug
2. 增加错误代码机制
3. BmobFile增加获取缩略图功能
4. BmobFile增加分片上传功能
5. BmobObject增加ACL权限控制
6. 增加BmobRole角色管理类
7. 修复扩展BmobInstallation属性后查询不到的bug

v3.0.9_Beta   2014年5月21日
1. 添加无初始化时的提示信息
2. 修复BmobUser类不支持Pointer类型的bug
3. 修复云端代码不支持返回数据类型为String的bug
	
v3.0.8_Beta   2014年5月14日
1. 修复重复查询N次后出现内存溢出的bug
2. 修复count查询不支持条件的bug
3. BmobFile增加文件上传进度的支持

v3.0.7_Beta   2014年5月05日
1.修复云端代码请求失败的bug

v3.0.6_Beta   2014年4月30日
1. 增加BmobPushManager类, 实现客户端推送消息功能
2. 增加BmobInstallation类, 可实现自定义推送
3. 同步推出Bmob消息推送SDK 0.1.0Beta版本

v3.0.5_Beta   2014年4月29日
1. 修复登陆成功时出现空指针异常的bug

v3.0.4_Beta   2014年4月22日
1. BmobUser增加邮件验证功能
2. BmobObject增加批量操作功能，可批量添加、更新、删除

v3.0.3_Beta   2014年4月17日
1. 增加网络数据包压缩功能，更好的节省客户端流量。
2. 增加缓存数据的加密、压缩功能，更好的节省了缓存空间和提高数据的安全性。
3. BmobObject对象增加计数器，可以对任何数字字段进行原子增加（或减少）的功能。
4. BmobObject对象增加关联数据类型的支持，可实现不同对象间的一对一、一对多的数据关联。
5. BmobQuery对象增加对关联数据类型查询的支持，可实现关系查询。
6. 修复更新对象时存在系统字段的情况导致更新失败的bug。
7. BmobQuery对象增加复合查询支持，可实现or条件查询。

v3.0.2_Beta   2014年4月10日
1. 修复addWhereContainedIn和addWhereNotContainedIn添加条件无效的bug
2. BmobQuery对象新增addWhereMatches、addWhereContains、addWhereStartsWith、addWhereEndsWith条件添加方法
3. BmobQuery对象新增地理位置查询等方法
4. 修改BmobFile实例化方法
5. BmobUser对象新增getCurrentUser、logOut方法
6. 修改BmobUser.resetPassword为静态方法
7. BmobQuery对象增加查询用户表的特殊处理
8. BmobQuery对象新增查询时的缓存策略。

v3.0.1_Beta   2014年4月08日
1. 优化BmobQuery查询方法getObject、findObjects

v3.0_Beta   2014年4月04日
1. 新版本3.0beta和之前的版本使用方式有所不同，新的版本使用更加方便和稳定。
2. 新版已解决之前在主线程执行操作时抛出的android.os.NetworkOnMainThreadException异常
3. 新版的数据请求模块基于Volley网络通信框架，能使网络通信更快，更简单，更健壮。
4. 新版的对象解析模块基于Gson,提高接口数据和javaBean之间的转换速度。
5. 新增查询指定数据列的功能。
6. 具体使用方法请参考示例程序。

v2.2.8_Beta   2014年3月24日
1. 修复保存文件失败的bug
2. 增加获取服务器时间的方法Bmob.getServerTime 
3. 增加华为云推送功能 

v2.2.7_Beta   2014年1月07日
1. 修复调用云端代码时BmobClientCallback不能引用的bug 
2. 修复CountCallback不能引用的bug 

v2.2.6_Beta   2013年12月27日
1.修正无网络情况下，获取BmobFileUrl为null的bug
2.改善Context为null时出现的bug 3.新增云端代码功能 

v2.2.5_Beta   2013年11月15日
1. 修正updateAt为null的bug

v2.2.4_Beta   2013年11月13日
1. 修正Date类型数据格式
2. 新增getDate方法获取Date类型数据
3. 新增ACL功能，提高数据安全性
4. 新增加密码重置功能

v2.2.3_Beta   2013年9月27日
1. 新增Count查询功能
2. 新增地理位置查询功能 
3. 修正BmobGeoPoint数据类型的存储格式
4. 修正上传文件失败的bug

v2.2.2_Beta   2013年9月18日
1. 完善缓存策略功能，提供自定义缓存时间，默认缓存时间为5小时
2. 更新接口服务，提升响应速度

v2.2.1_Beta   2013年9月04日
1. 修正一些崩溃的bug 
2. 修正更新数据成功后返回的数据结构体 
3. 修正查询数据成功后返回的file、GeoPoint、Data等数据类型结构错误的bug 
4. 修正无效objectId删除成功的bug 
5. 修正分页查询数据不正确的bug 
6. 修正未添加网络权限及无网络状态时引起崩溃的bug 
7. 修正用户使用正确用户名、密码登陆失败的bug 
8. 修改创建数据成功后返回的数据内容 
9. 新增查询数据默认的limit为100，最大limit值不可超过1000 
10. 新增查询数据时提供6种缓存策略的功能 
11. 增加服务器以扩大集群来支撑更大用户量，明显提高响应速度 

v2.2.0_Beta   2013年4月23日
1. 修复重复添加数据的bug.
2. 更新完善开发者指南，如文件查询方式等.
3. 对内部流程的优化精简，减少冗余步骤，提供执行效率.
4. 增加一些操作的callback回调方法.
5. 优化数据传输格式.
6. 提升每一项操作的安全性.

v2.1.0_Beta   2013年2月05日
1. 修复旧版本发现和反馈的bug.
2. 优化SDK的性能，压缩了网络请求的流量损耗.
3. 提升了SDK在网络传输过程中的数据安全.
4. 进一步提升了后端云的并发承载和存储能力，确保服务高效运行.
5. 暂停推送服务，寻求erlang解决方案.

v1.0.4_Beta   2012年4月28日
1. 修复文件类型、地理坐标类型、JsonArray类型、JsonObject类型更新时出现的错误。

v1.0.3_Beta   2012年4月27日
1. 修复用户注册功能中存在的Bug。
2. 修复更新数据失败的重要Bug。
3. 修复更新数据后updateAt(更新时间)字段的值未修改的Bug。
4. 修复Bmob初始化等方法中的参数名称顺序颠倒的问题。
5. 增加用户登录功能。
6. 增加本地用户登录、注销功能。

v1.0.2_Beta   2012年4月24日
1. 修复创建用户对象时，提示数据表名称不正确的Bug。
2. 提供BmobObject数据对象的创建时间、更新时间两个字段值的获取。（BmobObject.getCreatedAt(),BmobObject.getUpdatedAt()）
3. 完善文件类型、地理坐标类型、JsonArray类型、JsonObject这几种数据类型的封装，便于更好的从查询结果中获取数据。

v1.0.0_Beta   2012年4月08日
1. 产品上线

