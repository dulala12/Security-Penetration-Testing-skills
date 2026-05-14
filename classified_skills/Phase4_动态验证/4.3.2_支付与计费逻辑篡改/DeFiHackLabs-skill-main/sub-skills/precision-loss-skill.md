---
name: precision-loss-vulnerability
description: Precision loss vulnerability analysis covering 18 real cases with $218M total loss. Use when analyzing integer division truncation, donation inflation attacks, nested calculation errors, or share manipulation. Includes defense strategies using higher precision constants, Math.mulDiv, and minimum deposit requirements.
---

# Precision Loss 漏洞分析

## 快速识别

### 核心特征
- ✓ 整数除法导致截断（`a / b` 丢失余数）
- ✓ 捐赠资产操纵 shares/assets 汇率
- ✓ 嵌套计算导致精度累积损失
- ✓ 缺少最小存款限制
- ✓ 未使用高精度常量或 Math.mulDiv

### 快速检查清单 (5 分钟)
- [ ] 检查除法运算是否会产生截断
- [ ] 验证是否可以通过捐赠操纵汇率
- [ ] 检查是否有嵌套的乘除运算
- [ ] 确认是否有最小存款限制
- [ ] 验证是否使用了高精度库

### 本质公式
```
精度损失 = 整数运算 + 舍入/截断 + 误差累积 + 可操纵性
```

## 漏洞分类

### 主要类型

**A. Donate Inflation + Rounding Error (捐赠通胀)** - 33.3%
- 捐赠资产操纵汇率，利用舍入误差
- 典型案例: HundredFinance ($7M), Sonne Finance ($20M)

**B. Division Precision Loss (除法精度损失)** - 11.1%
- 整数除法截断导致精度丢失
- 典型案例: BalancerV2 ($120M), KyberSwap ($48M)

**C. Nested Calculation Error (嵌套计算错误)** - 5.6%
- 多层嵌套计算累积误差
- 典型案例: Sturdy Finance ($800K)

**D. Share Manipulation (份额操纵)** - 其他
- 通过操纵份额计算获利
- 多个案例


## 核心攻击模式

### 模式 1: 捐赠通胀攻击

```solidity
// ❌ 漏洞代码
function deposit(uint256 amount) external returns (uint256 shares) {
    uint256 totalAssets = asset.balanceOf(address(this));
    uint256 totalShares = totalSupply();
    
    if (totalShares == 0) {
        shares = amount;
    } else {
        shares = amount * totalShares / totalAssets;  // 可被操纵
    }
    
    _mint(msg.sender, shares);
    asset.transferFrom(msg.sender, address(this), amount);
}

// 攻击步骤
// 1. 攻击者存入 1 wei，获得 1 share
// 2. 直接转账 1000000 ether 到合约（不通过 deposit）
// 3. 汇率变为: 1 share / 1000000 ether
// 4. 受害者存入 999999 ether，获得 0 shares（截断）
// 5. 攻击者赎回 1 share，获得所有资产
```

### 模式 2: 除法精度损失

```solidity
// ❌ 漏洞代码
function calculateReward(uint256 amount) public view returns (uint256) {
    uint256 rate = totalRewards / totalStaked;  // 精度损失
    return amount * rate;
}

// 如果 totalRewards = 100, totalStaked = 1000
// rate = 100 / 1000 = 0 (截断)
// 所有奖励都丢失了

// ✅ 正确做法
function calculateReward(uint256 amount) public view returns (uint256) {
    return amount * totalRewards / totalStaked;  // 先乘后除
}
```

### 模式 3: 嵌套计算误差

