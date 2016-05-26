# REST API 开发指南

## 简介
只要您的设备，您使用的语言能够发送HTTP请求，那么就可以用来和[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")进行数据交互，您可以使用REST API做很多事情,比如:

- 一个移动网站可以通过C、Java、Python、PHP、C#等甚至任何语言来获取[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上的数据。
- 一个网站可以展示来自[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")的数据。
- 您可以上传大量的数据，随后可以被一个移动App读取。
- 您可以下载最近的数据来进行您自定义的分析统计。
- 使用任何语言写的程序都可以操作[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上的数据。
- 如果您不再需要使用[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")，您可以导出您所有的数据。

## 请求格式
对于POST和PUT请求，请求的主体必须是JSON格式,而且HTTP请求头的 `Content-Type` 需要设置为 `application/json` 。

用户验证是通过HTTP请求头来进行的, `X-Bmob-Application-Id` 头表明您正在访问的是哪个App程序, 而 `X-Bmob-REST-API-Key` 头是用来授权的。在下面的例子中，您必须使用正确的key替换Your Application ID和Your REST API Key才能正常地发出Curl请求。

## 响应格式
对于所有REST API请求的响应内容体都是一个JSON对象.

一个请求是否成功是由HTTP状态码表明的， 一个2XX的状态码表示成功，而一个4XX表示请求失败。当一个请求失败时响应的主体仍然是一个JSON对象，但是总是会包含code和error这两个字段，您可以用它们来进行调试。举个例子，如果保存一个对象的时候，尝试用不允许的Key比如包含下划线的_name的话，就会得到如下请求失败的响应信息:
```
{
	"code": 105,
	"error": "invalid field name: bl!ng"
}
```

## 对象
### 对象格式
通过REST API保存数据需要将对象的数据通过JSON来编码，这个数据是无模式化的（Schema Less）,这意味着您不需要提前标注每个对象上有那些Key，您只需要随意设置key-value对就可以，Rest API后端会存储它的。

举个例子，假设您正在记录一局游戏的最高分，一个简单的对象可能包含:
```
{
	"score": 1337,
	"playerName": "Sean Plott",
	"cheatMode": false
}
```
Key必须是字母和数字组成的字符串,Value可以是任何可以JSON编码的东西.

每个对象都有一个类名，您可以通过类名来区分不同的数据，例如，我们可以把游戏得分对象称之为GameScore.我们推荐您使用  `NameYourClassesLikeThis` 和 `nameYourKeysLikeThis` 这样的格式为您的类名和Key命名，这可以使您的代码看起来很漂亮.

当您从[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")中获取对象时，一些字段会被自动加上: createdAt, updatedAt 和 objectId， 这些字段的名字是保留的，您不能自行设置它们，我们上面设置的对象在获取时应该是下面的样子.
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
针对于一个特定的对象的操作可以通过组织一个URL来做，例如，对GameScore中的一个objectId为e1kXT22L的对象的操作应使用如下URL:
```
https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 数据类型
到现在为止我们只使用了可以被标准JSON编码的值，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")移动客户端SDK库同样支持日期,地理位置数据和指针数据、关系型数据。在REST API中，这些值都被编码了，同时有一个"__type"字段来标识出它们所属的类型，所以如果您采用正确的编码的话就可以读或者写这些字段了。

Date类型包含了一个"iso"字段存储了一个UTC时间戳,以ISO 8601格式和毫秒级的精度来存储时间: YYYY-MM-DDTHH:MM:SS.MMMZ，或者 YYYY-MM-DDTHH:MM:SS
```
{
    "__type": "Date",
    "iso": "2011-08-21 18:02:52"
}
```
Date 和内置的 `createdAt` 字段和 `updatedAt` 字段相结合的时候特别有用，举个例子：为了找到在一个特殊时间创建的对象，只需要将Date编码在一个查询的where条件中:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"createdAt":{"$gte":{"__type":"Date","iso":"2011-08-21 18:02:52"}}}' \
    https://api.bmob.cn/1/classes/GameScore
```
Pointer 类型是当前对象要指向另一个对象时使用，它包含了 className 和 objectId 作为一个指针正确指向的必填值.
```
{
  "__type": "Pointer",
  "className": "Game",
  "objectId": "DdUOIIIW"
}
```
指向用户对象的 `Pointer` 的 `className` 为_User, 前面加一个下划线表示开发者不能定义的类名, 而且所指的类是系统内置的。

`Relation` 类型被用在多对多的类型上, 移动端的库将使用 `BmobRelation` 作为值, 它有一个 className 字段表示目标对象的类名：
```
{
  "__type": "Relation",
  "className": "GameScore"
}
```
当使用查询时， `Relation` 对象的行为很像是 `Pointer` 的数组, 任何操作针对于 `Pointer` 的数组的 (除了 include) 都可以对 `Relation` 起作用.

当更多的数据类型被加入的时候, 它们都会采用 hashmap `加上一个 type` 字段的形式, 所以您不应该使用type作为您自己的JSON对象的Key。

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
当创建成功时，响应的HTTP状态码的返回值是201 Created，而响应的HTTP头部中的Location的值是表示刚创建的该对象的URL:
```
Status: 201 Created
Location: https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
响应的主体是一个JSON对象,包含新的对象的objectId和createdAt时间戳：
```
{
    "createdAt": "2011-08-20 02:06:57",
    "objectId": "e1kXT22L"
}
```

## 查询数据
数据的查询可能是每个应用都会频繁使用的，它提供了非常丰富的方法来实现不同条件的查询，同时它的使用也是非常的简单和方便的。

### 查询所有数据
为了一次获取多个对象，您可以通过发送一个GET请求到类的URL上，不需要任何URL参数，下面就是简单地获取所有在GameScore类之中的对象:
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
怎么样，是不是很简单，而且查询的结果不需要任何处理，您直接使用即可。

### 查询单条数据
当您创建了一个对象时，您可以通过发送一个HTTP GET请求到创建对象成功时返回的HTTP请求头中的Location的URL获取它的内容。例如，为了得到我们上面创建的对象:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```
返回的主体是一个JSON对象包含所有用户提供的field，并且加上系统保留的createdAt,updatedAt和objectId三个Key的值：
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
当获取的对象有指向其子对象的Pointer类型指针Key时，您可以加入inclue选项来获取指针指向的子对象。按上面的实例，如果GameScore对象有一个game的Key为Pointer类型，并指向了Game游戏对象，那么可以通过GameScore的game这个Key来获取指向的一个Game对象:
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
通过where参数的形式可以对查询对象做出约束，where参数的值应该是JSON编码过的，就是说，如果您查看真正被发出的URL请求，它应该是先被JSON编码过，然后又被URL编码过。最简单的使用where参数的方式就是包含应有的key的值。举例说,如果我们想要得到Sean Plott的分数，而且他不在作弊情形下，我们应该这样构造查询:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"playerName":"Sean Plott","cheatMode":false}' \
    https://api.bmob.cn/1/classes/GameScore
```
where的参数的值除了上面的准确匹配外，还支持比较运算符的方式，除了给定一个确定的值的方式，提供一个hash中包含有key用于比较也是可以的，where参数支持下面一些选项:

| Key        | Operation |
| --------   | :----:  |
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

### 分页查询
您可以用limit和skip来做分页，limit的默认值是10，但是任何1到1000的值都是可选的，就是说，为了获取在400到600之间的对象:
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
您可以用order参数来指定一个字段来排序，前面加一个负号的前缀表示降序，这样返回的对象会以score升序排列:
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
您可以用多个字段进行排序，只要用一个逗号隔开的列表就可以，为了获取GameScore以score的升序和name的降序进行排序:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'order=score,-name' \
    https://api.bmob.cn/1/classes/GameScore
```

### 统计对象数量
如果您在使用limit，或者如果返回的结果很多，您可能想要知道到底有多少对象应该返回，而不用把它们全部获得以后再计数，此时您可以使用count参数。举个例子，如果您仅仅是关心一个特定的玩家玩过多少游戏：
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
| --------   | :----:  |
| $or     | 复合查询中的或查询 |
| $and     | 复合查询中的与查询 |

如果您想查询对象符合几种查询之一，您可以使用$or或$and操作符，带一个JSON数组作为它的值。例如，如果您想找到player赢了很多或者赢了很少，您可以用如下的方式:
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'where={"$or":[{"wins":{"$gt":150}},{"wins":{"$lt":5}}]}' \
    https://api.bmob.cn/1/classes/Player
```

任何在查询上的其他的约束都会对返回的对象生效，所以您可以用$or对其他的查询添加约束。

注意我们不会在 `组合查询的子查询` 中支持非过滤型的约束(例如:limit skip sort include)，但最外层的查询中是支持非过滤型的约束的。

### 查询指定列
您可以限定返回的字段，通过传入keys参数，值为用一个逗号分隔的字段名称列表，为了获取对象只包含score和playerName字段(还有特殊的内置字段比如objectId,createdAt和updatedAt)，请求如下：
```
curl -X GET \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'keys=score,playerName' \
    https://api.bmob.cn/1/classes/GameScore
```

## 修改数据
为了更改一个对象上已经有的数据，您可以发送一个PUT请求到对象相应的URL上，只有你指定的Key的值才会变更为新值，任何您未指定的Key的值都不会更改，所以您可以只更新对象数据的一个子集。例如，我们来更改我们对象的一个score的字段:
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
您可以在一个对象中删除一个字段，通过Delete操作:
```
curl -X PUT \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"opponents":{"__op":"Delete"}}' \
    https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

## 数组
为了存储数组型数据，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供3种操作来原子性地更改一个数组字段:

`Add` 在一个数组字段的后面添加一些指定的对象(包装在一个数组内)

`AddUnique` 只会在原本数组字段中没有这些对象的情形下才会添加入数组，插入数组的位置不固定的

`Remove` 从一个数组字段的值内移除指定的数组中的所有对象

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
每一种方法都会有一个objects，即包含了这些方法将被添加或删除的对象列表，举个例子，技能skills是一个类似于集合的数组类型，那么我们可以在skills中加入一些对象，只有skills原来的对象中不包含这些值的情况下才会被加入:
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

您同样可以使用"$all"操作符来找到类型为数组的Key的值中包含有2,3和4的对象:
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

## 数据关联

### 关联对象
对象可以与其他对象相联系。就像数据库中的主外键关系一样，数据表 A 的某一个字段是数据表 B 的外键，只有表 B 中存在的数据才可插入进表 A 中的字段。 

### 添加关联关系
为了更新 `Pointer` 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个指针, OK，我们可以像这样添加一行记录时并添加一个指针:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"game":{"__type":"Pointer","className":"Game","objectId":"DdUOIIIW"}}' \
  https://api.bmob.cn/1/classes/GameScore
```

为了更新 `Relation` 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个或多个关系, OK，我们可以像这样添加一行记录时并添加多个关系:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"opponents":{"__op":"AddRelation","objects":[{"__type":"Pointer","className":"Player","objectId":"z0lOxp1X"},{"__type":"Pointer","className":"Player","objectId":"MTzXDDDG"}]}}' \
  https://api.bmob.cn/1/classes/GameScore
```

### 修改关联对象
为了更新 `Pointer` 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个指针, OK，我们可以像这样添加一个指针:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"game":{"__type":"Pointer","className":"Game","objectId":"DdUOIIIW"}}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

为了更新 `Relation` 的Key类型，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")提供特殊的操作来原子化地添加和删除一个或多个关系, OK，我们可以像这样添加多个关系:
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"opponents":{"__op":"AddRelation","objects":[{"__type":"Pointer","className":"Player","objectId":"z0lOxp1X"},{"__type":"Pointer","className":"Player","objectId":"MTzXDDDG"}]}}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 查询关联对象
有几种方式来对关系 `Relation` 或 指针 `Pointer` 类型数据进行查询, 如果您将要获取对象中有个Key类型是关系 `Relation` 或 指针 `Pointer`，这都说明你要获取的对象是匹配到另一个特殊（关联或指向）的对象的, 您可以用一个 `where` 参数查询, 自己使用 `__type` 构造一个 `Pointer`, 就像你构造其他数据类型一样。举例说, 如果每一条评论(Comment对象)有一个Key叫post，类型是Pointer，并且指向了一个具体的帖子(Post对象，用objectId表示一个帖子)，那么您可以使用下面的请求获取一个帖子的所有评论:
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"__type":"Pointer","className":"Post","objectId":"1dafb9ed9b"}}' \
  https://api.bmob.cn/1/classes/Comment
