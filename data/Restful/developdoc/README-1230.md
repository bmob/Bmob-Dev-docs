## 简介
只要你的设备，你使用的语言能够发送HTTP请求，那么就可以用来和[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")进行数据交互，你可以使用REST API做很多事情,比如:

- 一个移动网站可以通过C、Java、Python、PHP、C#等甚至任何语言来获取[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上的数据。
- 一个网站可以展示来自[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")的数据。
- 你可以上传大量的数据，随后可以被一个移动App读取。
- 你可以下载最近的数据来进行你自定义的分析统计。
- 使用任何语言写的程序都可以操作[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上的数据。
- 如果你不再需要使用[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")，你可以导出你所有的数据。

## 案例源码

这里提供一些开发者写的其他语言调用RestApi的源码或者核心代码。

JAVA访问RestApi：[http://wenda.bmob.cn//?/question/51](http://wenda.bmob.cn//?/question/51)

PHP访问RestApi：[http://docs.bmob.cn/phpsdk/index.html?menukey=otherdoc&key=phpsdk](http://docs.bmob.cn/phpsdk/index.html?menukey=otherdoc&key=phpsdk)

AS3.0访问RestApi：[http://wenda.bmob.cn//?/question/193](http://wenda.bmob.cn//?/question/193)


## 请求格式
对于POST和PUT请求，请求的主体必须是JSON格式,而且HTTP请求头的 **Content-Type** 需要设置为 **application/json** 。

用户验证是通过HTTP请求头来进行的, **X-Bmob-Application-Id** 头表明你正在访问的是哪个App程序, 而 **X-Bmob-REST-API-Key** 头是用来授权的。在下面的例子中，你必须使用正确的key替换Your Application ID和Your REST API Key才能正常地发出Curl请求。

## 响应格式
对于所有REST API请求的响应内容体都是一个JSON对象.

一个请求是否成功是由HTTP状态码表明的， 一个2XX的状态码表示成功，而一个4XX表示请求失败。当一个请求失败时响应的主体仍然是一个JSON对象，但是总会包含code和error这两个字段，你可以用它们来进行调试。举个例子，如果保存一个对象的时候，尝试用不允许的Key，比如包含下划线的_name的话，就会得到如下请求失败的响应信息:
```
{
	"code": 105,
	"error": "invalid field name: bl!ng"
}
```

## 对象
### 对象格式
通过REST API保存数据需要将对象的数据通过JSON来编码，这个数据是无模式化的（Schema Less）,这意味着你不需要提前标注每个对象上有哪些Key，你只需要随意设置key-value对就可以，Rest API后端会存储它的。

举个例子，假设你正在记录一局游戏的最高分，一个简单的对象可能包含:
```
{
	"score": 1337,
	"playerName": "Sean Plott",
	"cheatMode": false
}
```
Key必须是字母和数字组成的字符串,Value可以是任何可以JSON编码的东西.

每个对象都有一个类名，你可以通过类名来区分不同的数据，例如，我们可以把游戏得分对象称之为GameScore.我们推荐你使用  **NameYourClassesLikeThis** 和 **nameYourKeysLikeThis** 这样的格式为你的类名和Key命名，这可以使你的代码看起来很漂亮.

当你从[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")中获取对象时，一些字段会被自动加上: createdAt, updatedAt 和 objectId， 这些字段的名字是保留的，你不能自行设置它们，我们上面设置的对象在获取时应该是下面的样子.
```
{
    "score": 1337,
    "playerName": "Sean Plott",
    "cheatMode": false,
    "createdAt": "2011-08-20 02:06:57",
    "updatedAt": "2011-08-20 02:06:57",
    "objectId": "e1kXT22L"
}
```

createdAt和updatedAt都是UTC时间戳,以ISO 8601标准和毫秒级精度储存:YYYY-MM-DD HH:MM:SS. objectId是一个string,在类中唯一表明了一个对象。

在REST API中class级的在一个资源上的操作只能根据类名来进行，例如，如果类名是GameScore，那么class的URL就是
```
https://api.bmob.cn/1/classes/GameScore
```
用户有一个特殊的类级的url:
```
https://api.bmob.cn/1/users
```
针对于一个特定对象的操作可以通过组织一个URL来做，例如，对GameScore中的一个objectId为e1kXT22L的对象的操作应使用如下URL:
```
https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 数据类型
到现在为止我们只使用了可以被标准JSON编码的值，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")移动客户端SDK库同样支持日期,地理位置数据和指针数据、关系型数据。在REST API中，这些值都被编码了，同时有一个"__type"字段来标识出它们所属的类型，所以如果你采用正确的编码的话就可以读或者写这些字段了。

Date类型包含了一个"iso"字段存储了一个UTC时间戳,以ISO 8601格式和毫秒级的精度来存储时间: YYYY-MM-DDTHH:MM:SS.MMMZ，或者 YYYY-MM-DDTHH:MM:SS
```
{
    "__type": "Date",
    "iso": "2011-08-21 18:02:52"
}
```
Date 与内置的 **createdAt** 字段和 **updatedAt** 字段相结合的时候特别有用，举个例子：为了找到在一个特殊时间创建的对象，只需要将Date编码在一个查询的where条件中:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"createdAt":{"$gte":{"__type":"Date","iso":"2011-08-21 18:02:52"}}}' \
    https://api.bmob.cn/1/classes/GameScore
```

File类型是在上传后返回的JSON数据再加一个Key为"__Type":"File", 用来保存到数据列为文件类型的值：
```
{
    "__type": "File",
    "group": "group1",
    "filename": "1.xml",
    "url": "M00/01/14/sd2lkds0.xml"
}
```
更新对象时可以为该对象保存上传后返回的文件信息：
```
curl -X PUT 
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":1337,"playerName":"Sean Plott","file":{"__type":"File","group":"group1","filename":"1.xml","url":"M00/01/14/sd2lkds0.xml"
}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

Pointer 类型是在当前对象要指向另一个对象时使用，它包含了 className 和 objectId 两个作为一个指针正确指向的必填值.
```
{
  "__type": "Pointer",
  "className": "Game",
  "objectId": "DdUOIIIW"
}
```
指向用户对象的 **Pointer** 的 **className** 为_User, 前面加一个下划线表示开发者不能定义的类名, 而且所指的类是系统内置的。

**Relation** 类型被用在多对多的类型上, 移动端的库将使用 **BmobRelation** 作为值, 它有一个 className 字段表示目标对象的类名：
```
{
  "__type": "Relation",
  "className": "GameScore"
}
```
当使用查询时， **Relation** 对象的行为很像是 **Pointer** 的数组, 任何操作针对于 **Pointer** 的数组的 (除了 include) 都可以对 **Relation** 起作用.

当更多的数据类型被加入的时候, 它们都会采用 hashmap **加上一个 type** 字段的形式, 所以你不应该使用type作为你自己的JSON对象的Key。

## 添加数据
为了在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上创建一个新的对象，应该向class的URL发送一个POST请求，其中内容体应该是包含对象本身的JSON格式，例如，要创建如上例子中说的对象:
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
    https://api.bmob.cn/1/classes/GameScore
```
当创建成功时，响应的HTTP状态码的返回值是201 Created，而响应的HTTP头部中Location的值是表示刚创建的该对象的URL:
```
Status: 201 Created
Location: https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
响应的主体是一个JSON对象,包含新对象的objectId和createdAt时间戳：
```
{
    "createdAt": "2011-08-20 02:06:57",
    "objectId": "e1kXT22L"
}
```

## 查询数据
数据的查询可能是每个应用都会频繁使用的，它提供了多样的方法来实现不同条件的查询，同时它的使用也是非常的简单和方便。

### 查询所有数据
为了一次获取多个对象，你可以通过发送一个GET请求到类的URL上，不需要任何URL参数，下面就是简单地获取所有在GameScore类之中的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/classes/GameScore
```
返回的值就是一个JSON对象包含了results字段,它的值就是对象的列表:
```
{
    "results": [
    {
        "playerName": "Jang Min Chul",
        "updatedAt": "2011-08-19 02:24:17",
        "cheatMode": false,
        "createdAt": "2011-08-19 02:24:17",
        "objectId": "51c3ba67e4b0f0e851c16221",
        "score": 80075
    },
    {
        "playerName": "Sean Plott",
        "updatedAt": "2011-08-21 18:02:52",
        "cheatMode": false,
        "createdAt": "2011-08-20 02:06:57",
        "objectId": "e1kXT22L",
        "score": 73453
    }
    ]
}
```
怎么样，是不是很简单，而且查询的结果不需要任何处理，你直接使用即可。

### 查询单条数据
当你创建了一个对象时，你可以通过发送一个HTTP GET请求到创建对象成功时返回的HTTP请求头中的Location的URL获取它的内容。例如，为了得到我们上面创建的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
返回的主体是一个JSON对象，它包含所有用户提供的field，并且加上系统保留的createdAt,updatedAt和objectId三个Key的值：
```
{
    "score": 1337,
    "playerName": "Sean Plott",
    "cheatMode": false,
    "skills": [
        "pwnage",
        "flying"
    ],
    "createdAt": "2011-08-20 02:06:57",
    "updatedAt": "2011-08-20 02:06:57",
    "objectId": "e1kXT22L"
}
```
当获取的对象有指向其子对象的Pointer类型指针Key时，你可以加入inclue选项来获取指针指向的子对象。按上面的实例，如果GameScore对象有一个game的Key为Pointer类型，并指向了Game游戏对象，那么可以通过GameScore的game这个Key来获取指向的一个Game对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'include=game' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
返回的主体是一个JSON对象包含GameScore的所有Key，并有game这个Pointer的Key被扩展为一个Game对象:
```
{
    "score": 1337,
    "playerName": "Sean Plott",
    "cheatMode": false,
    "skills": [
        "pwnage",
        "flying"
    ],
    "game": {
        "type": "Object",
        "className": "Game",
        "name": "愤怒的小鸡",
    }
    "createdAt": "2011-08-20 02:06:57",
    "updatedAt": "2011-08-20 02:06:57",
    "objectId": "e1kXT22L"
}
```

### 条件查询
通过where参数的形式可以对查询对象做出约束，where参数的值应该是JSON编码过的，就是说，如果你查看真正被发出的URL请求，它应该是先被JSON编码过，然后又被URL编码过。使用where参数最简单的方式就是包含应有的key的值。举例说,如果我们想要得到Sean Plott的分数，而且他在不作弊情形下，我们应该这样构造查询:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"playerName":"Sean Plott","cheatMode":false}' \
    https://api.bmob.cn/1/classes/GameScore
```
where的参数值除了上面的准确匹配外，还支持比较运算符的方式，除了给定一个确定值的方式，还可以提供一个hash中包含有key用于比较，where参数支持下面一些选项:

| Key        | Operation |
| :----:  | :----:  |
| $lt     | 小于 |
| $lte     | 小于等于 |
| $gt     | 大于 |
| $gte     | 大于等于 |
| $ne     | 不等于 |
| $in     | 包含在数组中 |
| $nin     | 不包含在数组中 |
| $exists     | 这个 Key 有值 |
| $select	 | 匹配另一个查询的返回值 |
| $dontSelect	 | 排除另一个查询的返回 |
| $all     | 包括所有给定的值 |
| $regex     | 匹配PCRE表达式 |

作为示例，为了获取score得分在[1000,3000]之间的对象，我们需要这样做:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"score":{"$gte":1000,"$lte":3000}}' \
    https://api.bmob.cn/1/classes/GameScore
```
为了获得score得分在10以下并且是一个奇数，我们需要这样做:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"score":{"$in":[1,3,5,7,9]}}' \
	https://api.bmob.cn/1/classes/GameScore
```

为了获得scoreArray得分包括数组中所有的值，如scoreArray是[1,3, 5, 7]就满足，是[1, 5,10]就不满足:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"scoreArray":{"$all":[1,3,5]}}' \
	https://api.bmob.cn/1/classes/GameScore
```

为了获取playerName不在列表中的GameScore对象们，我们可以:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"playerName":{"$nin":["Jonathan Walsh","Dario Wunsch","Shawn Simon"]}}' \
    https://api.bmob.cn/1/classes/GameScore
```
为了获取有分数的对象，我们应该用:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"score":{"$exists":true}}' \
    https://api.bmob.cn/1/classes/GameScore
```
为了获取没有分数的对象，用:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"score":{"$exists":false}}' \
    https://api.bmob.cn/1/classes/GameScore
```

你还可以使用模糊查询，支持PCRE正则表达式：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"playerName":{"$regex":"smile.*"}}' \
    https://api.bmob.cn/1/classes/GameScore 
```

如果您的查询条件某个列值要匹配另一个查询的返回值，举例有一个队伍(Team)保存了每个城市的得分情况且用户表中有一列为用户家乡(hometown), 您可以创建一个查询来寻找用户的家乡是得分大于0.5的城市的所有运动员， 就像这样查询:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"hometown":{"$select":{"query":{"className":"Team","where":{"winPct":{"$gt":0.5}}},"key":"city"}}}' \
    https://api.bmob.cn/1/users
```

反之查询Team中得分小于等于0.5的城市的所有运动员，构造查询如下：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"hometown":{"$dontSelect":{"query":{"className":"Team","where":{"winPct":{"$gt":0.5}}},"key":"city"}}}' \
    https://api.bmob.cn/1/users
```

### 分页查询
你可以用limit和skip来做分页，limit的默认值是100，但是任何1到1000的值都是可选的，就是说，为了获取在400到600之间的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'limit=200' \
    --data-urlencode 'skip=400' \
    https://api.bmob.cn/1/classes/GameScore
```

### 结果排序
你可以用order参数指定一个字段来排序，前面加一个负号的前缀表示降序，这样返回的对象会以score升序排列:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'order=score' \
    https://api.bmob.cn/1/classes/GameScore
```
而以下这样返回的对象会以score降序排列:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'order=-score' \
    https://api.bmob.cn/1/classes/GameScore
```
你可以用多个字段进行排序，只要用一个逗号隔开列表就可以，为了获取GameScore，以score的升序和name的降序进行排序:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'order=score,-name' \
    https://api.bmob.cn/1/classes/GameScore
```

### 统计对象数量
如果你在使用limit，或者如果返回的结果很多，你可能想要知道到底有多少对象应该返回，而不用把它们全部获得以后再计数，此时你可以使用count参数。举个例子，如果你仅仅是关心一个特定的玩家玩过的游戏数量：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"playerName":"Jonathan Walsh"}' \
    --data-urlencode 'count=1' \
    --data-urlencode 'limit=0' \
    https://api.bmob.cn/1/classes/GameScore
```
因为请求了count而且把limit设为了0，返回的值里面只有计数，results为空数组集。
```
{
    "results": [

    ],
    "count": 1337
}
```
如果有一个非0的limit的话，既会返回正确的results也会返回count的值。

### 复合查询
| Key        | Operation |
| :----:  | :----:  |
| $or     | 复合查询中的或查询 |
| $and     | 复合查询中的与查询 |

如果你想查询对象符合几种查询之一，你可以使用$or或$and操作符，带一个JSON数组作为它的值。例如，如果你想找到player赢了很多或者赢了很少，你可以用如下的方式:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"$or":[{"wins":{"$gt":150}},{"wins":{"$lt":5}}]}' \
    https://api.bmob.cn/1/classes/Player
```

查询今天内的数据，方式如下:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"$and":[{"createdAt":{"$gte":{"__type": "Date", "iso": "2014-07-15 00:00:00"}}},\
	{"createdAt":{"$lte":{"__type": "Date", "iso": "2014-07-15 23:59:59"}}}]}' \
    https://api.bmob.cn/1/classes/Player
```

**因为createdAt updatedAt服务器自动生成的时间，在服务器保存的是精确到微秒值的时间，所以基于时间类型比较的值要加1秒。**

任何在查询上的其他的约束都会对返回的对象生效，所以你可以用$or对其他的查询添加约束。


注意我们不会在 **组合查询的子查询** 中支持非过滤型的约束(例如:limit skip sort include)，但最外层的查询中是支持非过滤型约束的。

### 查询指定列
你可以限定返回的字段，通过传入keys参数，值为用一个逗号分隔的字段名称列表，为了获取对象只包含score和playerName字段(还有特殊的内置字段比如objectId,createdAt和updatedAt)，请求如下：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'keys=score,playerName' \
    https://api.bmob.cn/1/classes/GameScore
```


### 使用 BQL 查询

我们还提供类 SQL 语法的 BQL 查询语言来查询数据，例如：
```
curl -X GET \
	-H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'bql=select * from Player limit 0,100 order by name' \
  https://api.bmob.cn/1/cloudQuery
```

更多请参考 [BQL 详细指南](/bql/index.html?menukey=otherdoc&key=bql "BQL 详细指南") 。

BQL 还支持占位符查询，where 和 limit 子句的条件参数可以使用问号替换，然后通过 `values` 数组传入：
```
curl -X GET \
   -H "X-Bmob-Application-Id: Your Application ID" \
   -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'bql=select * from Player where name=? limit ?,? order by name' \
  --data-urlencode 'values=["dennis", 0, 100]'
  https://api.bmob.cn/1/cloudQuery
```

## 修改数据
为了更改一个对象上已经有的数据，你可以发送一个PUT请求到对象相应的URL上，只有你指定的Key的值才会变更为新值，任何你未指定的Key的值都不会更改，所以你可以只更新对象数据的一个子集。例如，我们来更改我们对象的一个score的字段:
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":73453}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
返回的JSON对象只会包含一个updatedAt字段，表明更新发生的时间：
```
{
    "updatedAt": "2011-08-21 18:02:52"
}
```

## 删除数据
为了在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上删除一个对象，可以发送一个DELETE请求到指定的对象的URL，比如:
```
curl -X DELETE \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 删除字段
你可以在一个对象中删除一个字段，通过Delete操作:
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"opponents":{"__op":"Delete"}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 数组
为了存储数组型数据，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供了3种操作来原子性地更改一个数组字段:

**Add** 在一个数组字段的后面添加一些指定的对象(包装在一个数组内)

**AddUnique** 只会在原本数组字段中没有这些对象的情形下才会添加入数组，插入数组的位置不固定的

**Remove** 从一个数组字段的值内移除指定的数组中的所有对象

### 添加数组数据

添加一行记录时创建一个普通的类似于列表的数组类型字段，可以使用以下方法添加：
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"list":{"__op":"Add","objects":["person1","person2"]}}' \
    https://api.bmob.cn/1/classes/GameScore
```
添加一行记录时创建一个普通的类似于集合的数组类型字段，可以使用以下方法添加：
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"list":{"__op":"Add","objects":["person1","person2"]}}' \
    https://api.bmob.cn/1/classes/GameScore
