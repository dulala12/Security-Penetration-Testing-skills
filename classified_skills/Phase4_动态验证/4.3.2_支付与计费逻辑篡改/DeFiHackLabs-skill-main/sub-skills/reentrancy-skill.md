---
name: reentrancy-vulnerability
description: Reentrancy vulnerability analysis covering 20+ real cases with $100M+ total loss. Use when analyzing external calls before state updates, missing reentrancy guards, or callback vulnerabilities. Includes defense strategies using checks-effects-interactions pattern and ReentrancyGuard.
---

# Reentrancy 漏洞分析

## 快速识别

### 核心特征
- ✓ 外部调用在状态更新之前
- ✓ 缺少 `nonReentrant` 修饰符
- ✓ 回调函数可被利用
- ✓ 状态检查在外部调用之后
- ✓ 跨函数重入可能性

### 快速检查清单 (5 分钟)
- [ ] 检查外部调用是否在状态更新后
- [ ] 验证是否使用 ReentrancyGuard
- [ ] 检查回调函数的安全性
- [ ] 确认跨函数重入的可能性
- [ ] 验证 CEI 模式的遵循

### 本质公式
```
重入攻击 = 外部调用 + 状态未更新 + 递归调用
```

## 漏洞分类

### 主要类型

**A. Classic Reentrancy (经典重入)** - 50%
- 外部调用前状态未更新
- 典型案例: The DAO ($60M)

**B. Cross-Function Reentrancy (跨函数重入)** - 30%
- 通过不同函数重入
- 典型案例: Cream Finance

**C. Read-Only Reentrancy (只读重入)** - 15%
- 读取未更新的状态
- 典型案例: Curve Finance

**D. Callback Reentrancy (回调重入)** - 5%
- 利用回调函数重入
- 典型案例: ERC777 相关


## 核心攻击模式

### 模式 1: 经典重入

```solidity
// ❌ 漏洞代码
function withdraw() external {
    uint256 amount = balances[msg.sender];
    (bool success,) = msg.sender.call{value: amount}("");  // 外部调用在前
    require(success);
    balances[msg.sender] = 0;  // 状态更新在后
}

// 攻击: 在 fallback 中递归调用 withdraw

// ✅ 正确做法
function withdraw() external nonReentrant {
    uint256 amount = balances[msg.sender];
    balances[msg.sender] = 0;  // 先更新状态
    (bool success,) = msg.sender.call{value: amount}("");
    require(success);
}
```

### 模式 2: 跨函数重入

```solidity
// ❌ 漏洞代码
function withdraw() external {
    uint256 amount = balances[msg.sender];
    (bool success,) = msg.sender.call{value: amount}("");
    balances[msg.sender] = 0;
}

function transfer(address to, uint256 amount) external {
    require(balances[msg.sender] >= amount);  // 使用未更新的状态
    balances[msg.sender] -= amount;
    balances[to] += amount;
}

// 攻击: 在 withdraw 的回调中调用 transfer
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 使用 OpenZeppelin ReentrancyGuard
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract SecureContract is ReentrancyGuard {
    function withdraw() external nonReentrant {
        uint256 amount = balances[msg.sender];
        balances[msg.sender] = 0;
        (bool success,) = msg.sender.call{value: amount}("");
        require(success);
    }
}

// ✅ 遵循 CEI 模式 (Checks-Effects-Interactions)
function withdraw() external {
    // 1. Checks
    require(balances[msg.sender] > 0, "No balance");
    
    // 2. Effects
    uint256 amount = balances[msg.sender];
    balances[msg.sender] = 0;
    
    // 3. Interactions
    (bool success,) = msg.sender.call{value: amount}("");
    require(success);
}
```


## 典型案例

### 案例 1: The DAO (2016-06-17)
- **损失**: $60M
- **类型**: Classic Reentrancy
- **根因**: 外部调用前状态未更新

### 案例 2: Cream Finance (2021-08-30)
- **损失**: $18M
- **类型**: Cross-Function Reentrancy
- **根因**: 跨函数重入攻击

### 案例 3: Lendf.Me (2020-04-19)
- **损失**: $25M
- **类型**: ERC777 Reentrancy
- **根因**: ERC777 回调函数重入


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 20+ |
| 总损失 | $100M+ |
| 平均损失 | $5M |
| 最大损失 | $60M (The DAO) |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/reentrancy-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#reentrancy)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 20+  
**最后更新**: 2026-01-29  
**行数**: ~180 (优化前: 991)