```

如果您想要获取对象, 这些对象的一个字段指向的对象是符合另一个查询的, 您可以使用 $inQuery 操作符，注意默认的 limit 是 100 而且最大的 limit 是 1000，这个限制同样适用于内部的查询, 所以对于较大的数据集您可能需要细心地构建查询来获得期望的行为。举例说, 假设您有一个 帖子(Post)类和一个评论(Comment)类, 每个评论(Comment)都有一个指向它的帖子(Post)的关系Key名为post，并且类型为Pointer, 您可以找到所有有图片的帖子(Post)的评论(Comment):
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"$inQuery":{"where":{"image":{"$exists":true}},"className":"Post"}}}' \
  https://api.bmob.cn/1/classes/Comment
```

同理，使用下面的请求，您可以找到所有没有图片的帖子(Post)的评论(Comment):
```
curl -X GET \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -G \
  --data-urlencode 'where={"post":{"$notInQuery":{"where":{"image":{"$exists":true}},"className":"Post"}}}' \
  https://api.bmob.cn/1/classes/Comment
```

如果您想获取的对象，是其父对象的关系 `Relation` 类型的Key的所有成员的话, 您可以使用 $relatedTo 操作符, 假设您有一个帖子(Post)类和一个系统默认的用户(_User)类, 而每一个帖子(Post)都可以被不同的用户(_User)所喜欢。 如果帖子(Post)类下面有一个Key名为likes，且是 `Relation` 类型, 存储了喜欢这个帖子(Post)的用户(_User)。那么您可以找到喜欢过同一个指定的帖子(Post)的所有用户：
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

