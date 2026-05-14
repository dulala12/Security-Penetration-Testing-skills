# Input Validation 详细分析

输入校验不足导致的越权或异常状态。

## 主要子类型

- **Boundary Check Missing**: 范围/长度未校验
- **Type/Format Mismatch**: 类型或格式验证缺失
- **Assumption Violation**: 对外部输入的假设不成立

## 典型攻击路径

1. 选择可操纵的输入/状态（价格源、回调、参数）
2. 放大可利用窗口（组合闪电贷、时序缺陷或权限绕过）
3. 触发漏洞获得不当收益
4. 清算或洗出资金

## 检测清单

- 检查外部输入的范围/长度
- 检查地址/枚举/标志位是否校验
- 检查是否依赖外部状态假设

## 防御要点

### P0 (必须)
- 对所有外部输入做严格校验
- 设置上下限与白名单

### P1 (推荐)
- 对复杂输入使用结构化验证
- 增加错误码与可观测性

## 案例库（来自 past/README.md 与根目录 README.md）

1. 2025-08-31 - Hexotic
2. 2024-12-23 - Moonhacker
3. 2024-04-24 - YIEDL
4. 2023-11-29 - AIS
5. 2023-07-31 - GYMNET
6. 2022-12-11 - - MEVbot_0x28d9
7. 2022-03-22 - CompoundTUSDSweepTokenBypass
8. 2021-09-16 - SushiSwap Miso
9. 2020-11-21 - Pickle Finance

---

**返回**: [Input 概览](../../sub-skills/input-validation-skill.md)
