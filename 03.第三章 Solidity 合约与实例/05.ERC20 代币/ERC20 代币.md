# ERC20

#### 课程目标

- 了解并掌握 ERC20 标准及其重要性
- 掌握代币的基本功能及实现方式
- 掌握如何使用 OpenZeppelin 库编写安全且高效的 ERC20 合约
- 通过实例了解代币的实际应用场景，如去中心化交易所（DEX）和代币转换

#### 课程结构

1. **介绍 ERC20 标准**

   ##### 1. ERC20 是什么？为什么它是最常用的代币标准？

   **ERC20** 是 Ethereum Request for Comments 20 的缩写，是以太坊平台上用于代币创建和管理的最流行的标准之一。ERC20 定义了一组标准化的接口和规则，开发者可以遵循这些标准来创建兼容的钱包、交易所和其他应用的代币。这使得所有符合 ERC20 标准的代币可以在不同的以太坊钱包和去中心化应用（dApps）之间无缝互操作。
   ERC20 是最常用的代币标准的原因包括：

   - **兼容性**：所有遵循 ERC20 标准的代币都可以在支持该标准的钱包和交易所中使用，确保广泛的兼容性。
   - **简化开发**：开发者不需要重新设计代币的核心功能，像转账、余额查询等标准功能都已经被明确规范，可以直接使用。
   - **广泛应用**：很多项目和企业通过 ERC20 标准创建自己的代币，形成了强大的生态系统，并且被广泛接受和支持。

   ##### 2. ERC20 标准的广泛应用：钱包、交易所支持的代币格式

   由于 ERC20 标准的广泛使用，它已经成为以太坊网络上的代币事实标准。以下是 ERC20 标准的一些重要应用：

   - **钱包支持**：所有兼容 ERC20 标准的钱包（如 MetaMask、Trust Wallet 等）都可以存储和管理任何符合该标准的代币。用户可以轻松地将 ERC20 代币发送到不同的账户或与 dApp 交互。
   - **交易所支持**：中心化和去中心化的交易所（如 Binance、Uniswap）都支持 ERC20 代币的交易。通过使用统一的标准，交易所能够快速集成和上架新代币，用户也可以方便地进行交易。
   - **智能合约集成**：由于 ERC20 代币遵循同样的接口，智能合约可以很容易地集成和使用不同的 ERC20 代币进行操作，例如在去中心化金融（DeFi）协议中进行借贷、质押等操作。

   ##### 3. ERC20 标准的核心功能和约束

   ERC20 标准定义了一组核心功能和接口，使得任何遵循该标准的代币都具备相同的基本操作能力。这些功能包括：

   - **代币信息**：
     - `name()`：返回代币名称
     - `symbol()`：返回代币符号（如 “ETH”）
     - `decimals()`：返回代币的小数位数，通常为 18 位
   - **核心功能**：
     - `totalSupply()`：返回代币的总供应量
     - `balanceOf(address)`：返回指定地址的代币余额
     - `transfer(address, uint256)`：从调用者账户向目标账户转移代币
     - `approve(address, uint256)`：授权指定地址在一定额度内代表自己使用代币
     - `allowance(address, address)`：查询授权的额度
     - `transferFrom(address, address, uint256)`：代表授权用户转移代币
   - **事件**：
     - `Transfer`：代币从一个账户转移到另一个账户时触发
     - `Approval`：当用户授权代币给其他用户时触发

   #### ERC20 标准的约束：

   - 必须实现标准中定义的所有函数，以确保代币能够被广泛使用和兼容。
   - 代币的转账操作必须触发 `Transfer` 事件，这有助于记录交易历史和方便事件监听。
   - 代币授权操作必须触发 `Approval` 事件，用于记录用户的授权行为。
2. **ERC20 接口解析**

   - `name()`、`symbol()`、`decimals()`：代币的可选信息
   - `totalSupply()`：获取代币的发行总量
   - `balanceOf()`：查询账户余额
     `balanceOf()` 函数用于查询指定账户的代币余额，返回该账户持有的代币数量。
     **实例代码：**

```solidity
pragma solidity ^0.5.0;
import "@openzeppelin/contracts/ERC20.sol";
contract MyToken is ERC20 {
constructor() public {
_mint(msg.sender, 1000 * 10 ** 18);  // 初始化账户，给合约部署者 1000 代币
}
function checkBalance(address account) public view returns (uint256) {
return balanceOf(account);  // 返回指定账户的代币余额
}
}
```
	
**使用方法：**
	假设你是合约的部署者，你可以调用 `checkBalance(address)` 方法来查询账户的代币余额，例如：