```

### 更新数组数据
每一种方法都会有一个objects，即包含了这些方法将被添加或删除的对象列表，举个例子，技能skills是一个类似于集合的数组类型，那么我们可以在skills中加入一些对象，只有在skills原来的对象中不包含这些值的情况下才会被加入:
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"skills":{"__op":"AddUnique","objects":["flying","kungfu"]}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 查询数组数据
对于Key的类型是数组的情况，可以查找Key的数组值中包含有2的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"arrayKey":2}' \
    https://api.bmob.cn/1/classes/RandomObject
```

你同样可以使用"$all"操作符来找到类型为数组的Key的值中包含有2,3和4的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"arrayKey":{"$all":[2,3,4]}}' \
    https://api.bmob.cn/1/classes/RandomObject
```

### 删除数组数据
同理我们可以使用Remove这个操作在把这些对象从skills中移除：
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"skills":{"__op":"Remove","objects":["flying","kungfu"]}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 使用索引和对象key修改数组中的对象
比如你当前行有一列叫用户的工作经验projectExperiences，是一个Array数组列，里面包含了多个对象值：[{"name":"项目名称","descr":"项目描述"，"startTime":"开始时间"，"endTime":"结束时间"}, ...]

那么我们要修改projectExperiences数组中第一个对象的name值：

```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"projectExperiences.0.name":"项目名称2"}' \
    https://api.bmob.cn/1/users/e1kXT22L
```

## 修改对象的某个值
比如你当前行有一列叫userAttibute,保存的是一个JSON 对象，比如是: {"name":"John", "gender":"男"}

那么我们要修改这个对象的某个Key的值：

```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"userAttibute.gender":"女"}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 数据关联

### 关联对象
对象可以与其他对象相联系。就像数据库中的主外键关系一样，数据表 A 的某一个字段是数据表 B 的外键，只有表 B 中存在的数据才可插入到表 A 中的字段。 

### 添加关联关系
为了更新 **Pointer** 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个指针, OK，我们可以像这样添加一行记录并添加一个指针:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"game":{"__type":"Pointer","className":"Game","objectId":"DdUOIIIW"}}' \
  https://api.bmob.cn/1/classes/GameScore
```

为了更新 **Relation** 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个或多个关系, OK，我们可以像这样添加一行记录时并添加多个关系:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"opponents":{"__op":"AddRelation","objects":[{"__type":"Pointer","className":"Player","objectId":"z0lOxp1X"},{"__type":"Pointer","className":"Player","objectId":"MTzXDDDG"}]}}' \
  https://api.bmob.cn/1/classes/GameScore
```

### 修改关联对象
为了更新 **Pointer** 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个指针, OK，我们可以像这样添加一个指针:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"game":{"__type":"Pointer","className":"Game","objectId":"DdUOIIIW"}}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

为了更新 **Relation** 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个或多个关系, OK，我们可以像这样添加多个关系:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"opponents":{"__op":"AddRelation","objects":[{"__type":"Pointer","className":"Player","objectId":"z0lOxp1X"},{"__type":"Pointer","className":"Player","objectId":"MTzXDDDG"}]}}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 查询关联对象
有几种方式来对关系 **Relation** 或 指针 **Pointer** 类型数据进行查询, 如果你想要获取对象中有个Key类型是关系 **Relation** 或 指针 **Pointer**，这都说明你要获取的对象是匹配到另一个特殊（关联或指向）的对象的, 你可以用一个 **where** 参数查询, 自己使用 **__type** 构造一个 **Pointer**, 就像你构造其他数据类型一样。举例说, 如果每一条评论(Comment对象)有一个Key叫post，类型是Pointer，并且指向了一个具体的帖子(Post对象，用objectId表示一个帖子)，那么你可以使用下面的请求获取一个帖子的所有评论:
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"__type":"Pointer","className":"Post","objectId":"1dafb9ed9b"}}' \
  https://api.bmob.cn/1/classes/Comment
```

如果你想要获取对象, 这些对象的一个字段指向的对象是符合另一个查询的, 你可以使用 $inQuery 操作符，注意默认的 limit 是 100 而且最大的 limit 是 1000，这个限制同样适用于内部的查询, 所以对于较大的数据集你可能需要细心地构建查询来获得期望的行为。举例说, 假设你有一个帖子(Post)类和一个评论(Comment)类, 每个评论(Comment)都有一个指向它的帖子(Post)的关系Key名为post，并且类型为Pointer, 你可以找到所有有图片的帖子(Post)的评论(Comment):
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"$inQuery":{"where":{"image":{"$exists":true}},"className":"Post"}}}' \
  https://api.bmob.cn/1/classes/Comment
```

同理，使用下面的请求，你可以找到所有没有图片的帖子(Post)的评论(Comment):
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"$notInQuery":{"where":{"image":{"$exists":true}},"className":"Post"}}}' \
  https://api.bmob.cn/1/classes/Comment
