# XLayer PPP Launchpad

一个部署在 XLayer 区块链上的去中心化代币发射台，允许用户创建和交易代币。

## 项目概述

PPP Launchpad 是一个完全去中心化的代币发射台，具有以下特性：

- **代币创建**: 任何人都可以创建自己的 ERC20 代币
- **虚荣地址**: 支持使用 CREATE2 创建具有特定后缀的代币地址
- **内置 AMM**: 每个代币都有自己的自动化做市商（AMM）
- **防狙击**: 创建者可以在创建时立即购买代币，防止机器人狙击
- **费用分配**: 交易费用 50% 给平台，50% 给代币创建者
- **废弃项目回收**: 4年无活动的项目可以回收 ETH

## 核心功能

### 🚀 代币创建
- 使用最小代理模式降低部署成本
- 支持自定义代币名称、符号和元数据 URI
- 可选择创建具有特定地址后缀的代币

### 💱 AMM 交易
- 基于恒定乘积公式的自动化做市商
- 虚拟初始流动性：10 ETH
- 初始代币供应量：10亿代币
- 最小交易量：0.0001 ETH

### 💰 费用结构
- 交易费率：1% (100 基点)
- 费用分配：50% 平台费用，50% 创建者奖励
- 创建者奖励实时发放

### 🛡️ 安全特性
- 滑点保护
- 重入攻击保护
- 所有权放弃（代币合约完全去中心化）

## 合约架构

### 主要合约

1. **PPPLaunchpad.sol** - 主合约
   - 代币创建和管理
   - AMM 交易逻辑
   - 费用分配和统计

2. **PPPLaunchpadToken.sol** - 代币实现合约
   - ERC20 可升级代币
   - 支持元数据 URI

3. **PPPMath.sol** - 数学库
   - AMM 计算公式
   - 费用计算

## 快速开始

### 环境要求

- Node.js >= 16
- npm 或 yarn

### 安装依赖

```bash
npm install
```

### 环境配置

创建 `.env` 文件：

```env
# XLayer 网络私钥
XLAYER_PRIVATE_KEY=your_private_key_here

# 以太坊主网配置（可选）
MAINNET_RPC_URL=https://mainnet.infura.io/v3/YOUR_INFURA_KEY
MAINNET_PRIVATE_KEY=your_mainnet_private_key

# API 密钥
ETHERSCAN_API_KEY=your_etherscan_api_key
OKXWEB3_API_KEY=your_okx_api_key
```

### 编译合约

```bash
npm run compile
```

### 部署合约

```bash
npm run deploy
```

### 验证合约

```bash
npm run verify
```

## 使用脚本

### 创建代币

```bash
npm run create-token
```

### 创建虚荣地址代币

```bash
# 首先找到满足条件的 salt
npm run find-vanity

# 使用找到的 salt 创建代币
npm run create-vanity-token
```

## 网络配置

### XLayer 主网
- RPC URL: https://rpc.xlayer.tech
- Chain ID: 196
- 区块浏览器: https://www.oklink.com/xlayer

## 合约常量

```solidity
// 交易费率（1%）
uint256 public constant TRADING_FEE_RATE = 100;

// 最小交易金额（0.0001 ETH）
uint256 public constant MIN_TRADE_AMOUNT = 0.0001 ether;

// 虚拟初始 ETH 流动性（10 ETH）
uint256 public constant VIRTUAL_ETH_LIQUIDITY = 10 ether;

// 初始代币供应量（10亿代币）
uint256 public constant INITIAL_TOKEN_SUPPLY = 1_000_000_000 * 10**18;

// 废弃项目回收期（4年）
uint256 public constant ABANDONMENT_PERIOD = 1460 days;
```

## API 文档

### 主要函数

#### 创建代币
```solidity
function createToken(
    string calldata name,
    string calldata symbol,
    string calldata uri
) external payable returns (uint256 tokenId, address tokenAddress)
```

#### 购买代币
```solidity
function buyTokens(
    uint256 tokenId, 
    uint256 minTokensOut
) external payable
```

#### 出售代币
```solidity
function sellTokens(
    uint256 tokenId,
    uint256 tokenAmount,
    uint256 minEthOut
) external
```

#### 获取价格信息
```solidity
function getCurrentPrice(uint256 tokenId) external view returns (uint256 price)
function calculateBuyAmount(uint256 tokenId, uint256 ethAmount) external view returns (uint256 tokenAmount, uint256 feeAmount)
function calculateSellAmount(uint256 tokenId, uint256 tokenAmount) external view returns (uint256 ethAmount, uint256 feeAmount)
```

## 安全考虑

1. **重入攻击防护**: 使用 OpenZeppelin 的 ReentrancyGuard
2. **滑点保护**: 用户可设置最小接收数量
3. **访问控制**: 关键函数使用 Ownable 保护
4. **整数溢出**: 使用 Solidity 0.8+ 内置保护
5. **代币安全**: 代币合约在初始化后立即放弃所有权

## 费用经济学

- **平台费用**: 每笔交易的 0.5%，用于平台维护和发展
- **创建者奖励**: 每笔交易的 0.5%，激励优质项目创建
- **实时分配**: 创建者奖励在每笔交易后立即发放
- **费用提取**: 平台费用由合约所有者提取

## 开发团队

- 基于 Hardhat 开发框架
- 使用 OpenZeppelin 安全库
- 集成 OKX Web3 浏览器验证

## 许可证

ISC License

## 贡献

欢迎提交 Issue 和 Pull Request！

## 免责声明

本项目仅供学习和研究使用。使用前请充分了解智能合约的风险，并进行充分的测试。作者不对任何损失承担责任。