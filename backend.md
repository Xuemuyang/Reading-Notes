# 记录后端知识

## 数字签名

内容 --hash函数--> 摘要(digest) --私钥加密--> 数字签名(signature)

签名会和内容一起发送

签名 --公钥解密--> 摘要

内容 --hash函数--> 摘要

对比两种方式得到的摘要，一致则没有被篡改

参考[阮一峰的博客-数字签名是什么](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)

## 身份验证

### 传统身份验证

后端根据用户信息生成 token，保存这个 token 和用户的 ID 到数据库或者 session，将 token 传给用户，存入浏览器 Cookie，浏览器请求带上 Cookie，后端根据 Cookie 中的 session 信息来查询用户，验证是否过期。

基于 Cookie 的方案，安全角度考虑会对 token 加以 HttpOnly 的处理，防止前端直接获取 token，避免 XSS 攻击。

或者可以通过设置 secure 强制要求 Cookie 值允许通过 HTTPS 传输，进一步提高安全级别。

CSRF-TOKEN 作双重验证。

传统方式的弊端:

- 数据库查询开销增加
- 服务端内存占用过高
- 兼容问题

Cookie 应该用在 Web 浏览器中，小程序等不支持 Cookie。

### JWT

JSON Web Token

为了方便在各系统之间安全的传送 JSON 对象格式的信息，而采用的开发标准。

#### 两个特点

- 紧凑(compact)

体积较小，较好的传输速度。

- 自包含(self-contained)

包含系统所需要的非敏感性关键业务数据，避免数据库查询次数。

#### 构成

##### header

指定了签名算法

##### payload

包含签发信息

- `iss`(issuer)：JWT 的签发者
- `sub`(subject)：JWT 所面向的用户
- `aud`(audience)：接收 JWT 的一方
- `exp`(expiresIn)：JWT 的过期时间，这个时间必须大于签发时间
- `nbf`(notBefore)：定义在什么时间之前，该 JWT 都是不可用的
- `iat`(issuedAt)：JWT 的签发时间
- `jti`(jwtid)：JWT 的唯一身份标识，主要用来作为一次性 token，从而避免重放攻击

##### signature

header 与 payload 皆为 base64 的编码内容，secret 需要在服务端保管好。

```jwt
# header
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
# payload
eyJ1c2VySWQiOjEsImV4cCI6MTUzNTMyMjc0NSwiaWF0IjoxNTM0NzE3OTQ1fQ.
# signature
6tOdn2R82bxJbXjAnwU5g4g9EKqGNe-qo4qCo6UZnQ4
```

#### 好处

- 跨语言性：payload 数据结构基于 JSON，可以被任何主流语言支持。
- 免疫 CSRF：对 Cookie 的不依赖性，决定了天然免疫 CSRF 攻击。
- 可跨域性：同样是对 Cookie 的不依赖性，决定了更好的跨域支持与独立服务化属性。
- 多端适配：iOS， Android，微信小程序等非网页客户端，Cookie 是不被支持的，JWT 的认证机制则会简单很多。
- 去耦可扩展性：JWT 可以在任何拥有正确 secret 私钥的 API 服务环境被身份验证和使用，便于微服务拆分。

#### 注意项

- 不要在 JWT 的 payload 中签入敏感信息
- 保护好 secret 秘钥
- 使用 HTTPS 传输 JWT
- 设置较短的 JWT 失效时间，并结合一个失效较长的 JWT RefreshToken 组合为宜。因为 JWT 无法轻易失效已签发的合法 JWT