```solidity
// 查询 msg.sender 的余额
uint256 balance = myToken.checkBalance(msg.sender);
```
- `transfer()`：代币转账



transfer() 函数用于将代币从调用者账户转移到指定的接收者账户。该函数需要两个参数：接收者地址和转移的代币数量。
**实例代码：**
```solidity
pragma solidity ^0.5.0;

import "@openzeppelin/contracts/ERC20.sol";

contract MyToken is ERC20 {
constructor() public {
_mint(msg.sender, 1000 * 10 ** 18);  // 给合约部署者 1000 代币
}

```
```solidity
function sendTokens(address recipient, uint256 amount) public returns (bool) {
    return transfer(recipient, amount);  // 将代币发送给接收者
}
```


- **使用方法：**
// 将 100 代币从 msg.sender 转移到 recipient
```solidity
myToken.sendTokens(recipient, 100 * 10 ** 18);
```

该操作完成后，调用者的账户余额将减少 100 个代币，接收者的账户余额将增加 100 个代币。

```solidity
- `allowance()`、`approve()`、`transferFrom()`：授权与第三方转账
- **approve()**：允许一个地址（被授权人）代表调用者使用一定数量的代币。
```

**allowance()**：查询调用者授权给某个地址的代币剩余额度。
**transferFrom()**：被授权的第三方可以使用此函数来转移授权的代币。

**实例代码：**
```solidity
pragma solidity ^0.5.0;

import "@openzeppelin/contracts/ERC20.sol";

contract MyToken is ERC20 {
    constructor() public {
        _mint(msg.sender, 1000 * 10 ** 18);  // 给合约部署者 1000 代币
    }

    // 授权第三方可以使用代币
    function approveTokens(address spender, uint256 amount) public returns (bool) {
        return approve(spender, amount);  // 授权 spender 可以使用 amount 个代币
    }

    // 查询剩余的授权额度
    function checkAllowance(address owner, address spender) public view returns (uint256) {
        return allowance(owner, spender);  // 查询 owner 授权给 spender 的额度
    }

    // 第三方（被授权者）代替 owner 使用代币
    function transferFromOwner(address owner, address recipient, uint256 amount) public returns (bool) {
        return transferFrom(owner, recipient, amount);  // 代表 owner 将代币转移给 recipient
    }
}
```

**使用方法：**
**授权代币：**

```solidity
// msg.sender 授权给 spender 地址可以使用 200 代币
myToken.approveTokens(spender, 200 * 10 ** 18);
```

**查询授权额度：**

```solidity
// 查询 msg.sender 授权给 spender 的剩余额度
uint256 remainingAllowance = myToken.checkAllowance(msg.sender, spender);
```

**被授权者使用授权的代币：**

```solidity
// spender 地址调用 transferFrom，将 100 代币从 msg.sender 转移给 recipient
myToken.transferFromOwner(msg.sender, recipient, 100 * 10 ** 18);
```

```solidity
事件 Transfer 和 Approval 的触发和意义
```

1. **关键学习点**

   - ERC20 代币的工作机制（代币转账、授权、代币总量）
   - `balanceOf()` 和 `transfer()` 的细节实现
     - 如何通过内部函数 `_transfer()` 安全执行转账，使用 SafeMath 防止溢出
   - `approve()` 和 `transferFrom()` 的授权机制
     - 真实世界应用：如公司管理代币发放（工资发放示例）
   - `allowance()` 授权额度控制与授权过期
   - 事件的使用：合约的日志与监控
2. **代码实现**

   - 使用 OpenZeppelin 库实现安全的 ERC20 代币合约
   - **基本合约实现**
     - `ERC20.sol` 和 `ERC20Detailed.sol` 的功能
     - 初始化代币供应 (`_mint()`)、安全转账 (`_transfer()`)、销毁代币 (`_burn()`)
     - `SafeMath` 的应用与重要性
   - **实现一个自定义代币**
     - 创建一个 MyToken，4 位小数，代币符号为 "MT"，代币总量 1,000,000,000
     - **代码实例**：

```solidity

pragma solidity ^0.5.0;
import "@openzeppelin/contracts/ERC20Detailed.sol";
import "@openzeppelin/contracts/ERC20.sol";

contract MyToken is ERC20, ERC20Detailed("My Token", "MT", 4) {
constructor() public {
_mint(msg.sender, 1000000000 * 10 ** 4);
}
}

``` 
- **WETH 实现**
	- 基于 ERC20 实现 ETH 和 ERC20 双向转换
	- **WETH 代币的实现**：通过 `deposit()` 和 `withdraw()` 处理 ETH 和 WETH 的互换

