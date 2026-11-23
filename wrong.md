# 错题本

## 第 11 周小测（64 分）

**1. 500 为什么在渗透测试里重要？**
答：说明服务器处理了我的输入但崩了 → 可能没做过滤 → 注入的信号。

**2. Cookie 什么情况下能被伪造？**
答：① 权限直接写在 Cookie 里（role=admin）；
　　② Session ID 可猜（1001、1002）；
　　③ JWT 不验签。

**3. curl 怎么发 JSON？**
答：curl.exe -X POST URL -H "Content-Type: application/json" -d '{"k":"v"}'
