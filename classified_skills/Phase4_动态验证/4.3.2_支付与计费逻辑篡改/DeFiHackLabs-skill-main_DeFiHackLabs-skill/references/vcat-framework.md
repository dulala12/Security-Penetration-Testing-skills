# VCAT 分类框架

快速识别和分类 DeFi 漏洞的决策树系统。

## 框架概述

VCAT (Vulnerability Classification and Analysis Tree) 是一个基于 327+ 真实案例的漏洞分类决策树。

**核心价值:**
- 10 分钟内快速定位漏洞类型
- 基于特征而非经验的系统化判断
- 提供相似案例参考

## 决策树

### Level 1: 是否涉及外部调用?

```
是否涉及外部调用 (call, transfer, delegatecall)?
├─ 是 → 进入 Level 2A
└─ 否 → 进入 Level 2B
```

### Level 2A: 外部调用相关

```
外部调用是否在状态更新之前?
├─ 是 → Reentrancy (29 cases, $35M)
│   ├─ 单函数重入 (52%)
│   ├─ 只读重入 (28%)
│   └─ 跨函数重入 (20%)
└─ 否 → 调用目标是否可控?
    ├─ 是 → Arbitrary Call (34 cases, $23M)
    └─ 否 → 进入 Level 3
```

### Level 2B: 非外部调用相关

```
是否涉及价格或预言机?
├─ 是 → Price Manipulation (58 cases, $493M)
│   ├─ 闪电贷型 (77.6%)
│   ├─ 捐赠型 (13.8%)
│   └─ 三明治型 (8.6%)
└─ 否 → 进入 Level 3
```

### Level 3: 深度分类

```
是否涉及权限检查?
├─ 是 → Access Control (44 cases, $669M)
│   ├─ 缺少修饰符 (63.6%)
│   ├─ 逻辑错误 (27.3%)
│   └─ 初始化漏洞 (9.1%)
└─ 否 → 是否涉及数学计算?
    ├─ 是 → 是否有除法运算?
    │   ├─ 是 → Precision Loss (18 cases, $218M)
    │   └─ 否 → Logic Flaw (39 cases, $109M)
    └─ 否 → 是否涉及输入验证?
        ├─ 是 → Input Validation (27 cases, $130M)
        └─ 否 → Business Logic Flaw (38 cases, $95M)
```

## 特征识别表

### Reentrancy 特征
- ✓ 外部调用存在
- ✓ 状态更新在外部调用之后
- ✓ 缺少 `nonReentrant` 修饰符
- ✓ 调用栈深度异常 (>5)
- ✓ 同一函数被多次调用

### Price Manipulation 特征
- ✓ 使用 DEX 即时价格
- ✓ 价格变化 >10%
- ✓ 使用 `balanceOf()` 计算价格
- ✓ 缺少 TWAP 或 Chainlink
- ✓ 常与闪电贷组合

### Access Control 特征
- ✓ 特权函数缺少修饰符
- ✓ 没有 `onlyOwner` 或 `require(msg.sender == owner)`
- ✓ 回调函数未验证调用者
- ✓ 初始化函数缺少保护
- ✓ 任何人都可以调用关键函数

### Arbitrary Call 特征
- ✓ 用户可控的调用目标
- ✓ 缺少地址白名单
- ✓ 使用 `delegatecall`
- ✓ 函数选择器未验证

### Precision Loss 特征
- ✓ 除法运算导致截断
- ✓ 多层嵌套计算
- ✓ 汇率计算精度不足
- ✓ 小额操作累积误差
- ✓ 捐赠通胀攻击

### Input Validation 特征
- ✓ 缺少参数范围检查
- ✓ 零地址未验证
- ✓ 数组长度未检查
- ✓ 参数组合未验证

### Logic Flaw 特征
- ✓ 状态更新不完整
- ✓ 边界条件处理不当
- ✓ 数学计算错误
- ✓ 业务流程设计缺陷

### Business Logic Flaw 特征
- ✓ 激励机制设计缺陷
- ✓ 清算逻辑不合理
- ✓ 费用计算错误
- ✓ 抵押率设置不当