```

如果你想获取的对象，是其父对象的关系 **Relation** 类型的Key的所有成员的话, 你可以使用 $relatedTo 操作符, 假设你有一个帖子(Post)类和一个系统默认的用户(_User)类, 而每一个帖子(Post)都可以被不同的用户(_User)所喜欢。 如果帖子(Post)类下面有一个Key名为likes，且是 **Relation** 类型, 存储了喜欢这个帖子(Post)的用户(_User)。那么你可以找到喜欢过同一个指定的帖子(Post)的所有用户：
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"$relatedTo":{"object":{"__type":"Pointer","className":"Post","objectId":"1dafb9ed9b"},"key":"likes"}}' \
  https://api.bmob.cn/1/users
```

还可以使用组合查询，比如下面这样，判断用户是否喜欢(likes)过这个帖子：
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"likes":{"$inQuery":{"where":{"objectId":"用户objectId"},"className":"_User"}}, "objectId":"帖子objectId"}'
  --data-urlencode 'limit=0'
  --data-urlencode 'count=true'
  https://api.bmob.cn/1/classes/Post
```

返回结果集如下：
```
{
    count: 1
    results: [ ]
}
```
你可以做如下判断，如果count=1，表明用户喜欢的这个帖子objectId存在，即用户喜欢过这个帖子；若count=0, 表明用户没有喜欢过这个帖子。

在某些情况之下，你可能需要在一个查询之中返回关联对象的多种类型，你可以通过传入字段名称到include参数中，多个字段名称用,间隔， 比如，我们想获得最近的10篇评论，而你想同时得到它们相关的post：
**include的Key必须是Pointer类型**
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'order=-createdAt' \
  --data-urlencode 'limit=10' \
  --data-urlencode 'include=post' \
  https://api.bmob.cn/1/classes/Comment
```
不是作为一个 **Pointer** 类型表示，post字段现在已经被展开为一个完整的帖子(Post)对象， __type 被设置为 **Object** 而 **className** 同样也被提供了。 举例说， 一个指向帖子(Post)的Pointer原本展示为：
```
{
  "__type": "Pointer",
  "className": "Post",
  "objectId": "51e3a359e4b015ead4d95ddc"
}
```
当一个查询使用include参数来包含进去取代 **Pointer** 之后，可以看到 **Pointer** 被展开为：
```
{
  "__type": "Object",
  "className": "Post",
  "objectId": "51e3a359e4b015ead4d95ddc",
  "createdAt": "2011-12-06T20:59:34.428Z",
  "updatedAt": "2011-12-06T20:59:34.428Z",
  "otherFields": "willAlsoBeIncluded"
}
```
你可以同样做多层的include, 这时要使用 "." 号. 如果你要include一条评论(Comment)对应的帖子(Post)的作者(author)：
**include的Key必须是Pointer类型**
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'order=-createdAt' \
  --data-urlencode 'limit=10' \
  --data-urlencode 'include=post.author' \
  https://api.bmob.cn/1/classes/Comment
```
如果你要构建一个查询, 这个查询要include多个 **Pointer** 类型的Key, 此时用逗号分隔Key名称列表即可。

include 也可以指定keys了，即 Pointer 类型的字段指向的表只返回指定的字段，举例如下：
**include的Key必须是Pointer类型**
**建议大家使用以下方式，性能更好，流量更少**
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'order=-createdAt' \
  --data-urlencode 'limit=10' \
  --data-urlencode 'include=post[likes].author[username|email],user[username]' \
  https://api.bmob.cn/1/classes/Comment
```
post 指向的帖子表只返回likes字段，而author指向的用户表只返回username和email字段，user指向的用户表只返回username字段。


### 删除关联关系
可以在一个对象中删除一个关系:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"opponents":{"__op":"RemoveRelation","objects":[{"__type":"Pointer","className":"Player","objectId":"z0lOxp1X"}]}}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 批量数据操作

为了减少因为网络通讯次数太多而带来的时间浪费, 你可以使用下面的批量(batch)操作，在一个请求中对多个普通对象进行添加(create)、更新(update)、删除(delete) 操作，上限为50个。

在一个批量(batch)请求中每一个操作都有自己对应的方法、路径和主体, 这些参数可以代替你通常使用的HTTP方法. 这些操作会以发送过去的顺序来执行, 比如我们要创建一系列的 GameScore 的对象:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "requests": [
          {
            "method": "POST",
            "path": "/1/classes/GameScore",
            "body": {
              "score": 1337,
              "playerName": "Sean Plott"
            }
          },
          {
            "method": "POST",
            "path": "/1/classes/GameScore",
            "body": {
              "score": 1338,
              "playerName": "ZeroCool"
            }
          }
        ]
      }' \
  https://api.bmob.cn/1/batch
```
批量操作的响应会是一个列表, 列表的返回值个数与给定的requests请求个数是相等的。列表中每个返回项都有一个字段是 **"success"** 或者 **"error"**， **"success"** 的值通常和你进行其他REST操作成功时返回的值是一样的:
```
{
  "success": {
    "createdAt": "2012-06-15T16:59:11.276Z",
    "objectId": "51c3ba67e4b0f0e851c16221"
  }
}
```
**"error"** 的值是有返回码和错误信息字符串的一个对象:
```
{
  "error": {
    "code": 101,
    "error": "object not found for delete"
  }
}
```
在 batch 操作中更新(update)和删除(delete)同样是有效的，如果相应记录有ACL规则，则必须传入该用户的Token才能进行更新或删除:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "requests": [
          {
            "method": "PUT",
            "token": "pnktnjyb996sj4p156gjtp4im",
            "path": "/1/users/51e3a334e4b0b3eb44adbe1a",
            "body": {
              "score": 999999
            }
          },
          {
            "method": "DELETE",
            "token": "pnktnjyb996sj4p156gjtp4im",
            "path": "/1/users/51a8a4d9e4b0d034f6159a35"
          }
        ]
      }' \
  https://api.bmob.cn/1/batch
```

## 原子计数器
很多应用可能会有需要计数器的功能，比如某条信息被点赞多少次等。Bmob提供了非常便捷的方式来保证原子性的修改某一数值字段的值。
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":{"__op":"Increment","amount":1}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
同理可以让score像下面一样减少一个固定的值：
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":{"__op":"Increment","amount":-2}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 文件

Bmob的文件上传有整个文件上传和分片上传两种方式，可以分别实现小文件上传和大文件的上传。

### 整个文件上传
上传整个文件到bmob，发送一个POST请求到file路径，参数是：文件名，可以选择BASE64加密，也可以选择不使用。
上传一个 hello.txt 文件实现方法如下（-d的值是文件内容）：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: text/plain" \
  -d 'Hello, World!' \
  https://api.bmob.cn/1/files/hello.txt
```
上传当前文件夹下的图片 myPicture.jpg 实现方法如下（--data-binary的值是文件二进制内容）：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: image/jpeg" \
  --data-binary '@myPicture.jpg' \
  https://api.bmob.cn/1/files/myPicture.jpg
```
Content-Type 设置可以参考：<http://tool.oschina.net/commons/>

返回的主体是一个JSON对象，包含：文件名（filename）、分组（group）、文件地址（url）。 `http://file.bmob.cn/ + url` 就是文件上传成功后的完整地址，返回的Http Headers中的Location会包含该完整地址:

```
{
	filename: "myPicture.jpg"
	group: "group1"
	url: "M00/00/01/wKgBP1N3Cg3IDgN1AAo7dN2HyQ0408.jpg"
}

```

然后你需要把上传后的文件对象关联到某行记录中:
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"score":73453, "file":{
        "__type": "File",
        "group": "group1",
        "filename": "myPicture.jpg",
        url: "M00/00/01/wKgBP1N3Cg3IDgN1AAo7dN2HyQ0408.jpg"
    }}' \
https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```


### 分片上传
相对于普通上传， 分片上传的优势是：
1.适合于尺寸较大的文件传输，通过切片来避免单个HTTP数据量过大而导致连接超时；
2.在网络条件较差的环境下，较小的尺寸可以有较高的上传成功率，从而避免无休止的失败重试；

实现方法：第一步， 创建文件并上传第一片, 发送一个POST请求去mkfile路径（ `https://api.bmob.cn/1/mkfile` ）中，参数是：文件名（需要进行BASE64加密）和文件总大小（单位：byte），发送主体（body）为文件第一片的二进制流（约定为256KB的大小）。其中，参数名之间用 `/` 分割开。实现方法如下（--data-binary的值是文件二进制内容）：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/octet-stream" \
  --data-binary '文件第一片的二进制流' \
  https://api.bmob.cn/1/mkfile/c3NzLmpwZw==/1024000
```

返回的主体是一个JSON对象，包含： 

```
crc32：文件片段的crc32校验码。
ctx：服务端不透明的内部参数，需要在下一次上传时提交。
file： 如果文件没有完整上传，返回false, 否则返回文件名、分组和文件地址。
host：下一个片的上传URL（注意：不固定）。
offset：下一个片的偏移位置。
```

下面是一个实例：

```
{
	crc32: 755192142
	ctx: "eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9"
	file: false
	host: https://api.bmob.cn/1/bput/
	offset: 13
}
```

第二步， 循环上传剩余片, 发送POST请求到mkfile路径返回的host, 参数是 ctx 和 offset，如下（--data-binary的值是文件二进制内容）： 

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/octet-stream" \
  --data-binary '其他文件片的流' \
  https://api.bmob.cn/1/bput/eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9/13
```

返回的主体是一个JSON对象，内容同上，包含：
```
{
	crc32: 755192142
	ctx: "eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9"
	file: false
	host: https://api.bmob.cn/1/bput/
	offset: 13
}
```

### 删除文件

删除文件，必须要知道文件的url，如下：

```
curl -X DELETE \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  https://api.bmob.cn/1/files/M00/00/01/wKgBP1N3FAWRJXsSAAAB_rYZATs52.html
```

其中M00/00/01/wKgBP1N3FAWRJXsSAAAB_rYZATs52.html是文件的url。

返回结果是个json对象，格式如下：

```
{
  "msg": "ok"
}
```
**删除文件不会删除文件关联的行记录中的文件列的值，需要自行通过更新行来删除关联。**

## 图片处理
提供一些工具接口， 方便开发者处理图片

#### 缩微图
提供原图的URL地址和相应的参数， 返回缩微图的URL地址， 具体参数定义如下：

    mode:模式 0: 指定宽， 高自适应，等比例缩放
         模式 1: 指定高， 宽自适应，等比例缩放
         模式 2: 指定最长边，短边自适应，等比例缩放
         模式 3: 指定最短边，长边自适应，等比例缩放
         模式 4: 指定最大宽高， 等比例缩放
         模式 5: 固定宽高， 居中缩放	
    image:原图片url
    width:宽度，模式 0, 4, 5必填
    height：高度，模式 1, 4, 5必填
    longEdge：长边，模式 2必填
    shortEdge：短边，模式 3必填
    quality：质量，选填, 范围 1-100(只对jpg文件有效)
    outType：输出类型，0:默认，输出url；1:输出base64编码的字符串流


调用方式如下：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"image":"http://file.bmob.cn/M00/01/49/wKhkA1OEmUmAXRToAAIAco88Nk08205940","mode":0,"quality":100,"width":100}' \
  https://api.bmob.cn/1/images/thumbnail
```
如果 outType = 0 返回云端url地址:
```
{
  "filename": "e6c8ac18c9.jpg",
  "group": "group1",
  "url": "M00/01/6E/wKhkA1OGpWKAGdNUAAAjdkbUqo4612.jpg"
}
```
如果 outType = 1 返回文件内容的 base64 字符串：
```
{
  "file":"base64的文件内容"
}
```

#### 水印图
提供原图的URL地址， 水印图的URL地址和相应的参数， 返回缩微图的URL地址， 具体参数定义如下：

    image：原图路径
    watermark：水印图路径
    dissolve:透明度，0-255
    distanceX：横轴边距，单位:像素(px)，缺省值为10
    distanceY：纵轴边距，单位:像素(px)，缺省值为10
    outType：输出类型，0:默认，输出url；1:输出base64编码的字符串流
    gravity:水印位置，见下图

![](image/anchor.png)

调用方式如下：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"image":"http://file.bmob.cn/M01/FB/94/oYYBAFVsLzaATYHUAAInI2Hg05M737.jpg","watermark":"http://file.bmob.cn/M01/F8/4C/oYYBAFVru0uAa0yyAAAsGVkLsy8979.jpg","dissolve":100,"gravity":"SouthWest","distanceX":10,"distanceY":10}' \
  https://api.bmob.cn/1/images/watermark
```
如果 outType = 0 返回云端url地址:
```
{
  "filename": "e605a6d0ab.jpg",
  "group": "group1",
  "url": "M00/01/6E/wKhkA1OGp2eAVyo9AAMUF3r_9N0925.jpg"
}
```
如果 outType = 1 返回文件内容的 base64 字符串：
```
{
  "file":"base64的文件内容"
}
```

