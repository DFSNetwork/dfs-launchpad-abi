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

## 前端集成指南

### 主网合约地址
```
PPPLaunchpad: 0xAB2fC0797F1741D98001e847B2206d25c7233659
网络: XLayer 主网
Chain ID: 196
RPC URL: https://rpc.xlayer.tech
```

### 前端访问 TokenMarket 数据

#### 1. 设置 Web3 连接

```javascript
// 使用 ethers.js
import { ethers } from 'ethers';

const provider = new ethers.JsonRpcProvider('https://rpc.xlayer.tech');
const contractAddress = '0xAB2fC0797F1741D98001e847B2206d25c7233659';

// 合约 ABI (主要函数)
const abi = [
  "function getMarket(uint256 tokenId) external view returns (tuple(address tokenAddress, address creator, string name, string symbol, string uri, uint256 createdAt, uint256 lastActivityTime, uint256 ethReserve, uint256 tokenReserve, uint256 maxSupply, uint256 tradingVolume, bool exists))",
  "function getTotalTokens() external view returns (uint256)",
  "function getCurrentPrice(uint256 tokenId) external view returns (uint256)",
  "function calculateBuyAmount(uint256 tokenId, uint256 ethAmount) external view returns (uint256 tokenAmount, uint256 feeAmount)",
  "function calculateSellAmount(uint256 tokenId, uint256 tokenAmount) external view returns (uint256 ethAmount, uint256 feeAmount)",
  "function getCreatorTokens(address creator) external view returns (uint256[])",
  "function tokenToId(address) external view returns (uint256)",
  "function buyTokens(uint256 tokenId, uint256 minTokensOut) external payable",
  "function sellTokens(uint256 tokenId, uint256 tokenAmount, uint256 minEthOut) external"
];

const contract = new ethers.Contract(contractAddress, abi, provider);
```

#### 2. 获取 TokenMarket 数据

```javascript
// 获取单个代币市场信息
async function getTokenMarket(tokenId) {
  try {
    const market = await contract.getMarket(tokenId);
    
    return {
      tokenAddress: market.tokenAddress,
      creator: market.creator,
      name: market.name,
      symbol: market.symbol,
      uri: market.uri,
      createdAt: Number(market.createdAt),
      lastActivityTime: Number(market.lastActivityTime),
      ethReserve: ethers.formatEther(market.ethReserve),
      tokenReserve: ethers.formatEther(market.tokenReserve),
      maxSupply: ethers.formatEther(market.maxSupply),
      tradingVolume: ethers.formatEther(market.tradingVolume),
      exists: market.exists
    };
  } catch (error) {
    console.error('获取市场信息失败:', error);
    throw error;
  }
}

// 获取所有代币列表
async function getAllTokens() {
  try {
    const totalTokens = await contract.getTotalTokens();
    const markets = [];
    
    for (let i = 1; i <= totalTokens; i++) {
      try {
        const market = await getTokenMarket(i);
        markets.push({ tokenId: i, ...market });
      } catch (error) {
        console.error(`获取代币 ${i} 失败:`, error);
      }
    }
    
    return markets;
  } catch (error) {
    console.error('获取代币列表失败:', error);
    throw error;
  }
}

// 获取创建者的所有代币
async function getCreatorTokens(creatorAddress) {
  try {
    const tokenIds = await contract.getCreatorTokens(creatorAddress);
    const markets = [];
    
    for (const tokenId of tokenIds) {
      try {
        const market = await getTokenMarket(Number(tokenId));
        markets.push({ tokenId: Number(tokenId), ...market });
      } catch (error) {
        console.error(`获取代币 ${tokenId} 失败:`, error);
      }
    }
    
    return markets;
  } catch (error) {
    console.error('获取创建者代币失败:', error);
    throw error;
  }
}
```

#### 3. 获取价格和交易信息

```javascript
// 获取当前价格
async function getCurrentPrice(tokenId) {
  try {
    const price = await contract.getCurrentPrice(tokenId);
    return ethers.formatEther(price);
  } catch (error) {
    console.error('获取价格失败:', error);
    throw error;
  }
}

// 计算购买数量
async function calculateBuyAmount(tokenId, ethAmount) {
  try {
    const ethAmountWei = ethers.parseEther(ethAmount.toString());
    const [tokenAmount, feeAmount] = await contract.calculateBuyAmount(tokenId, ethAmountWei);
    
    return {
      tokenAmount: ethers.formatEther(tokenAmount),
      feeAmount: ethers.formatEther(feeAmount)
    };
  } catch (error) {
    console.error('计算购买数量失败:', error);
    throw error;
  }
}

// 计算出售数量
async function calculateSellAmount(tokenId, tokenAmount) {
  try {
    const tokenAmountWei = ethers.parseEther(tokenAmount.toString());
    const [ethAmount, feeAmount] = await contract.calculateSellAmount(tokenId, tokenAmountWei);
    
    return {
      ethAmount: ethers.formatEther(ethAmount),
      feeAmount: ethers.formatEther(feeAmount)
    };
  } catch (error) {
    console.error('计算出售数量失败:', error);
    throw error;
  }
}
```

