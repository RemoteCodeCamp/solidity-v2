# Solidity 中的变量

变量来源于数学，是计算机语言中能储存计算结果或能表示值的抽象概念。变量可以通过变量名访问。在指令式语言中，变量通常是可变的；但在纯函数式语言（如 Haskell）中，变量可能是不可变的。

Solidity 支持三种类型的变量：状态变量（变量值永久保存在合约存储空间中的变量）、局部变量（变量值仅在函数执行过程中有效的变量，函数退出后，变量无效）、全局变量（保存在全局命名空间，用于获取区块链相关信息的特殊变量）。Solidity 是一种静态类型语言，这意味着需要在声明期间指定变量类型。每个变量声明时，都有一个基于其类型的默认值。没有 undefined 或 null 的概念。

### 

1.1.1 状态变量

如果一个变量是状态变量，那么它的值将永久保存在合约存储空间中。下面的合约首先声明了一个状态变量 storedData，随后在构造函数中给其赋值。

```solidity
pragma solidity ^0.5.0; 
contract SolidityTest { 
uint storedData; // 状态变量 
constructor() public { 
storedData = 10; // 使用状态变量 
} 
}
```

### 1.1.2 局部变量

局部变量是仅限于在函数执行过程中有效的变量，函数执行完毕后，变量就不再受任何影响。函数参数也是局部变量。下述案例中可以看到，函数内部的变量被认为是局部变量。

```solidity
pragma solidity ^0.5.0; 
contract SolidityTest { 
uint storedData; // 状态变量 
constructor() public { 
storedData = 10; 
} 
function getResult() public view returns(uint){ 
uint a = 1; // 局部变量 
uint b = 2; 
uint result = a + b; 
return result; // 访问局部变量 
} 
}
```

### 1.1.3 全局变量

因为区块链的特性，solidity 语言有一类变量叫做全局变量，它们是全局工作区中存在的特殊变量，提供有关区块链和交易属性的信息。下面列举几个常用的全局变量：

blockhash(uint blockNumber) returns (bytes32) 给定区块的哈希值 – 只适用于 256 最近区块, 不包含当前区块

block.coinbase (address payable) 当前区块矿工的地址

block.difficulty (uint) 当前区块的难度

block.gaslimit (uint) 当前区块的 gaslimit

block.number (uint) 当前区块的 number

block.timestamp (uint) 当前区块的时间戳，为 unix 纪元以来的秒

gasleft() returns (uint256) 剩余 gas

msg.data (bytes calldata) 完成 calldata

msg.sender (address payable) 消息发送者 (当前 caller)

msg.sig (bytes4) calldata 的前四个字节 (function identifier)

msg.value (uint) 当前消息的 wei 值

now (uint) 当前块的时间戳

tx.gasprice (uint) 交易的 gas 价格

tx.origin (address payable) 交易的发送方

### 1.1.4 Solidity 变量名

为了规范变量名称的书写，在为变量命名时，需要记住以下规则：

不应使用 Solidity 保留关键字作为变量名。

例如，break 或 boolean，这类变量名是无效的。不应以数字(0-9)开头，必须以字母或下划线开头。

例如，123test 是一个无效的变量名，但是_123test 是一个有效的变量名。变量名区分大小写。

例如，Name 和 name 是两个不同的变量。