在某些情况之下，您可能需要在一个查询之中返回关联对象的多种类型，您可以通过传入字段名称到include参数中，多个字段名称用,间隔， 比如，我们想获得最近的10篇评论，而您想同时得到它们相关的post：
`include的Key必须是Pointer类型`
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
不是作为一个 `Pointer` 类型表示，post字段现在已经被展开为一个完整的帖子(Post)对象， __type 被设置为 `Object` 而 `className` 同样也被提供了。 举例说， 一个指向帖子(Post)的Pointer原本展示为：
```
{
  "__type": "Pointer",
  "className": "Post",
  "objectId": "51e3a359e4b015ead4d95ddc"
}
```
当一个查询使用include参数来包含进去来取代 `Pointer` 之后，可以看到 `Pointer` 被展开为：
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
您可以同样做多层的include, 这时要使用 "." 号. 如果您要include一条评论(Comment)对应的帖子(Post)的作者(author)：
`include的Key必须是Pointer类型`
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
如果您要构建一个查询, 这个查询要include多个 `Pointer` 类型的Key, 此时用逗号分隔Key名称列表即可。

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

为了减少因为网络通讯次数太多而带来的时间浪费, 您使用使用下面的批量(batch)操作，在一个请求中对多个普通对象(不支持系统内置的用户对象)进行添加(create)、更新(update)、删除(delete) 操作，上限为50个。

