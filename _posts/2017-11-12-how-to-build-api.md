---
layout: post
title:  "How to build you apis "
categories: API
tags: api
---
* content
{:toc}
如何构建自己的api，即不臃肿，也不让人讨厌！




#### 1  : 数据构建

快速构建安全可用的基础数据，fork data，保证 api 数据完整性！
    
#### 2  : 规划和创造端点

端点理论:将操作计划转换为实际端点需要的功能(参照:REST-FULL API的理论和命名约定的最佳实践)
API资源应该避免输出自增变量,而是唯一标识
TDD[测试驱动开发（Test-Driven Development）]开发模式
##### 2.1 POST 和 PUT 的区别!

PUT是在您事先知道整个URL并且操作是幂等的情况下使用的。(幂等性是一个很花哨的词，意思是“可以一遍又一遍地做而不会产生不同的结果”)
复数、单数还是两者都有?( /user/1[用户1的信息]  /users[所有用户|1,2用户信息])
一个谓词是一个操作,一个执行术语,我们的API只需要一个谓词—HTTP方法。所有其他动词都需要远离URL。
名词是地方或事物。资源就是事物，而URL就是事物存在于互联网上的地方。( POST /users/5/messages HTTP/1.1 )

##### 2.2 常见的请求方式

GET POST PUT PATCH DELETE COPY HEAD OPTIONS LINK UNLINK LOCK UNLOCK PROPFIND VIEW
     
##### 2.3 规划端点:控制器,路由
Create POST /users Route::post('users','UsersController@create');

常见的REST-FUL API请求：Planning and Creating Endpoints，`规划` 和 `创造` 端点(url访问路径)

分析功能需求，映射成单一的功能模块

简单的功能需求[增删改查,列表]): 创建, 读取, 更新, 删除, 列表

 
```
class Chapter02
{
    //获取资源(功能点)
    //•获取/资源——特定数据的分页列表，以某种逻辑默认顺序。
    //•GET /resources/X -实体X，它可以是ID、散列、段塞、用户名等，只要它是唯一的一个“资源”。
    public function resourceGet()
    {

    }

    //删除资源(功能点)
    //•删除/位置/X -删除单个位置。
    //•删除/位置/X,Y,Z -删除一些位置。
    //•删除/位置-这是一个潜在的危险端点，可以跳过，因为它应该删除所有位置。
    //•删除/位置/X/图像-删除一个位置的图像，或:
    //•删除/位置/X/图像-如果你选择多个图像，这将删除所有的图像。
    public function resourceDel()
    {

    }
}
```

#### 3  : 输入输出


使用 PHP 和 Guzzle HTTP 库发出HTTP请求

GraphQL(所见即所得) 和 REST Ful API 的差异


1.请求:
>POST /try.php HTTP/1.1
>
>Host: localhost:20002
>
>Content-Type: application/json
>
>Cache-Control: no-cache
>
>Postman-Token: 2d7cca56-c0a4-ea21-68ad-0aa6e3482da8
>
>{"query": "query { echo(message: \"Hello World 111\") }" }
   

2.响应:
>HTTP/1.1 200 OK
>
>Date: Fri, 04 Jan 2019 07:33:38 GMT
>
>Content-Type: application/json; charset=utf-8
>
>Transfer-Encoding: chunked
>
>Server: localhost:20002
>
>Status: 200 OK
>
>{"code":"200","data":"test"}


3.格式:
>Content-Type: application/x-www-form-urlencoded
>
>Content-Type: multipart/form-data; ...
>
>js: $(selector).serializeArray() 将匹配的元素转成json字符串
>
>多维数组拼接: arr[test]=1&arr[name]=name

 
4.JSON 和 XML(不易于存储,文件较大,一切都为字符串[整数,布尔,null可能会混淆])
 
5.内容结构：Json Api (优点:响应一致,结构相同|确定:无法多响应)
```
/**输入输出理论(HTTP请求和响应)
 * Class Chapter3
 * @package bookLog\buildApis
 */
class Chapter03
{
    //GraphQL
    //get /compareGraphUsers
    /*{
     user(id: "1") {
        id
        email
        }
    }*/  //获取用户1的id和email
    public function compareGraphUsers()
    {

    }
    //RESTFul
    //get /compareRestFulUsers/1 (获取用户1的信息)
    public function compareRestFulUsers()
    {

    }

    //(new Chapter03())->test(function(){return 11;});
    public function test(?callable $function)
    {
        $a = $function();
        return $a ;
    }

    //Json Api 名称空间
    public function nameSpace5()
    {
        $json = '{"name":"Tome","age":"17"}';
        $jsonNameSpace = '{"data":[{
            "name":"Tome","age":"17"
        }]}';
    }
}
```

