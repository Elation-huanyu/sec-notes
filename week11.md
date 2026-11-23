# Week 11 · 看懂一个请求

## 实验记录

| 网站 | 方法 | 状态码 | Cookie |
|---|---|---|---|
| bilibili.com | GET | 200 | 有 |
| zhihu.com | GET | 200 | 有 |
| https://www.whut.edu.cn/ | GET | 200 | 有 |

## 一个请求由哪几部分组成

一个 HTTP 请求由四部分组成。

第一部分是方法，它告诉服务器我要做什么。GET 是读，POST 是写。我这次用的是 POST，因为登录要在服务器上创建一个登录状态，属于写操作。

第二部分是 URL，它告诉我请求的是哪个资源，由协议、域名、路径组成。比如 https://www.example.com/login：https 是协议，www.example.com 是域名，/login 是路径。

第三部分是 Header，装的是附加信息，一行一个键值对。我认识了 Host、Cookie、Content-Type。其中 Cookie 的作用是识别身份，服务器靠它认出「你还是刚才那个人」。

第四部分是 Body，只有 POST 请求通常才有，装的是我要提交的数据。比如登录时，Body 里是账号密码。

四部分合起来，就是服务器收到的一封完整的信。

## 我卡住的地方

- 一开始把 Body 理解成「不可改变的信息」，其实它是最容易被改的
- `###` 后面少空格，标题渲染不出来

## curl 实测

- 命令：`curl.exe https://httpbin.org/get`
- origin：218.197.x.x（已打码）
- User-Agent：curl/8.22.0
- 方法：GET
- 我注意到的：curl 不带 Cookie，所以服务器当我是陌生人

## 周二阅读与订正

### 1. GET 和 POST 的区别

- 本质：GET 是读，POST 是写（会在服务器上创建/修改东西）
- 位置：GET 参数在 URL，POST 参数在 Body
- 判断：重复做一次会不会多出东西？会 → POST
- 例外：搜索用 GET 但在「提交」；GraphQL 查询用 POST；老网站用 GET 传密码 = 漏洞
- 原则：先按读/写猜，再以实际 Method 为准

### 2. 404 和 500 是谁的错

404 是我错了（地址写错、资源不存在）；500 是服务器错了（代码崩了）。500 是信号弹——输入一个单引号就返回 500，说明后端 SQL 拼接出错了。

### 3. 最没看懂的 Header

原本是 Content-Type。现在懂了：它决定服务器怎么解析 Body，而且是客户端自己声明的，不是服务器验证的——所以可以伪造，这就是类型绕过、上传绕过的原理。

## Content-Type

- 作用：告诉服务器 Body 是什么格式，决定解析方式
- 三个值：x-www-form-urlencoded（表单）、application/json（API）、multipart/form-data（文件）
- 实验：同样的 name=test，改 Content-Type，一个进 form、一个进 json
- 安全含义：客户端声明 ≠ 服务器验证 → 可伪造


一个 HTTP 请求由四部分组成。

第一部分是方法，它告诉服务器我要做什么。GET 是读，POST 是写。
我这次用的是 POST，因为登录要在服务器上创建一个登录状态，属于写操作。

第二部分是 URL，它告诉我请求的是哪个资源，由协议、域名、路径组成。
比如 https://www.example.com/login：https 是协议，www.example.com 是域名，
/login 是路径。

第三部分是 Header，装的是附加信息，一行一个键值对。
我认识了 Host、Cookie、Content-Type。其中 Cookie 的作用是识别身份，
服务器靠它认出「你还是刚才那个人」。

第四部分是 Body，只有 POST 请求通常才有，装的是我要提交的数据。
比如登录时，Body 里是账号密码。

四部分合起来，就是服务器收到的一封完整的信。

## httpbin 表单实验

- 表单提交走 POST
- 数据在 Body 里，地址栏看不到
- Payload：customer=test&telephone=123

## Cookie 预习

- 不带 Cookie：返回空 → 服务器当我是陌生人
- 带 name=test：服务器就认我叫 test
- 带 role=admin：httpbin 会认（它只回显）；真实系统查 Session，伪造的查不到

### 结论

- Cookie 存在客户端，由服务器 Set-Cookie 下发，浏览器自动携带
- 服务器不信任 Cookie 内容，只把它当钥匙去查自己的档案
- 所以：明文 Cookie 可伪造（漏洞）；Session / 签名 不可伪造（安全）

- # 错题本

## 第 11 周小测

1. 500 为什么在渗透测试里重要？
   答：说明服务器处理了我的输入但崩了 → 可能没过滤 → 注入信号

2. Cookie 什么情况下能被伪造？
   答：服务器把权限/身份直接放在 Cookie 里且不校验；
       Session ID 可猜；JWT 不验签

3. curl 怎么发 JSON？
   答：curl.exe -X POST URL -H "Content-Type: application/json" -d '{"k":"v"}'

# Week 12 · Cookie 与 Session

## 实验：Set-Cookie 往返

- 服务器发证：`set-cookie: name=test; Secure; Path=/`
- 客户端存证：`-c cookies.txt` 写进文件（Netscape 格式，7 列）
- 下次亮证：`-b cookies.txt` → 请求头带上 `Cookie: name=test`
- 服务器认证：返回 `{"cookies":{"name":"test"}}`

## 学到的四个属性

- `Secure`：只在 HTTPS 下发送
- `Path`：生效路径
- `HttpOnly`：JS 读不到（防 XSS 偷 Cookie）
- `SameSite`：跨站请求不带（防 CSRF）

## Cookie vs Session（周三填）

| | Cookie | Session |
|---|---|---|
| 存在哪 |  |  |
| 谁生成 |  |  |
| 能不能伪造 |  |  |

## 我的疑问

- 服务器拿到 `name=test` 之后，怎么知道这是哪个用户？
