## web 结构组件
#### web 服务器
互联网的服务提供者

#### web 客户端
互联网上客户访问服务提供者的入口，比如浏览器，APP，微信小程序

#### 代理
介于客户端与服务端之间，位于客户端中，接受客户端的请求，对请求进行一定的筛选、处理，然后转发到外网访问服务器。

#### 缓存
缓存有很多理解，比如浏览器缓存，DNS缓存，主机缓存，缓存服务器等。其核心思想就是把经常被请求的数据保存在本地或者更容易获得的地方，从而提高访问的速度，解决性能瓶颈，减小服务器的压力。比如redis和mongodb做缓存数据库。

#### 网关
可以理解成翻译器，一台外部服务器，使不同结构，不同协议的互联网主机可以互相访问，可以理解成设计模式中的适配器模式，其中的一个应用方面为安全网关，过渡互联网上不安全不期望的访问。

#### 防火墙
主机本地的安全防护工具，比如端口禁止访问。

#### 反向代理
和代理很像，但是其主要在服务器端中，外网访问服务器时，优先访问反向代理服务器，由反向代理决定由哪台服务器响应，具体的考虑涉及到不同的服务器内容，以及均衡。

#### Agent 代理
客户端代理机，你可以理解成你机器上浏览器，如：google浏览器。

#### 隧道：如SSL
隧道技术：我的理解是建立点对点的连接，如同操作系统的管道一样。


#### URI: 统一资源标识符

- url：统一资源定位符 <br> 基本就是URI的代名词了，通过服务器输入地址访问资源。如：https://www.jianshu.com/p/e403d204984d <br>组成形式为：协议+服务器域名/ip+<端口号>+本地资源路径
- urn：统一资源名 <br> 在URL上提出的一个新的概念，不标记主机地址，近标记资源，因此你可以通过资源标记，获得任意一个可以访问的地方的该资源。类似于P2P下载概念。


#### 报文：
**以下为google开发者工具拦截简书首页请求的报文信息。**
	
	// 通用首部	
	Request URL: https://www.jianshu.com/
	Request Method: GET 
	Status Code: 200 OK
	Remote Address: 183.214.140.231:443
	Referrer Policy: unsafe-url
	
请求报文

	//请求首部
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
	Accept-Encoding: gzip, deflate, br
	Accept-Language: zh-CN,zh;q=0.9
	Cache-Control: no-cache			// 不缓存信息
	Connection: keep-alive			// 保持连接一定的时间
	Host: www.jianshu.com
	Pragma: no-cache

响应报文
	
	// 响应首部	
	Cache-Control: max-age=0, private, must-revalidate
	Connection: keep-alive
	Content-Encoding: gzip
	Content-Type: text/html; charset=utf-8 // 媒体类型 MIME
	Date: Sat, 18 Aug 2018 08:54:20 GMT	  // date 时间

报文以键值对的方式发送，data以json或xml（建议json）的形式传输。很多键名已经显示出其功能含义，不做详细介绍。其中媒体类型 MIME，在响应中标记为	Content-Type，例如: text/html、image/png（主分类/次分类）。

#### Method分为：

- get： 从服务器获取资源 ？带参数，&链接多个参数
- post： 向服务器发送需要处理的数据
- put/patch： 向服务器增加数据
- delete：删除文件
- head： 只获取文档的首部
- trace： 对报文进行追踪

#### code状态码：
- 100-199：信息提示
- 200-299:成功
- 300-399:重定向
- 400-499:客户端错误
- 500-599:服务器错误

#### 常见状态码：
- 200:成功
- 401:未授权
- 404:未找到
- 500:内部服务器错误

虽然分为以上这么多方法，但是功能基本可以通过get和post方法都能实现，因此hybrid风格的接口下仅用该两种方案即可完成客户端和浏览器之间的交互。感兴趣的同学可以了解下restful风格的接口。

---

### 浏览器输入网址的一系列动作：

