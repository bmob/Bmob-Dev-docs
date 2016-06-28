Q:php为什么添加数据的时候字段的值为中文就会出错
A:检查下你的php编码，建议改为utf-8编码。

---

Q:有没有ts的sdk
A:php的sdk只有一个

---

Q:使用PHP CURL 推送消息的代码，推送不成功,能否给个例子
A:请检查证书是否设置好

---

Q:操作数据库的话，比如更新两个表，数据回滚有没有？
A:Bmob暂时没有事务操作，你要想同时更新两个表的话，可以使用批量操作。

---

Q:test.php出现unauthorized
下载sdk之后，修改了配置文件的Application ID和REST API Key
运行test.php出现BmobException: [0]: unauthorized 
A:app id 或 restful key不正确

---

Q:php 这边可以插入关联对象吗？
A:现在官方有生成关联对象的方法 ：
具体在bmob官方php接口中的BmobRestClient.class.php文件中的dataType()方法,可以自行阅读

---

Q:bmob 最多能取多少条数据
A:一次最多1000条

---

Q:bmob bql多表查询如何实现
A:目前不支持多表查询

---

Q:php如何使用get（）方法取得不只100条数据？(PHPSDK如何循环获取数据?)
A:请使用分页查询，设置limit值，limit值默认为100，可以根据需要进行设置，具体可查看php文档https://github.com/bmob/bmob-php-sdk/tree/master/doc_develop中的分页查询章节

---

Q:获取表中所有数据后怎么单个输出自己需要的数据
A:查询后返回的是对象数组，您只需要遍历该数组即可。

---

Q:PC上如何使用Bmob
PC上只能用C#和PHP开发吗？
A:#和PHP是我们官方出的SDK，你还可以根据restapi文档开发更多的SDK，非常简单。开发的时候需要注意点的是：1、https协议的问题； 2、header 和 body 的问题； 3、发送方式的问题，如POST、GET、PUT、DELETE。
 

---

Q:我有两个表，_User可以放用户的数据，另一个表的password怎么设置成密码？
A:只有使用_User表的密码才能使用BmobUser的功能，否则接口需要自己再写

---

Q:实时数据是什么原理？是客户端轮询吗？
A:客户端与服务器维护了一个长连接，有消息时由服务端主动推送消息




