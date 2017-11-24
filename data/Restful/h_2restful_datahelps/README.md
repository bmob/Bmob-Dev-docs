Q:RestApi 如何进行ACL查询呢
比如有一张表，设置了某条数据，某个用户才能读取查看，那该用户如何获取该条数据呢使用RestApi
A:普通的查询就可以获取了，只要设置了ACL，其它使用就是正常的读写，如果没有权限会有提示

---

Q:上传成功，移动文件位置，请问keyoffile 和group代表什么
请问我用C#调用restApi上传文件成功，想把文件移动到指定的位置，文档说用PUT请求，发送data： {keyOfFile:{"__type":"File","group":"upyun","filename": fileName, url: url}，请问里面的keyoffile 和group代表什么
A:keyOfFile表示你存文件的那个表的字段名，group填个group1就可以了

---

Q:STM32主板上其他模块获取到的数据怎么通过GPRS上传到服务器上
A:得看你的板子是否支持https请求，如果支持https请求则可以直接使用restful接口进行上传

---

Q:RESTAPI 的文档中提到了发送请求创建ACL规则时的body内容，请问发送该请求时的url是什么？
A:访问哪张表，就给那张表的acl字段进行更新就可以了

---

Q:bql是否支持limit order by
A:支持，写法如下bql=select * from VersionInfo limit 0,1 order by -version，limit 后面跟两个参数，第一个表示跳过的记录，第二个表示返回的记录数

---

Q:restAPI只能通过443端口https访问不能通过80http访问吗？
A:是的，只能通过htts访问

---

Q:RestApi如何让数据库的某一字段增加1
A:使用原子计算器来实现

---

Q:想通过GPRS模块利用tcp协议上传数据，利用restapi，怎么确认云服务器的ip地址？
A:restapi都是通过https协议来进行请求的


---

Q:在Android应用中，不使用BmobSDK，仅用RestAPI, 做到接收推送消息
A:发布推送可以不使用sdk，但接收推送需要用到。

---

Q:C++ 使用curl post 数据产生中文乱码
A:，把文件改为 UTF-8 无BOM格式

---

Q:restAPI 使用短信验证码进行密码重置问题
用短信验证码进行密码重置 的接口 https://api.bmob.cn/1/resetPasswordBySmsCode ，从接口描述来看，需要上传的信息只有 验证码和新密码，用户的 session 或手机号都不需要上传。
那后台如何知道这个验证码是哪个手机号发来的呢？
A:后台是根据你的手机号来生成验证码的，服务端可以知道具体的验证码对应哪个手机号，请放心使用

---

Q:我需要多图上传，云端数据库的表应该怎么处理多图逻辑，类似于qq空间的多图上传，
A:可以在图片上传后把url保存在一个数组当中

---

Q:如何用Rest api创建表设置唯一键
A:唯一键的的设置暂时没有开放restapi接口，我们会考虑进去，后续加入。

---

Q:使用Pointer能否“反向”查询？
有一个用户表User，一个公司表Company。每个用户都有一个Pointer字段指向某一个公司。请问如何在查询公司信息的时候，一次性把公司包含的所有用户都查出来？
A:目前没有这样的功能，只能在查询到具体的公司信息后，再用Company对象去约束用户表，把该公司下的用户信息查询出来

---

Q:可否使用C++集成
A:用restapi文档对接C++就可以了

---

Q:如何比较updatedAt字段
A:例:where={"createdAt":{"$gte":{"__type":"Date","iso":"2011-08-21 18:02:52"}}}

---

Q:想要测试一下平台的短信服务，出现错误10011 no remaining number for send messages. 该账户无可用的发送短信条数，每个账号不是存在100条可用短信吗？
A:除了100条限制外还有以下限制的，短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条。

---

Q:如果列没数据为空，返回行据然列字段名都不返回
A:Bmob使用的MongoDB数据库，该数据库是无模式的，也就是说您并不需要事先设置列值，而对于某个对象，如果你没有明确设置其值，那么该值就是空的，不存在默认值。

---

Q:我在一张表中加关系性字段，为什么是在关联表里新建一行，而不可以选择关联表里已经存在的对象
A:目前只能通过请求添加relation关系，web端无法添加。

---

