---
title: API开发规范
tags: API
category: API
date: 2018-02-28 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0148.jpg)

API开发规范
<!--more-->
###### 整体规范建议采用RESTful 方式来实施。

# 1. 协议

API与用户的通信协议，总是使用HTTPs协议，确保交互数据的传输安全。

# 2. 域名

应该尽量将API部署在专用域名之下。

```
https://api.example.com
```

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

```
https://example.org/api/
```

# 3. api版本控制

应该将API的版本号放入URL。

```
https://api.example.com/v{n}/
```

另一种做法是，将版本号放在HTTP头信息中，但不如放入URL方便和直观。Github采用这种做法。

采用多版本并存，增量发布的方式

v{n} n代表版本号,分为整形和浮点型

整形的版本号: 大功能版本发布形式；具有当前版本状态下的所有API接口 ,例如：v1,v2

浮点型：为小版本号，只具备补充api的功能，其他api都默认调用对应大版本号的api 例如：v1.1 v2.2

# 4. API 路径规则

路径又称"终点"（endpoint），表示API的具体网址。

在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

```
https://api.example.com/v1/products

https://api.example.com/v1/users

https://api.example.com/v1/employees
```

# 5. HTTP请求方式

对于资源的具体操作类型，由HTTP动词表示。

常用的HTTP动词有下面四个（括号里是对应的SQL命令）。

GET（SELECT）：从服务器取出资源（一项或多项）。

POST（CREATE）：在服务器新建一个资源。

PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。

DELETE（DELETE）：从服务器删除资源。

下面是一些例子。

GET /product：列出所有商品

POST /product：新建一个商品

GET /product/ID：获取某个指定商品的信息

PUT /product/ID：更新某个指定商品的信息

DELETE /product/ID：删除某个商品

GET /product/ID/purchase ：列出某个指定商品的所有投资者

get /product/ID/purchase/ID：获取某个指定商品的指定投资者信息

# 6. 过滤信息

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。

下面是一些常见的参数。

?limit=10：指定返回记录的数量

?offset=10：指定返回记录的开始位置。

?page=2&per_page=100：指定第几页，以及每页的记录数。

?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。

?producy_type=1：指定筛选条件

# 7. API 传入参数

传入参数分为4种类型：

地址栏参数

\* restful 地址栏参数 /api/v1/product/122 122为产品编号，获取产品为122的信息

\* get方式的查询字串 见过滤信息小节

请求body数据

cookie

request header

cookie和header 一般都是用于OAuth认证的2种途径

# 8. 返回数据

只要api接口成功接到请求，就不能返回200以外的HTTP状态。

为了保障前后端的数据交互的顺畅，建议规范数据的返回，并采用固定的数据格式封装。

接口返回模板：

```
{
    status:0,

    data:{}||[],

    msg:’’
}
```

status: 接口的执行的状态

=0表示成功

<0 表示有异常=""

Data 接口的主数据

可以根据实际返回数组或JSON对象

Msg 信息

当status!=0 都应该有错误信息

# 9. 非Restful Api的需求

由于实际业务开展过程中，可能会出现各种的api不是简单的restful 规范能实现的，因此，需要有一些api突破restful规范原则。特别是移动互联网的api设计，更需要有一些特定的api来优化数据请求的交互。

## 9.1 页面级的api

把当前页面中需要用到的所有数据通过一个接口一次性返回全部数据

举例

api/v1/get-home-data 返回首页用到的所有数据

这类API有一个非常不好的地址，只要业务需求变动，这个api就需要跟着变更。

## 9.2 自定义组合api

把当前用户需要在第一时间内容加载的多个接口合并成一个请求发送到服务端，服务端根据请求内容，一次性把所有数据合并返回,相比于页面级api，具备更高的灵活性，同时又能很容易的实现页面级的api功能。

规范

地址：api/v1/batApi

传入参数：

```
data:[
    {url:'api1',type:'get',data:{...}},

    {url:'api2',type:'get',data:{...}},

    {url:'api3',type:'get',data:{...}},

    {url:'api4',type:'get',data:{...}}
]
```

返回数据

```
{
    status:0,
    msg:'',
    data:[
        {status:0,msg:'',data:[]},

        {status:-1,msg:'',data:{}},

        {status:1,msg:'',data:{}},

        {status:0,msg:'',data:[]},
    ]
}
```

