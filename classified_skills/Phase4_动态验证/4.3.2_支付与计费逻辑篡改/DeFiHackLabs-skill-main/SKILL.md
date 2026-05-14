---
name: defi-vulnerability-analysis
description: Comprehensive DeFi security vulnerability analysis framework covering 10 major vulnerability types with 327+ real-world cases. Use when analyzing DeFi security incidents, conducting smart contract audits, identifying vulnerability patterns, writing PoC exploits, or learning DeFi security best practices. Includes systematic analysis methodologies (IPDOR, VCAT), vulnerability classification, attack path templates, and defense strategies.
---

# DeFi 漏洞分析技能

## 概述

本技能提供系统化的 DeFi 安全漏洞分析框架,基于 327+ 个真实案例,覆盖 10 大核心漏洞类型。

**核心能力:**
- 快速识别和分类 DeFi 漏洞
- 系统化分析攻击事件
- 编写和验证 PoC 代码
- 提供分层防御方案

**适用场景:**
- 分析链上攻击事件
- 智能合约安全审计
- 漏洞研究和学习
- 编写安全测试用例

## 快速开始

### 分析新攻击事件

使用 **IPDOR 框架** 进行系统化分析:

1. **Information Gathering (信息采集)** - 5-10 分钟
   - 获取交易哈希和区块信息
   - 识别受影响的合约地址
   - 收集损失金额和代币信息

2. **Pattern Recognition (模式识别)** - 10-20 分钟
   - 使用漏洞分类决策树快速定位类型
   - 查看类似案例模式

3. **Deconstruction (攻击解构)** - 20-40 分钟
   - 分析调用栈和状态变化
   - 识别关键漏洞函数
   - 理解攻击者获利路径

4. **Root Cause Analysis (根因分析)** - 30-60 分钟
   - 定位漏洞代码
   - 分析为何防御失效
   - 评估影响范围

5. **Reproduction (可执行复现)** - 1-2 小时
   - 编写 Foundry PoC
   - 验证攻击路径
   - 生成分析报告

详细方法论请参考: [IPDOR 分析框架](references/ipdor-framework.md)

### 识别漏洞类型

使用 **VCAT 决策树** 快速分类:

```
是否涉及价格/预言机?
├─ 是 → Price Manipulation (58 cases, $493M)
└─ 否 → 是否涉及权限检查?
    ├─ 是 → Access Control (44 cases, $669M)
    └─ 否 → 是否涉及外部调用?
        ├─ 是 → Reentrancy (29 cases, $35M)
        └─ 否 → 继续检查...
```

完整决策树: [VCAT 分类框架](references/vcat-framework.md)

## 十大漏洞类型

每个漏洞类型都有详细的子技能文档:

### 1. Access Control (访问控制)
- **案例数**: 32 | **平均损失**: $15.2M
- **典型案例**: Ronin Bridge ($625M), Poly Network ($611M)
- **详细文档**: [access-control-skill.md](sub-skills/access-control-skill.md)

### 2. Price Manipulation (价格操纵)
- **案例数**: 58 | **平均损失**: $8.5M
- **典型案例**: Mango Markets ($116M), Cream Finance ($130M)
- **详细文档**: [price-manipulation-skill.md](sub-skills/price-manipulation-skill.md)

### 3. Reentrancy (重入攻击)
- **案例数**: 29 | **平均损失**: $1.2M
- **典型案例**: Curve ($41M), Penpiexyz ($27M)
- **详细文档**: [reentrancy-skill.md](sub-skills/reentrancy-skill.md)

### 4. Business Logic Flaw (业务逻辑缺陷)
- **案例数**: 37 | **平均损失**: $2.5M
- **典型案例**: Euler Finance ($197M), Nomad Bridge ($190M)
- **详细文档**: [business-logic-flaw-skill.md](sub-skills/business-logic-flaw-skill.md)