#### 4. 实现交易功能

```javascript
// 购买代币 (需要连接钱包)
async function buyTokens(tokenId, ethAmount, slippagePercent = 3) {
  try {
    if (!window.ethereum) {
      throw new Error('请安装 MetaMask');
    }
    
    const provider = new ethers.BrowserProvider(window.ethereum);
    const signer = await provider.getSigner();
    const contractWithSigner = new ethers.Contract(contractAddress, abi, signer);
    
    // 计算最小接收代币数量（滑点保护）
    const { tokenAmount } = await calculateBuyAmount(tokenId, ethAmount);
    const minTokensOut = ethers.parseEther((tokenAmount * (100 - slippagePercent) / 100).toString());
    
    const tx = await contractWithSigner.buyTokens(tokenId, minTokensOut, {
      value: ethers.parseEther(ethAmount.toString())
    });
    
    console.log('交易发送:', tx.hash);
    const receipt = await tx.wait();
    console.log('交易确认:', receipt);
    
    return receipt;
  } catch (error) {
    console.error('购买失败:', error);
    throw error;
  }
}

// 出售代币 (需要连接钱包)
async function sellTokens(tokenId, tokenAmount, slippagePercent = 3) {
  try {
    if (!window.ethereum) {
      throw new Error('请安装 MetaMask');
    }
    
    const provider = new ethers.BrowserProvider(window.ethereum);
    const signer = await provider.getSigner();
    const contractWithSigner = new ethers.Contract(contractAddress, abi, signer);
    
    // 计算最小接收 ETH 数量（滑点保护）
    const { ethAmount } = await calculateSellAmount(tokenId, tokenAmount);
    const minEthOut = ethers.parseEther((ethAmount * (100 - slippagePercent) / 100).toString());
    
    const tx = await contractWithSigner.sellTokens(
      tokenId,
      ethers.parseEther(tokenAmount.toString()),
      minEthOut
    );
    
    console.log('交易发送:', tx.hash);
    const receipt = await tx.wait();
    console.log('交易确认:', receipt);
    
    return receipt;
  } catch (error) {
    console.error('出售失败:', error);
    throw error;
  }
}
```

#### 5. 使用示例

```javascript
// 示例：获取并显示代币市场信息
async function displayTokenMarkets() {
  try {
    const markets = await getAllTokens();
    
    console.log('所有代币市场:');
    markets.forEach(market => {
      console.log(`
代币ID: ${market.tokenId}
名称: ${market.name} (${market.symbol})
创建者: ${market.creator}
ETH储备: ${market.ethReserve} ETH
代币储备: ${market.tokenReserve} tokens
交易量: ${market.tradingVolume} ETH
创建时间: ${new Date(market.createdAt * 1000).toLocaleString()}
      `);
    });
  } catch (error) {
    console.error('显示失败:', error);
  }
}

// 示例：监听交易事件
function listenToTradeEvents() {
  const eventFilter = contract.filters.TokenTraded();
  
  contract.on(eventFilter, (tokenId, trader, isBuy, ethAmount, tokenAmount, platformFee, creatorReward, creator, event) => {
    console.log('新交易:', {
      tokenId: Number(tokenId),
      trader,
      type: isBuy ? '购买' : '出售',
      ethAmount: ethers.formatEther(ethAmount),
      tokenAmount: ethers.formatEther(tokenAmount),
      platformFee: ethers.formatEther(platformFee),
      creatorReward: ethers.formatEther(creatorReward),
      creator,
      txHash: event.log.transactionHash
    });
  });
}

// 调用示例
displayTokenMarkets();
listenToTradeEvents();
```

### 注意事项

1. **网络配置**: 确保前端连接到正确的 XLayer 网络
2. **错误处理**: 实现适当的错误处理和用户反馈
3. **Gas 费用**: 交易需要 XLayer 网络的原生代币作为 Gas 费
4. **滑点保护**: 在波动市场中建议使用 3-5% 的滑点保护
5. **实时更新**: 使用事件监听或定时刷新来更新市场数据

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