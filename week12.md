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
- 刷新后自动变成未登录
- 结论：Cookie 是钥匙，服务器表是档案；钥匙丢了进不了门
- 注意：删 Cookie ≠ 退出登录。删 Cookie 只是本地丢钥匙，
  服务器上的会话仍然有效，必须点「退出登录」才会作废

## 会话劫持 vs 会话固定

### 一句话区分

- 会话劫持：偷你的钥匙
- 会话固定：让你用我的钥匙

### 判断方法

只问一句：这个 SESSID 最初是谁的？
- 受害者的 → 劫持
- 攻击者的 → 固定

时间顺序：
- 劫持：受害者先登录，攻击者后偷
- 固定：攻击者先拿到 ID，受害者后登录

### 会话劫持

原理：服务器认卡不认人。拿到 SESSID 就能冒充，不需要密码。

偷的途径：
- XSS 读 document.cookie（Cookie 没开 HttpOnly）
- 公共 WiFi 嗅探（没开 Secure）
- 日志、截图、分享链接泄露

### 会话固定

五步：
1. 攻击者先拿到一个未登录的 SESSID
2. 让受害者的浏览器用上这个 ID
3. 受害者登录
4. 服务器若不重新生成 ID，就把它标记为受害者
5. 攻击者用同一个 ID 直接进去

三种投递方式：
- 共用电脑（不需要任何技术）
- URL 里带 ID（;jsessionid=xxx）
- XSS / 子域名覆盖 Cookie（需要额外漏洞）

成立前提：服务器无条件接受客户端提供的 SESSID。
防护：登录后重新生成 ID；不认识的 ID 直接丢弃。

### A 如何使用这串 ID

1. 浏览器 F12 → Application → Cookies 手动添加
2. curl.exe -b "SESSID=abc123" https://site.com/profile
3. Python: requests.get(url, cookies={"SESSID": "abc123"})

服务器分不清的原因：请求里只有 ID，没有「人」。

### 防御六条

1. 登录后重新生成 Session ID（防固定）
2. Cookie 加 HttpOnly（防 XSS 偷）
3. Cookie 加 Secure（防嗅探）
4. 会话超时（防长期有效）
5. 退出登录时服务端销毁（防复用）
6. 绑定 UA / IP（弱防护，可绕过）

### 实验结论

- 删除 SESSDATA 后自动变成未登录 → Cookie 是钥匙，服务器表是档案
- 删 Cookie ≠ 退出登录：前者是本地丢钥匙，后者才让服务器作废会话
- B站 登录前后 SESSDATA 变了 → 有防「会话固定」的机制
- 但这不防「劫持」：登录后的 SESSDATA 若被偷，仍然有效直到退出/超时
