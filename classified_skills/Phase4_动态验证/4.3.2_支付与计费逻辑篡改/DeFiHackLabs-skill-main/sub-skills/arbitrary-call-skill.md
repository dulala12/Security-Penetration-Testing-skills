---
name: arbitrary-call-vulnerability
description: Arbitrary call vulnerability analysis covering 34 real cases with $23M total loss. Use when analyzing user-controlled call targets, missing address whitelists, unchecked delegatecall, or arbitrary external calls. Includes defense strategies using address validation, function selector checks, and whitelist patterns.
---

# Arbitrary Call 漏洞分析

## 快速识别

### 核心特征
- ✓ 用户可控的 `call` 目标地址
- ✓ 缺少地址白名单验证
- ✓ 未检查函数选择器
- ✓ `delegatecall` 到用户指定地址
- ✓ 批量调用功能缺少验证

### 快速检查清单 (5 分钟)
- [ ] 检查 `call`/`delegatecall` 的目标是否用户可控
- [ ] 验证是否有地址白名单
- [ ] 检查函数选择器是否被验证
- [ ] 确认批量调用是否有限制
- [ ] 验证回调函数的调用者

### 本质公式
```
任意调用 = 用户可控目标 × 缺少验证 × 特权上下文
```

## 漏洞分类

### 主要类型

**A. Arbitrary External Call (任意外部调用)** - 52.9%
- 用户可控 `call` 目标和数据
- 典型案例: Seneca ($6M), DFXFinance ($4M)

**B. Arbitrary Call (任意调用)** - 44.1%
- 批量调用或回调缺少验证
- 典型案例: UnizenIO ($2M), Unizen ($2M)

**C. Unchecked Delegatecall (未检查的委托调用)** - 2.9%
- `delegatecall` 到用户指定地址
- 典型案例: Parity Wallet ($30M+)


## 核心攻击模式

### 模式 1: 用户可控 Call 目标

```solidity
// ❌ 漏洞代码
function execute(address target, bytes calldata data) external {
    (bool success,) = target.call(data);  // 用户完全可控
    require(success, "Call failed");
}

// 攻击: 调用任意合约的任意函数
execute(victimToken, abi.encodeWithSignature("transfer(address,uint256)", attacker, balance));
```

### 模式 2: 批量调用缺少验证

```solidity
// ❌ 漏洞代码
function multicall(address[] calldata targets, bytes[] calldata data) external {
    for (uint i = 0; i < targets.length; i++) {
        targets[i].call(data[i]);  // 没有验证
    }
}

// 攻击: 批量调用恶意合约
```

### 模式 3: 未检查的 Delegatecall

```solidity
// ❌ 漏洞代码
function execute(address logic, bytes calldata data) external {
    (bool success,) = logic.delegatecall(data);  // 在当前合约上下文执行
    require(success);
}

// 攻击: delegatecall 到恶意合约，修改存储
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 地址白名单
mapping(address => bool) public whitelist;

function execute(address target, bytes calldata data) external {
    require(whitelist[target], "Target not whitelisted");
    (bool success,) = target.call(data);
    require(success);
}

// ✅ 函数选择器验证
bytes4 constant TRANSFER_SELECTOR = bytes4(keccak256("transfer(address,uint256)"));

function execute(address target, bytes calldata data) external {
    bytes4 selector = bytes4(data[:4]);
    require(selector == TRANSFER_SELECTOR, "Invalid function");
    target.call(data);
}

// ✅ 禁止 delegatecall 到用户地址
function execute(address logic, bytes calldata data) external onlyOwner {
    require(trustedLogic[logic], "Untrusted logic");
    logic.delegatecall(data);
}
```


## 典型案例

### 案例 1: Seneca (2024-02-28)
- **损失**: $6M
- **类型**: Arbitrary External Call
- **根因**: 用户可控 call 目标和数据

### 案例 2: DFXFinance (2022-11-11)
- **损失**: $4M
- **类型**: Arbitrary External Call
- **根因**: 批量调用缺少地址验证

### 案例 3: UnizenIO (2024-09-21)
- **损失**: $2M
- **类型**: Arbitrary Call
- **根因**: 回调函数未验证调用者


## PoC 快速模板

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.10;

import "forge-std/Test.sol";

contract ArbitraryCallExploitTest is Test {
    function testArbitraryCall() public {
        // 调用受害合约的任意调用函数
        bytes memory data = abi.encodeWithSignature(
            "transfer(address,uint256)", 
            attacker, 
            victimBalance
        );
        
        IVictim(victim).execute(targetToken, data);
        
        // 验证攻击成功
        assertGt(IERC20(targetToken).balanceOf(attacker), 0);
    }
}
```

## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 34 |
| 总损失 | $23M |
| 平均损失 | $676K |
| 最大损失 | $6M (Seneca) |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/arbitrary-call-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#arbitrary-call)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 34  
**最后更新**: 2026-01-29  
**行数**: ~200 (优化前: 1739)
