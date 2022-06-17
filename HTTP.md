### 1. 基础

---



#### HTTP是啥？

- 超文本传输协议（HTTP，Hyper Text Transfer Protocol）是互联网上应用最为广泛的—种网络协议。

- **HTTP协议是在Web上进行数据交换的基础，是一种“客户端-服务器端”协议。**也就是说，请求通常是由像刘览器这样的接受方发起的。一个完整的Web文档通常是由不同的子文档拼接而成的，像是文本、布局描述、图片、视频、脚本等等。

- 设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。





#### 基本概念

**1. 报文格式**

客户端发送一个请求报文给服务器，服务器根据请求报文中的信息进行处理，并将处理结果放入响应报文中返回给客户端。



请求报文

- 第一行是包含了请求方法、URL、协议版本；
- 接下来的多行都是请求首部 Header，每个首部都有一个首部名称，以及对应的值。
- 一个空行用来分隔首部和内容主体 Body
- 最后是请求的内容主体

```http
GET http://www.example.com/ HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cache-Control: max-age=0
Host: www.example.com
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947+gzip"
Proxy-Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 xxx

param1=1&param2=2
```



响应报文

- 第一行包含协议版本、状态码以及描述，最常见的是 200 OK 表示请求成功了
- 接下来多行也是首部内容
- 一个空行分隔首部和内容主体
- 最后是响应的内容主体

```http
HTTP/1.1 200 OK
Age: 529651
Cache-Control: max-age=604800
Connection: keep-alive
Content-Encoding: gzip
Content-Length: 648
Content-Type: text/html; charset=UTF-8
Date: Mon, 02 Nov 2020 17:53:39 GMT
Etag: "3147526947+ident+gzip"
Expires: Mon, 09 Nov 2020 17:53:39 GMT
Keep-Alive: timeout=4
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Proxy-Connection: keep-alive
Server: ECS (sjc/16DF)
Vary: Accept-Encoding
X-Cache: HIT

<!doctype html>
<html>
<head>
    <title>Example Domain</title>
	// 省略... 
</body>
</html>
```





**2.URL**

HTTP 使用 URL（ Uniform Resource Locator，统一资源定位符）来定位资源，它是 URI（Uniform Resource Identifier，统一资源标识符）的子集，URL 在 URI 的基础上增加了定位能力。URI 除了包含 URL，还包含 URN（Uniform Resource Name，统一资源名称），它只是用来定义一个资源的名称，并不具备定位该资源的能力。例如 urn:isbn:0451450523 用来定义一个书籍名称，但是却没有表示怎么找到这本书。

![image-20220324222735184](F:\Typora\笔记\Go语言笔记图片\image-20220324222735184.png)





#### HTTP方法

客户端发送的 **请求报文** 第一行为请求行，包含了方法字段。



1.GET

> 获取资源

当前网络请求中，绝大部分使用的是 GET 方法。



2.HEAD

> 获取报文首部

和 GET 方法类似，但是不返回报文实体主体部分。

主要用于确认 URL 的有效性以及资源更新的日期时间等。



3.POST

> 传输实体主体

POST 主要用来传输数据，而 GET 主要用来获取资源。



4.PUT

> 上传文件 

由于自身不带验证机制，任何人都可以上传文件，因此存在安全性问题，一般不使用该方法。

```html
PUT /new.html HTTP/1.1
Host: example.com
Content-type: text/html
Content-length: 16

<p>New File</p>
```



5.PATCH

> 对资源进行部分修改

PUT 也可以用于修改资源，但是只能完全替代原始资源，PATCH 允许部分修改。

```html
PATCH /file.txt HTTP/1.1
Host: www.example.com
Content-Type: application/example
If-Match: "e0023aa4e"
Content-Length: 100

[description of changes]
```



6.DELETE

> 删除文件

与 PUT 功能相反，并且同样不带验证机制。

```html
DELETE /file.html HTTP/1.1
```



7.OPTIONS

> 查询支持的方法 

查询指定的 URL 能够支持的方法。

会返回 `Allow: GET, POST, HEAD, OPTIONS` 这样的内容。



8.CONNECT

> 要求在与代理服务器通信时建立隧道

使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容加密后经网络隧道传输。

```html
CONNECT www.example.com:443 HTTP/1.1
```

