# 工具和资源

DeFi 安全分析必备工具和学习资源。

## 开发和测试工具

### Foundry
最流行的智能合约测试框架。

**安装:**
```bash
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

**基础命令:**
```bash
forge init my-project    # 创建项目
forge build             # 编译合约
forge test              # 运行测试
forge test -vvv         # 详细输出
forge coverage          # 测试覆盖率
```

**Fork 测试:**
```bash
forge test --fork-url $RPC_URL --fork-block-number 12345678
```

**优点:**
- 速度快 (Rust 实现)
- 内置 Cheatcodes (vm.prank, vm.roll 等)
- 原生 Solidity 测试
- 强大的 Fork 功能

### Hardhat
功能丰富的开发环境。

**安装:**
```bash
npm install --save-dev hardhat
npx hardhat init
```

**基础命令:**
```bash
npx hardhat compile
npx hardhat test
npx hardhat node
npx hardhat run scripts/deploy.js
```

**优点:**
- 插件生态丰富
- TypeScript 支持好
- 调试功能强大

## 安全分析工具

### Slither
静态分析工具,检测常见漏洞。

**安装:**
```bash
pip3 install slither-analyzer
```

**基础用法:**
```bash
slither .                                    # 分析当前目录
slither contract.sol                         # 分析单个文件
slither . --detect reentrancy-eth           # 检测重入
slither . --detect arbitrary-send-eth       # 检测任意转账
slither . --print human-summary             # 生成摘要
```

**常用检测器:**
- `reentrancy-eth` - 重入攻击
- `arbitrary-send-eth` - 任意转账
- `controlled-delegatecall` - 可控 delegatecall
- `suicidal` - 自毁函数
- `unprotected-upgrade` - 未保护的升级

**优点:**
- 快速扫描
- 误报率低
- 支持多种检测器

### Mythril
符号执行工具,深度分析。

**安装:**
```bash
pip3 install mythril
```

**基础用法:**
```bash
myth analyze contract.sol
myth analyze contract.sol --execution-timeout 300
myth analyze contract.sol --modules reentrancy,integer
```

**优点:**
- 深度分析
- 发现复杂漏洞
- 生成攻击路径

**缺点:**
- 速度较慢
- 可能超时

### Echidna
模糊测试工具,自动生成测试用例。

**安装 (Docker):**
```bash
docker pull trailofbits/echidna
```

**基础用法:**
```bash
echidna-test contract.sol --contract TestContract
echidna-test contract.sol --config config.yaml
```

**不变量示例:**
```solidity
contract EchidnaTest {
    MyContract target;
    
    constructor() {
        target = new MyContract();
    }
    
    // 不变量: 总供应量 = 所有余额之和
    function echidna_total_supply() public view returns (bool) {
        return target.totalSupply() == sumOfBalances();
    }
}
```

**优点:**
- 自动化测试
- 发现边界条件
- 验证不变量

## 链上分析工具

### Tenderly
交易模拟和调试平台。

**功能:**
- 交易模拟 (Simulate)
- 调用栈可视化
- Gas 分析
- 状态变化追踪
- 告警和监控

**使用场景:**
- 分析攻击交易
- 调试失败交易
- 模拟交易结果

**网址:** https://dashboard.tenderly.co/

### Etherscan
区块链浏览器和数据平台。

**功能:**
- 交易查询
- 合约验证
- 代币追踪
- API 服务

**API 示例:**
```bash
# 获取合约 ABI
curl "https://api.etherscan.io/api?module=contract&action=getabi&address=0x..."

# 获取交易列表
curl "https://api.etherscan.io/api?module=account&action=txlist&address=0x..."
```

**网址:** https://etherscan.io/

### Dune Analytics
链上数据分析和可视化。

**功能:**
- SQL 查询链上数据
- 创建仪表板
- 社区查询共享

**使用场景:**
- 统计漏洞类型分布
- 分析攻击趋势
- 追踪资金流向

**网址:** https://dune.com/

## 学习资源

### 在线课程

#### CryptoZombies
Solidity 入门课程,游戏化学习。

**内容:**
- Solidity 基础语法
- 智能合约开发
- DApp 开发

**网址:** https://cryptozombies.io/

#### Ethernaut
OpenZeppelin 的安全挑战。

**内容:**
- 20+ 安全挑战
- 涵盖常见漏洞
- 实战练习

**网址:** https://ethernaut.openzeppelin.com/

#### Damn Vulnerable DeFi
DeFi 安全挑战集。

**内容:**
- 15+ DeFi 安全挑战
- 闪电贷、价格操纵等
- 真实场景模拟

**网址:** https://www.damnvulnerabledefi.xyz/

#### Secureum
专业安全培训。

**内容:**
- 系统化安全课程
- 审计方法论
- 最佳实践

**网址:** https://secureum.substack.com/

### 文档和指南

#### Solidity Documentation
官方文档,必读。

**网址:** https://docs.soliditylang.org/

#### OpenZeppelin Docs
安全合约库文档。

**网址:** https://docs.openzeppelin.com/

#### Smart Contract Security Best Practices
Consensys 安全指南。

**网址:** https://consensys.github.io/smart-contract-best-practices/

### 博客和研究

#### Trail of Bits Blog
顶级安全公司的研究博客。

**内容:**
- 安全研究文章
- 工具发布
- 审计报告

**网址:** https://blog.trailofbits.com/

#### OpenZeppelin Blog
安全分析和最佳实践。

**网址:** https://blog.openzeppelin.com/

#### Rekt News
安全事件报道和分析。

**网址:** https://rekt.news/

### 社区和论坛

#### Ethereum Magicians
技术讨论论坛。

**网址:** https://ethereum-magicians.org/

#### Secureum Discord
安全社区。

**网址:** https://discord.gg/secureum

#### OpenZeppelin Forum
开发者论坛。

**网址:** https://forum.openzeppelin.com/

## 竞赛和赏金平台

### Code4rena
审计竞赛平台。

**特点:**
- 社区驱动审计
- 奖金池分配
- 学习和实践

**网址:** https://code4rena.com/

### Sherlock
审计竞赛和保险。

**特点:**
- 专业审计员社区
- 保险覆盖
- 高质量项目

**网址:** https://www.sherlock.xyz/

### Immunefi
漏洞赏金平台。

**特点:**
- 最大的 DeFi 赏金
- 快速响应
- 专业支持

**网址:** https://immunefi.com/

### HackerOne
传统漏洞赏金平台。

**特点:**
- 企业级项目
- 完善的流程
- 全球社区

**网址:** https://www.hackerone.com/

## 推荐工作流

### 分析攻击事件
1. Etherscan 查看交易
2. Tenderly 分析调用栈
3. Foundry 编写 PoC
4. Slither 扫描漏洞

### 审计智能合约
1. Slither 快速扫描
2. 手动代码审查
3. Foundry 编写测试
4. Echidna 模糊测试
5. Mythril 深度分析

### 学习安全知识
1. CryptoZombies 学习 Solidity
2. Ethernaut 练习安全挑战
3. Damn Vulnerable DeFi 实战
4. 参与 Code4rena 竞赛
5. 阅读 Trail of Bits 博客

## 相关文档

- [IPDOR 分析框架](ipdor-framework.md) - 使用工具进行系统化分析
