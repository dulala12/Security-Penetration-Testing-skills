# 案例索引

327+ 个真实 DeFi 安全事件的结构化索引。

## 按损失金额排序 (Top 20)

| 排名 | 项目 | 日期 | 损失 | 类型 | 链 | PoC |
|------|------|------|------|------|-----|-----|
| 1 | Ronin Bridge | 2022-03-23 | $625M | Access Control | Ethereum | `src/test/2022-03/Ronin_exp.sol` |
| 2 | Poly Network | 2021-08-10 | $611M | Access Control | Multiple | `src/test/2021-08/PolyNetwork_exp.sol` |
| 3 | Wormhole | 2022-02-02 | $326M | Access Control | Solana | - |
| 4 | Euler Finance | 2023-03-13 | $197M | Business Logic | Ethereum | - |
| 5 | Nomad Bridge | 2022-08-01 | $190M | Business Logic | Multiple | `src/test/2022-08/NomadBridge_exp.sol` |
| 6 | Cream Finance | 2021-10-27 | $130M | Price Manipulation | Ethereum | `src/test/2021-10/Cream_2_exp.sol` |
| 7 | Multichain | 2023-07-06 | $126M | Input Validation | Multiple | - |
| 8 | BalancerV2 | 2025-11-03 | $120M | Precision Loss | Ethereum | - |
| 9 | Mango Markets | 2022-10-11 | $116M | Price Manipulation | Solana | - |
| 10 | OrbitChain | 2023-12-31 | $81M | Input Validation | Multiple | - |
| 11 | The DAO | 2016-06-17 | $60M | Reentrancy | Ethereum | - |
| 12 | KyberSwap | 2023-11-22 | $48M | Precision Loss | Multiple | - |
| 13 | HedgeyFinance | 2024-04-19 | $48M | Logic Flaw | Ethereum | - |
| 14 | PancakeBunny | 2021-05-20 | $45M | Flashloan | BSC | `src/test/2021-05/PancakeBunny_exp.sol` |
| 15 | Curve | 2023-07-30 | $41M | Reentrancy | Ethereum | `src/test/2023-07/Curve_exp01.sol` |
| 16 | Harvest Finance | 2020-10-26 | $34M | Flashloan | Ethereum | `src/test/2020-10/HarvestFinance_exp.sol` |
| 17 | Spartan | 2021-05-02 | $30.5M | Logic Flaw | BSC | `src/test/2021-05/Spartan_exp.sol` |
| 18 | Penpiexyz | 2024-09-03 | $27.3M | Reentrancy | Ethereum | - |
| 19 | Lendf.Me | 2020-04-19 | $25M | Reentrancy | Ethereum | `src/test/2020-04/LendfMe_exp.sol` |
| 20 | Sonne Finance | 2024-05-14 | $20M | Precision Loss | Optimism | - |

## 按漏洞类型分类

### Access Control (44 cases)
**特征**: 缺少权限检查、初始化漏洞、回调验证不当

**Top 5 案例:**
1. Ronin Bridge (2022-03) - $625M
2. Poly Network (2021-08) - $611M
3. Wormhole (2022-02) - $326M
4. Corkprotocol (2025-05) - $12M
5. SafeMoon (2023-03) - $8.9M

**详细文档**: [access-control-skill.md](../sub-skills/access-control-skill.md)

\1(58 cases)
**特征**: DEX 即时价格、预言机操纵、闪电贷组合

**Top 5 案例:**
1. Cream Finance (2021-10) - $130M
2. Mango Markets (2022-10) - $116M
3. PancakeBunny (2021-05) - $45M
4. Harvest Finance (2020-10) - $34M
5. Indexed Finance (2021-10) - $16M

**详细文档**: [price-manipulation-skill.md](../sub-skills/price-manipulation-skill.md)

\1(32 cases)
**特征**: 外部调用在状态更新前、缺少 nonReentrant

**Top 5 案例:**
1. The DAO (2016-06) - $60M
2. Curve (2023-07) - $41M
3. Penpiexyz (2024-09) - $27.3M
4. Lendf.Me (2020-04) - $25M
5. Cream Finance (2021-08) - $18.8M

**详细文档**: [reentrancy-skill.md](../sub-skills/reentrancy-skill.md)

### Business Logic Flaw (38 cases)
**特征**: 激励机制缺陷、清算逻辑错误、经济模型失衡

**Top 5 案例:**
1. Euler Finance (2023-03) - $197M
2. Nomad Bridge (2022-08) - $190M
3. Indexed Finance (2021-10) - $16M
4. Rari Capital (2022-04) - $80M
5. Grim Finance (2021-12) - $30M

**详细文档**: [business-logic-flaw-skill.md](../sub-skills/business-logic-flaw-skill.md)

\1(37 cases)
**特征**: 状态更新错误、边界条件、数学计算错误

**Top 5 案例:**
1. HedgeyFinance (2024-04) - $48M
2. Spartan (2021-05) - $30.5M
3. Popsicle (2021-08) - $20M
4. Revest Finance (2022-03) - $11.2M
5. Visor Finance (2021-12) - $8.2M

**详细文档**: [logic-flaw-skill.md](../sub-skills/logic-flaw-skill.md)

