# Java 框架专项审计清单

## Spring Boot / Spring MVC

- Controller 是否直接接收敏感参数并透传到 DAO/命令执行/模板渲染。
- `@InitBinder`、Validator 是否覆盖所有关键参数。
- 是否存在 `@RequestMapping("/**")` 等过宽路由。
- 是否有 debug/测试接口未下线。
- Actuator 是否暴露敏感端点（`/env`、`/heapdump`、`/mappings`）。

## MyBatis / MyBatis-Plus

- `${}` 直接拼接风险（表名/列名/排序字段）。
- XML 动态 SQL 是否受白名单约束。
- `wrapper.last()`、自定义 SQL 片段是否可控。

## JPA / Hibernate

- 原生 SQL 是否拼接参数。
- 动态 JPQL/HQL 是否拼接 where/order 条件。
- 分页排序字段是否做字段白名单。

## Shiro / Spring Security

- URL 规则顺序是否错误（放行规则覆盖鉴权规则）。
- 方法级注解（`@PreAuthorize`）是否缺失。
- 是否存在仅前端控制按钮、后端未鉴权的接口。
- 租户 ID / 用户 ID 是否来自客户端并直接信任。

## Jackson / Fastjson

- 是否开启危险配置（AutoType 或宽松多态反序列化）。
- 反序列化目标类型是否受控。
- 是否处理未知字段导致的逻辑绕过。

## 模板引擎

- 用户输入是否进入模板表达式上下文。
- 是否允许动态模板片段拼接。
- 是否启用模板沙箱与函数白名单。

## 文件处理

- 上传文件名是否用于拼路径。
- 扩展名/MIME/魔数是否同时校验。
- 下载接口是否允许任意路径读取。
- 解压缩是否有 Zip Slip 校验。

## 出网能力（SSRF）

- URL 是否可控。
- 是否限制协议（http/https）。
- 是否限制域名/IP（禁止内网和 metadata）。
- 是否禁用重定向和 DNS rebinding 风险路径。