# 10. Api共建平台

RAP是一个GUI的WEB接口管理工具。在RAP中，您可定义接口的URL、请求&响应细节格式等等。通过分析这些数据，RAP提供MOCK服务、测试服务等自动化工具。RAP同时提供大量企业级功能，帮助企业和团队高效的工作。

## 10.1 什么是RAP?

在前后端分离的开发模式下，我们通常需要定义一份接口文档来规范接口的具体信息。如一个请求的地址、有几个参数、参数名称及类型含义等等。RAP 首先方便团队录入、查看和管理这些接口文档，并通过分析结构化的文档数据，重复利用并生成自测数据、提供自测控制台等等... 大幅度提升开发效率。

## 10.2 RAP的特色

强大的GUI工具 给力的用户体验，你将会爱上使用RAP来管理您的API文档。

完善的MOCK服务 文档定义好的瞬间，所有接口已经准备就绪。有了MockJS，无论您的业务模型有多复杂，它都能很好的满足。

庞大的用户群 RAP在阿里巴巴有200多个大型项目在使用，也有许多著名的公司、开源人士在使用。RAP跟随这些业务的成行而成长，专注细节，把握质量，经得住考验。

免费 + 专业的技术支持 RAP是免费的，而且你的技术咨询都将在24小时内得到答复。大多数情况，在1小时内会得到答复。

RAP是一个可视化接口管理工具 通过分析接口结构，动态生成模拟数据，校验真实接口正确性， 围绕接口定义，通过一系列自动化工具提升我们的协作效率。我们的口号：提高效率，回家吃晚饭！


# 11. RESTful接口规范

## URI

### URI规范

1. 不用大写；

2. 用中杠 - 不用下杠 _ ；

3. 参数列表要encode；

4. URI中的名词表示资源集合，使用复数形式。

5. 在RESTful[架构](http://lib.csdn.net/base/architecture)中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词（特殊情况可以使用动词），而且所用的名词往往与[数据库](http://lib.csdn.net/base/mysql)的表格名对应。



资源集合 vs单个资源

URI表示资源的两种方式：资源集合、单个资源。

资源集合：

       /zoos //所有动物园

       /zoos/1/animals //id为1的动物园中的所有动物

单个资源：

       /zoos/1//id为1的动物园

       /zoos/1;2;3//id为1，2，3的动物园

避免层级过深的URI

在url中表达层级，用于 按实体关联关系进行对象导航 ，一般根据id导航。

过深的导航容易导致url膨胀，不易维护，如 GET /zoos/1/areas/3/animals/4 ，尽量使用查询参数代替路径中的实体导航，如 GET/animals?zoo=1&area=3 ；

## 版本

应该将API的版本号放入到URI中

​           https://api.example.com/v1/zoos

## Request

HTTP方法

通过标准HTTP方法对资源CRUD：

1. GET：查询（从服务器取出资源一项或多项）

GET /zoos

GET /zoos/1

GET/zoos/1/employees

POST：创建单个新资源。 POST一般向“资源集合”型uri发起

POST/animals  //新增动物

POST/zoos/1/employees //为id为1的动物园雇佣员工

2. PUT：更新单个资源（全量），客户端提供完整的更新后的资源。与之对应的是 PATCH，PATCH负责部分更新，客户端提供要更新的那些字段。 PUT/PATCH一般向“单个资源”型uri发起

PUT/animals/1

PUT /zoos/1

3. DELETE：删除

DELETE/zoos/1/employees/2

DELETE/zoos/1/employees/2;4;5

DELETE/zoos/1/animals  //删除id为1的动物园内的所有动物

HEAD / OPTION/ PATCH用的不多，就不多解释了。

4. HEAD：获取资源的元数据
5. OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的
6. PATCH：在服务器更新资源（客户端提供改变的属性）

安全性和幂等性

1.      安全性 ：不会改变资源状态，可以理解为只读的；

2.      幂等性 ：执行1次和执行N次，对资源状态改变的效果是等价的。

| .      | 安全性 | 幂等性 |
| ------ | ------ | ------ |
| GET    | √      | √      |
| POST   | ×      | ×      |
| PUT    | ×      | √      |
| DELETE | ×      | √      |

安全性和幂等性均不保证反复请求能拿到相同的response。以 DELETE为例，第一次DELETE返回200表示删除成功，第二次返回404提示资源不存在，这是允许的。

复杂查询

查询可以捎带以下参数：

| .        | 示例                     | 备注                                        |
| -------- | ------------------------ | ------------------------------------------- |
| 过滤条件 | ?type=1&age=16           | 允许一定的uri冗余，如 /zoos/1 与 /zoos?id=1 |
| 排序     | ?sort=age&order=asc      | 指定返回结果按照哪个属性排序，以及排序顺序  |
| 投影     | ?whitelist=id,name,email |                                             |
| 分页     | ? page=2&per_page=100    | 指定第几页，以及每页的记录数                |

Bookmarker

经常使用的、复杂的查询标签化，降低维护成本。

如：GET /trades?status=closed&sort=created,desc

快捷方式：GET /trades#recently-closed或者GET /trades/recently-closed

状态码

    服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

§200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。

§201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。

§202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）

§204 NO CONTENT - [DELETE]：用户删除数据成功。

§400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。

§401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。

§403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。

§404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。

§406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。

§410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。

§422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。

§500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

状态码的完全列表参见[这里](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

 

URI失效

随着系统发展，总有一些API失效或者迁移，对失效的API，返回404 not found 或 410 gone；对迁移的API，返回 301重定向。

 

## Response

1.     不要包装：

response的 body 直接就是数据，不要做多余的包装。错误示例：

{

   "success":true,

   "data":{"id":1,"name":"xiaotuan"},

}

2.     各HTTP方法成功处理后的数据格式：

| ·     | response 格式 |
| --------- | ----------------- |
| GET       | 单个对象、集合    |
| POST      | 新增成功的对象    |
| PUT/PATCH | 更新成功的对象    |
| DELETE    | 空                |

 

## 错误处理

1.      不要发生了错误但给2xx响应，客户端可能会缓存成功的http请求；

2.      正确设置http状态码，不要自定义；

3.      Response body提供

即:返回的信息中将error作为键名，出错信息作为键值即可

1)错误的代码（日志/问题追查）；

2)错误的描述文本（展示给用户）。