## 用户
很多跨平台和跨系统的应用都有一个统一的登录流程，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")通过REST API访问用户的账户让你实现该功能。

通常来说，用户这个类的功能与其他的对象是相同的，比如都没有限制模式(Schema Less)，User对象和其他对象不同的是一个用户必须有用户名(username)和密码(password)，密码会被自动地加密和存储。[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")强制你username和email这两个Key的值必须是不重复的。

### 属性
[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")默认会有几个特定的属性：
username: 用户的用户名`（必需）`。
password: 用户的密码`（必需）`。
email: 用户的电子邮件地址`（可选）`。

### 注册用户
注册一个新用户与创建一个新的普通对象之间的不同点在于其username和password字段都是必要的，password字段会以与其他的字段不一样的方式处理，它在保存时会被加密而且永远不会被返回给任何来自客户端的请求。

在你的应用设置页面中，你可以向[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")来请求认证邮件地址，这项设置启用了的话，所有用户在注册时填写email这个Key的值，并且邮箱有效的情况下，就会向这个邮箱地址发出一封邮件，邮件中会包含一个来自[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")的邮箱验证的链接，当你的用户查收邮件并点击这个链接后，这个用户emailVerified的Key的值会置为True，你可以在emailVerified字段上查看用户的email是否已经通过验证了。

为了注册一个新的用户，需要向user路径发送一个POST请求，你可以加入一个甚至多个新的字段，例如，创建一个有家庭电话字段的新用户:
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"username":"cooldude6","password":"b_m7!-o8","phone":"415-392-0202"}' \
    https://api.bmob.cn/1/users
```
当创建成功时,HTTP响应头的状态码返回为201 Created，Http响应头的Location值包含了该新用户的URL:
```
Status: 201 Created
Location: https://api.bmob.cn/1/users/Kc3M222J
```
返回的主体是一个JSON对象，包含objectId，表示唯一的用户, createdAt时间戳表示用户注册时间, sessionToken可以被用来认证更新或删除这名用户信息的请求。
```
{
    "createdAt": "2011-11-07 20:58:34",
    "objectId": "Kc3M222J",
    "sessionToken": "pnktnjyb996sj4p156gjtp4im"
}
```

**这里需要注意一点的是，有些时候你可能需要在用户注册时发送一封验证邮件，以确认用户邮箱的真实性。这时，你只需要登录自己的应用管理后台，在应用设置->邮件设置（下图）中把“邮箱验证”功能打开，Bmob云后端就会在注册时自动发动一封验证邮件给用户。**

![](image/email_verify.jpg)
设置邮箱验证功能

### 使用手机号码一键注册或登陆

[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 支持让用户直接输入手机号码进行注册，如果手机号码存在则自动登陆：
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"mobilePhoneNumber":"185xxxxxxxx","smsCode":"6位短信验证码"}' \
    https://api.bmob.cn/1/users
```
其中 **mobilePhoneNumber** 就是手机号码，而 **smsCode** 是使用 [请求短信验证码API](/restful/developdoc/index.html?menukey=develop_doc&key=develop_restful#index_请求短信验证码 "请求短信验证码API")发送到用户手机上的 6位验证码字符串。如果是新用户且不传入 username，默认用户名将是手机号码。

注册或者登陆成功后，返回的应答跟登陆接口类似：

{
  "username": "185xxxxxxxx",
  "mobilePhoneNumber": "185xxxxxxxx",
  "mobilePhoneVerified": true,
  "createdAt": "2011-11-07 20:58:34",
  "updatedAt": "2011-11-07 20:58:34",
  "objectId": "Kc3M222J",
  "sessionToken": "pnktnjyb996sj4p156gjtp4im"
  ……其他属性
}
如果是第一次注册，将默认设置_User表的 **mobilePhoneVerified** 属性为 true。


### 登录用户
你的用户注册之后，你需要让他们用自己的用户名和密码登录，为了做到这一点，发送一个HTTP GET请求到 **/1/login** ，加上username和password作为URL编码后的参数：
```
curl -X GET
    \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'username=cooldude6' \
    --data-urlencode 'password=b_m7!-o8' \
    https://api.bmob.cn/1/login
```
username 支持传入_User表的username或email或mobilePhoneNumber字段的值，作为登录的扩展功能，以实现邮箱和密码、手机号和密码登录功能。

除了有用户名或邮箱或手机号码和密码登录的功能，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 还支持使用手机号码和验证码一键快速登录的功能，而 **smsCode** 是使用 [请求短信验证码API](/restful/developdoc/index.html?menukey=develop_doc&key=develop_restful#index_请求短信验证码 "请求短信验证码API")发送到用户手机上的 6位验证码字符串：
```
curl -X GET
    \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'mobilePhoneNumber=185xxxxxxxx' \
    --data-urlencode 'smsCode=6位短信验证码' \
    https://api.bmob.cn/1/login
```
返回的主体是一个JSON对象，包括所有除了password以外的自定义字段，它同样包含了createdAt,updateAt,objectId和sessionToken字段：
```
{
    "username": "cooldude6",
    "phone": "415-392-0202",
    "createdAt": "2011-11-07 20:58:34",
    "updatedAt": "2011-11-07 20:58:34",
    "objectId": "Kc3M222J",
    "sessionToken": "pnktnjyb996sj4p156gjtp4im"
}
```


### 获取当前用户
当注册一个用户后，你可以通过发送一个HTTP GET请求到用户注册成功时返回的HTTP请求头中的Location的URL获取用户的信息。比如，为了获取上面注册成功的用户：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/users/Kc3M222J
```
返回的body是一个JSON对象,包含所有用户提供的字段,除了密码以外.也包括了createdAt,updatedAt和objectId字段.
```
{
    "username": "cooldude6",
    "phone": "415-392-0202",
    "createdAt": "2011-11-07 20:58:34",
    "updatedAt": "2011-11-07 20:58:34",
    "objectId": "51fa6886e4b0cc0b5a3792e9"
}
```

### 更新用户
在通常的情况下，我们都不希望用户去修改自己的数据，但可以通过认证让用户去做这件事，用户必须加入一个 **X-Bmob-Session-Token** 头部来请求这个更新操作，这个sessionToken在注册和登录时都会返回。

为了改动一个用户已经有的数据，需要对这个用户的URL发送一个HTTP PUT请求，任何你没有指定的key会保持不变，所以你可以只改动用户信息中的一部分，username和password可以更改，但是新的username不能重复。

比如，如果我们想对 cooldude6 的电话做出一些改动：
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
    -H "Content-Type: application/json" \
    -d '{"phone":"415-369-6201"}' \
    https://api.bmob.cn/1/users/Kc3M222J
```
返回的body是一个JSON对象，只有一个updatedAt字段表明更新发生的时间.
```
{
    "updatedAt": "2011-11-07 21:25:10"
}
```
**在更新用户信息时，如果用户邮箱有变更并且在管理后台打开了邮箱验证选项的话，Bmob云后端同样会自动发动一封验证邮件给用户。**

### 删除用户
为了在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上删除一个用户，可以向用户的URL上发送一个DELETE请求，前提是你必须提供一个X-Bmob-Session-Token在Http请求头以便认证授权，例子：
```
curl -X DELETE \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
    https://api.bmob.cn/1/users/g7y9tkhB7O
```

当然了，你也可以直接把MasterKey传入到X-Bmob-Master-Key中, 这个就可以实现在不需要提供SessionToken的情形下更新和删除用户了，但希望只在开发环境下使用，不要把MasterKey发布出去。

### 查询用户
你可以一次获取多个用户，只要向用户的根URL发送一个GET请求，没有任何URL参数的话，可以简单地列出所有用户：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/users
```
返回的值是一个JSON对象包括一个results字段, 值是包含了所有对象的一个JSON数组.
```
{
    "results": [
        {
            "username": "bigglesworth",
            "phone": "650-253-0000",
            "createdAt": "2011-11-07 20:58:06",
            "updatedAt": "2011-11-07 20:58:06",
            "objectId": "Kc3M222J"
        },
        {
            "username": "cooldude6",
            "phone": "415-369-6201",
            "createdAt": "2011-11-07 20:58:34",
            "updatedAt": "2011-11-07 21:25:10",
            "objectId": "Kc3M222k"
        }
    ]
}
```

所有的对普通对象的查询选项都适用于对用户对象的查询，所以可以查看 [查询](#index_查询) 部分来获取详细信息。

User表是一个特殊的表，专门用于存储用户对象。在浏览器端，你会看到一个User表旁边有一个小人的图标。



### 密码重置
你可以使用这项功能，前提是用户将email与他们的账户关联起来，如果要重设密码，发送一个POST请求到 **/1/requestPasswordReset**, 同时在request的body部分带上email字段.
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"email":"coolguy@iloveapps.com"}' \
    https://api.bmob.cn/1/requestPasswordReset
```
如果成功的话,返回的值是一个JSON对象。
密码重置流程如下：

1. 用户输入他们的电子邮件，请求重置自己的密码。
2. Bmob向他们的邮箱发送一封包含特殊的密码重置连接的电子邮件，此邮件的模板可在Bmob后台中修改。
3. 用户根据向导点击重置密码连接，打开一个特殊的Bmob页面，输入一个新的密码。
4. 用户的密码已被重置为新输入的密码。

### 使用短信验证码进行密码重置

如果用户有绑定了手机号码，就可以通过手机验证码短信来实现使用手机号码找回密码的功能，先调用 [请求短信验证码API](/restful/developdoc/index.html?menukey=develop_doc&key=develop_restful#index_请求短信验证码 "请求短信验证码API")会将验证码发送到用户手机上，用户收到验证码并输入后，调用PUT /1/resetPasswordBySmsCode/:smsCode 来为用户设置新的密码：
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"password": "new password"}' \
    https://api.bmob.cn/1/resetPasswordBySmsCode/smsCode即收到的6位验证码
```

如果成功的话,返回的值是一个JSON:
```
{"msg": "ok"}
```
这时，用户就可以用新密码登陆了。

### 提供旧密码方式安全修改用户密码

很多开发者希望让用户输入一次旧密码做一次校验，旧密码正确才可以修改为新密码，因此我们提供了一个单独的 API **PUT /1/updatePassword** 来安全地修改用户密码：
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
	-H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
    -H "Content-Type: application/json" \
    -d '{"oldPassword": "用户的老密码","newPassword": "用户的新密码"}' \
    https://api.bmob.cn/1/updateUserPassword/g7y9tkhB7O
```

g7y9tkhB7O：为当前登录用户的objectId。

**注意：仍然需要传入 X-Bmob-Session-Token，也就是登录用户才可以修改自己的密码。**


### 邮箱验证
设置邮件验证是一个可选的应用设置, 这样可以对已经确认过邮件的用户提供一部分保留的体验，邮件验证功能会在用户(User)对象中加入emailVerified字段, 当一个用户的邮件被新添加或者修改过的话，emailVerified会默认被设为false，如果应用设置中开启了邮箱认证功能，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")会对用户填写的邮箱发送一个链接, 这个链接可以把emailVerified设置为 true.

emailVerified 字段有 3 种状态可以考虑：

**true** : 用户可以点击邮件中的链接通过[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")来验证地址，一个用户永远不会在新创建这个值的时候出现emailVerified为true。

**false** : 用户(User)对象最后一次被刷新的时候, 用户并没有确认过他的邮箱地址, 如果你看到emailVerified为false的话，你可以考虑刷新 用户(User)对象。

**missing** : 用户(User)对象已经被创建，但应用设置并没有开启邮件验证功能； 或者用户(User)对象没有email邮箱。

#### 请求验证Email
发送到用户邮箱验证的邮件会在一周内失效，可以通过调用 **/1/requestEmailVerify** 来强制重新发送：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"email":"coolguy@iloveapps.com"}' \
  https://api.bmob.cn/1/requestEmailVerify
```

### 用户账户连接

Bmob允许你连接你的用户到第三方账户服务系统，比如新浪微博和QQ，这样就允许您的用户用已经存在的第三方账户直接登录您的App。通过注册或者更新的用户信息的功能，使用 **authData** 字段来保存第三方服务的授权信息就可以做到。一旦用户关联了某个第三方账户，**authData** 将被存储到您的Bmob的用户信息里，并通过登录即可重新获取到。

**authData** 是一个普通的 JSON 对象，它所要求的key根据第三方账户服务不同而不同，具体要求见下面。每种情况下，你都需要自己负责完成整个授权过程 (一般是通过 OAuth 协议，1.0 或者 2.0) 通过连接的API来获取授权信息。

新浪微博的 **authData** 内容:
```
{
  "authData": {
    "weibo": {
      "uid": "123456789",
      "access_token": "2.00ed6eMCV9DWcBcb79e8108f8m1HdE",
      "expires_in": 1564469423540
    }
  }
}
```

腾讯QQ的 **authData** 内容:
```
{
  "authData": {
    "qq": {
      "openid": "2345CA18A5CD6255E5BA185E7BECD222",
      "access_token": "12345678-SM3m2avZxh5cjJmIrAfx4ZYyamdofM7IjU",
      "expires_in": 1382686496
    }
  }
}
```
匿名用户 (Anonymous user) 的 authData 内容:
```
{
  "anonymous": {
    "id": "random UUID with lowercase hexadecimal digits"
  }
}
```

#### 注册和登录

使用一个第三方账户连接服务来注册用户并登录，同样使用POST请求/1/users，只是需要提供authData字段。例如，使用新浪微博账户注册或者登录用户:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
     "authData": {
       "weibo": {
          "uid": "123456789",
          "access_token": "2.00ed6eMCV9DWcBcb79e8108f8m1HdE",
          "expires_in": 1564469423540
        }
      }
    }' \
  https://api.bmob.cn/1/users
```

Bmob 会校验提供的 authData 是否有效，并检查是否已经有一个用户连接了这个 authData 服务。如果已经有用户存在并连接了同一个 authData，那么Http响应头将返回 200 OK 和详细信息 (包括用户的 sessionToken):
```
Status: 200 OK
Location: https://api.bmob.cn/1/users/Kc3M222J
```
应答的 body 类似:
```
{
  "username": "Bmob",
  "createdAt": "2011-11-07 21:25:10",
  "updatedAt": "2011-11-07 21:25:10",
  "objectId": "Kc3M222J",
  "sessionToken": "pnktnjyb996sj4p156gjtp4im",
  "authData": {
    "weibo": {
      "uid": "123456789",
      "access_token": "2.00ed6eMCV9DWcBcb79e8108f8m1HdE",
      "expires_in": 1564469423540
    }
  }
}
```
如果用户还没有连接到这个帐号，则你会收到 201 Created 的应答状态码，标识新的用户已经被创建:
```
Status: 201 Created
Location: https://api.bmob.cn/1/users/Kc3M222J
```
应答内容包括 objectId,createdAt,sessionToken 以及一个自动生成的随机 username，例如:
```
{
  "username": "f4d34d27e3f48010",
  "createdAt": "2011-11-07 21:25:10",
  "objectId": "Kc3M222J",
  "sessionToken": "pnktnjyb996sj4p156gjtp4im",
}
```

#### 连接

连接一个现有的用户到新浪微博或者腾讯QQ帐号，可以通过发送一个 PUT 请求附带 authData 字段到以上Location返回的用户URL做到。例如，连接一个用户到腾讯QQ帐号发起的请求类似这样:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
  -H "Content-Type: application/json" \
  -d '{
        "authData": {
           "qq": {
              "openid": "2345CA18A5CD6255E5BA185E7BECD222",
              "access_token": "12345678-SM3m2avZxh5cjJmIrAfx4ZYyamdofM7IjU",
              "expires_in": 1382686496
            }
        }
      }' \
  https://api.bmob.cn/1/users/Kc3M222J
```

完成连接后，你可以使用匹配的 authData 来认证他们。


#### 断开连接

断开一个现有用户到某个服务，可以发送一个 PUT 请求设置 authData 中对应的服务为 null 来做到。例如，取消新浪微博关联:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
  -H "Content-Type: application/json" \
  -d '{
        "authData": {
          "weibo" : null
        }
      }' \
  https://api.bmob.cn/1/users/Kc3M222J
```

## ACL和角色

数据安全是软件系统中最重要的组成部分，为了更好的保护应用数据的安全，Bmob在软件架构层面提供了应用层次、表层次、ACL（Access Control List：访问控制列表）、角色管理（Role）四种不同粒度的权限控制的方式，确保用户数据的安全（详细请查看[Bmob数据与安全页面](http://docs.bmob.cn/datasafety/index.html?menukey=otherdoc&key=datasafety)，了解Bmob如何保护数据安全）。

其中，最灵活的方法是通过ACL和角色，它的思路是每一条数据有一个用户和角色的列表，以及这些用户和角色拥有什么样的许可权限。

大多数应用程序需要对不同的数据进行灵活的访问和控制，这就可以使用Bmob提供的ACL模式来实现。例如：

- 对于私有数据，读写权限可以只局限于数据的所有者。
- 对于一个论坛，会员和版主有写的权限，一般的游客只有读的权限。
- 对于日志数据只有开发者才能够访问，ACL可以拒绝所有的访问权限。
- 属于一个被授权的用户或者开发者所创建的数据，可以有公共的读的权限，但是写入权限仅限于管理者角色。
- 一个用户发送给另外一个用户的消息，可以只给这些用户赋予读写的权限。

### ACL的格式

在Bmob中，ACL是按JSON对象格式（key-value）来表示的。这个JSON对象的key是objectId（用户表某个用户对应的objectId）或者是 *(表示公共的访问权限)，ACL 的值是 "读和写的权限", 这个JSON对象的key总是权限名, 而这些key的值总是 true。

如果您想让一个 id 为 "Kc3M222k" 的用户有读和写一条数据的权限, 而且这个数据应该可以被全部人读取的话，这个ACL的表达方式如下：

```
{
  "Kc3M222k": {
    "read": true,
    "write": true
  },
  "*": {
    "read": true
  }
}
```

### 角色和相关操作

在很多情况下，你需要定义一些用户具有某种相同的数据操作权限，而另外一群用户具有另外一种相同的数据操作权限，这时你就可以使用到Bmob的角色（对应Bmob在Web提供的Role表、SDK中的BmobRole类）功能，设置不同的用户组不同的操作权限。角色表有三个特殊字段：

`name` : 必须字段，表示角色名称,而且只允许被设置一次（命名必须由字母, 空格, 减号或者下划线构成）；

`users` :一个指向一系列用户的关系, 这些用户会继承角色的权限；

`roles` : 一个指向一系列子角色的关系, 这些子关系会继承父角色所有的权限。


#### 创建角色

创建一个新角色的方法如下（固定POST数据到https://api.bmob.cn/1/roles中，且必须提供 `name` 字段）：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "Moderators",
        "ACL": {
          "*": {
            "read": true
          }
        }
      }' \
  https://api.bmob.cn/1/roles
```

如果你要创建一个包括了“用户和子角色”的角色，实现方式如下：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "Moderators",
        "ACL": {
          "*": {
            "read": true
          }
        },
        "roles": {
          "__op": "AddRelation",
          "objects": [
            {
              "__type": "Pointer",
              "className": "_Role",
              "objectId": "Fe441wZ5"
            }
          ]
        },
        "users": {
          "__op": "AddRelation",
          "objects": [
            {
              "__type": "Pointer",
              "className": "_User",
              "objectId": "Kc3M222k"
            }
          ]
        }
      }' \
  https://api.bmob.cn/1/roles
```

当创建成功后返回HTTP如下：

```
Status: 201 Created
Location: https://api.bmob.cn/1/roles/51e3812D
```

#### 获取角色

获取角色对象的方法如下：

```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  https://api.bmob.cn/1/roles/51e3812D
```

响应结果如下：

```
{
  "createdAt": "2012-04-28 17:41:09",
  "objectId": "51e3812D",
  "updatedAt": "2012-04-28 17:41:09",
  "ACL": {
    "*": {
      "read": true
    },
    "role:Administrators": {
      "write": true
    }
  },
  "name": "Moderators"
}
```

注意 users 和 roles 关系无法在 JSON 结果中看到, 您需要使用 $relatedTo 操作符来查询。

#### 更新角色

更新角色时，一个很重要的一点是： `name` 字段不可以更改。添加和删除 `users` 和 `roles` 可以通过使用 AddRelation 和 RemoveRelation 操作符进行。

如给 "Moderators" 角色增加 2 个用户，实现如下:

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "users": {
          "__op": "AddRelation",
          "objects": [
            {
              "__type": "Pointer",
              "className": "_User",
              "objectId": "eba635d9"
            },
            {
              "__type": "Pointer",
              "className": "_User",
              "objectId": "51dfb8bd"
            }
          ]
        }
      }' \
  https://api.bmob.cn/1/roles/51e3812D
```

删除 "Moderrators" 的一个子角色的实现如下：

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "roles": {
          "__op": "RemoveRelation",
          "objects": [
            {
              "__type": "Pointer",
              "className": "_Role",
              "objectId": "eba635d9"
            }
          ]
        }
      }' \
  https://api.bmob.cn/1/roles/51e3812D
```

#### 删除角色

删除角色这里有一个需要注意的是：需要传入 X-Bmob-Session-Token ，即对这条数据有操作权限的用户SessionToken。实现如下：

```
curl -X DELETE \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
  https://api.bmob.cn/1/roles/51e3812D
```

### 角色的使用

设置一条数据的角色权限，需要在ACL中把Key的名字设置为 “`role:` + 角色名称” 。如限制一条数据可以被在 "Members" 里的任何人读到, 而且可以被它的创建者（objectId为`f1766d0b42`）和任何有 "Moderators" 角色的人所修改, 实现方式如下:

```
{
  "f1766d0b42": {
    "write": true
  },
  "role:Members": {
    "read": true
  },
  "role:Moderators": {
    "write": true
  }
}
```

如果这个用户和 "Moderators" 本身就是 "Members" 的子角色和用户，那么，您不必为创建的用户和 "Moderators" 指定读的权限，因为它们都会继承授予 "Members" 的权限。

#### 角色的继承

一个角色可以包含另一个，可以为 2 个角色建立一个父-子关系。 这个关系的结果就是任何被授予父角色的权限隐含地被授予子角色。

这样的关系类型通常在用户管理的内容类的应用上比较常见, 比如在论坛中，有一些少数的用户是 "管理员（Administartors）", 有最高的权限，可以调整系统设置、 创建新的论坛等等。 另一类用户是 "版主（Moderators）"，他们可以对用户发帖的内容进行管理。可见，任何有管理员权限的人都应该有版主的权限。为建立起这种关系, 您应该把 "Administartors" 的角色设置为 "Moderators" 的子角色, 具体来说就是把 "Administrators" 这个角色加入 "Moderators" 对象的 roles 关系之中，实现如下：

```java

curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "roles": {
          "__op": "AddRelation",
          "objects": [
            {
              "__type": "Pointer",
              "className": "_Role",
              "objectId": "<AdministratorsRoleObjectId>"
            }
          ]
        }
      }' \
  https://api.bmob.cn/1/roles/<ModeratorsRoleObjectId>

```

## 地理位置
Bmob允许用户根据地球的经度和纬度坐标进行基于地理位置的信息查询。你可以在查询中添加一个GeoPoint的对象查询。你可以实现轻松查找出离当前用户最接近的信息或地点的功能。

### 创建地理位置对象
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"location":{            
            "__type": "GeoPoint",
            "latitude": 50.934755,
            "longitude": 24.52065
        }}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 查询地理位置信息