#### 4  : 状态码,错误,信息
 
 1.HTTP状态代码、自定义错误代码和消息

 2.HTTP状态码

>2xx是关于成功的(在发送响应之前，客户机尝试做的任何事情都是成功的。请记住，像202 accept这样的状态并不表示实际结果，它只表示接受了一个请求并正在异步处理。)
>
>3xx是关于重定向的(这些都是关于将调用应用程序发送到实际资源的其他地方。其中最著名的是303 See Other和301永久移动，web上经常使用它们将浏览器重定向到另一个URL。)
>
>4xx是关于客户端错误的
>
>5xx是关于服务器的

![http_code.jpg][1]

[1]: /assets/image/http_code.jpg
  
 3.错误代码和错误消息
   以编程的方式去检查错误代码(测试脚本)

 4.错误或者更多错误
   (通常情况下,在验证的时候,有一个错误后就会终止控制器)
   (一次性验证所有的信息,若有错就返回所有的错误信息)

 5.错误响应标准
 
>Json Api: https://jsonapi.org
>
>RFC : https://tools.ietf.org/html/draft-nottingham-http-problem-07
>
>Crell/ApiProblem PHP: https://github.com/Crell/ApiProblem

 6.常见的陷阱
 
   200 有错误信息 ×
```

/** 状态码,错误,信息
 * Class Chapter04
 * @package bookLog\buildApis
 */
class Chapter04
{
    /** 单个错误提示
     * @return string
     */
    public function errorOne()
    {
        $backData = [
            'code' => '400' ,
            'messages'=>'操作错误!',
            'errors'=>[],
        ] ;
        return json_encode($backData,true);
    }

    /** 多个错误提示
     * @return string
     */
    public function errorMore()
    {
        $backData = [
            "errors" => [
                [
                    'code'=>'400',
                    'messages'=>'操作错误!',
                ],
                [
                    'code'=>'403',
                    'messages'=>'无操作权限!',
                ]
            ]
        ];
        return json_encode($backData,true);
    }

    /** Json API 返回格式
     * @return string
     */
    public function errorJsonApi()
    {
        //错误 : [{ 代码,标题,详细信息,问题的更多细节 }]
        $json = '{
        "errors": 
            [{
            "code": "ERR-01234",
            "title": "OAuth Exception",
            "details": "Session has expired at unix time 1385243766. The current unix time is 1385848532.",
            "href": "http://example.com/docs/errors/#ERR-01234"
            }] 
        }';
        return $json ;
    }
}
```

#### 5  : 端点测试

1.介绍API测试
 
2.概念和工具

>对于API，有一些东西需要测试，但最基本的思想是，“当我请求这个URL时，我想看到一个foo资源”，“当我向API抛出一个JSON时，它应该接受它或拒绝它。”

TDD(测试驱动开发):

>API接口测试,如果有很多接口,那么测试代码就会越写越多,一处报错整体无法运行 phpunit

BDD(行为驱动开发) :

>cucumber(ruby) : https://cucumber.io/
>
>behat(php) : http://docs.behat.org/en/latest/
       
行为驱动开发（BDD）采取的立场是:
>
>您可以简单有效地将需求的想法转变为实施，测试，生产就绪的代码，只要要求足够具体，每个人都知道发生了什么。
>
>要做到这一点，我们需要一种方法来描述需求，以便每个人 - 业务人员，分析师，开发人员和测试人员 - 对工作范围有共同的理解。
>
>由此他们可以达成共同的“完成”定义，并且我们摆脱了“那不是我要求的”或“我忘了告诉你关于其他事情” 的双重陷阱。
 
3.安装(behat)
    
    composer require --dev behat/behat
    
    ./vendor/bin/behat -V  查看当前版本
 
4.初始化

    ./vendor/bin/behat --init (会自动生成框架所需文件=>features\bootstrap\)
 
  5.特性(编码)，在features\bootstrap\FeatureContext.php中进行API测试编写
  