```solidity
// ❌ 漏洞代码
function complexCalculation(uint256 x) public pure returns (uint256) {
    uint256 a = x / 100;        // 第一次精度损失
    uint256 b = a * 50 / 100;   // 第二次精度损失
    uint256 c = b * 200 / 100;  // 第三次精度损失
    return c;
}

// 输入 199: 199/100=1, 1*50/100=0, 0*200/100=0
// 实际应该: 199 * 0.5 * 2 = 199

// ✅ 正确做法
function complexCalculation(uint256 x) public pure returns (uint256) {
    return x * 50 * 200 / (100 * 100);  // 先乘后除，减少精度损失
}
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 1. 使用高精度常量
uint256 constant PRECISION = 1e18;

function calculateShare(uint256 amount) public view returns (uint256) {
    return amount * totalShares * PRECISION / totalAssets / PRECISION;
}

// ✅ 2. 设置最小存款限制
uint256 constant MIN_DEPOSIT = 1e6;  // 1 USDC

function deposit(uint256 amount) external {
    require(amount >= MIN_DEPOSIT, "Amount too small");
    // 存款逻辑
}

// ✅ 3. 预铸造 shares 防止捐赠攻击
constructor() {
    _mint(address(this), 1000);  // 预铸造 1000 shares
}

// ✅ 4. 使用 OpenZeppelin Math.mulDiv
import "@openzeppelin/contracts/utils/math/Math.sol";

function calculateShare(uint256 amount) public view returns (uint256) {
    return Math.mulDiv(amount, totalShares, totalAssets);
}
```

### 推荐实施 (P1)

```solidity
// ✅ 先乘后除
function calculate(uint256 x, uint256 y, uint256 z) public pure returns (uint256) {
    return x * y / z;  // 而不是 (x / z) * y
}

// ✅ 使用 SafeMath 或 Solidity 0.8+
// Solidity 0.8+ 自动检查溢出

// ✅ 添加精度检查
function deposit(uint256 amount) external returns (uint256 shares) {
    shares = calculateShares(amount);
    require(shares > 0, "Shares cannot be zero");
    _mint(msg.sender, shares);
}
```


## 典型案例

### 案例 1: BalancerV2 (2025-11-03)
- **损失**: $120M
- **类型**: Division Precision Loss
- **根因**: 除法精度损失导致汇率计算错误
- **PoC**: `src/test/2025-11/BalancerV2_exp.sol`

### 案例 2: KyberSwap (2023-11-23)
- **损失**: $48M
- **类型**: Division Precision Loss
- **根因**: 复杂的数学运算中的精度损失

### 案例 3: Sonne Finance (2024-05-15)
- **损失**: $20M
- **类型**: Donate Inflation
- **根因**: 捐赠攻击操纵 shares/assets 汇率


## PoC 快速模板

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.10;

import "forge-std/Test.sol";

contract PrecisionLossExploitTest is Test {
    address constant VICTIM_VAULT = 0x...;
    address constant ASSET_TOKEN = 0x...;
    
    function setUp() public {
        vm.createSelectFork("mainnet", BLOCK_NUMBER - 1);
    }
    
    function testDonateInflation() public {
        // 1. 存入最小金额获得 1 share
        IERC20(ASSET_TOKEN).approve(VICTIM_VAULT, 1);
        IVault(VICTIM_VAULT).deposit(1);
        
        // 2. 直接转账大量资产（捐赠）
        IERC20(ASSET_TOKEN).transfer(VICTIM_VAULT, 1000000 ether);
        
        // 3. 受害者存款获得 0 shares
        vm.prank(victim);
        IERC20(ASSET_TOKEN).approve(VICTIM_VAULT, 999999 ether);
        IVault(VICTIM_VAULT).deposit(999999 ether);
        
        // 4. 攻击者赎回获得所有资产
        uint256 shares = IVault(VICTIM_VAULT).balanceOf(address(this));
        IVault(VICTIM_VAULT).redeem(shares);
        
        // 验证利润
        assertGt(IERC20(ASSET_TOKEN).balanceOf(address(this)), 1000000 ether);
    }
}
```


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 18 |
| 总损失 | $218M |
| 平均损失 | $12.1M |
| 最大损失 | $120M (BalancerV2) |
| 高发年份 | 2023-2024 |
| 主要协议 | Compound V2 forks, Balancer, Kyber |

## 子类型分布

| 子类型 | 案例数 | 占比 | 总损失 |
|--------|--------|------|--------|
| Donate Inflation | 6 | 33.3% | $10.5M |
| Division Precision Loss | 2 | 11.1% | $168M |
| Nested Calculation | 1 | 5.6% | $800K |
| 其他 | 9 | 50% | $39M |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/precision-loss-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#precision-loss)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 18  
**最后更新**: 2026-01-29  
**行数**: ~250 (优化前: 2128)
