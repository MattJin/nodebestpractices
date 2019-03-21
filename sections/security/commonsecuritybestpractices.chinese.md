[✔]: ../../assets/images/checkbox-small-blue.png

# Node.js常见的安全最佳实践

The common security guidelines section contains best practices that are standardized in many frameworks and conventions, running an application with ssl/tls for example should be a common guideline and convention followed in every setup to achieve great security benefits.

## ![✔] 使用SSL/TLS加密客户端-服务器连接

**TL;DR:** In the times of [free SSL/TLS certificates](https://letsencrypt.org/) and easy configuration of those, you do no longer have to weigh advantages and disadvantages of using a secure server because the advantages such as security, support of modern technology and trust clearly outweigh the disadvantages like minimal overhead compared to pure http.

**Otherwise:** Attackers could perform man-in-the-middle attacks, spy on your users' behaviour and perform even more malicious actions when the connection is unencrypted

🔗 [**Read More: Running a secure Node.js server**](secureserver.md)

<br/><br/>

## ![✔] Comparing secret values and hashes securely

**TL;DR:** When comparing secret values or hashes like HMAC digests, you should use the [`crypto.timingSafeEqual(a, b)`](https://nodejs.org/dist/latest-v9.x/docs/api/crypto.html#crypto_crypto_timingsafeequal_a_b) function Node provides out of the box since Node.js v6.6.0. This method compares two given objects and keeps comparing even if data does not match. The default equality comparison methods would simply return after a character mismatch, allowing timing attacks based on the operation length.

**Otherwise:** Using default equality comparison operators you might expose critical information based on the time taken to compare two objects

<br/><br/>

## ![✔] 使用Node.js产生随机字符串

**TL;DR:** Using a custom-built function generating pseudo-random strings for tokens and other security-sensitive use cases might actually not be as random as you think, rendering your application vulnerable to cryptographic attacks. When you have to generate secure random strings, use the [`crypto.RandomBytes(size, [callback])`](https://nodejs.org/dist/latest-v9.x/docs/api/crypto.html#crypto_crypto_randombytes_size_callback) function using available entropy provided by the system.

**否则:** When generating pseudo-random strings without cryptographically secure methods, attackers might predict and reproduce the generated results, rendering your application insecure

<br/><br/>

Going on, below we've listed some important bits of advice from the OWASP project.

## ![✔] OWASP A2: Broken Authentication

- Require MFA/2FA for important services and accounts
- Rotate passwords and access keys frequently, including SSH keys
- Apply strong password policies, both for ops and in-application user management ([🔗 OWASP password recommendation](https://www.owasp.org/index.php/Authentication_Cheat_Sheet#Implement_Proper_Password_Strength_Controls.22))
- Do not ship or deploy your application with any default credentials, particularly for admin users or external services you depend on
- Use only standard authentication methods like OAuth, OpenID, etc.  - **avoid** basic authentication
- Auth rate limiting: Disallow more than _X_ login attempts (including password recovery, etc.) in a period of _Y_
- On login failure, don't let the user know whether the username or password verification failed, just return a common auth error
- Consider using a centralized user management system to avoid managing multiple account per employee (e.g. GitHub, AWS, Jenkins, etc) and to benefit from a battle-tested user management system

## ![✔] OWASP A5:  Broken access control

- Respect the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)  -  every component and DevOps person should only have access to the necessary information and resources
- **Never** work with the console/root (full-privilege) account except for account management
- Run all instances/containers on behalf of a role/service account
- Assign permissions to groups and not to users. This should make permission management easier and more transparent for most cases

## ![✔] OWASP A6: Security Misconfiguration

- Access to production environment internals is done through the internal network only, use SSH or other ways, but _never_ expose internal services
- Restrict internal network access  - explicitly set which resource can access other resources (e.g. network policy or subnets)
- If using cookies, configure it to "secured" mode where it's being sent over SSL only
- If using cookies, configure it for "same site" only so only requests from same domain will get back the designated cookies
- If using cookies, prefer "http only" configuration that prevent browser side JavaScript code from accessing the cookies
- Protect each VPC with strict and restrictive access rules
- Prioritize threats using any standard security threat modeling like STRIDE or DREAD
- Protect against DDoS attacks using HTTP(S) and TCP load balancers
- Perform periodic penetration tests by specialized agencies

## ![✔] OWASP A3: 暴露敏感数据

- 仅接受SSL/TLS连接, 使用headers加强Strict-Transport-Security
- Separate the network into segments (i.e. subnets) and ensure each node has the least necessary networking access permissions
- Group all services/instances that need no internet access and explictly disallow any outgoing connection (a.k.a private subnet)
- 存储机密信息通过vault产品，比如AWS KMS, HashiCorp Vault或者Google Cloud KMS
- Lock down sensitive instance metadata using metadata
- 在数据离开物理边界时对传输中的数据进行加密
- 不在日志语句中包含机密信息
- 避免在前端显示密码, 在后端采取必要措施, 永远不要以明文形式存储敏感信息

## ![✔] OWASP A9: 使用具有已知安全漏洞的组件

- 扫描docker镜像中的已知漏洞(使用Docker和其他供应商提供的扫描服务)
- 启用实例(机器)的自动修补和升级, 以避免运行缺少安全修补程序的旧操作系统
- 为用户提供'id', 'access'和'refresh' token，以便access token是short-lived的，并通过refresh token进行更新。Provide the user with both 'id', 'access' and 'refresh' token so the access token is short-lived and renewed with the refresh token
- 使用服务，比如AWS CloudTrail，记录和审核对云和管理服务的每个API调用(例如，谁删除了S3 bucket?)
- 运行云提供商的安全检查程序(例如AWS security trust advisor)

## ![✔] OWASP A10: 不充分的日志和监控

- 对异常的，或可疑的审核事件（如用户登录、新用户创建、权限更改等待）发出警报

- 对非正常数量的登录失败（或类似的操作，如忘记密码）发出警报

- 在每个数据库记录启动更新时，包含时间和用户名

## ![✔] OWASP A7: Cross-Site-Scripting (XSS)

- 通知浏览器仅仅从同一域名加载资源，并使用Content-Security-Policy的http请求头

<br/><br/><br/>