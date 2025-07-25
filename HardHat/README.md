# ERC-20代币项目

这是一个基于Hardhat的ERC-20代币发行项目，通过OpenZeppelin库实现了标准的ERC-20代币功能。

## 项目依赖

安装以下依赖：

```shell
# 初始化项目
npm init -y

# 安装Hardhat开发框架
npm install --save-dev hardhat
# --save-dev表示这是开发依赖，只在开发环境中需要

# 安装OpenZeppelin合约库
npm install @openzeppelin/contracts
# 这个库提供了经过安全审计的智能合约实现，包括ERC-20标准

# 安装ethers.js
npm install ethers
# 用于与以太坊网络和智能合约交互
```

## 智能合约

在`contracts/MyToken.sol`中创建了ERC-20代币合约：

```solidity
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
  constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
    _mint(msg.sender, initialSupply * (10 ** decimals()));
  }
}
```

- 继承了OpenZeppelin的ERC20标准实现
- 构造函数接收初始供应量参数
- 代币名称为"MyToken"，符号为"MTK"
- 使用`_mint`函数创建初始代币并分配给部署者

## Ignition部署模块

在`ignition/modules/deploy.js`中创建了部署模块：

```javascript
// 这个设置使用Hardhat Ignition来管理智能合约部署
// 更多信息请访问 https://hardhat.org/ignition

const { buildModule } = require("@nomicfoundation/hardhat-ignition/modules");

// 代币初始供应量参数（默认1000）
const INITIAL_SUPPLY = 1000;

module.exports = buildModule("MyTokenModule", (m) => {
  // 获取初始供应量参数，默认值为1000
  const initialSupply = m.getParameter("initialSupply", INITIAL_SUPPLY);

  // 部署MyToken合约，传入初始供应量参数
  const myToken = m.contract("MyToken", [initialSupply]);

  return { myToken };
});
```

- 使用Hardhat Ignition声明式部署框架
- 定义初始代币供应量为1000
- 允许通过参数覆盖默认值
- 返回部署的合约实例供后续交互

## 执行步骤

### 1. 编译合约

```shell
npx hardhat compile
```

这会将Solidity合约编译为字节码和ABI，存储在`artifacts`目录。

### 2. 启动本地节点

```shell
npx hardhat node
```

启动本地以太坊节点，生成10个测试账户，每个账户有10000ETH。

### 3. 部署合约

```shell
npx hardhat ignition deploy ./ignition/modules/deploy.js --network localhost
```

成功部署后会输出合约地址，例如：
```
Deploying [ MyTokenModule ]

Batch #1
  Executed MyTokenModule#MyToken

[ MyTokenModule ] successfully deployed 🚀

Deployed Addresses

MyTokenModule#MyToken - 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

### 4. 配置MetaMask

1. 在MetaMask中添加Hardhat本地网络：
   - 网络名称：`Hardhat本地网络`
   - RPC URL：`http://127.0.0.1:8545`
   - 链ID：`31337`
   - 货币符号：`ETH`

2. 导入Hardhat生成的测试账户：
   - 复制节点输出的任一私钥
   - 在MetaMask中选择"导入账户"
   - 粘贴私钥并导入

3. 添加代币：
   - 点击"导入代币"
   - 输入合约地址
   - 代币符号和小数位应自动填充

### 5. 与合约交互

可以通过MetaMask进行以下操作：
- 查看代币余额
- 转账代币到其他账户
- 批准和授权其他地址使用代币

## 验证点

- 合约部署成功，有合约地址
- 部署者账户中有1000个代币（乘以10^18，即小数位数）
- 可以成功进行转账操作
- 接收者可以看到收到的代币