在一个批量(batch)请求中中每一个操作都有自己对应的方法、路径和主体, 这些参数可以代替您通常使用的HTTP方法. 这些操作会以发送过去的顺序来执行, 比如我们要创建一系列的 GameScore 的对象:
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
批量操作的响应会是一个列表, 列表的返回值个数同给定的requests请求个数是相等的。列表中每个返回项都有一个字段是 `"success"` 或者 `"error"`， `"success"` 的值是通常是和你进行其他REST操作成功时返回的值是一样的:
```
{
  "success": {
    "createdAt": "2012-06-15T16:59:11.276Z",
    "objectId": "51c3ba67e4b0f0e851c16221"
  }
}
```
`"error"` 的值是有返回码和错误信息字符串的一个对象:
```
{
  "error": {
    "code": 101,
    "error": "object not found for delete"
  }
}
```
在 batch 操作中更新(update)和删除(delete)同样是有效的:
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{
        "requests": [
          {
            "method": "PUT",
            "path": "/1/classes/GameScore/51e3a334e4b0b3eb44adbe1a",
            "body": {
              "score": 999999
            }
          },
          {
            "method": "DELETE",
            "path": "/1/classes/GameScore/51a8a4d9e4b0d034f6159a35"
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
文件上传有整个文件上传， 和分片上传两种。

### 整个文件上传
上传整个文件到bmob，发送一个POST请求去files URL，参数是用BASE64加密的带有后缀的文件名。该请求必须包含与该文件相关联的Content-Type头， 下面是简单例子：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: text/plain" \
  -d 'Hello, World!' \
  https://api.bmob.com/1/files/c3NzLmpwZw==
```
返回的主体是一个JSON对象，包含文件名， 分组，文件地址，  http://file.bmob.cn/ + url，就是完整的地址:
```
{
	filename: "hello.txt"
	group: "group1"
	url: "M00/00/01/wKgBP1N3Cg3IDgN1AAo7dN2HyQ0408.txt"
}
```
### 分片上传

第一步， 创建文件并上传第一片, 发送一个POST请求去files URL，参数是用BASE64加密的带有后缀的文件名，及文件总大小。该请求必须包含与该文件相关联的Content-Type头， 下面是简单例子：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: text/plain" \
  -d 'Hello, World!' \
  https://api.bmob.com/1/mkfile/c3NzLmpwZw==/1024000
```

返回的主体是一个JSON对象，包含： 
```
crc32：文件片段的crc32效验码。
ctx：服务端不透明的内部参数。
file： 如果文件没有完整上传， 返回false, 否则返回文件名， 分组， 和文件地址。
host：下一个片的上传URL。
offset：下一个片的偏移位置。
```
```
{
	crc32: 755192142
	ctx: "eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9"
	file: false
	host: https://api.bmob.com/1/bput/
	offset: 13
}
```

第二步， 循环上传剩余片, 发送POST请求到mkfile 接口返回的 host, 参数是 ctx/offset, 
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: text/plain" \
  -d '........' \
  https://api.bmob.com/1/bput/eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9/13
```
返回的主体是一个JSON对象，内容同上， 包含：
```
{
	crc32: 755192142
	ctx: "eyJTaXplIjoxMDAwLCJPZmZzZXQiOjY3MDU4MSwiRmlsZW5hbWUiOiI5ZTYxYTMyN2ZhLmpwZyIsIk9yZ25hbWUiOiJ0ZXN0LmpwZyIsIkNyYzMyIjo3NTUxOTIxNDJ9"
	file: false
	host: https://api.bmob.com/1/bput/
	offset: 13
}
```


### 删除文件

删除文件，必须要知道文件的组名和url，分别由https://api.bmob.com/1/files返回参数group和url提供。

```
curl -X DELETE \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
   http://api.bmob.cn/1/files/group1/M00/00/01/wKgBP1N3FAWRJXsSAAAB_rYZATs52.html
```

在上面的例子中，group1是组名，M00/00/01/wKgBP1N3FAWRJXsSAAAB_rYZATs52.html是url。

返回结果是个json对象：
```
{
  "msg": "delete file success"
}
```


## 用户
很多跨平台和跨系统的应用都有一个统一的登陆流程，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")通过REST API访问用户的账户让你实现该功能。

通常来说，用户这个类的功能与其他的对象是相同的，比如都没有限制模式(Schema Less)，User对象和其他对象不同的是一个用户必须有用户名(username)和密码(password)，密码会被自动地加密和存储。[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")强制您username和email这两个Key的值必须是没有重复的。

### 属性
[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")默认会有几个特定的属性：

username: 用户的用户名`（必需）`。

password: 用户的密码`（必需）`。

email: 用户的电子邮件地址`（可选）`。

### 注册用户
注册一个新用户与创建一个新的普通对象之间的不同点在于username和password字段都是必要的，password字段会以和其他的字段不一样的方式处理，它在保存时会被加密而且永远不会被返回给任何来自客户端的请求。

在您的应用设置页面中，您可以向[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")来请求认证邮件地址，这项设置启用了的话，所有用户注册时填写了email这个Key的值，并且邮箱有效的情况下，就会向这个邮箱地址发出一封邮件，邮件中会包含一个来自[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")的邮箱验证的链接，当您的用户查收邮件并点击这个链接后，则这个用户emailVerified这个Key的值会置为True，您可以在emailVerified字段上查看用户的email是否已经通过验证了。

为了注册一个新的用户，需要向user路径发送一个POST请求，您可以加入一个甚至多个新的字段，例如，创建一个有家庭电话字段的新用户:
```
curl -X POST \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "Content-Type: application/json" \
    -d '{"username":"cooldude6","password":"b_m7!-o8","phone":"415-392-0202"}' \
    https://api.bmob.cn/1/users
```
当创建成功时,HTTP响应头的状态码返回为201 Created，Http响应头的Location值为包含了该新用户的URL:
```
Status: 201 Created
Location: https://api.bmob.cn/1/users/Kc3M222J
```
返回的主体是一个JSON对象，包含objectId表示唯一的用户, createdAt时间戳表示用户注册时间, sessionToken可以被用来认证更新或删除这名用户信息的请求。
```
{
    "createdAt": "2011-11-07 20:58:34",
    "objectId": "Kc3M222J",
    "sessionToken": "pnktnjyb996sj4p156gjtp4im"
}
```

`这里需要注意一点的是，有些时候你可能需要注册时发送一封邮件，以确认用户邮箱的真实性。这时，你只需要登录自己的应用管理后台，在应用设置->邮件设置（下图）中把“邮箱验证”功能打开，Bmob云后端就会在注册时自动发动一封验证邮件给用户。`

![](image/email_verify.jpg)
设置邮箱验证功能

### 登陆用户
您的用户注册之后，您需要让他们用自己的用户名和密码登陆，为了做到这一点，发送一个HTTP GET请求到 `/1/login` ，加上username和password作为URL编码后的参数：
```
curl -X GET    \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -G \
    --data-urlencode 'username=cooldude6' \
    --data-urlencode 'password=b_m7!-o8' \
    https://api.bmob.cn/1/login
```
返回的主体是一个JSON对象包括所有除了password以外的自定义字段，它同样包含了createdAt,updateAt,objectId和sessionToken字段：
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
当注册一个用户后，您可以通过发送一个HTTP GET请求到用户注册成功时返回的HTTP请求头中的Location的URL获取用户的信息。比如，为了获取上面注册成功的用户：
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
在通常的情况下，我们都不希望用户去修改自己的数据，但可以通过认证让用户去做这件事，用户必须加入一个 `X-Bmob-Session-Token` 头部来请求这个更新操作，这个sessionToken在注册时和登录时都会返回。

为了改动一个用户已经有的数据，需要对这个用户的URL发送一个HTTP PUT请求，任何您没有指定的key会保持不变，所以您可以只改动用户信息中的一部分，username和password可以更改，但是新的username不能重复。

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
`在更新用户信息时，如果用户邮箱有变更并且在管理后台打开了邮箱验证选项的话，Bmob云后端同样会自动发动一封邮件验证信息给用户。`

### 删除用户
为了在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")上删除一个用户，可以向用户的URL上发送一个DELETE请求，前提是您必须提供一个X-Bmob-Session-Token在Http请求头上以便认证授权，例子：
```
curl -X DELETE \
    -H "X-Bmob-Application-Id: Your Application ID" \
    -H "X-Bmob-REST-API-Key: Your REST API Key" \
    -H "X-Bmob-Session-Token: pnktnjyb996sj4p156gjtp4im" \
    https://api.bmob.cn/1/users/g7y9tkhB7O
```

### 查询用户
您可以一次获取多个用户，只要向用户的根URL发送一个GET请求，没有任何URL参数的话，可以简单地列出所有用户：
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



### 密码重置
您可以使用这项功能，前提是用户将email与他们的账户关联起来，如果要重设密码，发送一个POST请求到 `/1/requestPasswordReset`, 同时在request的body部分带上email字段.
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
3. 用户根据向导点击重置密码连接，打开一个特殊的Bmob页面，让他们输入一个新的密码。
4. 用户的密码已被重置为新输入的密码。

### 邮箱验证
设置邮件验证是可选的一个应用设置, 这样可以对已经确认过邮件的用户提供一部分保留的体验，邮件验证功能会在用户(User)对象中加入emailVerified字段, 当一个用户的邮件被新添加或者修改过的话，emailVerified会默认被设为false，如果应用设置中开启了邮箱认证功能，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")会对用户填写的邮箱发送一个链接, 这个链接可以把emailVerified设置为 true.

emailVerified 字段有 3 种状态可以考虑：

`true` : 用户可以点击邮件中的链接通过[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")来验证地址，一个用户永远不会在新创建这个值的时候emailVerified为true。

`false` : 用户(User)对象最后一次被刷新的时候, 用户并没有确认过他的邮箱地址, 如果您看到emailVerified为false的话，您可以考虑刷新 用户(User)对象。

`missing` : 用户(User)对象已经被创建，但应用设置并没有开启邮件验证功能； 或者 用户(User)对象没有email邮箱。

#### 请求验证Email
发送给用户的邮箱验证的邮件会在一周内失效，可以通过调用 `/1/requestEmailVerify` 来强制重新发送：
```
curl -X POST \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"email":"coolguy@iloveapps.com"}' \
  https://api.bmob.cn/1/requestEmailVerify
```

## 地理位置
Bmob允许用户根据地球的经度和纬度坐标进行基于地理位置的信息查询。你可以在查询中添加一个GeoPoint的对象查询。您可以实现轻松查找出离当前用户最接近的信息或地点的功能。

### 创建地理位置对象
```
curl -X PUT \
  -H "X-Bmob-Application-Id: Your Application ID" \
  -H "X-Bmob-REST-API-Key: Your REST API Key" \
  -H "Content-Type: application/json" \
  -d '{"location":{            
            "__type": "GeoPoint",
            "latitude": 112.934755,
            "longitude": 24.52065
        }}' \
  https://api.bmob.cn/1/classes/GameScore/e1kXT22L