### Flashloan Attack (35 cases)
**特征**: 单笔交易、无需初始资金、常与其他漏洞组合

**Top 5 案例:**
1. PancakeBunny (2021-05) - $45M
2. Harvest Finance (2020-10) - $34M
3. Rari Capital (2022-04) - $80M
4. Grim Finance (2021-12) - $30M
5. bZx (2020-09) - $8M

**详细文档**: [flashloan-attack-skill.md](../sub-skills/flashloan-attack-skill.md)

\1(9 cases)
**特征**: 缺少参数检查、零地址、数组长度

**Top 5 案例:**
1. Multichain (2023-07) - $126M
2. OrbitChain (2023-12) - $81M
3. Poly Network (2021-08) - $611M (组合)
4. Bazaar (2024-06) - $1.4M
5. Paraswap (2024-03) - $24K

**详细文档**: [input-validation-skill.md](../sub-skills/input-validation-skill.md)

\1(2 cases)
**特征**: 除法截断、捐赠通胀、多层计算

**Top 5 案例:**
1. BalancerV2 (2025-11) - $120M
2. KyberSwap (2023-11) - $48M
3. Sonne Finance (2024-05) - $20M
4. Euler Finance (2023-03) - $197M (组合)
5. Hundred Finance (2022-03) - $6M

**详细文档**: [precision-loss-skill.md](../sub-skills/precision-loss-skill.md)

\1(5 cases)
**特征**: 用户可控调用目标、缺少白名单、delegatecall

**Top 5 案例:**
1. Seneca (2024-02) - $6M
2. DFXFinance (2022-11) - $4M
3. UnizenIO (2024-03) - $2M
4. Dexible (2023-02) - $1.5M
5. LeetSwap (2023-08) - $630K

**详细文档**: [arbitrary-call-skill.md](../sub-skills/arbitrary-call-skill.md)

\1(2 cases)
**特征**: 后门函数、时间触发器、魔法数字

**Top 5 案例:**
1. Roar (2025-04) - $777K
2. YziAI (2025-03) - $239K
3. BUBAI (2024-10) - $131K
4. SQUID (2021-11) - $3.38M
5. AnubisDAO (2021-10) - $60M

**详细文档**: [rug-pull-skill.md](../sub-skills/rug-pull-skill.md)

## 按时间线索引

### 2020 年 (6 cases, $62M)
- Lendf.Me (04-19) - $25M - Reentrancy
- Uniswap/ERC777 (04-18) - $300K - Reentrancy
- Balancer (06-28) - $500K - Business Logic
- Opyn (08-04) - $371K - Business Logic
- bZx (09-14) - $8M - Flashloan
- Harvest Finance (10-26) - $34M - Flashloan

### 2021 年 (45 cases, $850M)
**重大事件:**
- Poly Network (08-10) - $611M
- Cream Finance (10-27) - $130M
- PancakeBunny (05-20) - $45M

### 2022 年 (78 cases, $1.2B)
**重大事件:**
- Ronin Bridge (03-23) - $625M
- Wormhole (02-02) - $326M
- Nomad Bridge (08-01) - $190M

### 2023 年 (92 cases, $450M)
**重大事件:**
- Euler Finance (03-13) - $197M
- Multichain (07-06) - $126M
- OrbitChain (12-31) - $81M

### 2024 年 (85 cases, $180M)
**重大事件:**
- HedgeyFinance (04-19) - $48M
- Sonne Finance (05-14) - $20M

### 2025 年 (18 cases, $125M)
**重大事件:**
- BalancerV2 (11-03) - $120M

### 2026 年 (3 cases, $5M)
待更新

## 按区块链分类

### Ethereum (145 cases, $1.8B)
最大损失: Ronin Bridge ($625M)

### BSC (52 cases, $120M)
最大损失: PancakeBunny ($45M)

### Multiple Chains (38 cases, $850M)
最大损失: Poly Network ($611M)

### Solana (12 cases, $180M)
最大损失: Wormhole ($326M)

### Optimism (8 cases, $35M)
最大损失: Sonne Finance ($20M)

### 其他链 (72 cases, $17M)
Arbitrum, Polygon, Avalanche, Fantom 等

## 快速查找

### 按协议类型
- **DEX**: Uniswap, PancakeSwap, Curve, Balancer
- **Lending**: Compound, Aave, Cream, Euler
- **Bridge**: Ronin, Poly Network, Wormhole, Nomad
- **Stablecoin**: Terra, Iron Finance, Beanstalk

### 按攻击手法
- **闪电贷组合**: 35 cases
- **价格操纵**: 58 cases
- **重入攻击**: 29 cases
- **权限滥用**: 44 cases

### 按损失规模
- **巨额 (>$10M)**: 6 cases
- **重大 ($1M-$10M)**: 28 cases
- **中等 ($100K-$1M)**: 85 cases
- **小额 (<$100K)**: 208 cases

## 相关文档

- [VCAT 分类框架](vcat-framework.md) - 快速分类方法
- [IPDOR 分析框架](ipdor-framework.md) - 系统化分析
- 各子技能文档 - 详细漏洞分析
