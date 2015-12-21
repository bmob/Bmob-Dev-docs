## 简介

Cocos2d-x SDK是Bmob提供给Cocos2d-x开发者的工具，旨在帮助游戏开发者能够快速灵活使用我们提供的云服务。

## 安装Bmob SDK
首先，您应该拥有cocos2d-x的开发环境，由于SDK依赖于jsoncpp，因此，在使用SDK前，**请先正确集成jsoncpp库**。
确定上述条件之后，将下载得到的BmobSDK文件夹拷贝至cocos2d-x工程的Classes文件夹下并将代码正确导入进工程。至此，您就可以使用Bmob所提供的所有服务。

## 初始化
在使用其他功能之前，您应该先进行初始化。调用方法如下：

    Bmob::init(APP_ID);

其中APP_ID是您在网站注册的Application ID（获取Application ID之前，你需要先登录[www.bmob.cn](http://www.bmob.cn "bmob移动后端云服务")注册帐号，创建应用，最后可获取应用密钥）

## 测试Bmob SDK

初始化成功后，您可以使用如下方式保存一条数据来测试Bmob是否可以正常工作了。
```
BmobObject* gameScore = new BmobObject;
gameScore ->autorelease();
gameScore ->addParams("name", CCString::create("milo"));
gameScore ->addParams("score", CCFloat::create(10));
gameScore ->addParams("sort", CCInteger::create(1)); 
gameScore ->addParams("gender", CCBool::create(true));
Bmob::create(TABLE_NAME, gameScore,delegate);
```

- delegate是继承BmobDelegate的类，用于处理异步请求返回的结果，如果无需回调，可以传入NULL（下同）。

BmobDelegate包括两个回调方法，操作成功时调用：
```
    virtual void onRequestSuccess(std::string tag,void *data)
```

您需要对data进行强转，以转换成相应的类型（下同）。

操作异常时调用：
```
    virtual void onRequestError(std::string tag,BmobError * bmobError)
```

您可以在BmobError中获取错误代码与错误信息。

需要说明一点的是：Bmob提供的所有方法都是异步调用的，您无需新开线程。

- TABLE_NAME 是您想要操作的表名。运行您的应用程序后，对象类BmobObject会将数据传送到我们的服务器，并在您的应用表中保存。

## 对象

Bmob存储的数据是建立在BmobObject的基础上的，每个BmobObject包含键(Key)-值(value)对的JSON兼容的数据。这个数据是无模式的，这意味着不需要提前指定每个BmobObject存在什么键。您只需要设置想要的键值对，就可以在我们的后端存储。

例如，假设你要记录一个游戏的高分。一个单一的BmobObject对象可能包含：
```
    score: 1234, name: "Milo", cheatMode: false 
```

键必须是字母或数字类的字符串。值既可以是字符串、数字、布尔值，也可以是复杂数据。其中复杂数据包括BmobArray、BmobObject、BmobDate、BmobGeopoint和BmobFile对象等（具体用法见下面）。

游戏中可以调用出来的分数对象GameScore，也就相当于数据表名。

## 保存对象
假如你要保存数据到后端的GameScore表中，可以使用如下的方法（添加数据的形式类型与Map类似）：
```
BmobObject* gameScore = new BmobObject;
gameScore ->autorelease();
gameScore ->addParams("name", CCString::create("milo"));
gameScore ->addParams("score", CCFloat::create(10)); 
gameScore ->addParams("cheatMode", CCBool::create(false));
Bmob::create("GameScore", gameScore,this);
```

运行完以上代码后，数据即可保存到服务器端。为了确认数据是否真的已经保存成功，你可以在Bmob服务器端你的应用程序的数据浏览项目中查看。
这里需要注意几点：

1.在运行以上代码时，如果服务器端您创建的应用程序中已经存在GameScore数据表和相应的score、name、cheatMode字段，那么您此时添加的数据和数据类型也应该和服务器端的表结构一致，否则将保存数据失败。 

2.如果服务器端不存在GameScore数据表，那么Bmob将根据您第一次(也就是运行的以上代码)保存的GameSocre对象在服务器为您创建此数据表和插入相应数据。

3.每个BmobObject对象有几个默认的键(数据列)是不需要开发者指定的，objectId是为每个保存成功数据的唯一标识符。createdAt和updatedAt代表每个对象(每条数据)在服务器上创建和最后修改的时间。这些键(数据列)的创建和数据内容是由服务器端来完成的。

## 查询对象

### 简单查询
数据的保存是比较简单的，数据的获取也不难。您可以调用：
```
    Bmob:: query (TABLE_NAME, this) 
```

来获取表中的所有数据，数据将存储在BmobQueryResult类中。

在操作成功的回调函数中，您需要执行
```
    BmobQueryResult *result = (Result *)data
```

进行类型转换，在BmobQueryResult中包含了一个BmobObject的list，BmobObject中包含单一条目的所有数据，数据存储在Dictionary中，您可以根据数据存储时的key来获取响应的数据。

**注意：返回的数据中，所有的int、float和double都会以CCDouble的类型存在，您可以对CCDouble的value强制转换成存储时的实际类型。**

如果在你知道某条数据的objectId的情况下，可以使用BmobQuery 检索到一个具体的条目，结果同样存储在BmobQueryResult中，查找方式如下：
```
    Bmob::queryWithObjectId(TABLE_NAME, objectid, this); 
```
### 复杂查询

BmobQuery 是查询的基类，它有三个子类BmobQuerySimple 、BmobQueryWithGeoPoint和BmobQueryGroup。

您应该通过实例化BmobQuery的子类来进行具体的查询， 例如：
```
BmobQuerySimple *query = new BmobQuerySimple();
query->autorelease();
query->addCondition(BmobQueryEqual,"sort",CCInteger::create(1));
Bmob::query(TABLE_NAME, query,this);
```
针对查询结果，您还可以选择排序方式、limit等参数。
BmobQueryShow是用来指定结果显示参数的，例如：
```
BmobQueryShow * queryShow = new BmobQueryShow;
queryShow->autorelease();
queryShow->setLimit(3);
Bmob::query(TABLE_NAME, query,queryShow,this);
```
其他具体使用情况请参考demo。

**对于超过1000个对象的查询，这种计数请求可能会出现请求超时。它可能会遇到超时错误或者返回一个近似的值。因此，请仔细设计你的应用架构来避免依赖这种计数查询。**

## 更新对象
更新一个对象也是非常简单。您可以通过检索获取到要更新的BmobObject对象，并获取到改数据的objectId，然后进行修改值后保存数据。例如：
```
String tableName = "GameScore";
BmobQuery  *query = new BmobQuery (BmobQuery Equal,"name",CCString::create("Milo"));
Bmob:: query (TABLE_NAME, query,this);
```
在操作成功的回调函数中：
```
BmobQueryResult *result = (BmobQueryResult *)data;
std::vector<BmobObject *> list= result->getResultList();
```
从list中取出想要更新的BmobObject，获取到objectId，针对想要更新的数据重新赋值，例如：
```
gameScore ->addParams("score", CCFloat::create(20));
Bmob::update(TABLE_NAME, objectid, gameScore, this);
```
操作成功后会返回一个BmobUpdateResult类型的数据。

## 删除对象
与更新数据一样，您应该先获取到数据的objectId，然后调用
```
    Bmob::remove(TABLE_NAME, objectid, this); 
```
操作成功后会返回一个BmobResultInfo类型的数据。

## 数据类型的使用

Bmob除支持string（字符串）、float（单精度浮点数）、integer（整型）、double（双精度浮点数）和boolean（布尔型）等常见数据类型之外，还支持object（对象）、array（数组）、date（时间）、geo（地理坐标）和file（文件）等复杂的数据类型。这里列举Bmob支持的所有数据类型的使用方法，方便大家的查询检索，详细如下：
 ```   
//常见类型的使用
BmobObject* params = new BmobObject;
params->autorelease();
params->addParams("name", CCString::create ("name1"));
params->addParams("score", CCFloat::create(10));
params->addParams("sort", CCInteger::create(1));
params->addParams("effect", CCDouble::create(1));
params->addParams("gender", CCBool::create(true));
   
//object对象类型的使用
BmobObject *object = new BmobObject();
object->addParams("key1", CCString::create("hello bmob"));
object->addParams("key2", CCInteger::create(1));
object->addParams("key3", CCFloat::create(11.0));
object->addParams("key4", CCDouble::create(222.3));
object->addParams("key5", CCBool::create(0));
object->autorelease();
params->addParams("obj1", object);

//array数组类型的使用
BmobArray* array = new BmobArray();
array->addParams(CCString::create("test"));
array->addParams(CCInteger::create(12));
array->addParams(CCDouble::create(123.3));
array->addParams(CCDouble::create(1234.5));
array->addParams(CCBool::create(1));
array->autorelease();
params->addParams("arr1", array);

//date时间类型的使用
BmobDate *date = new BmobDate();
date->autorelease();
params->addParams("date", date);

//geo地理信息类型的使用
BmobGeopoint *geo = new BmobGeopoint(116,36);
geo->autorelease();
params->addParams("loc", geo);

//file文件类型的使用
BmobFile *file = new BmobFile();
file->setFileName(CCString::create("filename1")->getCString());
file->setFileUrl(CCString::create("fileurl1")->getCString());
file->setGroup(CCString:: create ("filegroup")->getCString());
file->autorelease();
params->addParams("file", file);
Bmob::create(TABLE_NAME, params, this); 
```

## 文件对象

BmobFile可以让你的应用程序将文件存储到服务器中，比如常见的文件类型图像文件、影像文件、音乐文件和任何其他二进制数据都可以使用。
**注意：单个文件大小不可超过10M**

下面这个例子中，我们将一个图片文件保存到服务器端（以Resources文件夹下的Icon.png为例）：
```
std::string name = "Icon.png";
std::string path = CCFileUtils::sharedFileUtils()->fullPathForFilename(name.c_str());
Bmob::uploadFile(path,name,this);
```
操作结果将存储在BmobFile中返回给您，拿到BmobFile后您可以做为一个参数与指定的数据关联起来。

## 用户操作
用户是一个应用程序的核心。对于个人开发者来说，能够让自己的应用程序积累到多的用户，就能给自己带来更多的创作动力。因此Bmob提供了一个专门的用户类，BmobUser来自动处理用户账户管理所需的功能。有了这个类，您就可以在您的应用程序中添加用户账户功能。

BmobUser是一个BmobObject的子类，它继承了BmobObject所有的方法具有BmobObject相同的功能。不同的是，BmobUser增加了一些特定的关于用户账户相关的功能。

### BmobUser属性
BmobUser除了从BmobObject继承的属性外，还有几个特定的属性：

- username: 用户的用户名（必需）。
- password: 用户的密码（必需）。
- email: 用户的电子邮件地址（可选）。

和其他BmobObject对象不同的是，在设置BmobUser这些属性的时候不是使用的addParams方法，而是专门的setXXX方法。

### 用户注册
你的应用程序会做的第一件事可能是要求用户注册。下面的代码是一个典型的注册过程：
```
BmobUser *user = new BmobUser;
User->autorelease();
user.setUsername("Milo");
user.setPassword("12345678");
user.setEmail("milo@example.com");
Bmob::userSignup(user,this);
```
**其他属性可以像其他BmobObject对象一样使用addParams方法添加。**

1. 在注册过程中，服务器会进行注册用户信息的检查，以确保注册的用户名和电子邮件地址是独一无二的。此外，对于用户的密码，你可以在应用程序中进行相应的加密（由于开发者需求的不尽相同，服务器不进行用户密码的加密工作）处理后提交。
2. 如果注册不成功，你可以查看返回的错误对象。最有可能的情况是，用户名或电子邮件已经被另一个用户注册。这种情况您可以提示用户，要求他们尝试使用不同的用户名进行注册。
3. 你也可以要求用户使用Email做为用户名注册，这样做的好处是，你在提交信息的时候可以将输入的“用户名“默认设置为用户的Email地址，以后在用户忘记密码的情况下可以使用Bmob提供重置密码功能。

### 用户登录
当用户注册成功后，您需要让他们以后能够登录到他们的账户使用应用。
```
BmobUser *user = new BmobUser;
User->autorelease();
user.setUsername("Milo");
user.setPassword("12345678");
Bmob::userLogin(user,this);
```
登录成功后将会返回一个BmobLoginResult类型的数据，通过getUser方法可以获得BmobUser的实例，该实例中保存了用户的所有数据。

### 重置密码
一旦你引入了一个密码系统，那么肯定会有用户忘记密码的情况。对于这种情况，我们提供了一种方法，让用户安全地重置起密码。重置密码的流程很简单，开发者只要求用户输入注册的电子邮件地址即可：
```
    Bmob::userReset(“milo@example.com”);
```
密码重置流程如下：

1. 用户输入他们的电子邮件，请求重置自己的密码。

2. Bmob向他们的邮箱发送一封包含密码重置链接的电子邮件。

3. 用户打开邮件，点击重置密码链接，打开重置密码的页面，输入一个新的密码。

4. 用户的密码已被重置为新输入的密码。

## 地理位置

Bmob允许用户根据地球的经度和纬度坐标进行基于地理位置的信息查询。你可以在BmobObject的查询中添加一个BmobGeoPoint的对象查询，轻松查找出离当前用户最接近的信息或地点的功能。

### 地理位置对象
首先需要创建一个BmobGeopoint对象。例如，创建一个北纬39.913768382429105度-东经116.39727786183357度的BmobGeopoint对象，并关联到TABLE_NAME表的loc列中：
```
    BmobGeopoint *point = new BmobGeopoint(116.39727786183357, 39.913768382429105);
    point->autorelease();
    Bmob::whereNear(TABLE_NAME, "loc",point, this); 
```
如果要限制查询指定距离范围的数据可以使用whereWithinKilometers、whereWithinMiles或whereWithinRadians方法，如下：

```
BmobGeopoint *point = new BmobGeopoint(116,36);
point->autorelease();
Bmob::whereWithinKilometers(TABLE_NAME,"loc", point, 10,this);
```
如果要查询一个矩形范围内的信息可以使用whereWithinGeoBox来实现，如下：
``` 
BmobGeopoint *point1 = new BmobGeopoint(116,36);
BmobGeopoint *point2 = new BmobGeopoint(119,39);
point1->autorelease();
point2->autorelease();
Bmob::whereWithinGeoBox(TABLE_NAME, "loc",point1, point2, this);
```
### 注意事项
地理位置的点不能超过规定的范围。纬度的范围应该是在-90.0到90.0之间。经度的范围应该是在-180.0到180.0之间。如果您添加的经纬度超出了以上范围，将导致程序错误。

## 其他接口
除了上述的基本操作外，我们还为您提供了另外的接口。
### 获取服务器时间戳
```
    static void getTimestamp(BmobDelegate* delegate);
```
使用起来非常简单，如下：
```
    Bmob::getTimestamp(this);
```
返回的结果存储在BmobTimeStampResult中，包括一个秒级的int型数据和一个微秒级的int型数据。

### 云端代码

云端代码是为了满足您一些无法直接用SDK实现的功能，或希望能够让客户端更灵活方便，或希望客户端的代码更简洁，因此将客户端的业务逻辑放到云端来实现（需要您熟悉Nodejs的一些脚本语言），客户端只是调用触发。调用云端代码的方法包含两个静态方法：
```
static void endPoint(std::string fuctionName,BmobDelegate* delegate);
static void endPoint(std::string fuctionName,BmobObject* params,BmobDelegate* delegate);
```
用于调用您在服务器端自定义的代码，fuctionName是您在服务器端定义的代码段的名称，params是需要传递的参数。使用方法如下：
```
BmobObject* params = new BmobObject;
params->autorelease();
params->addParams("name", CCString::create ("name "));
params->addParams("score", CCFloat::create(10));
params->addParams("sort", CCInteger::create(1));
params->addParams("effect", CCDouble::create(1));
params->addParams("gender", CCBool::create(true));
Bmob::endPoint("send", params, this);
```
关于自定义代码段，请参考云端代码的开发文档。
