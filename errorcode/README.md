# 简介

本文档是REST API的错误码相对详细的解释。

## 401 Unauthorized

当HTTP响应头中返回的状态码是401时，返回的内容体错误信息格式如下：
```
{
    "error": "unauthorized"
}
```

含义：不能通过认证，一般是参数错误，如缺失应用密钥或应用密钥不正确。

## 500 Internal Server Error

当HTTP响应头中返回的状态码是500时，返回的内容体错误信息格式如下：
```
{
    "error": "It is busy...Try it later!"
}
```

含义：服务器内部错误，一般是因为传入了错误的参数，或者没有在本文档里明确定义的运行时错误。

## 404 Not Found
当HTTP响应头中返回的状态码是404时，返回的内容体错误信息格式样例如下：
```
{
    "code": 101,
    "error": "object not found for e1kXT22L"
}
```
以下是404状态码详细的错误信息说明，信息中的%s，%d，%f将替换为详细的信息或具体的值。

### 101

信息 - object not found for %s. OR username or password incorrect

含义 - 查询的 对象或Class 不存在 或者 登录接口的用户名或密码不正确

### 102

信息 - Invalid key '%s' for find OR Invalid value for key '%s'. OR %s: invalid geopoint object.

含义 - 查询中的字段名是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。，或查询对应的字段值不匹配，或提供的地理位置格式不正确

### 103

信息 - objectId required. OR classname '%s' must start with a letter.

含义 - 查询单个对象或更新对象时必须提供objectId 或 非法的 class 名称，class 名称是大小写敏感的，并且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。

### 104

信息 - relation className '%s' not exists .

含义 - 关联的class名称不存在

### 105

信息 - invalid field name: %s. OR It is a reserved field: %s.

