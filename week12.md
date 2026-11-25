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
| 存在哪 |客户端（浏览器/文件）|服务端（内存/数据库）|
| 谁生成 |服务器发，客户端存|服务器自己生成并保存|
| 能不能伪造 |  |  |

## 实验：删掉 SESSDATA

- 在 F12 → Application → Cookies 里删除 SESSDATA
- 刷新后变成未登录
- 结论：Cookie 是钥匙，服务器表是档案；钥匙丢了进不了门
- 注意：SESSDATA 带 HttpOnly + Secure + SameSite=Lax
