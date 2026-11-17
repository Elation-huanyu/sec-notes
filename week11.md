# Week 11 · 看懂一个请求

## 实验记录

| 网站 | 方法 | 状态码 | Cookie |
|---|---|---|---|
| bilibili.com |GET|200|有|
| zhihu.com |GET|200|有|
|https://www.whut.edu.cn/ |GET|200|有|

## 一个请求由哪几部分组成

（这里写 200 字，今天写不出就明天补）

## 我卡住的地方

（有就写，没有写「无」）

curl 实测
- 命令：`curl.exe https://httpbin.org/get`
- origin：218.197.x.x（已打码）
- User-Agent：curl/8.22.0
- 方法：GET
- 我注意到的：curl 不带 Cookie，所以服务器当我是陌生人

## 周二阅读与订正

## GET vs POST

- 本质：GET 是读，POST 是写（会在服务器上创建/修改东西）
- 位置：GET 参数在 URL，POST 参数在 Body
- 判断：重复做一次会不会多出东西？会 → POST
- 例外：搜索用 GET 但在“提交”；GraphQL 查询用 POST；
  老网站用 GET 传密码 = 漏洞
- 原则：先按读/写猜，再以实际 Method 为准

2. 404 和 500 是谁的错：
   404 是我错了（地址写错、资源不存在）；500 是服务器错了（代码崩了）。
   500 是信号弹——输入一个单引号就返回 500，说明后端 SQL 拼接出错了。

3. 最没看懂的 Header：
   原本是 Content-Type。现在懂了：它决定服务器怎么解析 Body，
   而且是客户端自己声明的，不是服务器验证的——所以可以伪造，
   这就是类型绕过、上传绕过的原理。

## Content-Type

- 作用：告诉服务器 Body 是什么格式，决定解析方式
- 三个值：x-www-form-urlencoded（表单）、application/json（API）、multipart/form-data（文件）
- 实验：同样的 name=test，改 Content-Type，一个进 form、一个进 json
- 安全含义：客户端声明 ≠ 服务器验证 → 可伪造
