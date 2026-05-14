---
name: input-validation-vulnerability
description: Input validation vulnerability analysis covering 25 real cases with $15M total loss. Use when analyzing missing parameter checks, unchecked array lengths, invalid address validation, or boundary condition errors. Includes defense strategies using require statements, SafeMath, and comprehensive input validation.
---

# Input Validation 漏洞分析

## 快速识别

### 核心特征
- ✓ 缺少参数范围检查
- ✓ 未验证数组长度
- ✓ 地址验证不完整
- ✓ 边界条件未处理
- ✓ 用户输入直接使用

### 快速检查清单 (5 分钟)
- [ ] 检查所有外部函数的参数验证
- [ ] 验证数组长度是否被检查
- [ ] 确认地址是否验证非零
- [ ] 检查数值范围是否合理
- [ ] 验证边界条件处理

### 本质公式
```
输入验证漏洞 = 缺少验证 × 用户可控输入 × 关键操作
```

## 漏洞分类

### 主要类型

**A. Missing Parameter Validation (缺少参数验证)** - 40%
- 参数范围未检查
- 典型案例: 多个 DeFi 协议

**B. Array Length Mismatch (数组长度不匹配)** - 30%
- 多个数组长度不一致
- 典型案例: Batch 操作漏洞

**C. Address Validation Error (地址验证错误)** - 20%
- 未检查零地址或无效地址
- 典型案例: 转账到零地址

**D. Boundary Condition Error (边界条件错误)** - 10%
- 边界值未正确处理
- 典型案例: 溢出/下溢


## 核心攻击模式

### 模式 1: 缺少参数验证

```solidity
// ❌ 漏洞代码
function setFee(uint256 fee) external onlyOwner {
    feeRate = fee;  // 没有范围检查
}

// 攻击: 设置 fee = 100% 或更高

// ✅ 正确做法
function setFee(uint256 fee) external onlyOwner {
    require(fee <= 1000, "Fee too high");  // 最大 10%
    feeRate = fee;
}
```

### 模式 2: 数组长度不匹配

```solidity
// ❌ 漏洞代码
function batchTransfer(address[] calldata recipients, uint256[] calldata amounts) external {
    for (uint i = 0; i < recipients.length; i++) {
        token.transfer(recipients[i], amounts[i]);  // 可能越界
    }
}

// ✅ 正确做法
function batchTransfer(address[] calldata recipients, uint256[] calldata amounts) external {
    require(recipients.length == amounts.length, "Length mismatch");
    for (uint i = 0; i < recipients.length; i++) {
        token.transfer(recipients[i], amounts[i]);
    }
}
```

### 模式 3: 地址验证缺失

```solidity
// ❌ 漏洞代码
function setTreasury(address newTreasury) external onlyOwner {
    treasury = newTreasury;  // 未检查零地址
}

// ✅ 正确做法
function setTreasury(address newTreasury) external onlyOwner {
    require(newTreasury != address(0), "Invalid address");
    treasury = newTreasury;
}
```


## 关键防御

### 必须实施 (P0)

```solidity
// ✅ 完整的参数验证
function deposit(uint256 amount, address recipient) external {
    require(amount > 0, "Amount must be positive");
    require(amount <= maxDeposit, "Amount too large");
    require(recipient != address(0), "Invalid recipient");
    require(recipient != address(this), "Cannot deposit to self");
    
    // 存款逻辑
}

// ✅ 数组长度验证
function batchOperation(
    address[] calldata targets,
    uint256[] calldata amounts,
    bytes[] calldata data
) external {
    require(targets.length == amounts.length, "Length mismatch");
    require(targets.length == data.length, "Length mismatch");
    require(targets.length > 0, "Empty array");
    require(targets.length <= 100, "Too many operations");
    
    // 批量操作逻辑
}
```


## 统计数据

| 指标 | 数值 |
|------|------|
| 案例数 | 25 |
| 总损失 | $15M |
| 平均损失 | $600K |

## 相关资源

- 📖 [详细分析](../references/vulnerabilities/input-validation-detailed-analysis.md)
- 📊 [案例索引](../references/case-index.md#input-validation)
- 🎯 [VCAT 分类框架](../references/vcat-framework.md)
- 🔍 [IPDOR 分析框架](../references/ipdor-framework.md)
- 🛠️ [工具和资源](../references/tools-and-resources.md)

---

**版本**: v2.0 (优化版)  
**案例数**: 25  
**最后更新**: 2026-01-29  
**行数**: ~180 (优化前: 1455)