```

### 查询地理位置信息
现在您有一系列的对象对应的地理坐标，如果能发现那些对象离指定的点近就好了，这可以通过GeoPoint数据类型加上在查询中使用$nearSphere做到。获取离用户最近的10个地点应该看起来像下面这个样子:

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
这会按离纬度30.0，经度-20.0的距离排序返回一系列的结果，第一个就是最近的对象。(注意如果一个特定的order参数给了的话，它会覆盖按距离排序)，例如，下面是两个上面的查询返回的结果：
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
为了限定搜素的最大举例，需要加入$maxDistanceInMiles和$maxDistanceInKilometers或者$maxDistanceInRadians参数来限定.比如要找的半径在10公里内的话:
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
同样做查询寻找在一个特定的范围里面的对象也是可以的，为了找到在一个矩形的区域里的对象，按下面的格式加入一个约束 {"$within": {"$box": {[southwestGeoPoint, northeastGeoPoint]}}}：
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


## 云端代码
在REST API中调用云端代码的方法如下：

相关云端代码的编写方式，请参考[云端代码开发文档][2]

## 消息推送

### 消息推送简介
推送通知是让用户及时被通知、和你的应用保持联系的一种非常棒的方式，你可以快速而有效地通知到所有的用户，下面这个教程将会教会你使用Bmob来推送消息。

### 安装消息推送服务
每一个Bmob的App安装在用户设备后，如果要使用消息推送功能，Bmob SDK会自动生成一个Installation对象。Installation对象包含了推送所需要的所有信息。举例：一个棒球的App，你可以让用户订阅感兴趣的棒球队，然后及时将这个球队的消息推送给用户 。
您可以使用 REST API，通过请求URL `/1/installations` 对Installation对象进行一系列操作，就像你存储和获取其他的普通对象如GameScore对象一样。
注意Installation对象一般只在Client SDK进行修改，但REST API对于大量的导入或导出订阅数据的Installation对象是极其有用的。

Installation对象有几个系统默认的特殊字段来帮助你的管理和定位到设备：
- `badge` : iOS应用中右上角的图标标识，这会在服务端修改，用于未来的推送消息的自增统计
- `channels` : 当前这个设备订阅的渠道名称数组
- `timeZone` : 设备所在位置的时区， 如Asia/Shanghai，这个会在每个Installation对象更新时同步（只读）
- `deviceType` : 设备的的类型, 值为："ios" 或 "android" (只读)
- `installationId` : Bmob使用的设备唯一号，Android设备是必须的，iOS可选 (只读)
- `deviceToken` : iOS设备由Apple APNS生成的唯一性token标识 (只读)

### 保存 installation

#### 保存iOS设备的deviceToken
iOS设备通常使用deviceToken来惟一标识一台设备。
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

对于Android设备，[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")SDK会自动生成uuid作为installationId保存到 Bmob。 您可以使用以下REST API保存Android设备的installation ID。
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
  https://api.parse.com/1/push
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
  https://api.parse.com/1/push
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
          "alert": "Hello From Bmob."
        }
      }' \
  https://api.bmob.cn/1/push
```