|Action  |       Endpoint  |      Feature |
| --------   | -----   | ---- |
|Create |        POST |           /users features/users.feature |
|Read |          GET |            /users/X features/users.feature |
|Update |        PUT |            /users/X features/users.feature |
|Delete |        DELETE |         /users/X features/users.feature |
|List |          GET |            /users features/users.feature |
|Image |         PUT |            /users/X/image features/users-image.feature |
|Favorites |     GET |            /users/X/favorites features/users-favorites.feature |
|Checkins |      GET |            /users/X/checkins features/users-checkins.feature |
 
  6.示例
```

  Endpoint Testing (测试):
  Feature: Places
  Scenario: Finding a specific place
       When I request "GET /places/1"
       Then I get a "200" response
       And scope into the "data" property
           And the properties exist:
               """
               id
               name
               lat
               lon
               address1
               address2
               city
               state
               zip
               website
               phone
               """
           And the "id" property is an integer
  Scenario: Listing all places is not possible
       When I request "GET /places"
       Then I get a "400" response
  Scenario: Searching non-existent places
       When I request "GET /places?q=c800e42c377881f8ae509cf9a516d4eb59&lat=1&lon=1"
       Then I get a "200" response
       And the "data" property contains 0 items
  Scenario: Searching places with filters
       When I request "GET /places?lat=40.76855&lon=-73.9945&q=cheese"
       Then I get a "200" response
       And the "pagination" property is an object
       And the "data" property is an array
       And scope into the first "data" property
           And the properties exist:
               """
               id
               name
               lat
               lon
               address1
               address2
               city
               state
               zip
               website
               phone
               """
           And reset scope
```
 
  7.编写behat
 
  8.运行behat
  
    ./vendor/bin/behat
 
9.其他
  
/ ** ... * /是PHP中的一种称为doc-block的特殊语法。它在运行时可被发现，并被不同的PHP框架用作为类，方法和函数提供附加元信息的方法。

Behat使用doc-blocks进行步骤定义，步骤转换和钩子。首先，像Behat这样的工具实际上关闭了故事的沟通循环。

这意味着不仅您和您的利益相关者可以共同定义您的功能在实现之前应该如何工作，BDD工具允许您在实现此功能后自动执行该行为检查。

所以每个人都知道什么时候完成以及团队何时可以停止编写代码。

从本质上讲，这就是Behat。

#### 6  : 输出数据
1.介绍：输出数据

2.直接法：每个开发人员要做的第一件事就是使用他们最喜欢的

ORM(对象关系映射)、ODM、DataMapper或Query Builder，调出一个查询，然后将结果直接导入输出。
>性能(获取所有数据时,数据过大)
>
>显示(格式都为json)
>
>安全性(非必要字段)
>
>稳定性(v1、v2、v3，不同版本号)

3.分形变换

简单的API数据可以用json_encode,复杂多变的数据json_encode输出类型可能改变,

提供一个单独的API输出方法或者类

http://fractal.thephpleague.com/

https://marshmallow.readthedocs.io/en/3.0/

4.隐藏模式更新

  'website' => $data->website  update  'website' => $data->url
  
5.输出错误

  API: 400 ,404 ,403
  
6.测试输出(有效输出)

#### 7  : 数据关系

1.介绍
>    API输出的关系不需要直接映射到数据库关系。
>
>    如果正确地构建了数据库关系，那么关系通常是相似的，但是输出可能具有额外的动态关系，这些关系不是由连接定义的，而且不一定包含所有可能的数据库关系。
>
>   REST组件:https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm#sec_5_2
 
2.子资源
>Station: 一个区域有50个地点
>
>Get place/x/info  info(地点,经纬度,图像,相关信息,[这里假设是4个请求])
>请求数: 1 + (50×4) = 251 ;请求太过频繁
>
>Get places 一个请求获取所有的数据,数据量太大,对客户端不友好.
>
>Get places/main 获取地点的主要信息(图像) 点击时获取其他信息 Get place/x/info
>
>这里的权衡是，下载足够的数据以避免用户等待后续加载和下载太多数据以使他们等待初始加载是困难的。
>API需要这种灵活性，而将子资源作为加载相关数据的唯一方式对于API使用者来说是有限制的。
 
  3.外键数组
  
       {
           "post": {
               "id": 1,
               "title": "Progressive Enhancement is Dead",
               "_links": {
                           "comments": ["1", "2"]
                         }
               }
       }
       
