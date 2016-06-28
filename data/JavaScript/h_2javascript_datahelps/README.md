Q:请问比目有TypeScript的前端sdk吗？主要是用在白鹭游戏引擎引擎里的。
A:这个暂时没有，有普通js的sdk

---

Q:angularJs如何与bmob配合使用
A:一般这种情况需要将和Bmob的数据交互封装为一个service，从service中返回数据
基本的调用层次就是controller调用service，service调用Bmob
bmobservice.js

```
app.service("bmobservice", function () {
//添加一个资源  Bmob对象在index中初始化或者在app.run中进行初始化
this.AddResource =function(resource){
    var ResourceInfo = Bmob.Object.extend("ResourceInfo");

    //创建对象
    var resourceInfo = new ResourceInfo();
    //为对象赋值
    resourceInfo.set("Title",resource.Title);
    resourceInfo.set("ResourceType",resource.ResourceType);
    resourceInfo.set("Target",resource.Target);

    //resourceInfo.save();
    var array = new Array();
    var SubResourceInfo = Bmob.Object.extend("SubResourceInfo");
    var subResourceInfo = new SubResourceInfo();
    subResourceInfo.set("Name",resource.SubResourceInfo[0].Name);
    subResourceInfo.set("Url",resource.SubResourceInfo[0].Url);
    resourceInfo.set("SubResource",subResourceInfo);
    resourceInfo.save();
}
});

```

testcontroller.js

```
app.controller('testcontroller',function($scope,$resource,bmobservice){
        $scope.addresource = function(){
            var resource = {
                "Title":"test",
                "ResourceType":0,
                "Target":"test",
                "SubResourceInfo":[{
                    "Url":"testurl",
                    "Name":"testname"
                }]
            }
            bmobservice.AddResource(resource);
        }
```

---

Q:没有客户端请求的情况下服务端能主动向客户端发送数据吗？
A:不能

---

Q:有没有比较数据表中内容相似度的方法
例如 我表里面有 一个组数据 name: 你好中国人， 我要往里面房数据但是如果我的数据为 name：中国人 ，就不放进去了。。判断两者为同一数据
A:这种定制化的需求需要开发者自行实现。

---

Q:有没有办法可以获取password
A:为安全考虑，我们不对外提供获取password字段值的功能。

---

Q:Javascript的bmob的数据处理都是异步的，如何设置为同步
A:如果是在nodejs中，可使用async这个同步类库

---

Q:如何在node.js的代码中调用bmob
A:用bmob的nodejs模块

---

Q:JS如果想产生多行数据保存到表中怎么办?
A:，一次只能保存一条数据，多条数据需要使用批量操作，可以使用restful接口

---

Q:js sdk中有更新某个表某个字段所有值的函数吗
A:没有的，只能一个个更新，restful有一个批量更新的接口，但是每次最多只能操作50条数据

---

Q:JavaScript 传输数据时，自动加密了吗？
A:Bmob所有SDK的通讯过程都进行了加密。

---

Q:消息推送 JS SDK支持吗？我使用H5进行APP开发
A:JS有推送功能，可以查看推送文档。

---

Q:BmobSocketIo.onUpdateTable可以无视ACL
A:实时监听功能不受ACL的限制。

---

Q:JS SDK 的初始化语句应该放在哪里？
A:用框架集成到一个页面，例如angularjs 的ng-view。不然只能哪里用到，哪里调用

---

Q:JS SDK可以增加模糊查询吗
A:目前JS并没有该接口，可使用JS的网络访问接口调用restful api实现，restful api中含有模糊查询的功能。

---

Q:我需要将数据加密后再保存到Bmob表吗？
A:所有SDK到服务器之间的数据都是经过对称加密算法加密后传输的。

---

Q:JavaScript怎么在bomb数据库里面存入date类型。
A:

```
var number = 42;
var string = "the number is " + number;
var date = new Date();
var array = [string, number];
var object = { number: number, string: string };

var bigObject = new BigObject();
bigObject.set("myNumber", number);
bigObject.set("myString", string);
bigObject.set("myDate", date);
bigObject.set("myArray", array);
bigObject.set("myObject", object);
bigObject.set("myNull", null);
bigObject.save();
```


---

Q:bmob的query查询可以做对指定列做sum之类的聚合查询吗？
A:可以。具体查看JS使用文档

---

Q:JS版里有多图片上传吗
A:JS版没有多图片上传，需要自行处理

---

Q:js中用户登录返回的session是不是都一样？
A:同一个用户多次登录返回的SessionToken是一样

---