现在你有一系列的对象对应的地理坐标，如果能发现哪些对象离指定的点近就好了，这可以通过GeoPoint数据类型加上在查询中使用$nearSphere做到。获取离用户最近的10个地点应该看起来像下面这个样子:

```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'limit=10' \
    --data-urlencode 'where={
    "location": {
        "$nearSphere": {
            "__type": "GeoPoint",
            "latitude": 30.0,
            "longitude": -20.0
        }
      }
    }' \
    https://api.bmob.cn/1/classes/PlaceObject
```
这操作会按离纬度30.0，经度-20.0的距离排序返回一系列的结果，第一个就是最近的对象。(注意如果一个特定的order参数是给定了的话，它会覆盖按距离排序的结果)，例如，下面是两个上面的查询操作返回的结果：
```
{
    "results": [
    {
        "location": {
             "__type": "GeoPoint",
            "latitude": 40.0,
            "longitude": -30.0
        },
        "updatedAt": "2011-12-06 22:36:04",
        "createdAt": "2011-12-06 22:36:04",
        "objectId": "e1kXT22L"
        },
        {
        "location": {
             "__type": "GeoPoint",
            "latitude": 60.0,
            "longitude": -20.0
        },
        "updatedAt": "2011-12-06 22:36:26",
        "createdAt": "2011-12-06 22:36:26",
        "objectId": "51e3a2a8e4b015ead4d95dd9"
        }
    ]
}
```
为了限定搜索的最大距离范围，需要加入$maxDistanceInMiles(英里)和$maxDistanceInKilometers(公里d)或者$maxDistanceInRadians(弧度)参数来限定，如果不加，则默认是100KM的半径。比如要找的半径在10公里内的话:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={
        "location": {
            "$nearSphere": {
                "__type": "GeoPoint",
                "latitude": 30.0,
                "longitude": -20.0
            },
        "$maxDistanceInKilometers": 10.0
        }
    }' \
    https://api.bmob.cn/1/classes/PlaceObject
