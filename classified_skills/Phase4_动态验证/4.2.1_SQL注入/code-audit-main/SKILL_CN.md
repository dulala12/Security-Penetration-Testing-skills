---
name: code-audit
description: |
  专业代码安全审计技能，涵盖55+漏洞类型。
  增强集成了WooYun 88,636个真实漏洞案例（2010-2016年）。
  此技能用于执行安全审计、漏洞扫描、渗透测试准备或代码安全审查。
  支持9种语言：Java、Python、Go、PHP、JavaScript/Node.js、C/C++、.NET/C#、Ruby、Rust。
  包含所有语言的143项强制检测项及特定语言检查清单。
  涵盖SQL注入、XSS、RCE、反序列化、SSRF、JNDI注入、JDBC协议注入、
  身份认证绕过、业务逻辑缺陷、竞争条件和现代安全领域（LLM、Serverless、Android）。
  WooYun集成添加：统计驱动的参数优先级、绕过技术库、
  逻辑漏洞模式和真实案例参考。
  v1.0：初始公开版本，包含Docker部署验证框架。
tools:
  - Read
  - Grep
  - Glob
  - Bash
  - Task
  - LSP
model: sonnet
priority: high
file_patterns:
  - "**/*.java"
  - "**/*.py"
  - "**/*.go"
  - "**/*.php"
  - "**/*.js"
  - "**/*.ts"
  - "**/*.jsx"
  - "**/*.tsx"
  - "**/*.c"
  - "**/*.cpp"
  - "**/*.h"
  - "**/*.cs"
  - "**/*.rb"
  - "**/*.rs"
  - "**/*.xml"
  - "**/*.yml"
  - "**/*.yaml"
  - "**/*.json"
  - "**/*.properties"
  - "**/Dockerfile"
  - "**/*.tf"
exclude_patterns:
  - "**/node_references/**"
  - "**/vendor/**"
  - "**/dist/**"
  - "**/build/**"
  - "**/.git/**"
  - "**/test/**"
  - "**/tests/**"
  - "**/__pycache__/**"
---

# 代码审计技能

> 专业代码安全审计技能 | Professional Code Security Audit
> 支持模式: quick / standard / deep

## 何时使用此技能

此技能适用于以下场景：

- 用户请求**代码审计**、**安全审计**或**漏洞扫描**
- 用户要求**检查代码安全**或**查找安全问题**
- 用户提到**/audit**或**/code-audit**
- 用户希望在部署前**审查代码漏洞**
- 用户需要**渗透测试准备**或**安全评估**

**触发短语:**
- "审计这个项目" / "Audit this project"
- "检查代码安全" / "Check code security"
- "找出安全漏洞" / "Find security vulnerabilities"
- "/audit", "/code-audit"

---

## 快速参考

### 扫描模式

| 模式 | 使用场景 | 范围 |
|------|----------|-------|
| **Quick（快速）** | CI/CD、小型项目 | 高危漏洞、密钥、依赖CVE |
| **Standard（标准）** | 常规审计 | OWASP Top 10、认证、加密 |
| **Deep（深度）** | 关键项目、渗透测试 | 全面覆盖、攻击链、业务逻辑 |

### 核心工作流程

```
1. 侦察（Reconnaissance） → 识别技术栈，映射攻击面
2. 漏洞搜索（Vulnerability Hunt） → 搜索模式，追踪数据流
3. 验证（Verification） → 确认可利用性，过滤误报
4. Docker验证 → [新增] 在沙箱中动态验证（可选）
5. 报告（Report） → 记录发现，包含PoC和修复建议
```

### Docker部署验证

对于深度审计，可使用Docker沙箱进行**动态验证**:

```bash
# 生成验证环境
code-audit --generate-docker-env

# 启动并验证
docker-compose up -d
docker exec -it sandbox python /workspace/poc/verify_all.py
```

详见: `references/core/docker_verification.md`

---

## 执行控制器（必经路径）

> ⚠️ 以下步骤是审计执行的必经路径，不是参考建议。
> 每步有必须产出的输出，后续步骤依赖前序输出。不产出 = 用户可见缺失。

### Step 1: 模式判定

根据用户指令确定审计模式：

| 用户指令关键词 | 模式 |
|--------------|------|
| "快速扫描" "quick" "CI检查" | quick |
| "审计" "扫描" "安全检查"（无特殊说明） | standard |
| "深度审计" "deep" "渗透测试准备" "全面审计" | deep |
| 无法判定 | **问用户，不得自行假设** |

