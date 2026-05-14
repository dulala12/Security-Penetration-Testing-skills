---
name: flashloan-attack-vulnerability
description: Flashloan attack vulnerability analysis covering 35 real cases with $159M total loss. Use when analyzing flashloan + price manipulation, flashloan + reentrancy, or flashloan + logic flaw combinations. Includes defense strategies using TWAP, reentrancy guards, and rate limiting.
---

# Flashloan Attack 漏洞分析

## 快速识别

### 核心特征
- ✓ 闪电贷 + 价格操纵组合
- ✓ 闪电贷 + 重入攻击组合
- ✓ 闪电贷 + 逻辑缺陷组合
- ✓ 使用 spot price 而非 TWAP
- ✓ 缺少速率限制或滑点保护

### 快速检查清单 (5 分钟)
- [ ] 检查价格源是否使用 spot price
- [ ] 验证是否有重入保护
- [ ] 检查大额操作是否有限制
- [ ] 确认是否有滑点保护
- [ ] 验证状态更新的原子性

### 本质公式
```
闪电贷攻击 = 闪电贷 × 其他漏洞 × 放大效应
```

## 漏洞分类

### 主要类型

**A. Flashloan + Price Manipulation (价格操纵)** - 45.7%
- 使用闪电贷操纵 spot price
- 典型案例: PancakeBunny ($45M), Harvest Finance ($34M)

**B. Flashloan + Reentrancy (重入)** - 20%
- 闪电贷放大重入攻击
- 典型案例: Cream Finance ($130M)

**C. Flashloan + Logic Flaw (逻辑缺陷)** - 34.3%
- 闪电贷利用逻辑漏洞
- 典型案例: bZx ($8M)


## 核心攻击模式

### 模式 1: 闪电贷 + 价格操纵

```solidity
// ❌ 漏洞代码
function getPrice() public view returns (uint256) {
    uint256 balance0 = token0.balanceOf(pair);
    uint256 balance1 = token1.balanceOf(pair);
    return balance1 * 1e18 / balance0;  // spot price 可被操纵
}

// 攻击步骤
// 1. 闪电贷借入大量 token0
// 2. 在 pair 中 swap，操纵 price
// 3. 利用被操纵的价格获利
// 4. 归还闪电贷
```

### 模式 2: 闪电贷 + 重入

```solidity
// ❌ 漏洞代码
function withdraw() external {
    uint256 amount = balances[msg.sender];
    (bool success,) = msg.sender.call{value: amount}("");  // 重入点
    balances[msg.sender] = 0;  // 状态更新在后
}

// 攻击: 使用闪电贷放大重入攻击
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 使用 TWAP 而非 spot price
function getPrice() public view returns (uint256) {
    return oracle.getTWAP(token, 30 minutes);
}

// ✅ 重入保护
modifier nonReentrant() {
    require(!locked);
    locked = true;
    _;
    locked = false;
}

// ✅ 速率限制
mapping(address => uint256) public lastActionTime;

function deposit() external {
    require(block.timestamp >= lastActionTime[msg.sender] + 1 hours);
    lastActionTime[msg.sender] = block.timestamp;
    // 存款逻辑
}
```


## 典型案例

### 案例 1: Rari Capital (2022-04-30)
- **损失**: $80M
- **类型**: Flashloan + Reentrancy
- **根因**: 闪电贷 + 重入攻击组合

### 案例 2: PancakeBunny (2021-05-20)
- **损失**: $45M
- **类型**: Flashloan + Price Manipulation
- **根因**: 使用 spot price 计算奖励

### 案例 3: Harvest Finance (2020-10-26)
- **损失**: $34M
- **类型**: Flashloan + Price Manipulation
- **根因**: 价格操纵导致套利


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 35 |
| 总损失 | $159M |
| 平均损失 | $4.5M |
| 最大损失 | $80M (Rari Capital) |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/flashloan-attack-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#flashloan-attack)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 35  
**最后更新**: 2026-01-29  
**行数**: ~200 (优化前: 1530)
