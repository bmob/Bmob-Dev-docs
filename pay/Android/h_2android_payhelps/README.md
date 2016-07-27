Q:网页支付的地址到底是什么？(php支付接口报异常)
A:因为支付宝安全团队封杀的问题，网页支付暂时不支持，待寻求到解决方案再开放。

---

Q:支付 弹出"抱歉，该商户未开通支付宝服务，无法付款"
A:如果出现这种情况则表示支付宝账号并封了，bmob需要一定的时间去申请账号重新恢复。

---

Q:新版的Android的Bmob Pay一直提示插件已过期
运行BP.pay()后会弹出toast显示“bmob pay plugin is out-of-date, please update it”
已经换成5月31日最新版本的sdk了，还是不行

A:versionCode必须为6和6以上的才是最新版，你可以通过高BP.getPluginVersion来获取

---

Q:新版支付SDK里的demo换成我自己的appID为什么不能支付，用Demo里的appID就可以？
A:请创建自己的应用，用自己的appId使用支付SDK。具体的使用方法可以参考Demo和文档。

---

Q:微信支付总是返回错误码10003， 是什么原因? 支付宝可以正常使用
A:商品名或者商品描述不符合规范，或者是金额不是double

---

Q:移动支付功能是否支持类似美团这种，用户购买了优惠券，资金暂存在我的账户，然后到期后，再由我把钱打给商家
A:购买优惠券存到您的账户可以用我们的支付来实现，您的钱打给商家需要自行实现

---

Q:Android客户端支付后如何反馈到自己的服务器
A:在web端的“支付”-->”支付配置“里可以填上你自己服务器的url地址，支付成功时会往这个地址发送一些订单成功的数据。

---

Q:新版Android支付SDK在虚拟机下测试报错
A:虚拟机上有很多的问题，不支持虚拟机调试。


---

Q:微信支付插件在哪里？
A:下载Bmob Android SDK之后，解压，可以看到 assets 文件夹下有一个 BmobPayPlugin.apk ，就是插件

---

Q:支付宝支付显示的订单信息，怎么过滤了空格..?
name of item ， 显示成了 nameofitem， 是支付宝过滤的空格还是bmob过滤的？这样英文显示不了
A:是我们过滤的，文档中有描述的哦“商品名及商品描述只支持输入中文、英语、数字、下划线(_)及英文破折号(-)，其余字符自动过滤”，这个是由于支付宝那边是这么要求的，如果有除了刚刚说的那些字符外的其它字符会报错，因此我们这边主动过滤了。


---

Q:开启验证后数据服务正常但发起支付提示verify error: sign incorrect.
A:你在后台开启了应用安全验证并填写了签名，可以在调试时先关掉。发布正式版本时开启

---

Q:bmob plugin isn't installed 支付这个是什么问题
A:支付功能需要先安装支付插件才能使用

---

Q:为什么一直提示支付中断，必须使用支付宝先发起一次支付请求后，然后切换为微信支付，点击支付后才可提示获取订单成功，并跳转至微信支付页面
A:这个问题是因为部分手机（小米手机）不允许未打开过Activity的App访问网络的权限，而支付宝需要先打开Activity，所以可以成功，但微信支付首先是在Service中调用，所以网络请求被拒绝。解决的办法是在BP.pay方法调用之前加上如下代码：
```java
try {
	Intent intent = new Intent(Intent.ACTION_MAIN);
	intent.addCategory(Intent.CATEGORY_LAUNCHER);
	ComponentName cn = new ComponentName("com.bmob.app.sport",
			"com.bmob.app.sport.wxapi.BmobActivity");
	intent.setComponent(cn);
	this.startActivity(intent);
} catch (Throwable e) {
	e.printStackTrace();
}
这段代码加在BP.pay方法调用之前
```
