---
name: ctf-writeup
description: 为比赛交接和组织者审查生成标准化的提交风格CTF writeup。在解决CTF挑战后使用，以结构化格式记录解决方案步骤、使用的工具和经验教训。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash和Python 3。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "true"
  argument-hint: "[challenge-name]"
---

# CTF Write-up生成器

为已解决的挑战生成标准化的提交风格CTF writeup。

默认行为：

- 在活跃比赛期间，优化速度、清晰度和可重现性
- 保持writeup足够短，以便队友或组织者可以快速验证解决方案
- 始终生成`submission`风格的writeup
- 优先选择从挑战数据到最终flag的一个完整求解脚本

## 工作流程

### 步骤1：收集信息

从当前会话、挑战文件和用户输入中收集以下内容：

1. **挑战元数据** — 名称、CTF事件、类别、难度、分数、flag格式
2. **解决方案工件** — 漏洞利用脚本、payload、截图、命令输出
3. **时间线** — 采取的关键步骤、死胡同、转向

```bash
# 扫描漏洞利用脚本和工件
find . -name '*.py' -o -name '*.sh' -o -name 'exploit*' -o -name 'solve*' | head -20
# 检查输出文件中的flag
grep -rniE '(flag|ctf|eno|htb|pico)\{' . 2>/dev/null
```

### 步骤2：生成Write-up

使用下面的提交模板将writeup文件写为`writeup.md`（或`writeup-<challenge-name>.md`）。

---

## 模板

### 提交格式

```markdown
---
title: "<Challenge Name>"
ctf: "<CTF Event Name>"
date: YYYY-MM-DD
category: web|pwn|crypto|reverse|forensics|osint|malware|misc
difficulty: easy|medium|hard
points: <number>
flag_format: "flag{...}"
author: "<your name or team>"
---

# <Challenge Name>

## Summary

<1-2句话：挑战内容和核心技术。保持直接。>

## Solution

### Step 1: <Action>

<用3-8行短行解释关键观察。保持直接。>

\`\`\`python
<从提供的挑战数据到打印最终flag的一个完整求解脚本>
\`\`\`

### Step 2: <Action> (optional)

<仅当第二个简短步骤确实有助于可读性时添加，例如将核心观察与最终验证分开。>

### Step 3: <Action> (optional)

<仅在挑战确实需要时使用。保持步骤总数少。>

## Flag

\`\`\`
flag{example_flag_here}
\`\`\`
```

指导原则：

- 优先选择总共1-3个简短步骤
- 保持代码为最小的完整求解脚本
- 不要将"恢复秘密"、"派生密钥"和"解密flag"拆分成单独的部分代码片段
- 脚本应从挑战数据开始，以打印flag结束
- 避免冗长的背景部分
- 避免死胡同，除非它们解释了关键转向
- 避免多种替代解决方案；选择一条清晰的路径
- 仅在用户明确要求时才编辑flag

---

## 最佳实践清单

在最终确定writeup之前，验证：

- [ ] **元数据完整** — 标题、CTF、日期、类别、难度、分数、作者全部填写
- [ ] **flag处理符合请求** — 保留真实flag，除非用户要求编辑
- [ ] **步骤可重现** — 读者可以按照您的writeup重现解决方案
- [ ] **代码可运行** — 漏洞利用脚本包含所有导入、正确的变量名和注释
- [ ] **无敏感数据** — 无真实凭证、API密钥或私有基础设施详情
- [ ] **长度保持简洁** — writeup足够短以便快速审查
- [ ] **注明工具和版本** — 如果行为依赖于特定工具版本，请提及
- [ ] **适当归因** — 归功于队友、引用的writeup或至关重要的工具
- [ ] **语法和格式** — 标题级别一致，代码块有语言标签

## 质量指南

**应该做：**
- 解释足够的内容以便快速验证
- 包含一条完整的求解路径，而不是多条替代路线
- 包含一个完整的脚本，一直运行到最终flag
- 显示实际输出（如果很长则截断）以证明方法有效
- 为代码块标记语言（`python`、`bash`、`sql`等）
- 保持主要路径靠前，以便读者可以快速验证

**不应该做：**
- 复制粘贴原始终端转储而不加解释
- 粘贴多个部分代码片段，迫使读者重建最终解决方案
- 在最终writeup中留下占位符文本
- 包含与解决方案无关的无关内容
- 假设读者知道特定的挑战设置

## 挑战

$ARGUMENTS