![image-20220324222752414](F:\Typora\笔记\Go语言笔记图片\image-20220324222752414.png)



9.TRACE

> 追踪路径

服务器会将通信路径返回给客户端。

发送请求时，在 Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1，当数值为 0 时就停止传输。

通常不会使用 TRACE，并且它容易受到 XST 攻击（Cross-Site Tracing，跨站追踪）。







#### GET和POST的区别

1.get通过url发送数据，post放在request body中（也就是通过报文发送数据）

2.get发送的数据长度有限制，而post没有。

3.get比post更不安全，因为参数直接暴露在url中，所以不能用来传递敏感信息。

4.get请求参数会被完整保留在浏览历史记录里，而post中的参数不会被保留。

5.get请求会被浏览器主动cache，而post不会，除非手动设置。

💥6.GET产生一个TCP数据包；POST产生两个TCP数据包。

​		对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；

​		而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

(也就是说，GET只需要汽车跑一趟就把货送到了，而POST得跑两趟，第一趟，先去和服务器打个招呼“嗨，我等下要送一批货来，你们打开门迎接我”，然后再回头把货送过去。)



附言：

因为POST需要两步，时间上消耗的要多一点，看起来GET比POST更有效。因此Yahoo团队有推荐用GET替换POST来优化网站性能。但这是一个坑！跳入需谨慎。为什么？

1. GET与POST都有自己的语义，不能随便混用。

2. 据研究，在网络环境好的情况下，发一次包的时间和发两次包的时间差别基本可以无视。而在网络环境差的情况下，两次包的TCP在验证数据包完整性上，有非常大的优点。

3. 并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。







#### HTTP状态码

服务器返回的 <font color=red>**响应报文**</font> 中第一行为状态行，包含了状态码以及原因短语，用来告知客户端请求的结果。

| 状态码 |               类别               |            含义            |
| :----: | :------------------------------: | :------------------------: |
|  1XX   |  Informational（信息性状态码）   |     接收的请求正在处理     |
|  2XX   |      Success（成功状态码）       |      请求正常处理完毕      |
|  3XX   |   Redirection（重定向状态码）    | 需要进行附加操作以完成请求 |
|  4XX   | Client Error（客户端错误状态码） |     服务器无法处理请求     |
|  5XX   | Server Error（服务器错误状态码） |     服务器处理请求出错     |

1XX 信息

- 100 Continue ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应。



2XX 成功

- 200 OK
- 204 No Content ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用。
- 206 Partial Content ：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容。



3XX 重定向

- 301 Moved Permanently ：永久性重定向
- 302 Found ：临时性重定向
- 303 See Other ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。

注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。

- 304 Not Modified ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。

- 307 Temporary Redirect ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。



4XX 客户端错误

- 400 Bad Request ：请求报文中存在语法错误。
- 401 Unauthorized ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。
- 403 Forbidden ：请求被拒绝。
- 404 Not Found



5XX 服务器错误

- 500 Internal Server Error ：服务器正在执行请求时发生错误。





#### HTTP首部

