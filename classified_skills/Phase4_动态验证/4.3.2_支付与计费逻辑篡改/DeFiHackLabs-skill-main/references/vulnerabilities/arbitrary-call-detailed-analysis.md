# Arbitrary Call 详细分析

任意调用/委托调用导致的权限绕过。

## 主要子类型

- **Unrestricted Call**: 目标地址或数据可控
- **Delegatecall Abuse**: 上下文被劫持
- **Callback Injection**: 回调被伪造或绕过

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查目标地址是否可控
- 检查是否允许任意 selector
- 检查 delegatecall 使用位置

## 防御要点

### P0 (必须)
- 对目标地址做白名单
- 限制可调用函数选择器
- 禁止或严格约束 delegatecall

### P1 (推荐)
- 对回调函数做严格鉴权
- 记录调用审计日志

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2024-08-14 - YodlRouter
2. 2024-05-31 - MixedSwapRouter
3. 2024-04-20 - Rico
4. 2022-09-28 - MEVBOT
5. 2022-03-26 - Auctus

---

**返回**: [Arbitrary 概览](../../sub-skills/arbitrary-call-skill.md)