对第三点的实现稍微多说一点：

Java服务器端一般用异常表示 RESTful API的错误。API 可能抛出两类异常：业务异常和非业务异常。 业务异常 由自己的业务代码抛出，表示一个用例的前置条件不满足、业务规则冲突等，比如参数校验不通过、权限校验失败。 非业务类异常 表示不在预期内的问题，通常由类库、框架抛出，或由于自己的代码逻辑错误导致，比如数据库连接失败、空指针异常、除0错误等等。

业务类异常必须提供2种信息：

1.      如果抛出该类异常，HTTP响应状态码应该设成什么；

2.      异常的文本描述；

在Controller层使用统一的异常拦截器：

1.     设置 HTTP响应状态码：对业务类异常，用它指定的 HTTPcode；对非业务类异常，统一500；

2.     Response Body的错误码：异常类名

3.     Response Body的错误描述：对业务类异常，用它指定的错误文本；对非业务类异常，线上可以统一文案如“服务器端错误，请稍后再试”，开发或测试环境中用异常的 stacktrace，服务器端提供该行为的开关。

常用的http状态码及使用场景：

| 状态码                    | 使用场景                                                     |
| ------------------------- | ------------------------------------------------------------ |
| 400 bad request           | 常用在参数校验                                               |
| 401 unauthorized          | 未经验证的用户，常见于未登录。如果经过验证后依然没权限，应该 403（即 authentication和 authorization的区别）。 |
| 403 forbidden             | 无权限                                                       |
| 404 not found             | 资源不存在                                                   |
| 500 internal server error | 非业务类异常                                                 |
| 503 service unavaliable   | 由容器抛出，自己的代码不要抛这个异常                         |

 

## 其他

（1）API的身份认证应该使用OAuth2.0框架

（2）服务器返回的数据格式，应该尽量使用JSON，避免使用XML

（3）比较复杂的接口不能确定是使用POST还是PUT时，要看具体的业务层代码，看看接口产生的结果是否幂等，如果幂等用PUT，相反用POST

​      如：接口接收到一资源，资源存在更新，不存在插入新数据，这个接口就要用PUT
