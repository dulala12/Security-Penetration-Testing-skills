# Business Logic Flaw 详细分析

面向业务流程与经济模型的缺陷梳理。

## 主要子类型

- **Invariant Violation**: 核心不变量被破坏
- **Reward/Accounting Error**: 奖励或会计逻辑错误
- **State Transition Bug**: 状态迁移缺失或顺序错误

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 识别关键不变量并检查约束
- 检查奖励/清算计算的边界条件
- 检查跨模块状态更新一致性

## 防御要点

### P0 (必须)
- 为关键流程建立不变量检查
- 在边界与极端输入下测试
- 关键状态变更前后做一致性校验

### P1 (推荐)
- 引入可回滚的安全开关
- 增加监控与异常阈值报警

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2025-02-23 - HegicOptions
2. 2024-06-08 - YYStoken
3. 2024-06-06 - MineSTM
4. 2024-06-04 - NCD
5. 2024-05-31 - Liquiditytokens
6. 2024-05-28 - Tradeonorion
7. 2024-05-28 - EXcommunity
8. 2024-05-26 - NORMIE
9. 2024-05-12 - TGC
10. 2024-04-30 - Yield
11. 2024-03-28 - LavaLending
12. 2024-03-09 - Juice
13. 2024-03-07 - GHT
14. 2024-03-06 - TGBS
15. 2024-02-23 - Zoomer

---

**返回**: [Business 概览](../../sub-skills/business-logic-flaw-skill.md)
