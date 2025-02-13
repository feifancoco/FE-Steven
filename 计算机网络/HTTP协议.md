## HTTP 协议
> 强烈建议阅读 [阮一峰HTTP入门](https://www.ruanyifeng.com/blog/2016/08/http.html)

关于HTTP协议，主要以版本来认识和复习，可分为`HTTP1.x`，`HTTP2`，`HTTP3`（谷歌基于QUIC协议）

### HTTP 1.x
#### 特点
1. 端到端（请求/响应模式）
2. 灵活可扩展（Content-Type字段标记传输内容）
3. 可靠（基于TCP/IP）
4. 无状态（每一次请求都是独立的）
5. 持久连接（从HTTP1.1开始，支持持续连接(`长连接`)，TCP默认不关闭，可悲多个请求复用，同一域下支持建立6个持久连接）

#### 缺点
1. 明文传输，可能被`监听`
2. 无法验证报文完整性，可能被`篡改`
3. 不验证通信方身份，可能被`伪装`
4. 无状态
5. 对头阻塞（Head-of-line blocking）（HTTP2多路复用解决问题，根源在于二进制分帧）

### HTTP2.0新特性
1. **二进制分帧**：HTTP1.x的解析是基于文本（请求头是文本，数据体可以是文本或二进制），HTTP2.0解析基于二进制，实现方便，健壮性更好。
2. **多路复用**：复用的是TCP连接，客户端/服务端可以同时发送请求或响应，也不用按序发送，避免了“队头阻塞”问题。
3. **数据流（stream）**： 每个请求或回应的所有数据包称为一个数据流，数据流ID区分属于哪个流；
4. **头部信息压缩**：头信息使用`gzip/compress`压缩，头信息映射表（`HPACK`）
5. **服务端推送**：客户端在请求一个资源时，会把相关资源发送给客服端，这样客户端就不需要再次发起请求。尽管可以主动向客户端发送资源，但是有别于`发送通知`，只有当"如果不推送这个资源，浏览器就会请求这个资源"的时候，浏览器才会使用推送过来的内容。这与websocket有区别。

   
### 常见状态码
- 1xx:
    - `100 Continue`: 表示正常，客户端可以继续发送请求;
    - `101 Switching Protocols`: 切换协议(websocket)，服务器根据客户端的请求切换协议。
- 2xx:
  - `200 OK`: 请求成功；
  - `204 No Content`: 服务器成功处理，但未返回内容。
  - `206 Partial Content`: 客户端进行了范围请求，响应报文应包含Content-Range指定范围的实体内容。
- 3xx:
  - `301 Moved Permanently`: 永久性重定向；
  - `302 Found`: 临时重定向；
  - `303 See Other`: 和301功能类似，但要求客户端采用get方法获取资源;
  - `304 Not Modified`: 所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。(协商缓存)
  - `305 Use Proxy`: 所请求的资源必须通过代理访问;
  - `307 Temporary Redirect`: 临时重定向，与302类似，要求使用get请求重定向。
- 4xx: 请求报文有误
  - `400 Bad Request`: 客户端请求的语法错误，服务器无法理解。
  - `401 Unauthorized`: 表示发送的请求需要有认证信息。
  - `403 Forbidden`: 服务器理解用户的请求，但是拒绝执行该请求。
  - `404 Not Found`: 服务器无法根据客户端的请求找到资源。
- 5xx: 服务端发生错误
  - `500 Internal Server Error`: 服务器内部错误，无法完成请求。
  - `501 Not Implemented`: 服务器不支持请求的功能，无法完成请求
  - `502: Bad Gateway`
  - `503: Service Unavailable`