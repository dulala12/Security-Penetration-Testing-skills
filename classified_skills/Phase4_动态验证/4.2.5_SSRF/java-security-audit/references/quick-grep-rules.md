# Java 审计快速检索规则（增强版）

> 用于快速命中可疑点，命中不等于漏洞成立，必须结合调用链验证。

## 命令执行
- `Runtime.getRuntime().exec(`
- `new ProcessBuilder(`
- `ScriptEngineManager`
- `javax.tools.JavaCompiler`

## 反序列化
- `ObjectInputStream`
- `readObject(`
- `resolveClass(`
- `enableDefaultTyping`
- `@JsonTypeInfo`

## SQL 注入
- `createStatement(`
- `${`（MyBatis）
- `@Select("` / `@Update("`
- `+ " where` / `+ " and` / `order by " +`
- `EntityManager.createNativeQuery(`

## 路径穿越/文件读写
- `new File(`
- `Paths.get(`
- `Files.write(` / `Files.copy(`
- `MultipartFile.transferTo(`
- `ZipInputStream`

## XXE
- `DocumentBuilderFactory.newInstance(`
- `SAXParserFactory.newInstance(`
- `XMLInputFactory.newFactory(`
- `TransformerFactory.newInstance(`

## SSRF
- `new URL(`
- `openConnection(`
- `RestTemplate`
- `WebClient`
- `OkHttpClient`

## 模板/表达式注入
- `SpelExpressionParser`
- `StandardEvaluationContext`
- `TemplateEngine.process(`
- `freemarker.template.Configuration`

## 鉴权缺失/越权
- `@PreAuthorize`（检查缺失）
- `hasRole(` / `hasAuthority(`
- 资源查询仅按客户端传入 `userId/tenantId`
- 管理接口缺统一鉴权切面