>使用 _links作为外键数组,即有数据的时候需要再次请求
>
>Get comments/1  Get comments/2  或 Get comments/1,2
>
>变向的减少了http并发请求，
>缺点是API使用者必须将所有这些数据连接在一起，这对于大型数据集来说可能需要大量的工作。
 
4.复合文件(边读)：也是需要做大量的拼接和映射
 
参考地址： https://canvas.instructure.com/doc/api/file.compound_documents.html

获取作者有那些文章
```
{
    "posts": [
                {
                    "id": "1",
                    "title": "Awesome API Book",
                    "_links": { "comments": ["1","2"] }
                },
                {
                    "id": "2",
                    "title": "But Really That API Book",
                    "_links": { "comments": ["3"] }
                }
            ],
    "_linked": {
        "comments": [
                {
                    "id": "1",
                    "message": "Great book",
                    "created_at": "2014-08-23T18:20:03Z"
                },
                {
                    "id": "2",
                    "message": "I lolled",
                    "created_at": "2014-08-24T20:04:01Z"
                },
                {
                    "id": "3",
                    "message": "Ugh JSON-API...",
                    "created_at": "2014-08-29T14:01:13Z"
                }
            ]
    }
}
```
 

 5.嵌入式文档(嵌套)
 ```
Get place?include=img,merchant,check
{
    "data": [
        {
            "id": 2,
            "name": "Videology",
            "lat": 40.713857,
            "lon": -73.961936,
            "created_at": "2013-04-02",
            "check": [],
            "merchant": [],
            "img": []
        }
    ]
}

用Rails嵌入 (将公用信息提取出来,进行分层)
{
    "id": 1,
    "name": "Konata Izumi",
    "age": 16,
    "created_at": "2006/08/01",
    "awesome": true,
    "posts": [
        {
            "id": 1,
            "author_id": 1,
            "title": "Welcome to the weblog"
        },
        {
            "id": 2,
            "author_id": 1,
            "title": "So I was thinking"
        }
    ]
}
```


6.总结：根据业务数据进行分析

http://fractal.thephpleague.com/serializers/

#### 8  : 调试

1.介绍

命令行调试,浏览器调试,网络调试

一般来说调试为 发送请求(请求参数,请求体,报文,浏览器信息等等),接收响应(响应数据,响应格式,响应体等等)
     
2.命令行调试,发送curl请求
     
3.浏览器调试

HTTP客户端(postman) : @link https://www.getpostman.com/

调试面板
>发条: https://github.com/itsgoingd/clockwork-chrome (php)
>
>Chrome Logger : https://craig.is/writing/chrome-logger (多平台)
>
>tampermonkey : https://tampermonkey.net/  (chrome 脚本控制)
>
>Chrome Logger(PHP版)使用
   
4.网络调试(工具调试)

>Charles : https://www.charlesproxy.com/ (windows)
>
>Wireshark : https://www.wireshark.org/ (Linux/OS/windows)
>
>本地和远程 (可配置的调试)
  
#### 9  : 身份验证
1.介绍

使用带有cookie、Memcache、Redis、Mongo或某些SQL平台等数据存储的会话被广泛接受为标准行为。

2.什么时候验证

只读api(不需要认证的),一般情况下提供一个账号给调用者使用,方便控制恶意请求,恶意数据,

内部API(运行在内部环境,或者局域网中的),可以跳过身份认证

3.不同的身份认证方法

3.1 SSL(Secure Sockets Layer 安全套接层),及其继任者传输层安全（Transport Layer Security，TLS）是为网络通信提供安全及数据完整性的一种安全协议

3.2 HTTP Basic :
>HTTP基本身份验证(BA)实现是对web资源实施访问控制的最简单技术，因为它不需要cookie、会话标识符和登录页面。
>
>相反，HTTP基本身份验证使用静态的标准HTTP报头，这意味着不需要预先握手。 ——来源:维基百科
>
>(易实现,易理解,工作在浏览器和任何其他HTTP客户端)(HTTP上非常不安全,HTTPS相当不安全,密码由浏览器保存不安全)

3.3 摘要身份认证: 摘要是一种类似于Basic的身份验证方法，但旨在改进安全性问题。

