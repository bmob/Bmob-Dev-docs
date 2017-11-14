
v3.2.3_171113	2017年11月14日
1.在v3.2.2_171019版本的基础上替换jar文件和payassets.db文件即可，如果是更早的版本，请将libs下的so文件悉数更新
2.增加了QQ支付,但是订单金额要求 >= 2.2元
3.调用支付的接口有所改变,建议通过 
	BP.pay(String name, String body, double price, int payType, PListener listener)
	接口进行调用，其中payType传入[BP.PayType_Alipay、BP.PayType_Wechat(暂未开通)、BP.PayType_QQ]中的一个
4.Unity Helper、文档、Demo更新
5.取消了PListener.unknow回调
6.Demo更新

v3.2.2_171019	2017年10月18日
1. 替换本目录中libs文件夹下的全部内容，包括一个jar文件和多个so文件(so文件按需)
2. 替换./assets/payassets.db到项目的assets文件夹中
3. 支付方式较之前版本有所改变，调起支付宝的时间会延长一点，超过一定时间会弹窗询问用户是否选择通过浏览器进行支付
4. 如果在使用中有任何疑问或bug，请反馈给客服或提交工单

v3.2.0   2017年6月02日
1 先挑重点：
需要替换assets文件夹下的bp.db文件和payassets.db文件

2 说明：

更换了支付插件，以前的插件包名为com.bmob.app.sport与应用市场上一些应用重复，导致部分用户会从应用市场更新到别的应用去，所以微信支付的插件更改如下：

(17年6月2号)
包名：cn.bmob.knowledge
目前最新版本号：8 
下载地址：https://www.bmob.cn/themes/2016-3/static/resource/BmobPlugin_Apk.apk 


3 不再提供getPluginVersion接口，插件安装情况、版本信息由开发者自行判断(可用新增接口BP.isAppUpToDate)

4 新增错误码：
-5 	未安装支付插件，且sdk调用系统浏览器打开插件下载地址
-6 	未安装支付插件，且无法调起系统浏览器

v3.1.1   2017年1月11日
1.需要在AndroidManifest.xml添加声明一个Activity(cn.bmob.pay.v3.act.PayAct)
2.需要添加so文件、更换jar文件、往assets文件夹放一个payassets.db文件
3.恢复了对支付宝支付的支持(不需添加支付sdk，要求用户手机已安装支付宝客户端)
4.网络请求过程不再放在插件，不会再因为插件无法请求网络导致出现9015、9016的错误，不会再出现不回调订单的错误
5.取消10077(重复提交订单)的错误，不再提供ForceFree方法，由开发者自己控制好，避免短时间内频繁生成订单
6.责任声明的页面由sdk统一提供了，开发者可以不再展示该声明(试用demo可体验)
7.初始化方法参数无需传入Context
8.初始化方法变成异步的，最好放在Application的onCreate方法内
9.回调fail时，部分编译阶段的错误码对应的文字换成了中文的，以便快速排查问题，如果有编码问题请及时反馈

v3.0.3   2016年6月21日
1.请勿将支付sdk用于违规途径,一经发现,本平台可在不通知开发者的情况下紧急封禁账号以避免损失;
2.支付宝支付和微信支付都需要使用支付插件(本目录下的’BmobPayPlugin.apk’),请确保有读写SD卡权限;
3.如果担心插件被应用市场扫描出带有支付功能,可将插件通过加密或下载的方式隐藏起来;
4.混淆规则如下:
-libraryjars libs/xxx.jar(替换jar包名)
-keepclasseswithmembers class c.b.** { *; }
-keep interface c.b.PListener{ *; }
-keep interface c.b.QListener{ *; }

v3.0.2   2016年5月31日
1.将过长字符串通过getResourceAsStream方式加载,兼容部分旧的编译器
2.更新了混淆规则,详细内容见文档
3.支付插件的名字统一为"安全支付控件"

v3.0.1   2016年5月30日
1.提供关闭Service的接口，BP.ForceExit();
2.修复102(安全认证)的问题
3.将插件上的支付宝sdk升级到最新版
4.支付插件改名为"BPay安全控件"
5.Demo中添加了检查支付插件版本并更新的示例

v3.0   2016年5月27日
1.请勿将支付sdk用于违规途径,一经发现,本平台可在不通知开发者的情况下紧急封禁账号以避免损失;
2.支付宝支付和微信支付都需要使用支付插件(本目录下的’BmobPlugin.apk’),请确保有读写SD卡权限;
3.如果担心插件被应用市场扫描出带有支付功能,可将插件通过加密或下载的方式隐藏起来;
4.混淆规则如下:
-keep class c.b.BP
-keep class c.b.PListener
-keep class c.b.QListener
-keepclasseswithmembers class c.b.BP{ *; }
-keepclasseswithmembers class * implements c.b.PListener{ *; }
-keepclasseswithmembers class * implements c.b.QListener{ *; }

