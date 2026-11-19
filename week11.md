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