3. **实际应用场景**
	- **去中心化交易所（DEX）中的使用**
		- ERC20 代币如何在去中心化交易所中流通
		- 交易过程中的 `approve()` 和 `transferFrom()` 如何应用（以 Bob 使用 100 个代币 A 购买 150 个代币 B 为例）
	- **代币分发与激励机制**
		- 使用 ERC20 代币进行奖励和工资发放的实际案例

4. **扩展功能**
	- 自定义 ERC20 代币的功能扩展（例如 WETH 的实现）
	- 如何通过事件监听与合约交互日志分析


### 作业
### 实现一个自定义的 ERC20 代币，使用 SafeMath 保障安全
### 编写一个代币授权与转账的智能合约，模拟工资发放
### 使用 Remix 部署代币并测试其基本功能

面试常见问题：

### 1，**ERC20 的 ****approve()**** 和 ****transferFrom()**** 函数的作用是什么？**

- **回答**：`approve()` 允许代币所有者授权某个地址可以代表自己花费一定数量的代币。`transferFrom()` 允许被授权的地址从授权人的账户中转移代币，常用于第三方应用如去中心化交易所。

### 2， **如何防止 ERC20 合约中的双重花费问题？**

- **回答**：ERC20 合约本身没有内置的双重花费防护机制，但可以通过适当使用 `require()` 函数来检查代币余额是否充足，以及适当管理交易的状态变化来防止双重花费。

### 3. **什么是 ****allowance()**** 函数？在什么场景下会用到？**

- **回答**：`allowance()` 返回被授权的地址能够使用的代币数量。常见使用场景是在 `approve()` 函数之后查询被授权的代币剩余额度。

### 4. **ERC20 合约中如何避免整数溢出问题？**

- **回答**：可以通过引入 `SafeMath` 库来避免溢出问题。`SafeMath` 提供了加、减、乘、除的安全实现，防止溢出和下溢。

### 5. **ERC20 的安全性考量有哪些？**

- **回答**：
	- 使用 `SafeMath` 防止整数溢出。
	- 防止 `approve()` 和 `transferFrom()` 的双花漏洞，如先将批准数量设置为 0，再更新新的授权数量。
	- 确保合约不会意外接收和锁定代币（如增加 `withdraw()` 函数，允许错误转入的代币退回）。
	- 确保 `transfer()` 和 `transferFrom()` 在代币余额不足时会正确回滚交易。

### 6. **ERC20 如何与 ERC777 或 ERC721 区分？**

- **回答**：ERC20 代币是同质化代币，所有代币单位是相同且可以互换的；ERC721 是非同质化代币，代表的是独特的资产；而 ERC777 是 ERC20 的升级版，增加了诸如发送钩子、操作员功能和增强的代币安全性。

### 7. **如何在 ERC20 合约中增加燃烧功能？**

- **回答**：可以通过实现 `burn()` 函数来销毁代币，从而减少总供应量。通常在执行 `burn()` 时会减少代币持有者的余额并减少 `totalSupply`。

### 8，**如何实现一个具备动态供应量的 ERC20 代币？**

- **回答**：可以通过增加 `mint()` 和 `burn()` 函数来实现动态供应量。`mint()` 用于创建新的代币，`burn()` 用于销毁代币。`mint()` 应该包含适当的访问控制机制，以确保只有授权用户可以调用。

### 9. **ERC20 合约中的代币小数位数如何定义？为什么需要 ****decimals()**** 函数？**

- **回答**：`decimals()` 函数定义代币可以被分割的精度。例如，`decimals()` 返回 18，表示该代币有 18 位小数。它允许代币在精度和实际金额之间进行转换，使得代币具备更灵活的单位表示。

### 10. **如何优化 ERC20 合约的 Gas 消耗？**

- **回答**：
	- 减少不必要的存储操作，存储写入的 Gas 消耗最高。
	- 使用批量处理交易的方式，如批量转账和授权，避免多次调用产生额外的 Gas 开销。
	- 避免在循环中频繁调用高成本的存储或转账操作。

### 11. **ERC20 合约的最大问题是什么？如何解决？**

- **回答**：ERC20 最大的问题之一是授权问题，即用户授权第三方代币花费额度后，第三方可能会在任何时间消耗授权的代币。这个问题可以通过实现 ERC777 或者增加 `permit()` 机制（如 ERC2612 标准）来部分解决。`permit()` 使用签名进行授权，而不依赖于 `approve()` 函数。