v2.7   2016年4月27日
 修复了Unity项目初始化时提示混淆失败的问题(在Assets/Android/assets下添加了s.db和j.db文件)

v2.6   2016年4月11日
BmobPay更新：
1.修复了部分旧版混淆工具混淆失败的问题；
2.处理了在非主线程初始化sdk失败的问题；
3.提高了加载效率；
4.sdk版本号为7
微信支付插件更新：
1.修复了未登录用户回调失败的问题；
2.隐藏了中转页面；
3.插件版本号改为4，可通过支付sdk的BP.getPluginVersion判断更新

v2.5   2016年3月24日
1、兼容armeabi、armeabi-v7a、arm64-v8a、x86、x86_64、mips等CPU指令集 2、修复某些特殊机型会崩溃的问题 3、修复已知Bug 

v2.4   2016年1月25日
1.兼容64位机器，如果发现还有不兼容的机型请联系客服

v2.3   2016年1月23日
1.取消so文件
2.提高sdk兼容性
3.Unity添加安装微信支付插件功能(需更新so文件和jar文件,并提供cs文件参考)
4.提示语优化
5.混淆规则在demo的proguard-project.txt文件有示例

v2.1   2016年1月18日
1.支持Unity调用并提供了教程和示例代码,不懂Android也可以快速接入
2.so文件缩小一半,更加轻量
3.安全性大大提升
4.推荐将插件后缀名改为db等,更容易上应用市场

v2.0   2016年1月13日
2.0版本与之前的版本有较大区别，不向下兼容。
主要更新内容如下：
1.去除SDK中Pay关键字
2.添加两个新的Activity需要在AndroidManifest文件中注册。(旧版已注册的Activity可删除)
3.类名改动，详细见支付文档说明。
4.混淆改动，详细见支付文档说明。

v1.0.3   2015年10月20日
1.支付宝sdk更新:     
	1)默认用主线程调用调用支付;     
	2)更新支付宝官方sdk,从3个jar包改成1个jar包;     
	3)版本号更新为3; 
2.微信支付插件:     
	1)微信支付插件无更新; 
3.支付Demo更新:     
	1)支付宝支付sdk更新成官方最新版;     
	2)Dialog的调用增加了异常捕捉; 

v1.0.2a   2015年7月09日
1.微信支付插件更新：
    1）在近期任务中不可见
    2）兼容微信的新Bug
    3）状态栏（TitleBar）设为不可见
    4）版本号更新为3
2.支付 Sdk无更新
PS：如果请求支付的页面为横屏，微信支付页面可能出现抽风现象（卡顿甚至重启），是因为微信出现莫名Bug不停开启新支付页面导致内存爆满，解决方法：在AndroidManifest.xml中将com.bmob.pay.tool.PayActivity设为强制竖屏（上方有示例），并尽可能将自己调用的Activity也设置为竖屏
支付Sdk版本号 : 2
微信支付插件版本号 : 3

v1.0.2   2015年5月12日
支付Sdk更新： 
1.BmobPay下增加了静态方法getPluginVersion，用于查看微信插件的版本号（当前版本为2），可按需更新； 
2.BmobPay下增加了静态方法getPaySdkVersion，用于查看Bmob支付Sdk的版本号（当前版本为2）； 微信支付插件更新： 修复了部分用户在未安装微信时调用微信支付未回调fail的Bug 
支付Sdk版本号 : 2 
微信支付插件版本号 : 2 

V1.0.1a   2015年4月14日
支付宝官方的sdk有更新，Bmob的支付sdk无更新 
支付宝sdk更新信息如下：     
安卓版本号：v2.2.1    
修改时间：2015-03-31    
安卓更新点：修正部分机型横屏下安全输入法兼容问题，改成强制竖屏     
          
V1.0.1   2015年4月10日
1.修复了商品名、描述字符串非法导致支付宝报ALI64的问题                              
2.修复了防止多次提交订单时的锁只对下一次有效的BUG                                        3.优化了微信支付插件的Logo 

V1.0.0   2015年4月08日
1、调用支付宝、微信支付的支付以及查询功能完成
2、添加支付成功、失败、返回订单号等监听事件；
3、更改支付宝支付、查询订单时无需安装插件，微信支付需要安装插件  

