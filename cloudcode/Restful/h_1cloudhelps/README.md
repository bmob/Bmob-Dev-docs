
Q:使用云端逻辑需要掌握什么语言
A:`Javascript`，因为云端逻辑是用`Nodejs`部署架构的。

---

Q:云端逻辑能做什么事情
A:云端逻辑的推出是为了给大家解决更多后端业务逻辑的问题，让大家尽可能把更多的业务逻辑挪到云端，实现更快的更新迭代。目前，云端逻辑除可以自由操作云端数据库外，还提供了`邮件模块`、`HTTP模块`和`事件模块`等，方便大家自由发挥。

---

Q:云端逻辑能上传文件吗
A:暂不支持。

---

Q:如何调试云端逻辑
A:
- Bmob在Web端（当前云端逻辑的下边）为大家提供最简单的云端逻辑的调试工具。
- 云端逻辑本地化调试工具：[https://github.com/bmob/bmob-cloud-tool](https://github.com/bmob/bmob-cloud-tool) 。


---

Q:云端逻辑或者android上update能否不用objectid用组合条件
A:只能根据objectid来，在查询返回的结果集中有objectid

---

Q:云端逻辑，可以把消息发给IMSDK吗
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

Q:bmob的短信服务端验证接口云端代码怎么做？要发送一个post请求
A:使用云端代码发送http请求即可

---

Q:生成新应用时云端代码没有复制到新应用
A:可使用云端代码的本地调试工具可以运行后可以直接导入

---

Q:云端代码能否实现 WebSocket ？
A:云端代码无法使用第三方库

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

Q:云端逻辑怎么设定where条件
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

Q:云逻辑可以查询支付订单吗？返回订单结果和数额之类的，有相关函数吗
A:可以使用云逻辑去调用restful接口来查询

---

Q:云端代码可以实现抓取别的网页信息吗
比如说，我客户端去请求云端代码，让云端代码实现抓取某个咨询网站的信息
A:可以，使用云端代码的http请求抓取即可。

---

Q:请问云端代码可以发送短信吗？
A:可以通过云端代码调用restful接口来实现。

---

Q:云端代码更新用户表错误
但是最后返回的err包含错误信息
{"code":206,"message":"User cannot be altered without sessionToken Error."}
A:必须先登录才能更新

---

Q:请问云端代码怎么返回JSON数据
A:

```
var data = { dir: 'kunhony', param: 'archive' };
var str = JSON.stringify(data);
response.end(str);
```

---

Q:如何在云端请求微信
A:http://docs.bmob.cn/cloudcode/WEB/d_cloudcodeweixin/doc/index.html

---

Q:Bmob中如何支持Cookie？用于将SessionID分配给浏览器
A:不支持set-cookie的方法。

---

Q:请问云端逻辑如何返回错误？
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

Q:云端代码如何进行模糊查询
A:调用restful的模糊查询接口

---

Q:云逻辑能引入第三方模块吗？如underscore
A:不可以，如要使用第三方模块，可考虑使用窗口服务。

---

Q:我有一个云端代码，客户端访问的时候查看一条数据库对象，如果不存在，则创建、返回，如果存在则直接返回。所有客户端的访问都是查看同一个对象，如何保证多个客户端同时访问的时候不会同时创建多个对象？
A:目前这个无法做到。

---

Q:bql不支持 delete语句吗
A:目前bql只支持查询语句。

---

Q:表设置了 ACL， 我想用 master key 或 用户的 token 去更新表，在云端用批量更新模块，如何传入master key 或 用户的 token ？
A:目前不支持

---

Q:云端代码怎么延迟执行一个操作
A:目前并无该项功能

---

Q:云端代码中不同的模块中经常出现相同代码 有没有办法把这些代码提取到公共区域来复用
A:云端代码之间是可以相互调用的，具体查看云端代码云对象章节。

---

Q:如何清空某个数据表？
A:需要先将表的所有值查询下来，然后遍历去删除，具体参考文档的查询及删除对象章节


---

Q:怎么在云端调用 获取短信验证码、验证短信验证码
A:使用云端代码进行http请求，请求RESTful接口即可

---

Q:云端如何自定义返回数据
A:定义好格式后以JSON形式返回

---

Q:云端代码MD5加密中文，结果怎么和PHP的不一样？
A:编码问题，https://cnodejs.org/topic/54ad4e40ce87bace2444cc49

---

Q:BMOB云端数据库导入需手动导入CSV格式，如何做到自动抓取我本地CSV文件传入云端数据库
A:Bmob导入数据，只可以从web后台手动导入。如果你要自动从本地传入数据库的话，可以自己写代码（程序）实现，用Bmob提供的SDK或基于RestApi来插入数据到云端数据库中。

---

Q:云端数据库更新需上传CSV文件，如何实现自动云端更新
A:云端数据库与你本地数据库的结构可能会有区别，需要你针对自己的数据库特点，读取本地数据库，转换后再进行上传

---

Q:用云端查询db.find，查询到表里的数据，返回的字段名也是表的列名，有没有像sql里面as的方式修改这个名字
A:目前还没有这个功能

---

Q:如果一个字段里没有值，查询后返回的内容也没有这个字段，如果才能让这个字段也出现在返回的内容里。
A:由于后台使用的是MongoDB，是无模式的，没有初始值，因此只有显示地给该字段赋值才会有内容返回。

---

Q:写云端代码的时候，只能通过objectId来查询符合条件的一行数据？我想用表中的其他字段当做查询条件怎么写?
A:可以使用条件查询，具体查看数据库对象中的查询多条数据小节。

---

Q:用skip和limit来实现分页查询的话，如果表里的数据更新的很快的话，会不会查询出重复的数据？
A:会出现重复数据，因此，一般您在查询时可以加上限制，比如，查询第一页时的时间为A，那您可以约束查询创建时间在A之前的数据，在那之后的数据不查询，这样就不会有重复了。

---

Q:该如何实现类似乐观锁的功能
A:目前并没有提供该类型的接口

---

Q:连上vpn没有数据返回
A:可以在连上VPN时ping https://api.bmob.cn/看看能否ping通，有可能是VPN屏蔽了

---

Q:云端代码中where条件怎么表示!=？
A:您好，可以参考restful文档中的查询数据中的条件查询，大概的形式为"type":{"$ne":"delete"}

---

Q:如何更新1000条以上的记录
A:可以采用分页，先取1000条数据进行更新，再取1000条之后的数据接着更新

---

Q:请问云端代码请求HTTP时如何获取cookies和带cookies访问？
A:将var http = modules.oHttp；
改成var http = modules.oHttp.defaults({jar: true});
即可使用全局cookie，后面的链接就不需要手动输入cookie了。

---

Q:JavaScript能调用云端代码吗（xx平台能调用云端代码）
A:只要支持https请求就可以通过restful来调用云端代码，部分sdk直接封装了调用云端代码的接口，具体可以查看云端代码文档。

---

Q:请问云端new Date()如何获取和createTime里面一样的时间
A:createAt这个属性是特殊字段，为了节约HTTP流量，我们没有进行特殊字段类型处理，直接返回string类型。你在云端代码里面new Date()获取的是一样的时间，因为服务器的时间是保持一致的。至于两者的数据格式问题，你可以自行处理。

---

Q:云端代码数据库可以返回多少条记录？
A:一次最多只能返回1000次数据，如果要获取的数据大于整个数目，必须要分多次查询

---

Q:云端代码只能添加方法吗
我想在云端代码中添加一个功能模块，但是发现云端代码中的每个文件都是独立的，而且只能是方法，我想问一下能不能实现。
A:完全可以在方法内声明方法或者类的，这个不影响，而且可以通过间接调用的方式调用其他云端代码的执行。

---

Q:webstorm在本地搭好服务器，在不联网的情况下，使用ios调用运行在本地的服务端代码，能不能利用这样的方式测试？
A:不可以，数据保存在云端，不联网无法操作数据，本地调试过程中不需要客户端的参与。

---

Q:云端代码执行console.log无输出
A:在真正的云端代码上，不支持console.log这种输出，只能使用res.end()

---

Q:云端怎么实现 var wpwp = require('wpwp')('YOUR-KEY');
A:云端代码进行了封装，无法加载非官方模块。

---

Q:如何更新数据表中Date的数据
A:

```
var userData = dataObject.results[0];
var checkDate = userData.checkDate.iso;

var lastDate = new Date(checkDate.toString());
var nowDate = new Date();

db.setHeader({"x-bmob-session-token":request.body.sessionToken.toString()});
userData.checkDate.iso=nowDate;
db.updateUserByObjectId({"objectId":request.body.objectId.toString() ,data: {"checkDate":userData.checkDate,"diamond":parseInt(userData.diamond)+10}},function(err,data)
{
})
```

---

Q:containedIn在云端代码里面是什么指令
A:对应为 [http://docs.bmob.cn/data/Restful/b_developdoc/doc/index.html#查询](http://docs.bmob.cn/data/Restful/b_developdoc/doc/index.html#查询) 里面的 $in 查询，你可以参考restful文档，在where条件中使用就可以了。

---

Q:如何在云端代码中计算两个时间的时间差
A:

```
var lastDate;//一定要是Date哦
var nowDate = new Date(data); 

Date.parse(nowDate) - Date.parse(lastDate)

//注意：单位是毫秒级的哦
```


---

Q:批量操作对象中 "path": "/1/classes/GameScore" ，其中的 "/1/class/ "是什么来的？怎么确定下来的？
A:/1/classse/ 是系统规定的路径，其中1是系统内部的版本号，classes表示接下来要操作的是数据表。

---

Q:云代码能不能实现函数递归调用
A:可以，但要注意不能过于复杂，5s内无回调会提示超时。

---

Q:我想用云端代码修改user表中的数组，要如何设置？如何先获取表中的数组呢？获取了之后又要如何用arr.addUnique更新？求示例。
A:要修改User表中的数据需要注意：
1、要么你有登录用户的sessionToken信息（也就是权限），这样可以修改用户信息；
2、要么你用masterKey（也就是超级权限）来修改用户信息。

获取表中的信息非常简单，你直接Get就可以了，获取之后，你直接用类似如下的方法解决：
```
var arr = modules.oArray;
arr.addUnique({
"table":"_User",

"objectId":"j4w2DDDT", //这个对应是这个用户的objectId
"data":{"skills":{"__op":"AddUnique","objects":["flying","kungfu"]}}

},function(err,data){
//回调函数
});
```

---

Q:云端代码比较复杂的时候，很难找出根本原因
1. 能否支持加入日志，通过查看日志来debug？
2. 能否有类似IDE的断点，或者是报错信息更明确一点在哪里出错？
A:可以借助我们开发的这个云端代码本地化调试工具来调试：
https://github.com/bmob/bmob-cloud-tool

---

Q:对于设置了ACL为用户只读的数据，如何在云端代码里将其全部读出？
A:使用masterKey
云端代码设置masterkey的方法：
```
function onRequest(request, response, modules) {
var db = modules.oData;
db.setHeader({"X-Bmob-Master-Key":"这里填写Master Key信息"});
db.updateUserByObjectId({"objectId":"这里是需要更新的用户ObjectId信息" ,data:{"username":"123"}},function(err,data){
response.end("更新成功");
}); 
}
```

---

Q:如果把代码放在云端，本地JS调用时，是否需要输入Application ID、REST API Key才能进行调用呢？
A:只需要在初始化js sdk的时候传人Application ID、REST API Key就行了，在js调用云端代码的时候不需要传入

---

Q:云端代码里如何获取当前时间
A:云端代码是运行在nodejs的环境中，所以js的函数能用在云端代码上。
获取时间： var now = new Date();

---

Q:调用云端代码，能否获取调用者的真实IP,想用IP来做排行榜的地理位置统计
A:云端代码打印headers

```
function onRequest(request, response, modules) {
response.send(request.headers);
} 
```

结果：

```
Response Body
{
"code": 200,
"msg":
{
"x-real-ip": "114.114.114.114",
"x-forwarded-for": "114.114.114.114",
"host": "cloud.bmob.cn",
"x-nginx-proxy": "true",
"connection": "close",
"accept": "/",
"a": "",
"content-length": "7",
"content-type": "application/x-www-form-urlencoded"
}
}
```

x-real-ip就是用户的真实ip的


---

Q:请问怎么查询用户当前排名
比如:我有个GameScore 表 
字段有:username,score
现在排行榜里面数据有5000多条，我知道某个用户objectId，如果快速找出排名位置呢？
A:解决方案：
根据order排名，把所有排名按次序放到一个数组中，然后根据objectId查找到某个用户名，用户名在这个数组中的位置即是他的排名。 
RestAPI查询条件如下：
第一步：先查询到某个用户的用户名：

```
curl -X GET \
-H "X-Bmob-Application-Id: Your Application ID" \
-H "X-Bmob-REST-API-Key: Your REST API Key" \
-G \
--data-urlencode 'keys=username' \
https://api.bmob.cn/1/users/某个用户的objectId
```

第二步：score降序获取前1000名的用户的用户名：
```
curl -X GET \
-H "X-Bmob-Application-Id: Your Application ID" \
-H "X-Bmob-REST-API-Key: Your REST API Key" \
-G \
--data-urlencode 'keys=username&order=-score&count=true&limit=1000&skip=0' \
https://api.bmob.cn/1/classes/GameScore
```
返回前1000名的用户名，判断前一个查询的用户名是否在这个数组的哪个位置，如果没有找到，继续第二步, skip设为1000，直到找到为止。

优化方案：
GameScore应该添加Pointer类型指向某个用户，然后在GameScore添加一个排名的列，更新score的时候更新排名，这样就可以直接根据用户的objectId一条查询就出来了。


