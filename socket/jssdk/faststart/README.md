## 简介

Bmob提供了数据实时功能，当开发者监听某个变化事件，例如监听表更新时，表的内容一旦变化，服务器就会通知SDK，SDK提供了相应回调函数来给开发者使用。当然开发者也可以取消相对应的监听，这样就不会收到数据变化的消息了。


**注意！！！运行的html文件应该是在web环境中，不能在本地环境中运行。**
例如，合法的运行环境：http://local.project/appweb/sdk/js/demo/bmobSocketIo.html
无效的本地运行环境：file:///D:/jeff/project/appweb/sdk/js/%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%B9%B3%E5%8F%B0/test/bmobSocketIo.html


## 注册Bmob帐号
在网址栏输入www.bmob.cn或者在百度输入Bmob进行搜索，打开Bmob官网后，点击右上角的“注册”，在跳转页面填入你的姓名、邮箱、设置密码，确认后到你的邮箱激活Bmob账户，你就可以用Bmob轻松开发应用了。

![](image/rumen_zhuce.png)
## 网站后台创建应用

登录账号进入bmob后台后，点击后台界面左上角“创建应用”，在弹出框输入你应用的名称，然后确认，你就拥有了一个等待开发的应用。

![](image/rumen_chuangjian.png)
## 获取应用密钥和下载SDK

选择你要开发的应用，点击该应用下方对应的“应用密钥”。

![](image/rumen_miyue_1.png)

在跳转页面，获取Application ID，此ID将会在初始化SDK中使用到。

![](image/rumen_miyue_2.png)

获取Application ID后，下载SDK。


## 安装BmobSDK

一、把下面这行代码加入你的页面中，其中，"bmobSocketIo.js"为实时数据平台的js文件。

```
<script type="text/javascript" src="bmobSocketIo.js"></script>
```

二、接着是加入下面这行代码进行初始化。

```
BmobSocketIo.initialize("你的Application ID");
```

## 订阅数据表更新的事件

初始连接socket.io服务器后，需要监听的事件都写在这个函数内。

```   
    BmobSocketIo.onInitListen = function() {
      //订阅"GameScore"表更新的事件
      BmobSocketIo.updateTable("GameScore");
    };
```

## 触发更新的事件

用restful api或sdk在表"GameScore"中添加一行数据，触发表更新的事件。

**注意：在web界面进行操作无法触发相关的事件，必须使用restful api或sdk操作！！！**

下面演示用restful api添加一行数据
```   
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
    https://api.bmob.cn/1/classes/GameScore
```


## 监听数据表表更新的事件

当订阅了表更新的事件后，任何一个表的更新都会触发这个表更新的回调函数，其中tablename是标明那个表的数据更新了，data是更新的数据。

当“触发更新的事件”中，已经更新了表GameScore的数据，所以下面的函数将会被执行。


```
   BmobSocketIo.onUpdateTable = function(tablename,data) {    
     alert("onUpdateTable，tablename:"+tablename+" data:"+data); 
   };
```



## 源码下载

[快速入门相关源码下载](http://static.bmob.cn/new/developmentdoc/appdemo/bmob_jssdk_quickstart.zip "快速入门相关源码下载")

