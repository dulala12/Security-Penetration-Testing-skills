# Flashloan Attack 详细分析

闪电贷放大攻击的组合模式与防御策略。

## 主要子类型

- **Flashloan + Price Manipulation**: 借入资金操纵价格
- **Flashloan + Reentrancy**: 放大重入获利
- **Flashloan + Logic Flaw**: 利用逻辑缺陷获取不当收益

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查关键路径是否依赖单块内价格/状态
- 检查是否存在大额瞬时资金可利用窗口
- 检查是否缺少速率限制

## 防御要点

### P0 (必须)
- 限制单块内状态变化幅度
- 对敏感路径增加速率限制
- 引入 TWAP 或跨块验证

### P1 (推荐)
- 对高风险操作做延迟执行
- 将奖励或清算分批处理

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2022-09-28 - BXH
2. 2022-09-08 - NewFreeDAO
3. 2022-06-16 - InverseFinance
4. 2022-06-06 - Discover
5. 2022-05-29 - NOVO Protocol
6. 2022-05-17 - ApeCoin (APE)
7. 2022-04-30 - Rari Capital/Fei Protocol
8. 2022-04-28 - DEUS DAO
9. 2022-04-24 - Wiener DOGE
10. 2022-04-12 - ElephantMoney
11. 2022-04-09 - GYMNetwork
12. 2022-03-21 - OneRing Finance
13. 2022-03-13 - Paraluni
14. 2021-12-18 - Grim Finance
15. 2021-08-30 - Cream Finance

---

**返回**: [Flashloan 概览](../../sub-skills/flashloan-attack-skill.md)
