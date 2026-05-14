# IPDOR 分析框架

系统化的 DeFi 攻击事件分析方法论。

## 框架概述

IPDOR 是一个五阶段渐进式分析框架:

1. **I**nformation Gathering (信息采集) - 5-10 分钟
2. **P**attern Recognition (模式识别) - 10-20 分钟  
3. **D**econstruction (攻击解构) - 20-40 分钟
4. **R**oot Cause Analysis (根因分析) - 30-60 分钟
5. **R**eproduction (可执行复现) - 1-2 小时

## 阶段 1: Information Gathering

### 目标
快速收集攻击事件的基本信息。

### 检查清单
- [ ] 交易哈希 (Transaction Hash)
- [ ] 区块号 (Block Number)
- [ ] 攻击者地址 (Attacker Address)
- [ ] 受害合约地址 (Victim Contract)
- [ ] 损失金额和代币类型
- [ ] 区块链网络 (Ethereum, BSC, etc.)
- [ ] 攻击时间

### 信息来源
- Etherscan / BscScan 交易详情
- Peckshield / SlowMist 安全报告
- 项目方公告
- 社区讨论 (Twitter, Discord)

### 输出
结构化的基本信息表格。

## 阶段 2: Pattern Recognition

### 目标
快速识别漏洞类型和攻击模式。

### 决策树
```
是否使用闪电贷?
├─ 是 → 可能是 Flashloan Attack 或组合攻击
└─ 否 → 是否涉及价格/预言机?
    ├─ 是 → Price Manipulation
    └─ 否 → 是否涉及权限检查?
        ├─ 是 → Access Control
        └─ 否 → 继续检查...
```

### 关键指标
- 调用栈深度 (深度 > 5 可能是重入)
- 价格变化幅度 (>10% 可能是价格操纵)
- 代币铸造量 (异常铸造可能是访问控制)
- 循环调用次数 (多次调用可能是重入)

### 输出
初步的漏洞类型判断和相似案例列表。

## 阶段 3: Deconstruction

### 目标
详细分解攻击步骤和资金流向。

### 分析方法

#### 3.1 调用栈分析
使用 Tenderly 或 Etherscan 查看:
- 函数调用顺序
- 参数传递
- 返回值
- 事件日志

#### 3.2 状态变化追踪
关注关键状态变量:
- 余额变化 (balances)
- 总供应量 (totalSupply)
- 储备量 (reserves)
- 价格 (price)

#### 3.3 资金流向图
```
攻击者 → 闪电贷 → DEX 操纵 → 借贷协议 → 获利
```

### 输出
详细的攻击步骤流程图和状态变化表。

## 阶段 4: Root Cause Analysis

### 目标
定位漏洞代码和根本原因。

### 分析维度

#### 4.1 代码层面
- 缺少什么检查?
- 状态更新顺序是否正确?
- 是否使用了不安全的函数?

#### 4.2 设计层面
- 信任假设是否合理?
- 经济模型是否健壮?
- 是否考虑了极端情况?

#### 4.3 防御层面
- 为什么现有防御失效?
- 缺少哪些防御措施?
- 如何改进?

### 根因公式
```
攻击成功 = 信任假设被打破 × 经济激励充足 × 技术可行性高
```

### 输出
漏洞代码片段、根因分析和修复建议。

## 阶段 5: Reproduction

### 目标
编写可执行的 PoC 验证分析。

### PoC 结构

```solidity
contract ExploitTest is Test {
    function setUp() public {
        // Fork 攻击前一个区块
        vm.createSelectFork("mainnet", BLOCK_NUMBER - 1);
    }
    
    function testExploit() public {
        // 1. 准备阶段
        // 2. 执行攻击
        // 3. 验证结果
    }
}
```

### 验证标准
- [ ] PoC 可以成功运行
- [ ] 损失金额与实际相符
- [ ] 攻击路径与分析一致
- [ ] 可以解释所有关键步骤

### 输出
完整的 Foundry 测试代码和执行日志。

## 快速参考

### 5 分钟快速判断
1. 查看交易调用栈
2. 检查是否有闪电贷
3. 查看价格变化
4. 检查权限函数调用
5. 初步判断漏洞类型

### 30 分钟深度分析
1. 完成信息采集 (5 min)
2. 完成模式识别 (10 min)
3. 完成攻击解构 (15 min)

### 2 小时完整分析
1. 完成前三阶段 (30 min)
2. 完成根因分析 (30 min)
3. 编写 PoC (60 min)

## 案例示例

### 示例: PancakeBunny 攻击分析

**阶段 1: 信息采集**
- TX: 0x897c2de73dd55d7701e1b69ffb3a17b0f4801ced88b0c75fe1551c5fcce6a979
- Block: 7,412,809
- 损失: $45M
- 网络: BSC

**阶段 2: 模式识别**
- 使用闪电贷 ✓
- 价格大幅波动 ✓
- 初步判断: Flashloan + Price Manipulation

**阶段 3: 攻击解构**
1. 闪电贷借入 WBNB
2. 在 PancakeSwap 大量买入 BUNNY
3. 操纵 BUNNY 价格
4. 在 Bunny 协议铸造大量 BUNNY
5. 卖出获利

**阶段 4: 根因分析**
- 漏洞: 使用 PancakeSwap 即时价格作为预言机
- 根因: 缺少 TWAP 或 Chainlink 价格验证
- 修复: 使用去中心化预言机

**阶段 5: PoC**
参考: `src/test/2021-05/PancakeBunny_exp.sol`

## 相关文档

- [VCAT 分类框架](vcat-framework.md) - 漏洞快速分类
- [案例索引](case-index.md) - 历史案例参考