### Flashloan Attack 特征
- ✓ 使用闪电贷
- ✓ 单笔交易完成攻击
- ✓ 常与其他漏洞组合
- ✓ 攻击者无需大量初始资金

### Rug Pull 特征
- ✓ 隐藏的后门函数
- ✓ 时间条件触发器
- ✓ 魔法数字触发
- ✓ 未经审计的合约

## 快速分类流程

### 步骤 1: 收集特征 (2 分钟)
- [ ] 是否有外部调用?
- [ ] 是否使用闪电贷?
- [ ] 价格是否大幅波动?
- [ ] 是否调用特权函数?
- [ ] 是否有数学计算?

### 步骤 2: 匹配决策树 (3 分钟)
- 根据特征进入对应分支
- 逐层判断直到叶子节点
- 得到初步漏洞类型

### 步骤 3: 验证和细化 (5 分钟)
- 查看该类型的典型特征
- 对比相似案例
- 确认最终分类

## 组合攻击识别

### 常见组合模式

#### Flashloan + Price Manipulation (最常见)
- 占比: 45.7% 的闪电贷攻击
- 典型案例: PancakeBunny, Harvest Finance
- 特征: 大额借款 → 操纵价格 → 套利

#### Flashloan + Reentrancy
- 占比: 8.6% 的闪电贷攻击
- 典型案例: Rari Capital, Grim Finance
- 特征: 闪电贷 → 重入 → 超额提取

#### Flashloan + Oracle Manipulation
- 占比: 17.1% 的闪电贷攻击
- 典型案例: Mango Markets
- 特征: 操纵预言机 → 超额借款

#### Read-Only Reentrancy + Price Manipulation
- 新兴组合模式
- 典型案例: Curve, dForce, Conic
- 特征: 重入期间读取错误价格

## 分类统计

### 按案例数排序
1. Price Manipulation - 58 cases (17.7%)
2. Access Control - 44 cases (13.5%)
3. Logic Flaw - 39 cases (11.9%)
4. Business Logic - 38 cases (11.6%)
5. Flashloan Attack - 35 cases (10.7%)
6. Arbitrary Call - 34 cases (10.4%)
7. Reentrancy - 29 cases (8.9%)
8. Input Validation - 27 cases (8.3%)
9. Precision Loss - 18 cases (5.5%)
10. Rug Pull - 5 cases (1.5%)

### 按平均损失排序
1. Access Control - $15.2M
2. Precision Loss - $12.1M
3. Price Manipulation - $8.5M
4. Input Validation - $4.8M
5. Flashloan Attack - $3.2M
6. Logic Flaw - $2.8M
7. Business Logic - $2.5M
8. Reentrancy - $1.2M
9. Arbitrary Call - $676K
10. Rug Pull - $245K

## 实战案例

### 案例 1: 快速分类 Curve 攻击

**特征收集:**
- ✓ 有外部调用 (remove_liquidity)
- ✓ 状态更新在外部调用之后
- ✓ 同一函数被多次调用
- ✓ 调用栈深度异常

**决策树判断:**
Level 1: 有外部调用 → Level 2A
Level 2A: 外部调用在状态更新之前 → Reentrancy

**验证:**
- 符合重入攻击特征 ✓
- 相似案例: Visor Finance, Bacon Protocol ✓
- 最终分类: **Reentrancy (Read-Only)**

### 案例 2: 快速分类 PancakeBunny 攻击

**特征收集:**
- ✓ 使用闪电贷
- ✓ 价格大幅波动 (>100%)
- ✓ 使用 DEX 即时价格
- ✓ 缺少 TWAP

**决策树判断:**
Level 1: 无直接外部调用 → Level 2B
Level 2B: 涉及价格 → Price Manipulation

**验证:**
- 符合价格操纵特征 ✓
- 相似案例: Harvest Finance, Mango Markets ✓
- 最终分类: **Flashloan + Price Manipulation**

## 相关文档

- [IPDOR 分析框架](ipdor-framework.md) - 系统化分析方法
- [案例索引](case-index.md) - 按类型查看案例
- 各子技能文档 - 详细漏洞分析