3.4 OAuth 1.0 |1.0a (OAuth Token and OAuth Token Secret) : 无法轻松访问浏览器的移动和桌面应用程序设计
>
    POST/moments/1/giftHTTP/1.1
    Host: api.example.com
    Authorization: OAuthrealm="http://sp.example.com/",
    oauth_consumer_key="0685bd9184jfhq22",
    oauth_token="ad180jjd733klru7",
    oauth_signature_method="HMAC-SHA1",
    oauth_signature="wOJIO9A2W5mFwDgiDvZbTSMK%2FPY%3D",
    oauth_timestamp="137131200",
    oauth_nonce="4572616e48616d6d65724c61686176",
    oauth_version="1.0"11Content-Type: application/json1213
    {  "user_id": 2 }
   
3.5 OAuth 2.0 : 删除了秘密令牌,只需获得一个访问令牌。
>
    POST /moments/1/gift HTTP/1.1
    Host: api.example.com
    Authorization: Bearer vr5HmMkzlxKE70W1y4Mi
    Content-Type: application/json
    { "user_id" : 2 }
    无论何时，您都应该尝试使用授权头来发送令牌。
    生命令牌(任意一段时间后过期)
    Grant Types
    其他认证:
        OpenId : https://openid.net/
        Hawk   : https://github.com/hueniverse/hawk
        Oz     : https://github.com/hueniverse/oz
        
4.实现OAuth 2.0服务器

   http://oauth2.thephpleague.com/installation/
   
   http://bshaffer.github.io/oauth2-server-php-docs/
   
5.其他服务器OAuth 2.0 (Ruby,python,rack,...)

6.理解OAuth 2.0 授权类型

     授权代码 : (多站点共享登录) => (规范)    https://tools.ietf.org/html/rfc6749#section-4.1
     刷新令牌 : 一直使用相同的令牌可能会被破解 https://tools.ietf.org/html/rfc6749#section-6
     客户端凭证 : 我是一个应用程序,你知道我是一个应用程序,
                 因为这是我的client_id和client_secret值 . 认证过后,请让我通行!
                 http://tools.ietf.org/html/rfc6749
     密码(用户凭证) : 用户凭据可能是为用户获取访问令牌的最简单方法。
                     这就跳过了“身份验证代码”提供的整个重定向流，以及随之而来的用户心态平和，但确实提供了简单性。
                     https://tools.ietf.org/html/rfc6749#section-4.3
     自定义授权类型 :  access_token
                     eg : 登录
                         获取用户的数据
                         找出他们是否是一个系统用户，如果不是，创建一个系统用户记录
                         创建访问令牌、刷新令牌等，以给予该用户访问权
                         
