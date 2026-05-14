---
name: ctf-web
description: 为CTF挑战提供Web漏洞利用技术。当目标主要是HTTP应用程序、API、浏览器客户端、模板引擎、身份流程或智能合约前端/后端表面时使用，包括XSS、SQLi、SSTI、SSRF、XXE、JWT、认证绕过、文件上传、请求走私、OAuth/OIDC、SAML、原型污染和类似的Web漏洞。不要将其用于本机二进制内存损坏、独立可执行文件的逆向工程、磁盘或内存取证，或纯密码分析，除非Web漏洞仍然是获取flag的主要途径。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问以安装工具。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF Web漏洞利用

将此技能用作Web重挑战的路由和执行指南。保持第一遍简短：映射应用程序、确认信任边界，然后深入详细的技术说明。

## 先决条件

**Python包（所有平台）：**
```bash
pip install sqlmap flask-unsign requests
```

**Linux（apt）：**
```bash
apt install hashcat jq curl
```

**macOS（Homebrew）：**
```bash
brew install hashcat jq curl
```

**Go工具（所有平台，需要Go）：**
```bash
go install github.com/ffuf/ffuf/v2@latest
```

**手动安装：**
- ysoserial — [GitHub](https://github.com/frohoff/ysoserial)，需要Java（Java反序列化payload）

## 其他资源

- [sql-injection.md](sql-injection.md) - SQL注入技术：认证绕过、UNION提取、过滤器绕过、二阶SQLi、截断、竞争辅助泄露、INSERT ON DUPLICATE KEY UPDATE密码覆盖、innodb_table_stats WAF绕过
- [server-side.md](server-side.md) - PHP类型混淆、php://filter LFI、Python str.format遍历、SSTI（Jinja2、Twig、ERB、Mako、EJS、Vue.js、Smarty）、SSRF（Host头、DNS重绑定、curl重定向、未转义点正则、SNI FTP走私、mod_vhost_alias）、PHP hash_hmac NULL
- [server-side-2.md](server-side-2.md) - XXE（基本、OOB、DOCX上传）、通过X-Forwarded-For进行XML注入、PHP变量变量、PHP uniqid可预测文件名、顺序正则替换绕过、命令注入（换行、黑名单、sendmail CGI、多条形码、git CLI）、GraphQL注入（内省、批处理、插值）
- [server-side-exec.md](server-side-exec.md) - 直接代码执行路径、上传到RCE、反序列化相邻执行、LaTeX注入、头和API滥用
- [server-side-exec-2.md](server-side-exec-2.md) - 更多执行链：SQLi分片、路径解析器技巧、多语言上传、包装器滥用、文件名注入、BMP像素Webshell与文件名截断
- [server-side-deser.md](server-side-deser.md) - Java/Python/PHP反序列化和竞争条件手册、PHP SoapClient CRLF SSRF通过反序列化
- [server-side-advanced.md](server-side-advanced.md) - 高级SSRF、遍历、归档、解析器、框架和现代应用服务器问题、Nginx别名遍历
- [server-side-advanced-2.md](server-side-advanced-2.md) - Docker API SSRF、Castor/XML、Apache表达式读取、解析器差异、Windows路径技巧、恶意MySQL服务器文件读取
- [server-side-advanced-3.md](server-side-advanced-3.md) - 第3部分（CSAW/35C3/ASIS/PlaidCTF 2018）：WAV多语言上传、多斜杠URL `path.startswith`绕过、Xalan XSLT `math:random()`种子猜测、SoapClient `_user_agent` CRLF方法走私、`gopher:///`无主机URL方案绕过、通过攻击者指定的出站URL泄露SSRF凭证
- [server-side-advanced-4.md](server-side-advanced-4.md) - 第4部分：WeasyPrint SSRF/文件读取（CVE-2024-28184）、MongoDB正则/$where盲Oracle、Pongo2 Go模板注入、ZIP PHP webshell、basename()绕过、wget CRLF SSRF→SMTP、Gopher SSRF到MySQL盲SQLi、React Server Components Flight RCE（CVE-2025-55182）、AMQP/TLS通过sslsplit+arpspoof拦截、CairoSVG XXE、Bazaar仓库重建
- [client-side.md](client-side.md) - XSS、CSRF、缓存中毒、DOM技巧、管理员机器人滥用、请求走私、付费墙绕过
- [client-side-advanced.md](client-side-advanced.md) - CSP绕过、Unicode技巧、XSSI、CSS数据泄露、浏览器规范化怪癖、postMessage null origin绕过
- [auth-and-access.md](auth-and-access.md) - 认证/授权绕过、隐藏端点、IDOR、重定向链、子域名接管、AI聊天机器人越狱
- [auth-and-access-2.md](auth-and-access-2.md) - 第2部分（2018年）：`std::unordered_set`桶碰撞认证绕过、`nodeprep.prepare` Unicode同形字用户名碰撞、SRP A=0/A=N认证绕过、ArangoDB AQL MERGE权限提升
- [auth-jwt.md](auth-jwt.md) - JWT/JWE操作、弱密钥、头部注入、密钥混淆、重放
- [auth-infra.md](auth-infra.md) - OAuth/OIDC、SAML、CORS、CI/CD密钥、IdP滥用、登录中毒
- [node-and-prototype.md](node-and-prototype.md) - 原型污染、JS沙箱逃逸、Node.js攻击链
- [web3.md](web3.md) - Solidity和Web3挑战笔记
- [cves.md](cves.md) - CVE驱动的技术，您可以根据挑战banner、头、依赖泄露或版本字符串进行匹配
- [field-notes.md](field-notes.md) - 长篇漏洞利用笔记：SQLi、XSS、LFI、JWT、SSTI、SSRF、命令注入、XXE、反序列化、竞争条件、认证绕过和多阶段链的快速参考

## 何时转向

- 如果目标是本机二进制文件、自定义VM或固件镜像，首先切换到`/ctf-reverse`。
- 如果HTTP漏洞只给您代码执行，而困难部分变成内存损坏或seccomp逃逸，切换到`/ctf-pwn`。
- 如果"web"挑战实际上依赖于JWT数学、自定义MAC或密码学原语，切换到`/ctf-crypto`。
- 如果web挑战涉及分析日志、PCAP或从web服务器恢复工件，切换到`/ctf-forensics`。
- 如果挑战需要在漏洞利用前从公共web源、DNS记录或社交媒体收集情报，切换到`/ctf-osint`。

## 第一遍工作流程

1. 确定真正的边界：仅浏览器、仅后端、混合应用或认证流程。
2. 在模糊测试前为每个主要功能捕获一个正常的请求/响应对。
3. 从JS bundle、响应头、路由和替代方法枚举隐藏功能。
4. 分类可能的漏洞家族：注入、授权、解析器不匹配、上传、信任代理、状态机或客户端执行。
5. 首先构建最小的证明：泄露、绕过或原语。稍后保存完整的漏洞利用链。

## 快速启动命令

```bash
# 侦察
curl -sI https://target.com
ffuf -u https://target.com/FUZZ -w wordlist.txt
curl -s https://target.com/robots.txt

# SQLi快速测试
sqlmap -u "https://target.com/page?id=1" --batch --dbs

# JWT解码（无验证）
echo '<token>' | cut -d. -f2 | base64 -d 2>/dev/null | jq .

# Cookie解码（Flask）
flask-unsign --decode --cookie '<cookie>'
flask-unsign --unsign --cookie '<cookie>' --wordlist rockyou.txt

# SSTI探测
curl "https://target.com/page?name={{7*7}}"
curl "https://target.com/page?name={{config}}"

# 请求检查
curl -v -X POST https://target.com/api -H "Content-Type: application/json" -d '{}'
```

## 首先要回答的问题

- flag可能在浏览器中、API响应中、本地文件中、数据库行中还是内部服务中？
- 应用程序是否信任模板、重定向、文件路径、头、序列化对象或后台作业中的用户控制数据？
- 是否有多个解析器彼此不同意：代理vs应用、URL解析器vs获取器、清理器vs浏览器、序列化器vs过滤器？
- 您能否先将漏洞变成更小的原语：读取一个文件、伪造一个令牌、调用一个内部端点、触发一个机器人访问？

## 高价值侦察检查

- 在猜测API表面之前，阅读HTML、内联脚本和捆绑的JS。
- 比较UI提交的内容与后端接受的内容；可选的JSON字段通常解锁隐藏路径。
- 尽早检查明显的元数据和辅助路径：`/robots.txt`、`/sitemap.xml`、`/.well-known/`、`/admin`、`/debug`、`/.git/`、`/.env`。
- 在有趣的路由上尝试替代动词和内容类型：`GET`、`POST`、`PUT`、`PATCH`、`TRACE`、JSON、表单、multipart、XML。
- 将文件上传、PDF/导出、webhook、OAuth回调和管理员机器人功能视为可能的漏洞利用倍增器。

## 快速模式映射

- SQL错误、奇怪的过滤或依赖状态的数据库行为：从[sql-injection.md](sql-injection.md)开始。
- 模板化、文件读取、SSRF、命令执行、XML或解析器漏洞：从[server-side.md](server-side.md)和[server-side-exec.md](server-side-exec.md)开始。
- XSS、CSP绕过、管理员机器人、客户端路由、DOM问题或无脚本数据泄露：从[client-side.md](client-side.md)开始。
- 会话伪造、隐藏管理员路由、JWT、OAuth、SAML或弱信任边界：从[auth-and-access.md](auth-and-access.md)、[auth-jwt.md](auth-jwt.md)和[auth-infra.md](auth-infra.md)开始。
- Node.js应用、原型污染、VM沙箱或SSRF到内部服务：添加[node-and-prototype.md](node-and-prototype.md)。
- 智能合约前端或区块链集成应用：添加[web3.md](web3.md)。

## 常见链形状

- 侦察 -> 隐藏路由 -> 认证绕过 -> 内部文件读取 -> 令牌或flag
- XSS或HTML注入 -> 管理员机器人 -> 特权操作 -> 密钥泄露
- 遍历或上传 -> 配置/源代码泄露 -> 密钥恢复 -> 会话伪造
- SSRF -> 元数据或内部API -> 凭证泄露 -> 代码执行
- SQLi或NoSQL注入 -> 凭证绕过 -> 第二阶段模板或上传滥用

## 深入笔记

一旦确认挑战确实是web重的并且您需要长漏洞利用目录，请使用[field-notes.md](field-notes.md)。

- 侦察、SQLi、XSS、遍历、JWT、SSTI、SSRF、XXE和命令注入快速笔记
- 反序列化、竞争条件、文件上传到RCE和多阶段链示例
- Node、OAuth/SAML、CI/CD、Web3、机器人滥用、CSP绕过和现代浏览器技巧
- CVE形状的手册和在现代CTF中仍然出现的旧挑战模式

## 常见flag位置

- 文件：`/flag.txt`、`/flag`、`/app/flag.txt`、`/home/*/flag*`
- 环境：`/proc/self/environ`、进程命令行、调试配置转储
- 数据库：名为`flag`、`flags`、`secret`的表或种子挑战内容
- HTTP：自定义头、归档响应、隐藏路由、管理员导出
- 浏览器：隐藏DOM节点、`data-*`属性、内联状态对象、源映射