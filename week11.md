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

1. GET 和 POST 的区别：
   GET 把参数放在网址里——我在淘宝搜「袜子」，把链接发给朋友，
   他能看到我搜的是袜子。POST 把内容放在请求体里，所以登录时
   密码不会出现在地址栏。

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