![握手](http://upload-images.jianshu.io/upload_images/13390267-728159dcda80904c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![拜拜](http://upload-images.jianshu.io/upload_images/13390267-10c631b537cb2ced.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 浏览器解析出主机名
2. 通过DNS查询主机IP
	- 缓存查询：浏览器缓存-->操作系统缓存+hosts解析
	- DNS代理服务器递归查询: 递归查询类似于栈
	- 外网（根）服务器迭代查询：迭代查询类似，访问，获得一个指向，获得另一个访问，另一个指向...最后的访问，获得结果。
3. 获取端口号
4. 发起请求
5. 建立TCP连接：建立端到端的连接
	- 三次握手
	- 1. 客户端发送SYN连接请求
	- 2. 服务器发送SYN连接请求，并且ACK
	- 3. ACK 建立连接
	- 从网络不可信，服务器端资源有限去思考为什么需要三次握手。
6. 建立HTTP/HTTPs连接
7. 传输数据
8. 中断HTTP/HTTPs连接
9. 中断TCP连接
	- 四次拜拜，图中以客户端发起为例，其中客户端和服务器都可以发起拜拜请求
	- 1.客户端发起FIN中止请求，进入可以接受数据的状态
	- 2.服务器接受到FIN请求，如果有数据未发送完，继续发送数据，否则应答FIN请求。
	- 3.客户端接受到FIN后进入到等待态（维持2MSL），避免服务器没有接受到ACK而再次发起FIN的情况。
	- 4.服务器接受到ACK，关闭连接。


#### cookie & session 与认证

#####cookie
以键值对的方式存储在本地浏览器，当浏览器访问服务器的时候带上cookie信息，便于服务器识别客户机，可以实现购物车等功能。建议设定会话cookie，关闭浏览器的时候，自动删除本地cookie。
#####session
和cookie相对应，保存在服务器的客户机信息。当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。
#####Oauth
![Oauth](http://upload-images.jianshu.io/upload_images/13390267-606e02fe3edc0f0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
开放授权认证协议，如上图资源拥有者通过授权，使得不同的client能够在不获取密码的情况下获得相应的权限访问相应的资源信息，下面会以微信为例介绍该内容，关于Oauth的更多内容可以参看阮一峰的博客[阮一峰Oauth](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)。
#####token
token：应该放在请求体中，而不是cookie中。防止CSRF（跨站点请求伪造）攻击的圣器。可以理解成服务器对于相应的客户端登录后，对一定信息（一般会带上当前时间戳）进行加密发送给客户端的数据，客户端每次访问的时候带着这个token使得服务器有理由相信这是一个靠谱的客户端。当然你也可以简单实现，不过那样安全性就降低了。


#### 微信小程序以及微信JS SDK认证机制：
**小程序登录：**
![](https://upload-images.jianshu.io/upload_images/13390267-b1178cf12ea190be.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

php代码实现：

        $code = user_code;
        $appid = youAppid;
        $secret = youSecret;
        $url = 'https://api.weixin.qq.com/sns/jscode2session?appid='.$appid.'&secret='.$secret.'&js_code='. $code .'&grant_type=authorization_code';
        // 获取url返回的数据，并且解析
        $wechatRes =file_get_contents($url);
        // json 解析
        $resp = json_decode($wechatRes, true);

微信JS SDK认证机制:

[官网API地址](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)

PHP代码实现：

	class WxHandle
	{
	    public function getAccessToken()
	    {
	        $appid = env('APPID');
	        $appSecret = env('APPSECRET');
	
	        $tokenUrl = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=".$appid."&secret=".$appSecret;
	
	        $weResp = file_get_contents($tokenUrl);
	        if($weResp.statusCode == 0)
	        {
	            $weRespJson = json_decode($weResp);
	            $accessToken = $weRespJson['access_token'];
	            return $accessToken;
	        }
	        else
	        {
	            return -1;
	        }
	
	    }
	
	    public function getJsapiTicket()
	    {
	        $accessToken = $this->getAccessToken();
	        if( $accessToken == -1)
	        {
	            return -1;
	        }
	
	        $ticketUrl = 'https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token='.$accessToken.'&type=jsapi';
	        $weResp = file_get_contents($ticketUrl);
	        $weRespJson = json_decode($weResp);
	        if( $weRespJson['errcode'] == 0)
	        {
	            $jsapiTicket = $weRespJson['ticket'];
	            return $jsapiTicket;
	        }
	        else{
	            return -2;
	        }
	
	    }
	
	
	    public function getParam()
	    {
	        $noncestr= 'noncestr=' . 'Wm3zbqTPz0wxwlnW';
	
	        $jsapi_ticket= $this->getJsapiTicket();
	        if( $jsapi_ticket == -1 || $jsapi_ticket == -2 )
	        {
	            return $jsapi_ticket;
	        }
	        $jsapi_ticket= 'jsapi_ticket='. $jsapi_ticket;
	        $timestamp= 'timestamp=' . time();
	        $url= 'url=' . env('SIGURL');
	
	        // 对数组进行字典排序
	        $strArray = array($jsapi_ticket, $noncestr, $timestamp, $url);
	        sort($strArray);
	        $string1 = '';
	        foreach ($strArray as $value) {
	            $string1 = '&' . $value;
	        }
	
	        // 去除多余的字符以及计算签名
	        $string1 = substr($string1,1);
	        $signature = sha1($string1);
	
	
	        return array($noncestr, $signature, $timestamp);
	    }
	}

#### CDN
内容分发网络（Content Delivery Network）：包括

- 分布式存储：数据储存在多个地方，冗余备份。
- 负载均衡：通过算法（如Nginx）实现负载均衡。
	- weight 轮询（默认）
	- ip_hash：根据ip计算hash
	- url_hash：根据url计算hash
	- fair：具体不详
- 网络请求的重定向
- 内容管理4个要件

主要作用就是通过以上技术，使得用户能够在浩瀚的互联网中以最快的速度获得自己想要的数据，因此CDN常用于加速。


#### 常见攻击
XSS 攻击（cross-site scripting跨域脚本攻击）

- 注入型：直接存在于页面，无须经过服务器返回就是基于本地的XSS攻击。
- 反射型：主要依靠站点服务端返回脚本，在客户端触发执行从而发起Web攻击。
- 存储型：通过留言注入恶意脚本，脚本存储在服务器，每次读取出来时后触发。
因此，只要有用户输入的地方都应该小心的进行检查，对其进行转义（htmlspecialchars）。

SQL 注入

通过拼写SQL语句获取数据库数据，因此最好对SQL进行预编译，避免此类攻击。

CSRF 攻击

![](https://upload-images.jianshu.io/upload_images/12031442-0ea7799941275e57.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/375)	
CSRF攻击可以伪造用户的请求，该请求中所有的用户验证信息都存在于Cookie中，因此攻击者可以在不知道这些验证信息的情况下直接利用用户自己的Cookie来通过安全验证。

抵御CSRF攻击的关键在于：在请求中放入攻击者所不能伪造的信息并且不存在于Cookie之中，现有技术为token，如果请求中没有token或者token内容不正确，则认为可能是CSRF攻击而拒绝该请求。

	
#### 再谈HTTP和HTTPs

HTTP： 默认80端口，不安全

HTTPs：采用HTTPS协议的服务器必须要有一套数字证书，将隧道技术和密码、认证用到HTTP中，在HTTP基础上多了一层SSL层。 默认443端口。

HTTPs加密过程：

1. 通过封装SSL层，浏览器将自己支持的一套加密规则发送给网站。

2. 网站从加密规则中选出一组加密算法与HASH算法，并将自己的数字证书发回给浏览器。

3. 获得网站证书之后：a)验证证书的合法性，如果证书受信任，则浏览器栏里面会显示一个小锁头，否则会给出证书不受信的提示。b) 如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密。c) 使用约定好的HASH计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。

4. 服务器接收浏览器的数据之后：a) 使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证HASH是否与浏览器发来的一致。b) 使用密码加密一段握手消息，发送给浏览器。

5. 浏览器解密并计算握手消息的HASH，如果与服务端发来的HASH一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密。

HTTPS一般使用的加密与HASH算法如下：

- 非对称加密算法：RSA，DSA/DSS
- 对称加密算法：AES，RC4，3DES
- HASH算法：MD5，SHA1，SHA256





