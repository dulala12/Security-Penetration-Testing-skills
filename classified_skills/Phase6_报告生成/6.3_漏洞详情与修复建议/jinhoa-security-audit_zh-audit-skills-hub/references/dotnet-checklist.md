# .NET 审计清单（通用）

## 1) 入口与路由
- WebForms：`.aspx` 页面与 `Page_Load` / 事件处理。
- ASMX：`[WebMethod]` 暴露方法及参数。
- MVC/Core：Controller/Action、路由属性、API 路径。
- 是否存在历史接口未下线（兼容路径、测试接口）。

## 2) 输入处理
- `Request.QueryString/Form/Params` 是否直接使用。
- 参数是否做类型、长度、范围、语义校验。
- 是否存在“仅前端校验”的关键参数。

## 3) SQL 注入
- `SqlCommand.CommandText` 是否字符串拼接。
- `string.Format`/`+` 拼接 where/order/limit 条件。
- 存储过程调用是否把动态 SQL 继续拼接执行。
- 是否统一使用参数化（`SqlParameter`）。

## 4) 鉴权与越权
- 接口是否校验会话/Token。
- 是否只校验登录而不校验角色与资源归属。
- 用户ID/组织ID是否信任客户端传参。

## 5) 文件上传/下载
- 上传是否只校验后缀或 Content-Type。
- 文件名与路径是否可控。
- 上传目录是否位于可执行路径。
- 下载读取是否可 `../` 穿越。

## 6) XML 与 XXE
- `XmlDocument.Load`、`XDocument.Load` 是否解析不可信 XML。
- 是否显式禁用 DTD/外部实体。

## 7) 反序列化
- 是否存在 `BinaryFormatter`、`LosFormatter` 等不安全反序列化。
- 反序列化数据是否来自不可信输入。

## 8) 敏感配置与信息泄露
- `web.config`、连接串、密钥是否泄露。
- 异常堆栈是否对外暴露。
- Debug/Trace 是否在生产启用。