**反降级规则**: 用户指定的模式不可自行降级。项目规模大不是降级理由，而是启用Multi-Agent的理由。降级需用户明确确认。

**必须输出**:
```
[MODE] {quick|standard|deep}
```

### Step 2: 文档加载

按模式加载必要文档（用Read工具实际读取，不是"知道有这个文件"）：

| 模式 | 必须Read的文档 |
|------|-----------------|
| quick | 当前SKILL.md已加载，无需额外文档 |
| standard | + `references/checklists/coverage_matrix.md` + 对应语言checklist |
| deep | + **`agent.md`（完整读取，不可跳过）** + `coverage_matrix.md` + 对应语言checklist |

deep模式下agent.md是必读文档 — Step 4的执行计划模板包含只有agent.md中才有的字段（维度权重、Agent切分模板、门控条件、执行状态机）。

**必须输出**:
```
[LOADED] {实际Read的文档列表，含行数}
```

### Step 3: 侦察（Reconnaissance）

对目标项目执行攻击面测绘。

**必须输出**:
```
[RECON]
项目规模: {X个文件, Y个目录}
技术栈: {语言, 框架, 版本}
项目类型: {CMS | 金融 | SaaS | 数据平台 | 身份认证 | IoT | 通用Web}
入口点: {Controller/Router/Handler数量}
关键模块: {列表}
```

### Step 4: 执行计划 → STOP

基于Step 1-3的输出生成执行计划。**输出后暂停，等待用户确认才能继续。**

**quick/standard模板**:
```
[PLAN]
模式: {mode}
技术栈: {from Step 3}
扫描维度: {计划覆盖的D1-D10维度}
已加载文档: {from Step 2}
```

**deep模板**（全部字段必填 — 标注了信息来源文档）:
```
[PLAN]
模式: deep
项目规模: {from Step 3}
技术栈: {from Step 3}
维度权重: {from agent.md状态机 → 项目类型维度权重，如 CMS: D5(++), D1(+), D3(+), D6(+)}
Agent方案: {from agent.md Agent模板 → 每个Agent负责的维度和max_turns}
Agent数量: {from agent.md规模建议 → 小型(<10K) 2-3, 中型(10K-100K) 3-5, 大型(>100K) 5-9}
D9覆盖策略: {若项目有后台管理/多角色/多租户 → D9必查，D3 Agent须同时覆盖D9a(IDOR+权限一致性+Mass Assignment)}
轮次规划: R1广度扫描 → R1评估 → R2增量补漏(按需)
门控条件: PHASE_1_RECON → ROUND_N_RUNNING → ROUND_N_EVALUATION → REPORT
预估总turns: {Agent数 × max_turns}
已加载文档: {from Step 2}
```

**⚠️ STOP — 输出执行计划后暂停。等待用户确认后才能开始审计。**

### Step 5: 执行

用户确认后，按执行计划和已加载文档执行：

- **quick**: 高危模式匹配扫描，直接输出
- **standard**: 按Phase 1→5顺序执行
- **deep**: 严格按agent.md执行状态机
  - 启动Multi-Agent并行（按Step 4确认的Agent方案）
  - 遵守每个State的门控条件
  - 轮次评估使用agent.md三问法则

### Step 6: 报告门控

生成报告前验证：

| 前置条件 | quick | standard | deep |
|---------|-------|----------|------|
| 高危模式扫描完成 | ✅ | ✅ | ✅ |
| D1-D10覆盖率标记（✅已覆盖/⚠️浅覆盖/❌未覆盖） | — | ✅ | ✅ |
| 所有Agent完成或超时标注 | — | — | ✅ |
| 轮次评估三问通过 | — | — | ✅ |

不满足前置条件 → 不得生成最终报告。

---

## 反幻觉规则（必须遵守）

```
⚠️ 每个发现必须基于通过工具实际读取的代码

✗ 不要根据"典型项目结构"猜测文件路径
✗ 不要凭空编造代码片段
✗ 不要在未读取的文件中报告漏洞

✓ 必须使用Read/Glob在报告前验证文件存在
✓ 必须引用Read工具输出的实际代码
✓ 必须匹配项目实际技术栈
```

**核心原则：宁可漏掉漏洞，也不报告误报。**

---

## 反确认偏误规则（必须遵守）

```
⚠️ 审计必须以方法论为驱动，而非案例驱动

✗ 不要说"基于之前的审计经验，我将重点关注..."
✗ 不要基于"已知CVE"优先处理某些漏洞类型
✗ 不要因为看似"可能性较小"而跳过检查项

✓ 必须枚举所有敏感操作，然后逐一验证
✓ 必须完成每种漏洞类型的完整检查清单
✓ 必须以同等严谨程度对待所有潜在漏洞
```

