# Precision Loss 详细分析

整数运算与舍入导致的精度损失类漏洞。

## 主要子类型

- **Division Precision Loss**: 除法截断导致汇率错误
- **Rounding Accumulation**: 多次舍入误差累积
- **Share/Rate Manipulation**: 份额或比率计算可被操纵

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查除法/乘法顺序
- 检查最小存取金额/份额
- 检查多次计算的舍入累积

## 防御要点

### P0 (必须)
- 先乘后除减少截断
- 限制最小存取金额

### P1 (推荐)
- 使用高精度库或固定点库
- 在关键计算处加入误差上限

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2025-11-03 - BalancerV2
2. 2023-06-17 - MidasCapitalXYZ

---

**返回**: [Precision 概览](../../sub-skills/precision-loss-skill.md)