### 发送选项设置
推送通知不仅仅可以用来发送消息，iOS中，推送内容还可以包含要播放的声音和要显示的badge数字值,还可以定制任何你想发送的内容，对于Android，你甚至可以指定Intent应用于接收消息，对于时间性有要求的消息您还可以设置一个消息过期时间。

#### 定制您的通知
如果你想发送更多的消息，你可以在data的字典对象中设置其他字段，下面这些保留的字段有其特殊的意义：
- `alert` : 通知的消息内容
- `badge` : (仅iOS)一个数字值将会高亮显示在应用图标的右上角。可以设置一个数字值，也可以对当前值进行自增加1.
- `sound` : (仅iOS)应用绑定包中的声音文件 
- `content-available` : (仅iOS)如果你的应用是新闻类的，或者你的应用正在使用iOS7的Remote Notification Background Mode，设定这个值为1将为触发离线下载功能。
- `action` : (仅Android)接收到推送消息时应用Intent，如果没有指定title或alert，Intent将被应用，但是没有通知呈现给用户。
- `title` : (仅Android)这个值将显示在系统通知栏

例如，推送一条通知，将当前的badge值自增1,并且播放iOS设备上定制的音乐，对于Android用户，将在系统通知栏中显示特殊的标题，则请求如下：
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
          "badge": "Increment",
          "sound": "cheering.caf",
          "title": "Mets Score!"
        }
      }' \
  https://api.parse.com/1/push
```

当然，你也可以在data字典对象中定制自己的字段，推送消息，但不显示在 Andoid 系统的通知栏中，而是执行应用程序预定义的逻辑，Android或iOS接收消息文档中对此有说明，iOS只有当用户从通知中打开应用，才能访问data的数据，Android将提供这个数据在action Intent应用之后,
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
  https://api.parse.com/1/push
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
也可以是相对时间（根据push_time做定期推送，从push_time时间开始算起，直到expiration_interval时间后过期），下面请求表示消息将在2012-01-28 00:51:13这个时间点开始推送，直到7天后过期:
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

## 注意事项和常见问题解答
### 注意事项
这是有一些问题是值得留心的:

1. 每一个对象只能有一个索引指向一个GeoPoint对象
2. GeoPoint不应该等于或者超出它的界. 纬度不应该是-90.0或者90.0,经度不应该是-180.0或者180.0. 试图在GeoPoint上使用超出范围内的经度和纬度会导致问题.


### FAQ常见问题解答