Q:在【快速入门】【RestAPI快速入门】的CURL例子中的URL的“GameScore”是什么意思？
在【快速入门】【RestAPI快速入门】的CURL例子中的URL的“GameScore”是什么意思？我需要将这个"GameScore"改为我的应用名字吗？还是改成我的表的名字？
A:GameScore是表名，你可以改为你自己的表的名字。
application id才是对应你的应用。

---

Q:restAPI一个post请求后，提示error：unauthorized是什么意思
A:发送方式有问题，没有把application id 和 rest key正确发送到Bmob后端

---

Q:使用httpClient POST请求一个接口返回的错误信息content is empty.
A:错误的含义：post请求里body内容为空

---

Q:Master Key不能用在RestApi上吗？
A:可以。masterkey是超级权限，不会受到表是否只读的限制，对于restapi、云函数和SDK都一样的。

---

Q:restApi中注册用户邮箱验证功能，可不可以在注册时emailVerified直接赋true
A:不行的，emailVerified是系统内置的字段，后端有邮件的触发行为，不能直接的赋值为true。如果你不想用这个字段，完全忽略就可以了。

---

Q:restApi查询表可以去掉重复的记录吗 就像sql的 distinct
A:暂无该功能。

---

Q:restapi 过滤用中文过滤不了
where={"name":"guangzhou"} 这个ok
where={"name":"广州"} 这个不行
A:注意使用urlencode编码，不会存在中文匹配不了的问题的。

---

Q:ajax不支持非标准的http请求头，像“X-Bmob-Application-Id”这种非标准的请求头，在ajax中是不被接受的，当ajax请求中设置了“X-Bmob-Application-Id”，这个请求的method就变成了"options"这个非标准的方法。
A:js的调用请使用bmob提供的js sdk，在sdk中已经解决了这个问题

---

Q:在我不知道用户密码的情况下 可以通过其他字段进行查询user用户表吗
A:可以查询

---

Q: 如何在线测试RestApi
A:使用Chrome浏览器的Postman插件就可以进行调试了。[点击链接Postman下载地址](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?utm_source=chrome-ntp-icon)。

界面效果[点击这里查看](https://docs.bmob.cn/restful/faststart/index.html?menukey=fast_start&key=start_restful#RestAPI调试工具)。

---
Q:Postman发起数据请求没有反应
A:首先先检查本地网络，通常是因为本地网络或者Postman没有成功发出数据请求，其次可以打开[https://api.bmob.cn/](https://api.bmob.cn/)查看是否能打开进行测试。

---

Q:其他语言用RestApi开发遇到请求security的错误

A:请查找相关语言访问HTTPS的配置问题。
如PHP用CURL开发时，需要添加如下脚本：

```java
curl_setopt($c, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($c, CURLOPT_SSL_VERIFYHOST, FALSE);
```

---

Q:能提供Java调用RestApi的示例代码吗
A:点击下面的链接可以看到Java调用RestApi的示例代码：

[http://wenda.bmob.cn//?/question/51](http://wenda.bmob.cn//?/question/51)

[http://wenda.bmob.cn/?/question/859](http://wenda.bmob.cn/?/question/859)

---

Q:关于where条件的问题
A:有开发者提出用PostMan请求的时候没有问题，但是用Java请求构造了where查询条件的时候有错（请求的格式大致如， `https://api.bmob.cn/1/classes/Footballer?limit=20&where={"location": {"$nearSphere": {"__type": "GeoPoint","latitude": 32.31735060,"longitude": 118.32457035 }}}` ）。

这个问题是因为特殊字符导致的问题，大家可以参考以下的解决方案：

[http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function](http://stackoverflow.com/questions/636770/is-there-any-java-equivalent-of-phps-http-build-query-function)

顺便说一句，PHP中的话，可以直接用`http_build_query`方法构造请求参数。

---

Q:Restapi有IM吗？
A:restapi可以发送推送信息，也可以通过get的方式获取聊天内容，但没有开放长连接服务，也就是说用restapi可以实现im功能，但方法只能通过定时器+get数据的方式。

---

Q:哪里可以看到错误码
A:[点击这里](https://docs.bmob.cn/data/Restful/g_errorcode/doc/index.html)可以查看RestApi的错误码列表。


