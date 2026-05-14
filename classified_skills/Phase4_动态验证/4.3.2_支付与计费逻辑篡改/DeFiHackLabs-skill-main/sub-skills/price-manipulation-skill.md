---
name: price-manipulation-vulnerability
description: Price manipulation vulnerability analysis covering 50+ real cases with $200M+ total loss. Use when analyzing spot price usage, oracle manipulation, AMM price attacks, or TWAP bypass. Includes defense strategies using Chainlink oracles, TWAP, and multi-source price feeds.
---

# Price Manipulation 漏洞分析

## 快速识别

### 核心特征
- ✓ 使用 spot price 而非 TWAP
- ✓ 单一价格源（无冗余）
- ✓ AMM 储备量可被操纵
- ✓ 缺少价格变化限制
- ✓ 预言机更新延迟

### 快速检查清单 (5 分钟)
- [ ] 检查价格源是否使用 spot price
- [ ] 验证是否有多个价格源
- [ ] 确认是否使用 TWAP
- [ ] 检查价格变化是否有限制
- [ ] 验证预言机的可靠性

### 本质公式
```
价格操纵 = 可操纵价格源 × 大额资金 × 即时利用
```

## 漏洞分类

### 主要类型

**A. Spot Price Manipulation (现货价格操纵)** - 60%
- 使用 AMM spot price
- 典型案例: Harvest Finance ($34M)

**B. Oracle Manipulation (预言机操纵)** - 25%
- 预言机数据可被操纵
- 典型案例: Mango Markets ($100M)

**C. TWAP Bypass (TWAP 绕过)** - 10%
- 绕过 TWAP 保护
- 典型案例: 多个案例

**D. Multi-Block Manipulation (多区块操纵)** - 5%
- 跨多个区块操纵价格
- 典型案例: 高级攻击


## 核心攻击模式

### 模式 1: Spot Price 操纵

```solidity
// ❌ 漏洞代码
function getPrice() public view returns (uint256) {
    uint256 balance0 = token0.balanceOf(pair);
    uint256 balance1 = token1.balanceOf(pair);
    return balance1 * 1e18 / balance0;  // spot price 可被操纵
}

// 攻击步骤
// 1. 闪电贷借入大量 token0
// 2. 在 pair 中 swap，操纵 balance
// 3. 调用使用 getPrice() 的函数获利
// 4. 归还闪电贷

// ✅ 正确做法
function getPrice() public view returns (uint256) {
    return oracle.getTWAP(token, 30 minutes);  // 使用 TWAP
}
```

### 模式 2: 预言机操纵

```solidity
// ❌ 漏洞代码
function updatePrice() external {
    uint256 newPrice = externalOracle.getPrice();  // 单一来源
    price = newPrice;
}

// ✅ 正确做法
function updatePrice() external {
    uint256 price1 = chainlink.getPrice();
    uint256 price2 = uniswapTWAP.getPrice();
    uint256 price3 = bandProtocol.getPrice();
    
    // 使用中位数
    price = median(price1, price2, price3);
}
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 使用 Chainlink 价格预言机
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

function getChainlinkPrice() public view returns (uint256) {
    (, int256 price,,,) = priceFeed.latestRoundData();
    require(price > 0, "Invalid price");
    return uint256(price);
}

// ✅ 使用 Uniswap V3 TWAP
import "@uniswap/v3-periphery/contracts/libraries/OracleLibrary.sol";

function getTWAP(uint32 period) public view returns (uint256) {
    (int24 tick,) = OracleLibrary.consult(pool, period);
    return OracleLibrary.getQuoteAtTick(tick, 1e18, token0, token1);
}

// ✅ 价格变化限制
uint256 constant MAX_PRICE_CHANGE = 10;  // 10%

function updatePrice(uint256 newPrice) external {
    uint256 change = abs(newPrice - currentPrice) * 100 / currentPrice;
    require(change <= MAX_PRICE_CHANGE, "Price change too large");
    currentPrice = newPrice;
}
```


## 典型案例

### 案例 1: Mango Markets (2022-10-11)
- **损失**: $100M
- **类型**: Oracle Manipulation
- **根因**: 预言机价格可被操纵

### 案例 2: Harvest Finance (2020-10-26)
- **损失**: $34M
- **类型**: Spot Price Manipulation
- **根因**: 使用 Curve spot price

### 案例 3: Cream Finance (2021-10-27)
- **损失**: $130M
- **类型**: Price Oracle Manipulation
- **根因**: 价格预言机漏洞


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 50+ |
| 总损失 | $200M+ |
| 平均损失 | $4M |
| 最大损失 | $100M (Mango Markets) |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/price-manipulation-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#price-manipulation)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 50+  
**最后更新**: 2026-01-29  
**行数**: ~180 (优化前: 547)
