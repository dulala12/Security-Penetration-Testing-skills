# Price Manipulation 详细分析

基于真实事件梳理价格操纵的常见模式、攻击路径与防御要点。

## 主要子类型

- **Spot Price Manipulation**: 现货价格被 AMM/池子操纵
- **Oracle Manipulation**: 预言机数据被篡改或时序被利用
- **TWAP Bypass**: 时间窗口被绕过或窗口过短

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查是否直接使用 spot price
- 检查是否仅依赖单一价格源
- 检查 TWAP 窗口是否过短
- 检查是否缺少价格变化阈值

## 防御要点

### P0 (必须)
- 多源预言机 + 中位数聚合
- 启用 TWAP 且设置最小窗口
- 关键操作前后做价格一致性校验

### P1 (推荐)
- 限制价格变化幅度与速率
- 对高风险操作加延迟或分段执行

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2025-11-10 - DRLVaultV3
2. 2025-09-18 - NGP
3. 2025-08-16 - d3xai
4. 2025-08-15 - PDZ
5. 2025-08-13 - YuliAI
6. 2025-06-23 - GradientMakerPool
7. 2025-05-11 - MBUToken
8. 2025-05-09 - Nalakuvara_LotteryTicket50
9. 2025-04-26 - Lifeprotocol
10. 2025-03-20 - BBXToken
11. 2025-03-07 - SBR Token
12. 2025-02-08 - Peapods Finance
13. 2025-01-11 - RoulettePotV2
14. 2024-10-06 - SASHAToken
15. 2024-10-02 - LavaLending

---

**返回**: [Price 概览](../../sub-skills/price-manipulation-skill.md)
