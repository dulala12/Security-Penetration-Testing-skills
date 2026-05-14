# 用友产品线审计映射

## NC / NC Cloud

- 高风险入口常见：`/servlet/~uap*`、`/portal/pt/servlet/*`、`/service/~iufo/*`
- 常见问题：反序列化、ActionServlet 参数拼接 SQL、上传/读取、XML 解析

## U8 / U8-Cloud

- 高风险入口常见：`/uapws/*`、`/service/*`、`/linux/pages/upload.jsp`、`/api/*`
- 常见问题：SQL 注入、XXE、上传接口鉴权弱、历史组件复用漏洞

## GRP

- 常见问题：XML 注入、未授权接口、部分命令执行链可疑点

## U9

- 常见问题：补丁/升级相关接口上传风险、SOAP 接口处理链弱校验

## 审计建议

- 同名接口在不同产品线中可能实现不同，禁止简单套用结论。
- 输出时务必标注“产品线 + 版本区间 + 组件名”。