**核心原则：发现所有潜在漏洞，而非仅熟悉的模式。**

---

## 两层检查清单

> **Layer 1**: `coverage_matrix.md` — Phase 2A后加载，验证10个安全维度覆盖率
> **Layer 2**: 语言语义提示 — 仅对未覆盖维度按需加载对应段落

| 文件 | 用途 |
|------|------|
| **`references/checklists/coverage_matrix.md`** | **覆盖率矩阵(D1-D10)** |
| `references/checklists/universal.md` | 通用架构/逻辑级语义提示 |
| `references/checklists/java.md` | Java语义提示(10维度) |
| `references/checklists/python.md` | Python语义提示 |
| `references/checklists/php.md` | PHP语义提示 |
| `references/checklists/javascript.md` | JavaScript/Node.js语义提示 |
| `references/checklists/go.md` | Go语义提示 |
| `references/checklists/dotnet.md` | .NET/C#语义提示 |
| `references/checklists/ruby.md` | Ruby语义提示 |
| `references/checklists/c_cpp.md` | C/C++语义提示 |
| `references/checklists/rust.md` | Rust语义提示 |

**核心原则**: Checklist不驱动审计，而是验证覆盖。LLM先自由审计(Phase 2A)，再用矩阵查漏(Phase 2B)。

---

## 模块参考

### 核心模块（优先加载）

| 模块 | 路径 | 用途 |
|--------|------|---------|
| **Capability Baseline** | `references/core/capability_baseline.md` | **防止能力丢失的回归测试框架** |
| Anti-Hallucination | `references/core/anti_hallucination.md` | 防止误报 |
| Audit Methodology | `references/core/comprehensive_audit_methodology.md` | 系统化框架，**覆盖跟踪** |
| Taint Analysis | `references/core/taint_analysis.md` | 数据流跟踪，**LSP增强跟踪**，Slot类型分类 |
| PoC Generation | `references/core/poc_generation.md` | 验证模板 |
| External Tools | `references/core/external_tools_guide.md` | Semgrep/Bandit集成 |

### 语言模块（按技术栈加载）

| 语言 | 模块 | 关键漏洞 |
|----------|--------|---------------------|
| Java | `references/languages/java.md` | SQL注入、XXE、反序列化 |
| Python | `references/languages/python.md` | Pickle、SSTI、命令注入 |
| Go | `references/languages/go.md` | 竞争条件、SSRF |
| PHP | `references/languages/php.md` | 文件包含、反序列化 |
| JavaScript | `references/languages/javascript.md` | 原型污染、XSS |

### 安全领域模块（按需加载）

| 领域 | 模块 | 加载时机 |
|--------|--------|--------------|
| API Security | `references/security/api_security.md` | REST/GraphQL APIs |
| LLM Security | `references/security/llm_security.md` | AI/ML应用 |
| Serverless | `references/security/serverless.md` | AWS Lambda、Azure Functions |
| Cryptography | `references/security/cryptography.md` | 加密、TLS、JWT |
| Race Conditions | `references/security/race_conditions.md` | 并发操作 |

---

## 工具优先级策略

```
Priority 1: 外部专业工具（如可用）
├─ semgrep scan --config auto          # 多语言SAST
├─ bandit -r ./src                      # Python安全
├─ gosec ./...                          # Go安全
└─ gitleaks detect                      # 密钥扫描

Priority 2: 内置分析（始终可用）
├─ LSP语义分析                # goToDefinition、findReferences、incomingCalls
├─ Read + Grep模式匹配         # 核心分析
└─ 模块知识库                # 55+漏洞模式

Priority 3: 验证
├─ PoC模板 from references/core/poc_generation.md
└─ 置信度评分 from references/core/verification_methodology.md
```

---

## 详细文档

完整的审计方法论、漏洞模式和检测规则，请参阅：

- **完整工作流程**: `agent.md` - 完整审计流程和检测命令
- **漏洞详情**: `references/` - 语言/框架特定模式
- **工具集成**: `references/core/external_tools_guide.md`
- **报告模板**: `references/core/taint_analysis.md`

---

## 版本

- **当前版本**: 1.0
- **更新日期**: 2026-02-13

### v1.0（初始公开版本）
- 9语言143项强制检测清单 (`references/checklists/`)
- 双轨并行审计框架: Sink-driven + Control-driven + Config-driven
- Docker部署验证框架 (`references/core/docker_verification.md`)
- WooYun 88,636案例库集成
- 安全控制矩阵框架
