## 简介

Bmob提供了数据实时功能，当开发者监听某个变化事件，例如监听表更新时，表的内容一旦变化，服务器就会通知SDK，SDK提供了相应回调函数来给开发者使用。当然开发者也可以取消相对应的监听，这样就不会收到数据变化的消息了。


**注意！！！运行的html文件应该是在web环境中，不能在本地环境中运行。**
例如，合法的运行环境：http://local.project/appweb/sdk/js/demo/bmobSocketIo.html
无效的本地运行环境：file:///D:/jeff/project/appweb/sdk/js/%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%B9%B3%E5%8F%B0/test/bmobSocketIo.html



## 应用程序
在Bmob平台注册后，每个账户可创建多个应用程序，创建的每个应用程序都有其独自的应用程序ID，此后所有的应用程序将凭其ID进行Bmob SDK的使用。即使只有一个应用程序，也可以以不同的版本进行测试和生产。

## 订阅事件

### 订阅表更新的事件

订阅表"GameScore"更新的事件。

```
BmobSocketIo.updateTable("GameScore");
```

### 订阅行更新的事件

订阅表"GameScore"中行objectId为"3342e40e4f"更新的事件。

```
BmobSocketIo.updateRow("GameScore","3342e40e4f");
```

### 订阅行删除的事件

订阅表"GameScore"中行objectId为"3342e40e4f"删除的事件。

```
BmobSocketIo.deleteRow("GameScore","1256e40e4f");
```

## 取消订阅事件

### 取消订阅表更新的事件

取消订阅表"GameScore"更新的事件。

```
BmobSocketIo.unsubUpdateTable("GameScore");
```

### 取消订阅行更新的事件

取消订阅表"GameScore"中objectId为"3342e40e4f"行更新的事件。

```
BmobSocketIo.unsubUpdateRow("GameScore","3342e40e4f");
```

### 取消订阅行删除的事件

取消订阅表"GameScore"中objectId为"3342e40e4f"行删除的事件。

```
BmobSocketIo.unsubDeleteRow("GameScore","1256e40e4f");
```


## 监听触发的事件


### 监听更新表的事件

当订阅了表更新的表数据发送变化时，js中会触发函数onUpdateTable。

tablename为更新的表，data为服务端返回的更新数据。

```
   BmobSocketIo.onUpdateTable = function(tablename,data) {    
      //业务逻辑的代码
   };
```

### 监听行更新的事件

tablename为更新的表，objectId为更新行的objectId，data为服务端返回的更新数据。

```
   BmobSocketIo.onUpdateRow = function(tablename,objectId,data) {    
      //业务逻辑的代码
   };
```


### 监听行删除的事件

tablename为更新的表，objectId为更新行的objectId，data为服务端返回的更新数据。

```
   BmobSocketIo.onDeleteRow = function(tablename,objectId,data) {    
      //业务逻辑的代码
   };
```