```
class Chapter09
{
    public function HttpBasic()
    {
        //$data = "eyJ2ZXJzaW9uIjoiNC4xLjAiLCJjb2x1bW5zIjpbImxvZyIsImJhY2t0cmFjZSIsInR5cGUiXSwicm93cyI6W1tbMjNdLCJEOlxcTWluXFxJbnN0YWxsXFxwaHBzdHVkeVxcUEhQVHV0b3JpYWxcXFdXV1xcU2VsZlxcTXlPYmpTdW1tYXJ5XFx0cnkucGhwIDogNSIsIiJdLFtbeyJET0NVTUVOVF9ST09UIjoiRDpcXE1pblxcSW5zdGFsbFxccGhwc3R1ZHlcXFBIUFR1dG9yaWFsXFxXV1dcXFNlbGZcXE15T2JqU3VtbWFyeSIsIlJFTU9URV9BRERSIjoiOjoxIiwiUkVNT1RFX1BPUlQiOiI2MTAxOSIsIlNFUlZFUl9TT0ZUV0FSRSI6IlBIUCA3LjEuMTMgRGV2ZWxvcG1lbnQgU2VydmVyIiwiU0VSVkVSX1BST1RPQ09MIjoiSFRUUFwvMS4xIiwiU0VSVkVSX05BTUUiOiJsb2NhbGhvc3QiLCJTRVJWRVJfUE9SVCI6IjIwMDAyIiwiUkVRVUVTVF9VUkkiOiJcL3RyeS5waHAiLCJSRVFVRVNUX01FVEhPRCI6IkdFVCIsIlNDUklQVF9OQU1FIjoiXC90cnkucGhwIiwiU0NSSVBUX0ZJTEVOQU1FIjoiRDpcXE1pblxcSW5zdGFsbFxccGhwc3R1ZHlcXFBIUFR1dG9yaWFsXFxXV1dcXFNlbGZcXE15T2JqU3VtbWFyeVxcdHJ5LnBocCIsIlBIUF9TRUxGIjoiXC90cnkucGhwIiwiSFRUUF9IT1NUIjoibG9jYWxob3N0OjIwMDAyIiwiSFRUUF9DT05ORUNUSU9OIjoia2VlcC1hbGl2ZSIsIkhUVFBfUFJBR01BIjoibm8tY2FjaGUiLCJIVFRQX0NBQ0hFX0NPTlRST0wiOiJuby1jYWNoZSIsIkhUVFBfVVBHUkFERV9JTlNFQ1VSRV9SRVFVRVNUUyI6IjEiLCJIVFRQX1VTRVJfQUdFTlQiOiJNb3ppbGxhXC81LjAgKFdpbmRvd3MgTlQgMTAuMDsgV09XNjQpIEFwcGxlV2ViS2l0XC81MzcuMzYgKEtIVE1MLCBsaWtlIEdlY2tvKSBDaHJvbWVcLzY2LjAuMzM1OS4xMzkgU2FmYXJpXC81MzcuMzYiLCJIVFRQX0FDQ0VQVCI6InRleHRcL2h0bWwsYXBwbGljYXRpb25cL3hodG1sK3htbCxhcHBsaWNhdGlvblwveG1sO3E9MC45LGltYWdlXC93ZWJwLGltYWdlXC9hcG5nLCpcLyo7cT0wLjgiLCJIVFRQX0FDQ0VQVF9FTkNPRElORyI6Imd6aXAsIGRlZmxhdGUsIGJyIiwiSFRUUF9BQ0NFUFRfTEFOR1VBR0UiOiJ6aC1DTix6aDtxPTAuOSIsIlJFUVVFU1RfVElNRV9GTE9BVCI6MTU0NzE3MzM5MS4xNDQyMzgsIlJFUVVFU1RfVElNRSI6MTU0NzE3MzM5MX1dLCJEOlxcTWluXFxJbnN0YWxsXFxwaHBzdHVkeVxcUEhQVHV0b3JpYWxcXFdXV1xcU2VsZlxcTXlPYmpTdW1tYXJ5XFx0cnkucGhwIDogNiIsImluZm8iXV0sInJlcXVlc3RfdXJpIjoiXC90cnkucGhwIn0=";
        //$a = json_decode(utf8_decode(base64_decode($data)));
        //dd($a);
        //base64_encode(utf8_encode(json_encode($data)));

       //dd(base64_encode('test:111'));
       //request header
        /*POST /try.php HTTP/1.1
        Host: localhost:20002
        Authorization: Basic dGVzdDoxMTE=  ( Basic认证方式[Digest,AWS,...]  dGVzdDoxMTE=认证auth )
        Cache-Control: no-cache
        Postman-Token: 15e7429a-5d84-1246-6d2a-f60bc060c01b*/
       $basic = $_SERVER['HTTP_AUTHORIZATION'] ?? '' ; //  Basic dGVzdDoxMTE=
       $basicArr = explode(" ",$basic) ; // ['Basic','dGVzdDoxMTE=']
       switch ($basicArr[0]){
           case 'Basic':
               return  base64_decode($basicArr[1]); // test:111
           default :
               return $basic;
       }
    }
}
```
#### 10 : 分页

1.为了限制HTTP响应大小，可以将数据分成多个HTTP请求。

下载更多的东西需要更长的时间,您的数据库可能不喜欢尝试一次返回100,000条记录

迭代超过100,000条记录的表示逻辑并不有趣,API可以有端点 100,000 可以是任意数字,(定义一个最大值)
         
2.Paginators(laravel 分页器)
```
{
    "data": [
    "..."
],
    "pagination": {
    "total": 1000,
        "count": 12,
        "per_page": 12,
        "current_page": 1,
        "total_pages": 84,
        "next_url": "/places?page=2&number=12"
    }
}
```

3.偏移量和游标

游标通常是唯一标识符或偏移量，因此API只能请求更多数据。

使用偏移量很简单。不管您的id是什么—自动递增的，UUID等等—您只需在其中输入12，
          然后说“我想要12条记录，偏移量为12”，而不是说“我想要id=12之后的记录”。
          
模糊游标: 加密ID . eg: base64_encode(1) // MQ==

额外请求 = 悲伤 : 一些客户端开发人员不喜欢这种方法，因为他们不喜欢必须发出额外的HTTP请求才能发现没有数据的想法。

使用链接头分页

       <https://api.github.com/user/repos?page=3&per_page=100>; rel="next"
       <https://api.github.com/user/repos?page=50&per_page=100>; rel="last"

