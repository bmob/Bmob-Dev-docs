#RestAPI 类库说明

以下的类库说明文档采用类似/1/classes/GameScore这样的相对路径表示，其中1表示RestAPI的版本号。{className}表示对象（对应数据表）名称，{objectId}表示某条记录的objectId信息，{userName}表示终端用户的用户名，{passWord}表示终端用户的密码，{fileName}表示文件名，{fileSize}表示文件大小。

## HTTP请求信息

### 请求路径

RestAPI的所有请求都发送到 https://api.bmob.cn/1 路径下，其中1是RestAPI的版本号。这个版本号在未来的计划中基本不会发生任何变化。

### HTTP头信息

- RestAPI的HTTP头信息必须包含X-Bmob-Application-Id和X-Bmob-REST-API-Key头信息。X-Bmob-Application-Id头表示正在访问的是哪个App程序，X-Bmob-REST-API-Key头表示APP的授权信息。

- 对于POST和PUT请求，HTTP头信息必须包含Content-Type信息，一般值为application/json，除文件API接口除外。

- 对于用户API接口，如果想要更新用户信息或者删除用户的话，还需要包含X-Bmob-Session-Token头信息。

### 请求内容体和回应信息

请求内容体（即HTTP包的Body信息）必须封装为JSON格式，请求回应信息也是JSON格式的数据。

字符串、数字、布尔值、数组数据类型为普通类型，地理位置、时间、指针、对象为特殊类型，请求时的格式封装如下：

```
{"__type": "GeoPoint", "latitude": 30.0, "longitude": -20.0}

{"__type":"Date","iso":"2012-01-29 11:33:53"}

{"__type": "Pointer", "className": "Game", "objectId": "*****"}

{"__type": "Object", "className": "Post","otherFields": "*****"}

```

回应信息需要注意一点的是，为了节省流量，objectId、createAt和updateAt系统内部字段在回应的JSON格式中为String类型，开发时需要自行进行处理。


## 数据操作

<table>
  <thead>
    <tr>
      <th>URL路径</th>
      <th>HTTP方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/classes/{className}</td>
      <td>POST</td>
      <td>添加数据</td>
    </tr>
    <tr>
      <td>/1/classes/{className}/{objectId}</td>
      <td>GET</td>
      <td>查询单条数据</td>
    </tr>
    <tr>
      <td>/1/classes/{className}</td>
      <td>GET</td>
      <td>查询多条数据</td>
    </tr>
    <tr>
      <td>/1/classes/{className}/{objectId}</td>
      <td>PUT</td>
      <td>修改数据</td>
    </tr>
    <tr>
      <td>/1/classes/{className}/{objectId}</td>
      <td>DELETE</td>
      <td>删除数据，删除字段</td>
    </tr>
  </tbody>
</table>

## 用户操作

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/users</td>
      <td>POST</td>
      <td>用户注册</td>
    </tr>
    <tr>
      <td>/1/login?username={userName}&password={passWord}</td>
      <td>GET</td>
      <td>用户登录</td>
    </tr>
    <tr>
      <td>/1/users/{objectId}</td>
      <td>GET</td>
      <td>获取当前登录用户信息</td>
    </tr>
	<tr>
      <td>/1/users</td>
      <td>GET</td>
      <td>查询全部用户信息</td>
    </tr>
    <tr>
      <td>/1/users/{objectId}</td>
      <td>PUT</td>
      <td>更新用户信息</td>
    </tr>
    <tr>
      <td>/1/users/{objectId}</td>
      <td>DELETE</td>
      <td>删除用户</td>
    </tr>
	<tr>
      <td>/1/requestPasswordReset</td>
      <td>POST</td>
      <td>重置用户密码</td>
    </tr>
	<tr>
      <td>/1/requestEmailVerify</td>
      <td>POST</td>
      <td>发送邮箱验证信息</td>
    </tr>
  </tbody>
</table>

## 文件操作

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/files/{fileName}</td>
      <td>POST</td>
      <td>上传整个文件，其中文件名{fileName}需要进行BASE64加密</td>
    </tr>
    <tr>
      <td>/1/mkfile/{fileName}/{fileSize}</td>
      <td>POST</td>
      <td>分片上传文件，其中文件名{fileName}需要进行BASE64加密，{fileSize}为文件大小（单位Byte）</td>
    </tr>
    <tr>
      <td>/1/files/{group}/{fileUrl}</td>
      <td>GET</td>
      <td>删除文件，其中{group}为文件的组名，{fileUrl}是文件的相对URL</td>
    </tr>
  </tbody>
</table>

## 图片服务

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/images/thumbnail</td>
      <td>POST</td>
      <td>创建缩略图</td>
    </tr>
    <tr>
      <td>/1/images/watermark</td>
      <td>POST</td>
      <td>创建水印图片</td>
    </tr>
  </tbody>
</table>


## 角色操作

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/roles</td>
      <td>POST</td>
      <td>创建角色</td>
    </tr>
    <tr>
      <td>/1/roles/{objectId}</td>
      <td>GET</td>
      <td>获取角色信息</td>
    </tr>
	<tr>
      <td>/1/roles/{objectId}</td>
      <td>PUT</td>
      <td>更新或者删除子角色信息</td>
    </tr>
	<tr>
      <td>/1/roles/{objectId}</td>
      <td>DELETE</td>
      <td>删除角色信息</td>
    </tr>
  </tbody>
</table>

## 安装数据

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/installations</td>
      <td>POST</td>
      <td>创建安装数据</td>
    </tr>
    <tr>
      <td>/1/installations/{objectId}</td>
      <td>GET</td>
      <td>获取安装数据</td>
    </tr>
	<tr>
      <td>/1/installations/{objectId}</td>
      <td>PUT</td>
      <td>更新安装数据</td>
    </tr>
	<tr>
      <td>/1/installations/{objectId}</td>
      <td>DELETE</td>
      <td>删除安装数据</td>
    </tr>
  </tbody>
</table>

## 消息推送

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/1/push</td>
      <td>POST</td>
      <td>推送消息（广播、组播、多播和点播）</td>
    </tr>
  </tbody>
</table>

## 云端代码

<table>
  <thead>
    <tr>
      <th>URL</th>
      <th>HTTP请求方式</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1/functions/{cloudName}</td>
      <td>POST</td>
      <td>执行名称为cloudName的云端代码</td>
    </tr>
  </tbody>
</table>