```
同样作查询寻找在一个特定的范围里面的对象也是可以的，为了找到在一个矩形区域里的对象，按下面的格式加入一个约束 {"$within": {"$box": [southwestGeoPoint, northeastGeoPoint]}}：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={
        "location": {
            "$within": {
                "$box": [
                    {
                        "__type": "GeoPoint",
                        "latitude": 37.71,
                        "longitude": -122.53
                    },
                    {
                        "__type": "GeoPoint",
                        "latitude": 30.82,
                        "longitude": -122.37
                    }
                ]
            }
        }
    }' \
    https://api.bmob.cn/1/classes/PizzaPlaceObject
```

**注意事项**

关于地理位置的有一些问题是值得留意的:

1. 每一个表`只能`一个地理位置列，每一个对象`只能`有一个索引指向一个GeoPoint对象
2. GeoPoint的点不能超过规定的范围。`纬度的范围`应该是在`-90.0到90.0`之间。`经度的范围`应该是在`-180.0到180.0`之间。如果您添加的经纬度超出了以上范围，将导致程序错误。
3. 删除文件不会删除文件关联的行记录中的文件列的值，需要自行通过更新行来删除关联。
4. 如果不加任何距离范围限制，则默认是100公里的半径范围。

## 统计相关的查询
Bmob的统计查询，提供以下关键字或其组合的查询操作：

| Key        | Operation |
| :----:  | :----:  |
| groupby     | 分组操作 |
| groupcount     | 返回每个分组的总记录 |
| sum     | 计算总和 |
| average     | 计算平均值 |
| max     | 计算最大值 |
| min     | 计算最小值 |
| having     | 分组中的过滤条件 |


为避免和用户创建的列名称冲突，Bmob约定以上统计关键字（sum, max, min)的查询结果值都用 '_(关键字)+首字母大写的列名' 的格式，如计算玩家得分列名称为score总和的操作，则返回的结果集会有一个列名为_sumScore。average返回的列为 '_avg+首字母大写的列名'，有groupcount的情形下则返回_count。

以上关键字除了groupcount是传Boolean值true或false，having传的是和where类似的json字符串，但having只应该用于过滤分组查询得到的结果集，即having只应该包含结果集中的列名如{"_sumScore":{"$gt":100}}，其他关键字必须是字符串而必须是表中包含的列名，多个列名用,分隔。

以上关键字可以自由组合并可以与前面查询语句中的where, order, limit, skip等组合使用。

比如，GameScore表是游戏玩家的信息和得分表，有playerName(玩家名称)、score(玩家得分)等你自己创建的列，还有Bmob的默认列objectId, createdAt, updatedAt,那么我们现在举例如何使用以上的查询关键字来作这个表的统计。 

### 计算总和
我们要计算GameScore表所有玩家的得分总和，sum后面只能拼接Number类型的列名，即要计算哪个列的值的总和，只对Number类型有效，多个Number列用,分隔，则查询如下：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'sum=score' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"_sumScore": 2398
	}   
]
                  
```

### 分组计算总和
比如我们以创建时间按天统计所有玩家的得分，并按时间降序, groupby后面只能拼接列名，如果该列是时间类型，则按天分组，其他类型，则按确定值分组:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'sum=score&groupby=createdAt&order=-createdAt' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"_sumScore": 2398,
		"createdAt": "2014-02-05"
	},
	{
		"_sumScore": 1208,
		"createdAt": "2014-01-01"
	},
]                 
```

### 多个分组并计算多个列的总和
比如我们以创建时间按天和按玩家名称分组统计所有玩家的得分1，得分2的总和，并按得分1的总和降序, groupby后面只能拼接列名，如果该列是时间类型，则按天分组，其他类型，则按确定值分组:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'sum=score1,score2&groupby=createdAt,playerName&order=-_sumscore1' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"_sumScore1": 399,
		"_sumScore2": 120,
		"playerName": "John"
		"createdAt": "2014-02-05"
	},
	{
		"_sumScore1": 299,
		"_sumScore2": 250,
		"playerName": "Bily"
		"createdAt": "2014-02-05"
	},
	{
		"_sumScore1": 99,
		"_sumScore2": 450,
		"playerName": "John"
		"createdAt": "2014-02-01"
	},
]                 
```

### 分组计算总和并只返回满足条件的部分值
比如我们以创建时间按天统计所有玩家的得分，并只返回某天的总得分大于2000的记录，并按时间降序，having是用于过滤部分结果，其中的_sumScore是 '_sum+首字母大写的列名' 的格式表示是计算这个列的总和的值:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'sum=score&having={"_sumScore":{"$gt": 2000}}&groupby=createdAt&order=-createdAt' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"_sumScore": 2398,
		"createdAt": "2014-02-05"
	},
]                 
```

### 分组计算总和并返回每个分组的记录数
比如我们以创建时间按天统计所有玩家的得分和每一天有多少条玩家的得分记录，并按时间降序:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'sum=score&groupby=createdAt&groupcount=true&order=-createdAt' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"_sumScore": 2398,
		"_count": 10,
		"createdAt": "2014-02-05"
	},
	{
		"_sumScore": 100,
		"_count": 2,
		"createdAt": "2014-01-01"
	},
]                 
```

### 获取不重复的列值
比如我们获取表中所有的唯一的score:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'groupby=score' \
    https://api.bmob.cn/1/classes/GameScore
```
返回内容如下：
```
[
	{
		"score": 78
	},
	{
		"score": 89
	}
]                 
```

### 其他关键字
average(计算平均值)， max(计算最大值)，min(计算最小值)和sum查询语句是类似的，只用把上面的例子中的sum替换为相应的average, max, min就可以了。

## 云端代码

相关云端代码的编写方式，请参考[云端代码开发文档](http://docs.bmob.cn/cloudcode/developdoc/index.html?menukey=develop_doc&key=develop_cloudcode)

### 运行云端代码

在REST API中可以调用云端代码。例如，想调用云端代码的方法hello:

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"name":1337.23,"playerName":"Sean Plott","cheatMode":false}' \
  https://api.bmob.cn/1/functions/hello
```

如果运行的云端代码不需要传入参数，请参考下面的例子。
**注意，"{}"是不能缺的**
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{}' \
  https://api.bmob.cn/1/functions/test
```


### 创建/云端代码

在REST API中可以创建/云端代码。当云端代码函数名不存在的时候，会自动添加；当云端代码函数名已存在，会自动覆盖原来的云端代码。

例如，我们云端代码：

```
function onRequest(request, response, modules) {
            response.end("111");
}                          
```

在restful api添加以上的云端代码，先把上面的云端代码转换为base64编码（可使用在线工具：[base64在线转换](http://www1.tc711.com/tool/BASE64.htm) )，然后运行下面的代码

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"code":"ZnVuY3Rpb24gb25SZXF1ZXN0KHJlcXVlc3QsIHJlc3BvbnNlLCBtb2R1bGVzKSB7CiAgICAgICAgICAgIHJlc3BvbnNlLmVuZCgiMTExIik7Cn0g"}' \
  https://api.bmob.cn/1/functions/test
```

上面的代码中，`test`是该云端代码的函数名，code的值是云端代码经base64编码后的结果


### 删除代码


例如，删除函数名为"test"的云端代码，运行

```
curl -X DELETE \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  https://api.bmob.cn/1/functions/test
```

上面的代码中，`test`是该云端代码的函数名



## 消息推送

### 消息推送简介
推送通知是让用户及时被通知、和你的应用保持联系的一种非常棒的方式，你可以快速而有效地通知到所有的用户，下面这个教程将会教你如何使用Bmob来推送消息。

### 安装消息推送服务
每一个Bmob的App被安装在用户的设备上后，如果要使用消息推送功能，Bmob SDK会自动生成一个Installation对象。Installation对象包含了推送所需要的所有信息。举例：一个棒球的App，你可以让用户订阅感兴趣的棒球队，然后及时将这个球队的消息推送给用户 。
你可以使用 REST API，通过请求URL **/1/installations** 对Installation对象进行一系列操作，就像你存储和获取其他的普通对象，如GameScore对象一样。
注意Installation对象一般只在Client SDK中进行修改，但REST API对于大量导入或导出订阅数据的Installation对象是极其有用的。

Installation对象有几个系统默认的特殊字段来帮助你进行定位到设备等管理：
- **badge** : iOS应用中右上角的图标标识，这不会在服务端进行自增，需要开发者自己实现自增统计
- **channels** : 当前这个设备订阅的渠道名称数组
- **timeZone** : 设备所在位置的时区， 如Asia/Shanghai，这个会在每个Installation对象更新时同步（只读）
- **deviceType** : 设备的类型, 值为："ios" 或 "android" (只读)
- **installationId** : Bmob使用的设备唯一号，Android设备是必须的，iOS可选 (只读)
- **deviceToken** : iOS设备由Apple APNS生成的唯一性token标识 (只读)
- **notificationUri** : Windows Phone设备由Windows Push Center生成的唯一性token标识

### 保存 installation

#### 保存iOS设备的deviceToken
iOS设备通常使用deviceToken来对一台设备进行惟一标识。
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "deviceType": "ios",
        "deviceToken": "abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789",
        "channels": [
          "Giants"
        ]
      }' \
  https://api.bmob.cn/1/installations
```
#### 保存Android设备的 installationId

对于Android设备，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")SDK会自动生成uuid作为installationId保存到 Bmob。 你可以使用以下REST API保存Android设备的installation ID。
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "deviceType": "android",
        "installationId": "12345678-4312-1234-1234-1234567890ab",
        "channels": [
          "Giants",
          "Mets"
        ]
      }' \
  https://api.bmob.cn/1/installations
