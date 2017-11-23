
Q:使用云函数需要掌握什么语言
A:`Javascript`，因为云函数是用`Nodejs`部署架构的。

---

Q:云函数能做什么事情
A:云函数的推出是为了给大家解决更多后端业务逻辑的问题，让大家尽可能把更多的业务逻辑挪到云端，实现更快的更新迭代。目前，云函数除可以自由操作云端数据库外，还提供了`邮件模块`、`HTTP模块`和`事件模块`等，方便大家自由发挥。

---

Q:云函数能上传文件吗
A:暂不支持。

---

Q:如何调试云函数
A:
- Bmob在Web端（当前云函数的下边）为大家提供最简单的云函数的调试工具。
- 云函数本地化调试工具：[https://github.com/bmob/bmob-cloud-tool](https://github.com/bmob/bmob-cloud-tool) 。


---

Q:云函数或者android上update能否不用objectid用组合条件
A:只能根据objectid来，在查询返回的结果集中有objectid

---

Q:云函数，可以把消息发给IMSDK吗
A:目前不可以。

---

Q:url转码怎么转
A: encodeURI(url)

---

Q:如何使用同步的ohttp.post请求
A:用eventproxy

---

Q:相同的代码，在Local中可以返回正确的结果，在Server中返回错误。请问如何解决？
A:把本地的nodejs版本改成和云端一样，然后调试通过就可以了

---

Q:bmob的短信服务端验证接口云函数怎么做？要发送一个post请求
A:使用云函数发送http请求即可

---

Q:生成新应用时云函数没有复制到新应用
A:可使用云函数的本地调试工具可以运行后可以直接导入

---

Q:云函数能否实现 WebSocket ？
A:云函数无法使用第三方库

---

Q:往表中添加基本类型的数据是调用数据库对象的insert方法，而添加关联关系对象是用add方法？？那我的表里有这两种数据类型，该怎么添加数据
A:分别存储

---

Q:保存一个Object类型字段的值为null的时候会报错
A:保存为这样{}

---

Q:如何实现一个签到的逻辑
A:在用户表添加一个字段--签到时间，当用户发送请求的时候，更新这个签到时间，如果签到时间为今天，说明已经签到

---

Q:where语句如何查询24小时之前创建的记录？
A:查询createdAt在24小时之前的记录就可以了

---

Q:删除数据库记录只能用objectId吗？不能用where条件吗
A:只能用objectId,where条件用于查询，查询后的结果集中会有ObjectId的。

---

Q:云函数怎么设定where条件
A:

```
"where":{
"updatedAt":{
"$lt":{"__type":"Date","iso":"2014-01-29 11:33:53"}
}

```

---

Q:在查找数据库的回调里面再查找，不能收到回调消息

```
db.find(
{
"table":strTableName,
"count":1,
"limit":0,
},
function(err1,data1)
{ 
var searchNum = data1; //表的总行数，用sql语句获得
var limitnum=1000; //默认最多返回1000条记录
var runcount= parseInt(searchNum/1000); 
var strOutID = '';

//分多次获取记录，因为每次只能获取1000条
var i = 0;

for(i = 0;i !== runcount; i++){

var skipNum= 1000*i;
if( i==runcount ) {
limitnum=searchNum-skipNum;
} else {
limitnum=1000;
}
//能执行到这里，
//response.send('data1'); 
db.find(
{
"table":strTableName,
"count":1,
"limit":0,
},
function(err2,data2)
{
//这里执行不到
response.send('data2'); 
}
);
} 
}
);
```

A:不能这样取，只能取一次，然后再取一次，不能在里面for循环

---

Q:云函数可以查询支付订单吗？返回订单结果和数额之类的，有相关函数吗
A:可以使用云函数去调用restful接口来查询

---

Q:云函数可以实现抓取别的网页信息吗
比如说，我客户端去请求云函数，让云函数实现抓取某个咨询网站的信息
A:可以，使用云函数的http请求抓取即可。

---

Q:请问云函数可以发送短信吗？
A:可以通过云函数调用restful接口来实现。

---

Q:云函数更新用户表错误
但是最后返回的err包含错误信息
{"code":206,"message":"User cannot be altered without sessionToken Error."}
A:必须先登录才能更新

---

Q:请问云函数怎么返回JSON数据
A:

```
var data = { dir: 'kunhony', param: 'archive' };
var str = JSON.stringify(data);
response.end(str);
```

---

Q:如何在云端请求微信
A:https://docs.bmob.cn/cloudcode/WEB/d_cloudcodeweixin/doc/index.html

---

Q:Bmob中如何支持Cookie？用于将SessionID分配给浏览器
A:不支持set-cookie的方法。

---

Q:请问云函数如何返回错误？
A:如下，代码一般为这种形式，如果错误，返回response.send(err);

```
function onRequest(request, response, modules) {

var functions = modules.oFunctions;

functions.run({
   "name": "test",
   "data":{"content":"你好","address":"guangzhou"}
},function(err,data){
   //回调函数
   if(err){
       response.send(err);
    }else {
       response.send(data);
    }
});
}
```

---

Q:开发公众号可以获取微信的openid吗
A:可以

---

Q:云函数如何进行模糊查询
A:调用restful的模糊查询接口

---

Q:云函数能引入第三方模块吗？如underscore
A:不可以，如要使用第三方模块，可考虑使用窗口服务。

---

Q:我有一个云函数，客户端访问的时候查看一条数据库对象，如果不存在，则创建、返回，如果存在则直接返回。所有客户端的访问都是查看同一个对象，如何保证多