### 5. Logic Flaw (逻辑缺陷)
- **案例数**: 39 | **平均损失**: $2.8M
- **典型案例**: HedgeyFinance ($48M), Spartan ($30.5M)
- **详细文档**: [logic-flaw-skill.md](sub-skills/logic-flaw-skill.md)

### 6. Flashloan Attack (闪电贷攻击)
- **案例数**: 18 | **平均损失**: $3.2M
- **详细文档**: [flashloan-attack-skill.md](sub-skills/flashloan-attack-skill.md)

### 7. Input Validation (输入验证缺陷)
- **案例数**: 9 | **平均损失**: $4.8M
- **详细文档**: [input-validation-skill.md](sub-skills/input-validation-skill.md)

### 8. Precision Loss (精度损失)
- **案例数**: 2 | **平均损失**: $12.1M
- **详细文档**: [precision-loss-skill.md](sub-skills/precision-loss-skill.md)

### 9. Arbitrary Call (任意调用)
- **案例数**: 5 | **平均损失**: $676K
- **详细文档**: [arbitrary-call-skill.md](sub-skills/arbitrary-call-skill.md)

### 10. Rug Pull (跑路)
- **案例数**: 2 | **平均损失**: $245K
- **详细文档**: [rug-pull-skill.md](sub-skills/rug-pull-skill.md)

## PoC 编写

### 使用 Foundry 模板

基础测试结构:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.10;

import "forge-std/Test.sol";

contract ExploitTest is Test {
    function setUp() public {
        vm.createSelectFork("mainnet", BLOCK_NUMBER - 1);
        // 初始化合约
    }
    
    function testExploit() public {
        // 1. 记录攻击前状态
        // 2. 执行攻击
        // 3. 验证攻击成功
    }
}
```

## 防御策略

### 分层防御框架

1. **开发阶段** - 使用安全编码规范和标准库
2. **测试阶段** - 静态分析 + 模糊测试 + 形式化验证
3. **部署阶段** - 渐进式启动 + 多签 + 时间锁
4. **运营阶段** - 实时监控 + 断路器 + 应急响应

## 工具和资源

### 分析工具
- **Tenderly** - 交易模拟和调试
- **Etherscan** - 链上数据查询
- **Foundry** - 智能合约测试框架

### 安全工具
- **Slither** - 静态分析
- **Mythril** - 符号执行
- **Echidna** - 模糊测试

工具使用指南: [工具和资源](references/tools-and-resources.md)

## 案例索引

### 按损失金额排序 (Top 10)
1. Ronin Bridge (2022-03) - $625M - Access Control
2. Poly Network (2021-08) - $611M - Access Control
3. Wormhole (2022-02) - $326M - Access Control
4. Euler Finance (2023-03) - $197M - Business Logic
5. Nomad Bridge (2022-08) - $190M - Business Logic
6. Cream Finance (2021-10) - $130M - Price Manipulation
7. Multichain (2023-07) - $126M - Input Validation
8. BalancerV2 (2025-11) - $120M - Precision Loss
9. Mango Markets (2022-10) - $116M - Price Manipulation
10. OrbitChain (2023-12) - $81M - Input Validation

完整案例库: [案例索引](references/case-index.md)

## 维护与更新

维护规则与更新流程: [维护指南](references/maintenance.md)

## 参考文档

### 核心框架
- [IPDOR 分析框架](references/ipdor-framework.md) - 系统化攻击分析方法
- [VCAT 分类框架](references/vcat-framework.md) - 漏洞快速分类决策树

### 技术指南
- [工具使用指南](references/tools-and-resources.md) - 分析和安全工具

### 维护
- [维护指南](references/maintenance.md) - 更新流程与质量检查

## 贡献和反馈

本技能基于 327+ 个真实 DeFi 安全事件持续更新。

**版本**: v1.0  
**最后更新**: 2026-01-29  
**案例覆盖**: 2017-2026  
**总损失**: $500M+

---

*本技能旨在帮助安全研究人员、审计人员和开发者系统性地理解和防御 DeFi 漏洞。*