#### 11 : 文档

phpDocument : https://phpdoc.org/

soundCloudApi : http://developers.soundcloud.com/docs/api/guide

Sculpin : https://sculpin.io/ 是一个用PHP编写的静态站点生成器。它将Markdown文件，Twig模板和标准HTML转换为可轻松部署的静态HTML站点。

Swagger定义了一个规范，各种语言或框架特定于spe的实现都有自己的解决方案。对于PHP，实现这一点的方法是通过一组相当混乱(且文档很少)的带有奇怪名称的注释。
此外，它要求您将这些注释分布到应用程序的一大块区域，包括您可能甚至没有的数据映射器样式模型。
它需要属性级注释，而我的模型和分形转换器都没有属性，所以这是一种疯狂而古怪的尝试和工作方式。
https://swagger.io/
            
Apiary API Blueprint : https://apiary.io/blueprint | https://apiary.io/ (本书推荐)
#### 12 : HATEOAS(超媒体控制)

1.介绍

  HATEOAS : 它代表作为应用程序状态引擎的超媒体，被宣布为hat-ee-os、hate O-A-S或hate-ee-ohs;
            包括 : 内容协商 , 超媒体控制
            更改Accept标头并在响应中将内容类型标头从JSON切换到XML或CSV非常好，而且非常容易做到。
 
2.内容协商

     /status/show.json?id=123  ×   json格式
     /status/show.xml?id=123   ×   xml格式
     
 上面API有点滥用资源概念
 
    /status/123     √ : 这样做的双重好处是，
    允许API使用默认的内容类型进行响应，
    或者尊重Accept头并输出请求内容类型，
    或者在API不支持的情况下输出415状态代码。

> 大多数流行API默认情况下都支持 JSON ,除此之外还有 Xml ,YAml(https://symfony.com/doc/current/components/yaml.html) 等等 .
>    
>URI = Universal Resource Identifier 统一资源标志符
>
>URL = Universal Resource Locator 统一资源定位符
>
>URN = Universal Resource Name 统一资源名称
>
>URI = ( URL or URN or (URL and URN))
 
3.超媒体控制 : 它们只是指向其他内容、关系和进一步操作的链接。

超媒体的基本主题是API应该能够对API客户机应用程序和人的外观产生完美的意义 .

缩写“URI”通常用于仅指协议、主机名和端口之后的内容(意味着URI是路径、扩展名和查询字符串)，而“URL”用于描述完整地址。

    rel 代表关系,uri 通用资源指定器
    {
        "data": [
            "id": 1,
            "name": "Mireille Rodriguez",
            "links": [
                {
                    "rel": "self",
                    "uri": "/places/2"
                },
                {
                    "rel": "place.checkins",
                    "uri": "/places/2/checkins"
                },
                {
                    "rel": "place.image",
                    "uri": "/places/2/image"
                }
            ]
        ]
    }

#### 13 : 版本控制
1.介绍

你会发现大多数专家给出的一般建议是:尽量限制变化。

这是一个非常公平的声明，但似乎有点逃避。

无论您的API规划得多么好，您的业务需求最终都可能迫使您做出重大更改。

2.不同API的版本控制

     URI : 在URI中抛出版本号是流行的公共api中非常常见的做法。 /v1/places   /v2/places
           根据业务 v1 v2可以对应不同的代码,服务器,甚至是编程语言
     Hostname : http://api-v1.com/places  http://api-v2.com/places
     Body And Query Params(主体和查询参数) :
         POST /places HTTP/1.1
         Host: api.example.com
         Content-Type: application/json
         {   "version" : "1.0"  } (自定义版本参数)
     自定义请求头 :
         Request :
             GET /places HTTP/1.1
             Host: api.example.com
             BadApiVersion: 1.0  (自定义header头)
         Response :
             HTTP/1.1 200 OK
             BadAPIVersion: 1.1
             Vary: BadAPIVersion
     内容协商 :
             application/vnd.github[.version].param[+json]
             eg:
                 Accept: application/vnd.github.v3+json|xml|yaml|...
     资源的内容协商 :
              application/vnd.github[.version].param[+json] ; version=1.0
              Accept: application/vnd.github.user.v4+json
              Accept: application/vnd.github.user+json; version=4.0
     特性标记 :
     
3.询问用户

#### 来源
> Build APIs You Won't Hate