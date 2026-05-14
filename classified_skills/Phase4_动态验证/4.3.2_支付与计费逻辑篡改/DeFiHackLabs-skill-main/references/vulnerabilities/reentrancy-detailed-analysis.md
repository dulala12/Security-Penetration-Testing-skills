# Reentrancy 详细分析

归纳重入攻击的类型、触发条件与防护策略。

## 主要子类型

- **Classic Reentrancy**: 外部调用在状态更新前
- **Cross-Function Reentrancy**: 跨函数共享状态导致重入
- **Read-Only Reentrancy**: 只读路径被利用影响估值/清算

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查外部调用是否早于状态更新
- 检查是否缺少 nonReentrant
- 检查回调函数是否鉴权
- 检查跨函数共享状态是否可重入

## 防御要点

### P0 (必须)
- 遵循 CEI 模式
- 使用 ReentrancyGuard
- 回调函数严格校验 msg.sender

### P1 (推荐)
- 拆分敏感函数并降低可重入面
- 对只读路径加价格/状态快照

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2025-02-22 - Unverified_35bc
2. 2024-12-27 - Bizness
3. 2024-12-10 - CloberDEX
4. 2024-11-11 - DeltaPrime
5. 2024-07-14 - Minterest
6. 2024-07-02 - MRP
7. 2024-05-14 - PredyFinance
8. 2024-04-12 - SumerMoney
9. 2024-02-28 - SMOOFSStaking
10. 2024-01-29 - PeapodsFinance
11. 2024-01-28 - BarleyFinance
12. 2024-01-25 - NBLGAME
13. 2023-12-16 - NFTTrader
14. 2023-10-07 - StarsArena
15. 2023-09-26 - XSDWETHpool

---

**返回**: [Reentrancy 概览](../../sub-skills/reentrancy-skill.md)