含义 - 字段名是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线 或 字段名是[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")默认保留的，如objectId,createdAt,updateAt,

### 106

信息 - %s: is not a valid Pointer. 

含义 - 不是一个正确的指针类型

### 107

信息 - invalid json: %s.

含义 - 输入的json不是正确的json格式

或

信息 - This endpoint only supports Content-Type: application/json requests, not %s.

含义 - 请求只支持Header头部Content-Type值为application/json或application/json; charset=utf-8

或

信息 - invalid date: %s.

含义 - 时间格式不正确

或

信息 - ACL shoud be like: {"*":{"read":true},"eAfHB29gP9":{"write":true}}.

含义 - ACL应该像这样的{"*":{"read":true},"eAfHB29gP9":{"write":true}}

或

信息 - invalid op value

含义 - 不是正确的__op的值

### 108

信息 - username and password required.

含义 - 用户名和密码是必需的

### 109

信息 - login data required.

含义 - 登录信息是必需的，如邮箱和密码时缺少其中一个提示此信息



### 111

信息 - invalid type for key '%s', expected '%s', but got '%s'.

含义 - 传入的字段值与字段类型不匹配，期望是这样(%s)的，但传过来却是这样(%s)的


### 112

信息 - requests must be an array.

含义 - requests的值必须是数组

### 113

信息 - every request shoud be an object like:
					{
                         "method": "POST",
        					"path": "/1/classes/GameScore",
        					"body": {
        						"score": 1337,
        						"playerName": "Sean Plott"
        					}
                    }
                    
含义 - requests数组中每个元素应该是一个像这样子的json对象

### 114

信息 - requests array larger than %d

含义 - requests数组大于50


### 117

信息 - Latitude must be in [-90, 90]: %f. OR Longitude must be in [-180, 180]: %f.

含义 - 纬度范围在[-90, 90] 或 经度范围在[-180, 180]

### 120

信息 - Email verify should be opened in your app setup page of bmob

含义 - 要使用此功能，请在[Bmob](http://www.bmob.cn/ "Bmob移动后端云服务平台")后台应用设置中打开邮箱认证功能开关

### 131

信息 - Invalid device token:%s

含义 - 不正确的deviceToken

### 132

信息 - Invalid installation ID:%s

含义 - 不正确的installationId

### 133

信息 - Invalid device type:%s

含义 - 不正确的deviceType

### 134

信息 - device token '%s' already token.

含义 - deviceToken已经存在

### 135

信息 - installation ID '%s' already token.

含义 - installationId已经存在

### 136

信息 - %s cannot be changed by this operation OR deviceToken may not be set for deviceType android

含义 - 只读属性不能修改 或 android设备不需要设置deviceToken

### 138

信息 - %s is read only.

含义 - 表是只读的

### 139

信息 - Role names must be restricted to alphanumeric characters, dashes(-), underscores(_), and spaces.

含义 - 角色名称是大小写敏感的，并且必须以英文字母开头，有效的字符仅限在英文字母、数字、空格、横线以及下划线。

或

信息 - role name '%s' already taken.

含义 - 角色名称已经存在。


### 141 

信息 - Missing the push data.

含义 - 缺失推送需要的data参数

### 142

信息 - %s shoule be like: 2013-12-04 00:51:13

含义 - 时间格式应该如下： 2013-12-04 00:51:13

### 143

信息 - %s must be a number"

含义 - 必须是一个数字

### 144

信息 - %s cannot before now

含义 - 不能是之前的时间

### 145

信息 - file size error

含义 - 文件大小错误

### 146

信息 - file name error

含义 - 文件名错误

### 147

信息 - file offeset error

含义 - 文件分页上传偏移量错误

### 148

信息 - file ctx error

含义 - 文件上下文错误

### 149

信息 - empty file

含义 - 空文件

### 150

信息 - file upload error

含义 - 文件上传错误

### 151

信息 - file delete error

含义 - 文件删除错误

### 160

信息 - image error

含义 - 图片错误 

### 161

信息 - image mode error

含义 - 图片模式错误 

### 162

信息 - image width error

含义 - 图片宽度错误 

### 163

信息 - image height error

含义 - 图片高度错误 

### 164

信息 - image longEdge error

含义 - 图片长边错误 

### 165

信息 - image shortgEdge error

含义 - 图片短边错误 

### 201

信息 - %s missing

含义 - 缺失数据

### 202

信息 - username '%s' already taken.

含义 - 用户名已经存在

### 203 

信息 - email '%s' already taken.

含义 - 邮箱已经存在

### 204

信息 - you must provide an email.

含义 - 必须提供一个邮箱地址

### 205

信息 - no user found with email '%s'.

含义 - 没有找到此邮件的用户

或

信息 - no user found with username '%s'.

含义 - 没有找到此用户名的用户

### 206

信息 - User cannot be altered without sessionToken Error.

含义 - 登录用户才能修改自己的信息。RestAPI的Http Header中没有提供sessionToken的正确值，不能修改或删除用户

### 207

信息 - code error.

含义 - 验证码错误

### 208

信息 - authData error.

含义 - authData不正确

或

信息 - authData already linked by other user.

含义 - authData已经绑定了其他用户账户

### 209

信息 - mobilePhoneNumber '%s' already taken.

含义 - 该手机号码已经存在

### 210

信息 - old password incorrect.

含义 - 旧密码不正确

### 301

信息 - %s

含义 - 验证错误详细提示，如邮箱格式不正确 

### 302

信息 - your app setting '%s'.

含义 - Bmob后台设置了应用设置值， 如'不允许SDK创建表 '

### 310

信息 - %s

含义 - 云端代码运行错误的详细信息

### 311

信息 - invalid cloudcode name: %s.

含义 - 云端代码名称是大小写敏感的，且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。

### 401 

信息 - unique index cannot has duplicate value: %s

含义 - 唯一键不能存在重复的值

### 402

信息 - query where larger than %d bytes.

含义 - 查询的wher语句长度大于具体多少个字节

### 601

信息 - Invalid bql:%s

含义 - 不正确的BQL查询语句

## VIP付费限制相关错误码

### 1002 

信息 - Tables of the app can create has reached to limit

含义 - 该应用能创建的表数已达到限制

### 1003 

信息 - Rows of the table can add has reached to limit

含义 - 该表的行数已达到限制

### 1004 

信息 - Fields of the table can add has reached to limit

含义 - 该表的列数已达到限制

### 1005

信息 - Request api of the this month has reached to limit

含义 - 每月api请求数量已达到限制

### 1006

信息 - Crontab jobs of the app has reached to limit

含义 - 该应用能创建定时任务数已达到限制

### 1007

信息 - CloudCodes of the app has reached to limit

含义 - 该应用能创建云端代码数已达到限制

### 1500

信息 - The file is larger than file size you can upload

含义 - 你上传的文件大小已超出限制

## 支付功能相关错误码

### 150

信息 - order_no is null.

含义 - 订单号是空的.

### 10001

信息 - %s required

含义 - 有些项是必填的

### 10002

信息 - order_no not exists.

含义 - 你要查询的订单号不存在.

### 10003

信息 - %s.

含义 - 详细错误信息，如微信或支付宝返回的详细错误.

### 10004

信息 - %s.

含义 - 详细错误信息，如微信或支付宝返回的详细错误.

## 短信功能相关错误码

短信功能包含的错误码，除了包括以上的109, 207, 209 状态码外，还定义了以下错误码：

### 10010

信息 - mobile '%s' send message limited.

含义 - 该手机号发送短信达到限制(对于一个应用来说，一天给同一手机号发送短信不能超过10条，一小时给同一手机号发送短信不能超过5条，一分钟给同一手机号发送短信不能超过1条)

### 10011

信息 - no remaining number for send messages.

含义 - 该账户无可用的发送短信条数

### 10012

信息 - your credit info must verify ok.

含义 - 身份信息必须审核通过才能使用该功能

### 10013

信息 - sms content illegal.

含义 - 非法短信内容






