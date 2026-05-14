# Source-Sink 审计矩阵（Java）

## 常见 Source（外部输入）

- URL 参数、Path 变量、Query 参数
- Header / Cookie / JWT Claims
- JSON/XML 请求体
- 文件上传内容与文件名
- MQ 消息、RPC 参数
- 缓存/数据库中的“二次不可信数据”

## 常见 Sink（危险操作）

- SQL 执行：`Statement`、动态 SQL 片段
- 命令执行：`Runtime.exec`、`ProcessBuilder`
- 文件系统：读写、删除、解压、移动
- 反序列化：`ObjectInputStream`、JSON 多态反序列化
- XML 解析：`DocumentBuilderFactory`、SAX/StAX
- 网络请求：`URL.openConnection`、HTTP 客户端
- 模板渲染：模板表达式执行

## 常见 Sanitizer（安全控制）

- 参数化查询（PreparedStatement / ORM 参数绑定）
- 严格白名单（枚举字段、固定映射）
- 路径规范化 + 基准目录校验
- 协议/域名/IP 白名单
- XML 外部实体禁用
- 鉴权注解 + 服务端资源归属校验

## 结论准则

- Source 可控 + Sink 可达 + Sanitizer 缺失 => 高优先级风险
- Source 可控 + Sink 可达 + Sanitizer 可绕过 => 中高风险
- Source 可控 + Sink 不可达 => 可疑点