```
你还可以更新Installation对象的更多属性，用来查询installationId，定制更通用的推送
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "scores": true,
        "gameResults": true,
        "injuryReports": true
      }' \
  https://api.bmob.cn/1/installations/mrmBZvsErB
```

#### 保存Windows Phone设备的 notificationUri

对于Windows Phone设备，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")SDK会自动从Windows Push Center获取到notificationUri并保存到 Bmob。 你可以使用以下REST API保存Windows Phone设备的notificationUri。
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "deviceType": "windows phone",
        "notificationUri": "https://hk1.notify.live.net/unthrottledthirdparty/01.00/AQHgHh6EuwNtTa1TbwvaAjnmAgAAAAADEAAAAAQUZm52OkZDM0U5RDkxQzIzREJBNDYFBkFTRUEwMQ",
        "channels": [
          "Giants",
          "Mets"
        ]
      }' \
  https://api.bmob.cn/1/installations
```

### 订阅频道和退订

#### 订阅频道

覆盖已订阅的频道：

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "channels": [
          "Giants",
          "Mets"
        ]
      }' \
  https://api.bmob.cn/1/installations/mrmBZvsErB
```

订阅一个新频道，不覆盖已订阅的频道：

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"channels":{"__op":"AddUnique","objects":["Giants"]}}' \
  https://api.bmob.cn/1/installations/mrmBZvsErB
```

#### 退订频道

```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"channels":{"__op":"Remove","objects":["Giants"，"Mets"]}}' \
  https://api.bmob.cn/1/installations/mrmBZvsErB
```

### 广播推送消息

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "data": {
          "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```

### 组播推送消息

发送给订阅了Giants频道的用户

```
 curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where":{
          "channels":["Giants"]
        },
        "data": {
          "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```
 或者更简便的方式
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "channels":["Giants"],
        "data": {
          "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```

### 多播推送消息

#### 推送给不活跃的用户

```
  curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where":{
            "updatedAt":{
                "$lt":{"__type":"Date","iso":"2012-01-29 11:33:53"}
              }
        },
        "data": {
            "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```

#### 根据查询条件做推送

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "scores": true
        },
        "data": {
          "alert": "Willie Hayes injured by own pop fly."
        }
      }' \
  https://api.bmob.cn/1/push
```
请注意，where 条件查询的都是 installations 表。这里是假设 installations 表存储了 scores 的布尔属性，你可以像查询普通对象一样构造where查询

#### 根据平台做推送

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "android"
        },
        "data": {
          "alert": "Your suitcase has been filled with tiny robots!"
        }
      }' \
  https://api.bmob.cn/1/push
```
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "ios"
        },
        "data": {
          "alert": "Your suitcase has been filled with tiny apples!"
        }
      }' \
  https://api.bmob.cn/1/push
```

Windows Phone平台的推送分三种格式：

a.推送Raw通知(**默认**)：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "windows phone"
        },
        "data": {
           "alert": "Your suitcase has been filled with tiny windows phone!",
           "wpAlert": "TextBoxValue2"
        }
      }' \
  https://api.bmob.cn/1/push
```
b.推送Toast通知：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "windows phone"
        },
        "data": {
          "alert": "Your suitcase has been filled with tiny apples!",
          "wpAlert": "TextBoxSubTitle",
          "wp": 2
        }
      }' \
  https://api.bmob.cn/1/push
```

c.推送磁贴通知：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "windows phone"
        },
        "data": {
          "alert": "Your suitcase has been filled with tiny apples!",
          "backgroundImage": "backgroundImage",
          "count":"1",
          "backBackgroundImage": "backBackgroundImage",
          "backTitle":"backTitle",
          "backContent":"backContent",
          "wp": 1
        }
      }' \
  https://api.bmob.cn/1/push
```
#### 根据地理信息位置做推送

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "user": {
            "$inQuery": {
              "location": {
                "$nearSphere": {
                  "__type": "GeoPoint",
                  "latitude": 30.0,
                  "longitude": -20.0
                },
                "$maxDistanceInMiles": 1.0
              }
            }
          }
        },
        "data": {
          "alert": "Free hotdogs at the bmob concession stand!"
        }
      }' \
  https://api.bmob.cn/1/push
```

上面的例子假设 installation 有个 user 属性指向_User 表的记录，并且用户有个 location 属性是 GeoPoint 类型，我们就可以根据地理信息位置做推送。

### 点播推送消息

发送给Android单个客户端

```
  curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where":{
            "installationId":"57234d4c-752f-4e78-81ad-a6d14048020d"
        },
        "data": {
          "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```

发送给iOS单个客户端

```
  curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where":{
            "deviceToken":"abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789"
         },
        "data": {
          "aps":{"alert":"cashi","badge":1,"sound":"default"}
        }
      }' \
  https://api.bmob.cn/1/push
```

发送给Windows Phone单个客户端Toast通知

```
  curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "where":{
            "notificationUri":"https://hk1.notify.live.net/unthrottledthirdparty/01.00/AQHgHh6EuwNtTa1TbwvaAjnmAgAAAAADEAAAAAQUZm52OkZDM0U5RDkxQzIzREJBNDYFBkFTRUEwMQ"
         },
        "data": {
          "alert": "Hello From bmob",
          "wpAlert": "bmob",
          "wp": 2
        }
      }' \
  https://api.bmob.cn/1/push
```

### 发送选项设置
推送通知不仅仅可以用来发送消息，iOS中，推送内容还可以包含要播放的声音和要显示的badge数字值,还可以定制任何你想发送的内容，对于Android，你甚至可以指定Intent应用于接收消息，对于时效有要求的消息你还可以设置一个消息过期时间。

#### 定制你的通知
如果你想发送更多的消息，你可以在data的字典对象中设置其他字段，下面这些保留的字段有其特殊的意义：
- **alert** : 通知的消息内容
- **badge** : (仅iOS)一个数字值将会高亮显示在应用图标的右上角。
- **sound** : (仅iOS)应用绑定包中的声音
- **content-available** : (仅iOS)如果你的应用是新闻类的，或者你的应用正在使用iOS7的Remote Notification Background Mode，设定这个值为1将会触发离线下载功能。
- **action** : (仅Android)接收到推送消息时应用Intent，如果没有指定title或alert，Intent将被应用，但是没有通知呈现给用户。
- **title** : (仅Android)这个值将显示在系统通知栏
- **wp** : (仅Windows Phone)通知类型 0-Raw(默认) 1-Toast 2-磁贴
- **wpAlert** : (仅Windows Phone)这个值将在Raw 和 Toast两种通知中有用，相对alert，作为子标题
- **backgroundImage** : (仅Windows Phone)通知类型wp=2，磁贴类通知中有用
- **count** : (仅Windows Phone)通知类型wp=2，磁贴类通知中有用
- **backBackgroundImage** : (仅Windows Phone)通知类型wp=2，磁贴类通知中有用
- **backTitle** : (仅Windows Phone)通知类型wp=2，磁贴类通知中有用
- **backContent** : (仅Windows Phone)通知类型wp=2，磁贴类通知中有用

例如，推送一条通知，将当前的badge值设置为一个数字值,并且播放iOS设备上定制的音乐，对于Android用户，将在系统通知栏中显示特殊的标题，其请求如下：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "channels": [
          "Mets"
        ],
        "data": {
          "alert": "The Mets scored! The game is now tied 1-1.",
          "badge": 10,
          "sound": "cheering.caf",
          "title": "Mets Score!"
        }
      }' \
  https://api.bmob.cn/1/push
```

当然，你也可以在data字典对象中定制自己的字段来推送消息，但推送到消息不显示在 Andoid 系统的通知栏中，而是执行应用程序预定义的逻辑，Android或iOS的接收消息文档中对此有说明，iOS只有当用户从通知中打开应用，才能访问data的数据，Android将提供这个数据在action Intent应用之后将提供这个数据,

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "channels": [
          "Indians"
        ],
        "data": {
          "action": "com.example.UPDATE_STATUS",
          "alert": "Ricky Vaughn was injured in last night's game!",
          "name": "Vaughn",
          "newsItem": "Man bites dog"
        }
      }' \
  https://api.bmob.cn/1/push
```

#### 设置消息过期时间 

过期时间，可以是绝对时间：

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "expiration_time": "2013-12-04 00:51:13",
        "data": {
          "alert": "Season tickets on sale until December  4, 2013"
        }
      }' \
  https://api.bmob.cn/1/push
```

也可以是相对时间（根据push_time做定期推送，从push_time时间开始算起，直到expiration_interval时间后过期），下面的请求表示消息将在2012-01-28 00:51:13这个时间点开始推送，直到7天后过期:

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{
        "push_time": "2012-01-28 00:51:13",
        "expiration_interval": 518400,
        "data": {
          "alert": "Season tickets on sale until December  4, 2013"
        }
      }' \
  https://api.bmob.cn/1/push
```

## 支付服务

### 网页端调起支付宝支付接口