[HTTP协议常用头部实例详解（Request、Response）](https://blog.csdn.net/selinda001/article/details/79338766)

[HTTP常用头部信息](https://blog.csdn.net/qq_37910492/article/details/84646564?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~LandingCtr~default-1.queryctr&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~LandingCtr~default-1.queryctr&utm_relevant_index=1)

有 4 种类型的首部字段：通用首部字段、请求首部字段、响应首部字段和实体首部字段。

各种首部字段及其含义如下（不需要全记，仅供查阅）：

![image-20220323224541305](F:\Typora\笔记\Go语言笔记图片\image-20220323224541305.png)

![image-20220323224607604](F:\Typora\笔记\Go语言笔记图片\image-20220323224607604.png)

![image-20220323224648288](F:\Typora\笔记\Go语言笔记图片\image-20220323224648288.png)

![image-20220323224709423](F:\Typora\笔记\Go语言笔记图片\image-20220323224709423.png)











### 2.Cookies

HTTP 协议是无状态的，主要是为了让 HTTP 协议尽可能简单，使得它能够处理大量事务。HTTP/1.1 引入 Cookie 来保存状态信息。

<font color=red>Cookie 是服务器发送到用户浏览器并保存在本地的一小块数据</font>，它会在浏览器之后向同一服务器再次发起请求时被携带上，用于告知服务端两个请求是否来自同一浏览器。由于之后每次请求都会需要携带 Cookie 数据，因此会带来额外的性能开销（尤其是在移动环境下）。

Cookie 曾一度用于客户端数据的存储，因为当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被淘汰。新的浏览器 API 已经允许开发者直接将数据存储到本地，如使用 Web storage API（本地存储和会话存储）或 IndexedDB。





#### 用途

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）







#### 创建过程

服务器发送的响应报文包含 Set-Cookie 首部字段，客户端得到响应报文后把 Cookie 内容保存到浏览器中。

```html
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```



客户端之后对同一个服务器发送请求时，会从浏览器中取出 Cookie 信息并通过 Cookie 请求首部字段发送给服务器。

```html
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```







#### 分类

- 会话期 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。
- 持久性 Cookie：指定过期时间（Expires）或有效期（max-age）之后就成为了持久性的 Cookie。

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

如果没有设置过期时间或有效期，那么这个cookie就是会话期cookie，若设置的时间是以前的时间，那么这个cookie会被立即删除。









#### 作用域

Domain 标识指定了哪些主机可以接受 Cookie。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。例如，如果设置 **Domain=mozilla.org**，则 Cookie 也包含在子域名中（如 **developer.mozilla.org**），即<font color=red>多个二级域名共享cookie</font>。

Path 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。例如，设置 Path=/docs，则以下地址都会匹配：

- /docs
- /docs/Web/
- /docs/Web/HTTP








#### JavaScript

浏览器通过 `document.cookie` 属性可创建新的 Cookie，也可通过该属性访问非 HttpOnly 标记的 Cookie。

```html
document.cookie = "yummy_cookie=choco";
document.cookie = "tasty_cookie=strawberry";
console.log(document.cookie);
```









#### HttpOnly

标记为 HttpOnly 的 Cookie 不能被 JavaScript 脚本调用。跨站脚本攻击 (XSS) 常常使用 JavaScript 的 document.cookie API 窃取用户的 Cookie 信息，因此<font color=red>使用 HttpOnly 标记可以在一定程度上避免 XSS 攻击</font>。

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```









#### Secure

标记为 Secure 的 Cookie 只能通过被 HTTPS 协议加密过的请求发送给服务端。但即便设置了 Secure 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，Secure 标记也无法提供确实的安全保障。









#### 🐱‍🐉Session

<font color=red>除了可以将用户信息通过 Cookie 存储在用户浏览器中，也可以利用 Session 存储在服务器端，存储在服务器端的信息更加安全</font>。

Session 可以存储在服务器上的文件、数据库或者内存中。**也可以将 Session 存储在 Redis 这种内存型数据库中，效率会更高**。

使用 Session 维护用户登录状态的过程如下：

- 用户进行登录时，用户提交包含用户名和密码的表单，放入 HTTP 请求报文中；

- 服务器验证该用户名和密码，如果正确则把用户信息存储到 Redis 中，它在 Redis 中的 Key 称为 Session ID；

- 服务器返回的响应报文的 Set-Cookie 首部字段包含了这个 Session ID，客户端收到响应报文之后将该 Cookie 值存入浏览器中；

- 客户端之后对同一个服务器进行请求时会包含该 Cookie 值，服务器收到之后提取出 Session ID，从 Redis 中取出用户信息，继续之前的业务操作。

  

应该注意 Session ID 的安全性问题，不能让它被恶意攻击者轻易获取，那么就不能产生一个容易被猜到的 Session ID 值。此外，还需要经常重新生成 Session ID。在对安全性要求极高的场景下，例如转账等操作，除了使用 Session 管理用户状态之外，还需要对用户进行重新验证，比如重新输入密码，或者使用短信验证码等方式。









#### 浏览器禁用 Cookie

此时无法使用 Cookie 来保存用户信息，只能使用 Session。除此之外，不能再将 Session ID 存放到 Cookie 中，而是使用 URL 重写技术，将 Session ID 作为 URL 的参数进行传递。



**解决办法：URL重写**

URL地址重写是对客户端不支持Cookie的解决方案。URL地址重写的原理是将该用户Session的id信息重写到URL地址中。服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。HttpServletResponse类提供了encodeURL(String url)实现URL地址重写，该方法会自动判断客户端是否支持Cookie。如果客户端支持Cookie，会将URL原封不动地输出来。如果客户端不支持Cookie，则会将用户Session的id重写到URL中。

eg：URL访问路径带上Query参数

```html
https://leetcode-cn.com/leetbook/read/tech-interview-cookbook/opexoj/?sessionid=xxxxx
```









#### Cookie 与 Session 选择

- Cookie 只能存储 ASCII 码字符串，而 Session 则可以存储任何类型的数据，因此在考虑数据复杂性时首选 Session；
- Cookie 存储在浏览器中，容易被恶意查看。如果非要将一些隐私数据存在 Cookie 中，可以将 Cookie 值进行加密，然后在服务器进行解密；
- 对于大型网站，如果用户所有的信息都存储在 Session 中，那么开销是非常大的，因此不建议将所有的用户信息都存储到 Session 中。







#### 🐱‍🐉同一浏览器下多用户登录问题解决

- 问题：在如果在同一个浏览器下只有一个用户登录的情况，在服务端可以使用 [Session](https://so.csdn.net/so/search?q=Session&spm=1001.2101.3001.7020) 存储用户登录信息。但是在项目中如果需要在同一个浏览器下允许多个不同的用户登录，这样做会存在问题，因为服务端区分不同用户是通过 Cookie 中存储的 JSESSIONID 区分的，如果 JSESSIONID 相同，那么他们在服务端将会使用同一个 Session 对象。而同一浏览器使用的 Cookie 是相同的， 从而 JSESSIONID 也是相同的，无法区分不同的用户。当浏览器登录第一个用户后，用户信息写入到 Session 中，第二个用户登录时，将会覆盖第一个用户的登录信息。
- 解决方法：不再依赖 Tomcat 默认的 JSESSIONID 来标识客户端，每进行一次登录后，服务端根据用户信息生成一份令牌，一份写入到数据库中并设置有效时间与身份信息存储在一起，定时清除，另一份返回到客户端。这个令牌可以放到页面的某个隐藏域中，但是不能存放在 Cookie 内，因为这样还是不能唯一区分用户。接下来客户端每次访问服务端都带上该令牌，以供校验身份信息，根据令牌的值，在数据库中找到对应的身份信息。这样就能区分同一浏览器下登录的不同用户了。校验通过后，再将此令牌返回给客户端，以供其下次访问使用。为防止令牌被盗用，尽量使用 https。









### 3. HTTPS

---

#### 概念

HTTP 有以下安全性问题：

- 使用明文进行通信，内容可能会被窃听；
- 不验证通信方的身份，通信方的身份有可能遭遇伪装；
- 无法证明报文的完整性，报文有可能遭篡改。

HTTPS 并不是新协议，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信，也就是说 HTTPS 使用了隧道进行通信。

通过使用 SSL，HTTPS 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）。

<img src="F:\Typora\笔记\Go语言笔记图片\image-20220324222830801.png" alt="image-20220324222830801" style="zoom:80%;" />





#### 加密（防窃听）

**1.对称密钥加密**

对称密钥加密（Symmetric-Key Encryption），加密和解密使用同一密钥。

- 优点：运算速度快；
- 缺点：无法安全地将密钥传输给通信方。





**2.非对称密钥加密**

非对称密钥加密，又称公开密钥加密（Public-Key Encryption），加密和解密使用不同的密钥。

公开密钥所有人都可以获得，通信发送方获得接收方的公开密钥之后，就可以使用公开密钥进行加密，接收方收到通信内容后使用私有密钥解密。

非对称密钥除了用来加密，还可以用来进行签名。因为私有密钥无法被其他人获取，因此通信发送方使用其私有密钥进行签名，通信接收方使用发送方的公开密钥对签名进行解密，就能判断这个签名是否正确。

- 优点：可以更安全地将公开密钥传输给通信发送方；
- 缺点：运算速度慢。





**3.HTTPS 采用的加密方式**

上面提到对称密钥加密方式的传输效率更高，但是无法安全地将密钥 Secret Key 传输给通信方。而非对称密钥加密方式可以保证传输的安全性，因此我们可以利用非对称密钥加密方式将 Secret Key 传输给通信方。HTTPS 采用混合的加密机制，正是利用了上面提到的方案：

- 使用非对称密钥加密方式，传输对称密钥加密方式所需要的 Secret Key，从而保证安全性;
- 获取到 Secret Key 后，再使用对称密钥加密方式进行通信，从而保证效率。







#### 认证（防伪装）

通过使用 **证书** 来对通信方进行认证。

数字证书认证机构（CA，Certificate Authority）是客户端与服务器双方都可信赖的第三方机构。

服务器的运营人员向 CA 提出公开密钥的申请，CA 在判明提出申请者的身份之后，会对已申请的公开密钥做数字签名，然后分配这个已签名的公开密钥，并将该公开密钥放入公开密钥证书后绑定在一起。

进行 HTTPS 通信时，服务器会把证书发送给客户端。客户端取得其中的公开密钥之后，先使用数字签名进行验证，如果验证通过，就可以开始通信了。







#### 完整性保护（防篡改）

<font color=red>SSL 提供报文摘要功能来进行完整性保护</font>。

HTTP 也提供了 MD5 报文摘要功能，但不是安全的。例如报文内容被篡改之后，同时重新计算 MD5 的值，通信接收方是无法意识到发生了篡改。

HTTPS 的报文摘要功能之所以安全，是因为它结合了加密和认证这两个操作。试想一下，加密之后的报文，遭到篡改之后，也很难重新计算报文摘要，因为无法轻易获取明文。







#### 缺点

- 因为需要进行加密解密等过程，因此速度会更慢；
- 需要支付证书授权的高额费用。











### 4.其他

---

#### 🐱‍🐉GET 和 POST 比较

**1.作用**

GET 用于获取资源，而 POST 用于传输实体主体。



**2.参数**

GET 和 POST 的请求都能使用额外的参数，但是 GET 的参数是以查询字符串出现在 URL 中，而 POST 的参数存储在实体主体中。<font color=red>不能因为 POST 参数存储在实体主体中就认为它的安全性更高，因为照样可以通过一些抓包工具（Fiddler）查看</font>。

因为 URL 只支持 ASCII 码，因此 GET 的参数中如果存在中文等字符就需要先进行编码。例如 中文 会转换为 %E4%B8%AD%E6%96%87，而空格会转换为 %20。POST 参数支持标准字符集。

```http
GET /test/demo_form.asp?name1=value1&name2=value2 HTTP/1.1
```

```http
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
```



**3.安全**

安全的 HTTP 方法不会改变服务器状态，也就是说它只是**可读的**。

<font color=red>GET 方法是安全的，而 POST 却不是，因为 POST 的目的是传送实体主体内容，这个内容可能是用户上传的表单数据，上传成功之后，服务器可能把这个数据存储到数据库中，因此状态也就发生了改变</font>。

安全的方法除了 GET 之外还有：HEAD、OPTIONS。

不安全的方法除了 POST 之外还有 PUT、DELETE。







**4.幂等性**

幂等的 HTTP 方法，同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。

所有的安全方法也都是幂等的。

在正确实现的条件下，GET，HEAD，PUT 和 DELETE 等方法都是幂等的，而 POST 方法不是。

(PUT是幂等的，因为PUT做的是覆盖操作，多次PUT同一个值，最终的结果是只有1个。比如修改 用户名为小李多次修改为小李的效果和一次修改是一样的，所以说是幂等的)



GET /pageX HTTP/1.1 是幂等的，连续调用多次，客户端接收到的结果都是一样的：

```http
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
```



POST /add_row HTTP/1.1 不是幂等的，如果调用多次，就会增加多行记录：

```http
POST /add_row HTTP/1.1   -> Adds a 1nd row
POST /add_row HTTP/1.1   -> Adds a 2nd row
POST /add_row HTTP/1.1   -> Adds a 3rd row
```



DELETE /idX/delete HTTP/1.1 是**幂等的**，即使不同的请求接收到的状态码不一样：

```http
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404
```





**5.可缓存**

如果要对响应进行缓存，需要满足以下条件：

- 请求报文的 HTTP 方法本身是可缓存的，包括 GET 和 HEAD，但是 PUT 和 DELETE 不可缓存，POST 在多数情况下不可缓存的。
- 响应报文的状态码是可缓存的，包括：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, and 501。
- 响应报文的 Cache-Control 首部字段没有指定不进行缓存。





**6.XMLHttpRequest**

为了阐述 POST 和 GET 的另一个区别，需要先了解 XMLHttpRequest：

> XMLHttpRequest 是一个 API，它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL 来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX 中被大量使用。

- 在使用 XMLHttpRequest 的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。
- 而 GET 方法 Header 和 Data 会一起发送。







#### HTTP/0.9 1.0 1.1

**1.http/0.9版本**

只有一个命令**GET**,只支持纯文本内容。



**2.http1.0版本**

- **任何格式**的内容都可以发送，这使得互联网不仅可以传输**文字**，还能传输**图像**、**视频**、**二进制**等文件。
- 除了GET命令，还引入了**POST**命令和**HEAD**命令。
- http**请求和回应的格式改变**，除了数据部分，每次通信都必须包括**头信息**（HTTP header），用来描述一些元数据



**3.http1.1版本**

- 引入了持久连接（ persistent connection），即TCP连接默认不关闭，可以被多个请求复用，不用声明Connection: keep-alive。
- 引入了管道机制（ pipelining），即在同一个TCP连接里，客户端可以同时发送多个请求，进一步改进了HTTP协议的效率。
- 新增方法：PUT、 PATCH、 OPTIONS、 DELETE。
- http协议不带有状态，每次请求都必须附上所有信息。请求的很多字段都是重复的，浪费带宽，影响速度。
  



**http1.x版本问题**

- http1.x在传输数据时，所有传输的内容都是**明文**，客户端和服务器端都无法验证对方的身份，无法保证数据的安全性。
- 1.1版本允许复用TCP连接，但是同一个TCP连接里面，所有的数据通信是按次序进行的。服务器只有**处理完一个回应，才会进行下一个回应**，可能会造成Head-of-line blocking的问题。（不支持有效的资源优先级，致使底层 TCP 连接的利用率低下）
- http1.x支持了keep-alive，来弥补多次创建连接产生的延迟，但是keepalive使用多了同样会给服务端带来大量的性能压力，并且对于单个文件被不断请求的服务(例如图片存放网站)， keep-alive可能会极大的影响性能，因为**它在文件被请求之后还保持了不必要的连接很长时间**。

- 不会压缩请求和响应首部，从而导致不必要的网络流量；
- 客户端需要使用多个连接才能实现并发和缩短延迟；







#### HTTP/2.0

- **二进制分帧层**

HTTP/2.0 传输数据量的大幅减少,主要有两个原因：以<font color=red>二进制方式传输</font>和<font color=red>Header 压缩</font>。

我们先来介绍二进制传输，HTTP/2.0 采用二进制格式传输数据，而非HTTP1.x 里纯文本形式的报文 ，二进制协议解析起来更高效。 HTTP/2.0 将请求和响应数据分割为更小的帧，并且它们采用二进制编码。

​	

- **多路复用（MultiPlexing）**

在 HTTP/2.0 中，有了二进制分帧之后，HTTP /2.0 不再依赖 TCP 链接去实现多流并行了，在 HTTP/2.0 中,

1) 同域名下所有通信都在单个连接上完成。
2) 单个连接可以承载任意数量的双向数据流。
3) 数据流以消息的形式发送，而消息又由一个或多个帧组成，多个帧之间可以乱序发送，因为根据帧首部的流标识可以重新组装。

这一特性，使性能有了极大提升：

4. 同个域名只需要占用一个 TCP 连接，使用一个连接并行发送多个请求和响应,这样整个页面资源的下载过程只需要一次慢启动，同时也避免了多个TCP连接竞争带宽所带来的问题。
5. 并行交错地发送多个请求/响应，请求/响应之间互不影响。
6. 在HTTP/2.0中，每个请求都可以带一个31bit的优先值，0表示最高优先级， 数值越大优先级越低。<font color=red>有了这个优先值，客户端和服务器就可以在处理不同的流时采取不同的策略，以最优的方式发送流、消息和帧</font>。
   

![image-20220324222907210](F:\Typora\笔记\Go语言笔记图片\image-20220324222907210.png)





- **Header压缩**

如上文中所言，对前面提到过 HTTP1.x 的 header 带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。

​	



- **服务端推送（Server push）**

HTTP/2.0 还在一定程度上改变了传统的“请求-应答”工作模式，服务器不再是完全被动地响应请求，也可以<font color=red>新建“流”主动向客户端发送消息</font>。比如，在浏览器刚请求HTML的时候就提前把可能会用到的JS、CSS文件发给客户端，减少等待的延迟，这被称为"服务器推送"（ Server Push，也叫 Cache push）。

例如下图所示,服务端主动把JS和CSS文件推送给客户端，而不需要客户端解析HTML时再发送这些请求。		

![image-20220324222926391](F:\Typora\笔记\Go语言笔记图片\image-20220324222926391.png)

另外需要补充的是,服务端可以主动推送，客户端也有权利选择是否接收。如果服务端推送的资源已经被浏览器缓存过，浏览器可以通过发送RST_STREAM帧来拒收。<font color=red>主动推送也遵守同源策略，换句话说，服务器不能随便将第三方资源推送给客户端，而必须是经过双方确认才行</font>。







#### 连接管理

**1.短连接与长连接**

当浏览器访问一个包含多张图片的 HTML 页面时，除了请求访问的 HTML 页面资源，还会请求图片资源。如果每进行一次 HTTP 通信就要新建一个 TCP 连接，那么开销会很大。

长连接只需要建立一次 TCP 连接就能进行多次 HTTP 通信。

- 从 HTTP/1.1 开始默认是长连接的，如果要断开连接，需要由客户端或者服务器端提出断开，使用 Connection : close；
- 在 HTTP/1.1 之前默认是短连接的，如果需要使用长连接，则使用 Connection : Keep-Alive。



**2.流水线**

默认情况下，HTTP 请求是按顺序发出的，下一个请求只有在当前请求收到响应之后才会被发出。由于受到网络延迟和带宽的限制，在下一个请求被发送到服务器之前，可能需要等待很长时间。

流水线是在同一条长连接上连续发出请求，而不用等待响应返回，这样可以减少延迟。







#### Keep-Alive 和非 Keep-Alive 区别

- 非Keep-alive：早期HTTP1.0，浏览器发起http请求需要与服务器建立新的TCP连接，请求处理后连接立即断开，重新请求重新连接。但每一个这样的连接，客户机和服务器都要分配 TCP 的缓冲区和变量，这给服务器带来的严重的负担。
- Keep-alive：HTTP1.1默认持久连接，同一客户机可以连续请求通过相同的连接进行传送，一台服务器多个web页面也可通过单个TCP连接传送给同一个客户机。但长时间保持TCP连接会导致系统资源被无效占用。



![image-20220326155059973](F:\Typora\笔记\Go语言笔记图片\image-20220326155059973.png)



**长连接:**连接→数据传输→保持连接(心跳)→数据传输→保持连接(心跳)→……→关闭连接（一个TCP连接通道多个读写通信）； 

**短连接:**连接→数据传输→关闭连接；







#### 🐱‍🐉HTTP 长连接短连接使用场景是什么

长连接：多用于操作频繁，点对点的通讯，而且客户端连接数目较少的情况。例如即时通讯、网络游戏等。

短连接：用户数目较多的Web网站的 HTTP 服务一般用短连接。例如京东，淘宝这样的大型网站一般客户端数量达到千万级甚至上亿，若采用长连接势必会使得服务端大量的资源被无效占用，所以一般使用的是短连接。







#### 🐱‍🐉如果你访问一个网站很慢，怎么排查和解决

网页打开速度慢的原因有很多，这里列举出一些较常出现的问题：

① 首先最直接的方法是查看本地网络是否正常，可以通过网络测速软件例如电脑管家等对电脑进行测速，若网速正常，我们查看网络带宽是否被占用，例如当你正在下载电影时并且没有限速，是会影响你打开网页的速度的，这种情况往往是处理器内存小导致的；

② 当网速测试正常时，我们对网站服务器速度进行排查，通过 ping 命令查看链接到服务器的时间和丢包等情况，一个速度好的机房，首先丢包率不能超过 1%，其次 ping 值要小，最后是 ping 值要稳定，如最大和最小差值过大说明路由不稳定。或者我们也可以查看同台服务器上其他网站的打开速度，看是否其他网站打开也慢。

③ 如果网页打开的速度时快时慢，甚至有时候打不开，有可能是空间不稳定的原因。当确定是该问题时，就要找你的空间商解决或换空间商了，如果购买空间的话，可选择购买购买双线空间或多线空间；如果是在有的地方打开速度快，有的地方打开速度慢，那应该是网络线路的问题。电信线路用户访问放在联通服务器的网站，联通线路用户访问放在电信服务器上的网站，相对来说打开速度肯定是比较慢。

④ 从网站本身找原因。网站的问题主要包括网站程序设计、网页设计结构和网页内容三个部分。

- 网站程序设计：当访问网页中有拖慢网站打开速度的代码，会影响网页的打开速度，例如网页中的统计代码，我们最好将其放在网站的末尾。因此我们需要查看网页程序的设计结构是否合理；
- 网页设计结构：如果是 table 布局的网站，查看是否嵌套次数太多，或是一个大表格分成多个表格这样的网页布局，此时我们可以采用 div 布局并配合 css 进行优化。
- 网页内容：查看网页中是否有许多尺寸大的图片或者尺寸大的 flash 存在，我们可以通过降低图片质量，减小图片尺寸，少用大型 flash 加以解决。此外，有的网页可能过多地引用了其他网站的内容，若某些被引用的网站访问速度慢，或者一些页面已经不存在了，打开的速度也会变慢。一种直接的解决方法是去除不必要的加载项。





#### HTTP 是不保存状态的协议,如何保存用户状态

我们知道，假如某个特定的客户机在短时间内两次请求同一个对象，服务器并不会因为刚刚为该用户提供了该对象就不再做出反应，而是重新发送该对象，就像该服务器已经完全忘记不久之前所做过的是一样。<font color=red>因为一个 HTTP 服务器并不保存关于客户机的任何信息，所以我们说 HTTP 是一个无状态协议</font>。

通常有两种解决方案：

① 基于 Session 实现的会话保持

在客户端第一次向服务器发送 HTTP 请求后，服务器会创建一个 Session 对象并将客户端的身份信息以键值对的形式存储下来，然后分配一个会话标识（SessionId）给客户端，这个会话标识一般保存在客户端 Cookie 中，之后每次该浏览器发送 HTTP 请求都会带上 Cookie 中的 SessionId 到服务器，服务器根据会话标识就可以将之前的状态信息与会话联系起来，从而实现会话保持。

优点：安全性高，因为状态信息保存在服务器端。

缺点：由于大型网站往往**采用的是分布式服务器**，<font color=red>浏览器发送的 HTTP 请求一般要先通过负载均衡器才能到达具体的后台服务器</font>，倘若同一个浏览器两次 HTTP 请求**分别落在不同的服务器上**时，基于 Session 的方法就不能实现会话保持了。

【解决方法：采用中间件，例如 Redis，我们通过将 Session 的信息存储在 Redis 中，使得每个服务器都可以访问到之前的状态信息】

② 基于 Cookie 实现的会话保持

当服务器发送响应消息时，在 HTTP 响应头中设置 Set-Cookie 字段，用来存储客户端的状态信息。客户端解析出 HTTP 响应头中的字段信息，并根据其生命周期创建不同的 Cookie，这样一来每次浏览器发送 HTTP 请求的时候都会带上 Cookie 字段，从而实现状态保持。基于 Cookie 的会话保持与基于 Session 实现的会话保持最主要的区别是前者完全将会话状态信息存储在浏览器 Cookie 中。

优点：服务器不用保存状态信息， 减轻服务器存储压力，同时便于服务端做水平拓展。

缺点：该方式<font color=red>不够安全</font>，因为状态信息存储在客户端，这意味着不能在会话中保存机密数据。除此之外，浏览器每次发起 HTTP 请求时都需要发送额外的 Cookie 到服务器端，会<font color=red>占用更多带宽</font>。



**拓展：Cookie被禁用了怎么办？**

若遇到 Cookie 被禁用的情况，则可以通过<font color=red>**重写 URL** </font>的方式将会话标识放在 URL 的参数里，也可以实现会话保持。