PHP等Web开发语言可使用以下接口调起支付宝的支付接口，PHP开发者可参考：[Bmob PHP SDK](https://github.com/bmob/bmob-php-sdk "Bmob PHP SDK")  仓库的pay目录下的payapi.php文件编写该接口。

```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"  \
  -H "X-Bmob-REST-API-Key: Your REST API Key"   \
  -H "Content-Type: application/json" \
  -d '{
        "order_price": 0.01,
        "product_name": "充值",
        "body":"给应用充值0.01元"
      }' \
  https://api.bmob.cn/1/webpay
```

成功返回以下JSON, 失败时返回请看 [支付功能相关错误码](/errorcode/index.html?menukey=otherdoc&key=errorcode#index_支付功能相关错误码 "支付功能相关错误码")
```
{
  "html": "html内容",
  "out_trade_no": "9f392618f449a71c6fcfdee38d2b29e4",
}
```
将以上返回的html内容输出到你的Web页面上，将会自动跳转至支付宝收银台。

关于移动端如何使用支付功能，请查看 [Android支付SDK](/androidpay/index.html?menukey=fast_start&key=start_android_pay "Android支付SDK") 或 [iOS支付SDK](/iospay/index.html?menukey=fast_start&key=start_ios_pay "iOS支付SDK")。

### 查询订单

```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  https://api.bmob.cn/1/pay/Bmob系统生成的订单号
```

成功返回以下JSON, 失败时返回请看 [支付功能相关错误码](/errorcode/index.html?menukey=otherdoc&key=errorcode#index_支付功能相关错误码 "支付功能相关错误码")

```
{
  "name": "商品",
  "body": "商品详情",
  "create_time": "2015-03-24 11:14:58",
  "out_trade_no": "9f392618f449a71c6fcfdee38d2b29e4",
  "transaction_id": "2015061100001000330057820379"
  "pay_type": "WECHATPAY",
  "total_fee": 0.01,
  "trade_state": "NOTPAY",
}
```

返回的信息简单描述如下：
name-订单或商品名称 
body-商品详情  
create_time - 调起支付的时间  
out_trade_no-Bmob系统的订单号  
transaction_id-微信或支付宝的系统订单号
pay_type-WECHATPAY（微信支付）或ALIPAY（支付宝支付） 
total_fee - 订单总金额  
trade_state-NOTPAY（未支付）或 SUCCESS（支付成功）

### Bmob支付回调
Bmob 加入了支付后页面跳转同步通知页面的URL和异步的通知URL功能，可供开发者在应用的设置页面自行增加。

填写页面跳转同步通知页面的URL(return_url)和异步的通知URL(notify_url)的页面在 应用列表->应用信息->支付设置 。

#### Bmob异步通知回调（支持微信和支付宝）

1. 必须保证服务器异步通知页面（notify_url）上无任何字符，如空格、HTML标签、开发系统自带抛出的异常提示信息等；

2. Bmob支付是用POST方式发送异步通知信息，因此该页面中获取参数的方式，如：
request.Form(“out_trade_no”)、$_POST[‘out_trade_no’]；

3. 支付宝主动发起通知，该方式才会被启用；

4.  只有在Bmob的交易管理中存在该笔交易，且发生了交易状态的改变，Bmob才会通过该方式发起服务器通知；

5. 服务器间的交互，不像页面跳转同步通知可以在页面上显示出来，这种交互方式是不可见的；

6. 第一次交易状态改变（即时到账中此时交易状态是交易完成）时，不仅页面跳转同步通知页面会启用，而且服务器异步通知页面也会收到Bmob发来的处理结果通知；

7. 程序执行完后必须打印输出“success”（不包含引号）。如果商户反馈给Bmob的字符不是success这7个字符，Bmob服务器会不断重发通知，直到超过24小时。

8. 一般情况下，24小时以内完成8次通知（通知的间隔频率一般是：2m,10m,10m,1h,2h,6h,15h）；

9. 程序执行完成后，该页面不能执行页面跳转。如果执行页面跳转，Bmob会收不到success字符，会被Bmob服务器判定为该页面程序运行出现异常，而重发处理结果通知；

10. 异步通URL的调试与运行必须在服务器上，即互联网上能访问；

11. 当用户的服务端收到Bmob服务器异步通知的$_POST[‘out_trade_no’]时，应该调起一次查询订单的接口获得订单的状态是1，才能准确的判断该笔订单是成功;

12. 支付成功结果以Bmob后台订单列表或查询订单接口查询到的订单状态为准。


#### Bmob页面跳转同步通知页面（只支持支付宝）

1. 用户在支付成功后会看到一个支付宝提示的页面，该页面会停留几秒，然后会自动跳转回商户指定的同步通知页面（参数return_url）。

2. 该页面中获得参数的方式，需要使用GET方式获取，如request.QueryString(“out_trade_no”)、$_GET[‘out_trade_no’]。

3. 该方式仅仅在用户支付完成以后进行自动跳转，因此只会进行一次。

4. 该方式不是支付宝主动去调用商户页面，而是支付宝的程序利用页面自动跳转的函数，使用户的当前页面自动跳转。

5. 该方式可在本机而不是只能在服务器上进行调试。

6. 返回URL只有一分钟的有效期，超过一分钟该链接地址会失效，验证则会失败。

7. 设置页面跳转同步通知页面（return_url）的路径时，不要在页面文件的后面再加上自定义参数。例如：
```
错误的写法：http://www.bmob.cn/pay/return_url.php?xx=11
正确的写法：http://www.bmob.cn/pay/return_url.php
```

## 短信服务

在一些应用场景下，你可能希望用户验证手机号码后才能进行一些操作，例如充值等。这些操作跟用户系统没有关系，可以通过我们提供的的短信验证API来实现。

每个 [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 帐户有 100 个免费额度的短信数量，超过需要购买短信条数才能继续使用。

为了保障短信的下发速度和送达率，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 为所有用户申请了一致的独享通道，默认使用 **【云验证】** 作为签名，且不可更改。

### 请求发送短信内容

这个接口可让开发者使用  [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 的短信功能灵活发送短信内容到用户的手机上。

此接口必须要开发者在后台提交身份证信息，在([Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")》账户管理》身份验证) 中填写，并通过了我们的审核后才可以使用。

请不要发送任何敏感内容，一经发现，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 有权立刻停止您的该接口使用权。


输入手机号码、短信内容，就可以给用户的手机号码发送自定义的短信内容了，以下的content只要在不违反相关规定的前提下，你可以随意输入：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "content":"您的验证码是：222222, 有效期是10分钟。"}' \
  https://api.bmob.cn/1/requestSms
```


你还可以选择定时发送，比如未来的某一时刻给某个手机发送一条短信，sendTime的格式必须是YYYY-mm-dd HH:ii:ss， 如: 2015-05-26 12:13:14，请求如下：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "content":"您的验证码是：222222, 有效期是10分钟。感谢您使用Bmob。", "sendTime":"2016-05-26 12:13:14"}' \
  https://api.bmob.cn/1/requestSms
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该条短信是否发送成功：
```
{
	"smsId": 1232222
}
```

### 请求短信验证码
如果没有在管理后台创建好模板，可使用默认的模板，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 默认的模板是: **您的验证码是%smscode%，有效期为%ttl%分钟。您正在使用%appname%的验证码**

使用默认的模板请求短信验证码：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx"}' \
  https://api.bmob.cn/1/requestSmsCode
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：
```
{
	"smsId": 1232222
}
```

如果你已经在 [Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台") 后台设置了自己的模板，并已经是审核通过了，则可以使用自己的模板给用户的手机号码发送短信验证码了：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx", "template":"注册模板"}' \
  https://api.bmob.cn/1/requestSmsCode
```

成功返回，短信验证码ID，可用于后面使用查询短信状态接口来查询该短信验证码是否发送成功和是否验证过：
```
{
	"smsId": 1232222
}
```

### 验证短信验证码

通过以下接口，你可以验证用户输入的验证码是否是有效的：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  -H "Content-Type: application/json" \
  -d '{"mobilePhoneNumber": "186xxxxxxxx"}' \
  https://api.bmob.cn/1/verifySmsCode/6位收到的短信验证码
```

成功返回以下JSON，表明验证码验证通过：
```
{
	"msg":"ok"
}
```

### 查询短信状态

通过以下接口，你可以查询某条短信是否发送成功，如果是使用了Bmob的模板的话还能查询到是否被验证过，其实:smsId是请求短信验证码API返回的smsId值：
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  https://api.bmob.cn/1/querySms/:smsId
```
成功返回以下JSON：
```
{
  "sms_state": "SENDING", 
  "verify_state": false
}
```
其中sms_state是发送状态，有值: SENDING-发送中，FAIL-发送失败 SUCCESS-发送成功
其中verify_state是验证码是否验证状态， 有值: true-已验证 false-未验证


**注意事项**

关于短信条数的计算规则如下:

1. 实际计算的短信字数 = 模板的内容或自定义短信的内容字数 + 5。加上5是因为默认的签名【云验证】占了5个字。
2. 实际计算的短信字数在70个字以下算1条。
3. 实际计算的短信字数超过70字的以67字为一条来计算的。也就是135个字数是计算为3条的。
4. 计算得到的短信条数在本条短信发送成功后将会从你的账户剩余的短信条数中扣除。

**短信发送限制规则是1/分钟，5/小时，10/天。即对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条**

## app服务

通过app restful api，你可以查看，创建或编辑你的app，在用户管理后台也实现了这样的功能。通过验证你的bmob email账号和密码，你可以获取所有的app信息，创建一个新的app或者修改旧的app的信息。

app restful api的验证流程和其它restful api有点不一样。在验证的时候，不是验证你的app key，而是验证账号的登录信息。头部 **X-Bmob-Email** 代表登录用的email，头部 **X-Bmob-Password** 代表登录用的密码。

### 获取app信息

获取所有的app信息

```
curl -X GET \
    -H "X-Bmob-Email: Your Account Email" \
    -H "X-Bmob-Password: Your Account Password" \
    https://api.bmob.cn/1/apps
```

获取某个特定的app信息

```
curl -X GET \
    -H "X-Bmob-Email: Your Account Email" \
    -H "X-Bmob-Password: Your Account Password" \
    https://api.bmob.cn/1/apps/Your Application ID
```

### 创建新的app

通过使用post 方法，可以在你的账号上创建一个app，创建app时支持如下的参数：

| 参数名 | 参数用途 | 取值范围 |是否必须要填|
| :----:  | :----:  | :----:  | :----:  |
|  appName | app的名称 | 少于30个字符     | 是|
|  status | app是否可用| 0：表示禁用，1：表示可用     | 否|
|  notAllowedCreateTable | 是否允许通过api创建表| 0：表示允许创建表，1：表示不允许创建表     | 否|


下面是一个创建app的例子

```
curl -X POST \
    -H "X-Bmob-Email: Your Account Email" \
    -H "X-Bmob-Password: Your Account Password" \
    -H "Content-Type: application/json" \
    -d '{"appName":"myapp","status":1,"notAllowedCreateTable":0}' \
    https://api.bmob.cn/1/apps
```

### 修改app信息

通过使用PUT 方法，可以修改app的信息，修改app信息时支持如下的参数：

| 参数名 | 参数用途 | 取值范围 |是否必须要填|
| :----:  | :----:  | :----:  | :----:  |
|  appName | app的名称 | 少于30个字符     | 否|
|  status | app是否可用| 0：表示禁用，1：表示可用     | 否|
|  notAllowedCreateTable | 是否允许通过api创建表| 0：表示允许创建表，1：表示不允许创建表     | 否|


下面是修改app信息的例子

```
curl -X PUT \
    -H "X-Bmob-Email: Your Account Email" \
    -H "X-Bmob-Password: Your Account Password" \
    -H "Content-Type: application/json" \
    -d '{"appName":"myapp","status":1,"notAllowedCreateTable":0}' \
    https://api.bmob.cn/1/apps/f6fe8d5ab8a7909a3c6f6a7a0adb9550
```


## 数据表

通过数据表的restful api，你可以查看，创建或编辑你的表结构，在用户管理后台的数据浏览页面也实现了这样的功能。

注意，调用数据表相关的api，必须指定Master Key。

### 获取app表的信息

```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-Master-Key: Your Master Key" \
    https://api.bmob.cn/1/schemas
```

获取某个特定表的信息

```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-Master-Key: Your Master Key" \
    https://api.bmob.cn/1/schemas/City
```

### 创建字段支持的数据类型

String
Number
Bool
Date
File
Geo
Array
Object
Pointer
Relation



### 创建一个表



创建表“City”，并添加字段“name”
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-Master-Key: Your Master Key" \
	-H "Content-Type: application/json" \
	-d ' 
		{
		  "className": "City",
		  "fields": {
			"name": {
			  "type": "String"
			}
		  }
		}' \
  https://api.bmob.cn/1/schemas/City

```

如果创建表的字段是Pointer或Relation类型，需要用targetClass指定关联的表，例如：
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-Master-Key: Your Master Key" \
	-H "Content-Type: application/json" \
	-d ' 
		{
		  "className": "City",
		  "fields": {
			"visiter": {
			  "type": "Pointer",
			  "targetClass":"_User"
			}
		  }
		}' \
  https://api.bmob.cn/1/schemas/City

```



### 修改表的结构


在表“City”中添加字段“name”
```
curl -X PUT \
	-H "X-Bmob-Application-Id: Your Application ID" \
	-H "X-Bmob-Master-Key: Your Master Key" \
	-H "Content-Type: application/json" \
	-d ' 
    {
      "className": "City",
      "fields": {
        "name": {
          "type": "String"
        }
      }
    }' \
  https://api.bmob.cn/1/schemas/City

```

在表“City”中删除字段“name”
```
curl -X PUT \
	-H "X-Bmob-Application-Id: Your Application ID" \
	-H "X-Bmob-Master-Key: Your Master Key" \
	-H "Content-Type: application/json" \
	-d ' 
    {
      "className": "City",
      "fields": {
        "name": {
          "type": "String",
          "__op": "Delete"  
        }
      }
    }' \
  https://api.bmob.cn/1/schemas/City

```

### 删除表
```
curl -X DELETE \
	-H "X-Bmob-Application-Id: Your Application ID" \
	-H "X-Bmob-Master-Key: Your Master Key" \
  https://api.bmob.cn/1/schemas/City

```

## 获取服务器的时间

有时，app需要获取服务器的时间，可通过下面的api

```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID"          \
  -H "X-Bmob-REST-API-Key: Your REST API Key"        \
  https://api.bmob.cn/1/timestamp
```

返回参数如下：

```	
{"timestamp":1437531770,"datetime":"2015-07-22 10:22:50"}
```
 
timestamp为时间戳，datetime为格式化的日期。
	

## 错误代码详细说明
[RestAPI错误码列表](/errorcode/index.html?menukey=otherdoc&key=errorcode "RestAPI错误码列表")



