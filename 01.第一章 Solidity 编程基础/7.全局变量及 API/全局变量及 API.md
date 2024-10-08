# 全局变量及 API 

#### 课程目标：

1. 理解 Solidity 中常用的全局变量及 API。
2. 学习如何通过全局变量获取区块和交易的相关信息。
3. 掌握 Solidity 中的 ABI 编码解码函数及其应用场景。
4. 了解 Solidity 中的数学和密码学 API，熟悉哈希算法与签名恢复。
5. 理解 Solidity 中的时间单位及其在智能合约中的应用。

#### **课程内容：**

---

### **第一节：区块和交易属性 API**

Solidity 提供了一些全局变量来获取当前区块链状态的信息，常用于获取区块信息和交易细节。

#### **1. 区块属性**

- **blockhash(uint blockNumber) returns (bytes32)**：返回指定区块的哈希值，`blockNumber` 仅支持最近的 256 个区块，且不包括当前区块。

```solidity
bytes32 hash = block.blockhash(100);
```

- **block.coinbase (address)**：返回挖出当前区块的矿工地址。

```solidity
address miner = block.coinbase;
```

- **block.difficulty (uint)**：返回当前区块的难度。

```solidity
uint difficulty = block.difficulty;
```

- **block.gaslimit (uint)**：返回当前区块的 Gas 上限。

```solidity
uint gasLimit = block.gaslimit;
```

- **block.number (uint)**：返回当前区块号。

```solidity
uint blockNumber = block.number;
```

- **block.timestamp (uint)**：返回当前区块的时间戳（单位：秒）。常用于时间条件判断。

```solidity
uint timestamp = block.timestamp;
```

#### **2. 交易属性**

- **gasleft() returns (uint256)**：返回当前合约执行剩余的 Gas 数量。

```solidity
uint remainingGas = gasleft();
```

- **msg.data (bytes)**：返回当前调用的完整 `calldata`。

```solidity
bytes memory data = msg.data;
```

- **msg.sender (address)**：返回当前调用的发送者地址。

```solidity
address sender = msg.sender;
```

- **msg.sig (bytes4)**：返回当前调用的函数选择器。

```solidity
bytes4 functionSelector = msg.sig;
```

- **msg.value (uint)**：返回此次调用发送的以太币数量（单位：wei）。

```solidity
uint sentValue = msg.value;
```

- **tx.gasprice (uint)**：返回当前交易的 Gas 价格。

```solidity
uint gasPrice = tx.gasprice;
```

- **tx.origin (address payable)**：返回交易的最初发起者地址。如果只有一个调用，`tx.origin` 与 `msg.sender` 相同；否则，`tx.origin` 始终是最初的交易发起者。

```solidity
address origin = tx.origin;
```

---

### **第二节：ABI 编码及解码函数 API**

ABI（应用二进制接口）函数用于编码和解码 Solidity 中的数据类型，特别适用于合约间交互时处理复杂数据结构。

#### **1. 编码函数**

- **abi.encode(...) returns (bytes)**：对输入的参数进行 ABI 编码，返回字节数组。

```solidity
bytes memory encodedData = abi.encode(uint(1), address(0x123));
```

- **abi.encodePacked(...) returns (bytes)**：将多个参数进行紧密打包编码，不填充到 32 字节。适用于哈希计算。

```solidity
bytes memory packedData = abi.encodePacked(uint(1), address(0x123));
```

- **abi.encodeWithSelector(bytes4 selector, ...) returns (bytes)**：将参数编码，并在前面加上函数选择器（用于外部调用）。

```solidity
bytes4 selector = bytes4(keccak256("transfer(address,uint256)")); 

bytes memory encodedWithSelector = abi.encodeWithSelector(selector, address(0x123), 100);
```

- **abi.encodeWithSignature(string signature, ...) returns (bytes)**：通过函数签名生成函数选择器，并将参数编码。

```solidity
bytes memory encodedWithSignature = abi.encodeWithSignature("transfer(address,uint256)", address(0x123), 100);
```

#### **2. 解码函数**

- **abi.decode(bytes memory encodedData, (...)) returns (...)**：对编码的数据进行解码，返回解码后的参数。

```solidity
(uint a, address b) = abi.decode(encodedData, (uint, address));
```

---

### **第三节：数学和密码学函数 API**

Solidity 提供了一些常用的数学与密码学函数，用于处理复杂运算和数据加密。

#### **1. 数学函数**

- **addmod(uint x, uint y, uint k) returns (uint)**：计算 `(x + y) % k`，在任意精度下执行加法再取模，支持大数运算。

```solidity
uint result = addmod(10, 20, 7); // 结果为 2
```

- **mulmod(uint x, uint y, uint k) returns (uint)**：计算 `(x * y) % k`，先进行乘法再取模。

```solidity
uint result = mulmod(10, 20, 7); // 结果为 6
```

#### **2. 密码学哈希函数**

- **keccak256(bytes memory) returns (bytes32)**：使用 Keccak-256 算法计算哈希值（以太坊的主要哈希算法）。

```solidity
bytes32 hash = keccak256(abi.encodePacked("Hello, World!"));
```

- **sha256(bytes memory) returns (bytes32)**：计算 SHA-256 哈希值。

```solidity
bytes32 hash = sha256(abi.encodePacked("Hello, World!"));
```

- **ripemd160(bytes memory) returns (bytes20)**：计算 RIPEMD-160 哈希值，生成较短的 20 字节哈希值。

```solidity
bytes20 hash = ripemd160(abi.encodePacked("Hello, World!"));
```

#### **3. 椭圆曲线签名恢复**

- **ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)**：通过椭圆曲线签名恢复公钥对应的地址，常用于验证签名。

```solidity
address signer = ecrecover(hash, v, r, s);
```

---

### **第四节：时间单位及其应用**

#### **1. Solidity 中的时间单位**

- Solidity 提供了内置的时间单位，可以将秒转换为更常见的时间单位，如分钟、小时、天、周和年。这些单位在智能合约中可以用于表示和操作时间。
- **时间单位及对应的秒数**：

  - **1 seconds** = 1 秒。
  - **1 minutes** = 60 秒。
  - **1 hours** = 3600 秒。
  - **1 days** = 86400 秒。
  - **1 weeks** = 604800 秒。
  - **1 years** = 31536000 秒（非精确，忽略了闰年）。

#### **2. 示例：使用时间单位进行锁定**

- 下面的代码示例演示了如何使用时间单位创建一个定时锁定合约，只有在锁定时间之后才能解锁代币：

```solidity
solidity pragma solidity ^0.8.0;
contract TimeLock { 
uint public unlockTime; 
address public owner;
constructor(uint _lockTime) { 
    owner = msg.sender; 
    unlockTime = block.timestamp + _lockTime * 1 days; // 锁定指定天数 
  }
function withdraw() public { 
    require(block.timestamp >= unlockTime, "Funds are locked."); 
    require(msg.sender == owner, "Only owner can withdraw."); // 执行提款操作
     }
      }
```

- 在这个示例中，`unlockTime` 被设定为当前区块时间加上指定的天数。只有在达到解锁时间后，所有者才能提取资金。

---

### **第五节：实践练习**

1. **获取区块和交易信息**：

   - 编写合约，使用区块和交易的 API 输出当前区块号、时间戳、矿工地址和交易的 Gas 价格。
2. **ABI 编码与解码**：

   - 编写函数，接收多个参数并将其编码为字节数组，另一个函数解码并返回原始值。

3.使用时间单位操作：

编写一个定时奖励合约，用户可以存入资金，但只有在指定的锁定时间（如 1 周、1 月后）才能领取奖励。 锁定时间应基于合约中的时间单位（如 days、weeks）进行设置。 数学与密码学函数应用：

编写一个合约，使用 addmod 和 mulmod 函数进行大数运算，验证结果是否正确。 使用 keccak256 哈希函数对输入的字符串进行哈希计算，提供一个方法验证输入与哈希值的匹配。

第六节：时间单位与实际应用 1. 时间锁定合约的设计 在实际应用中，时间锁定（Time-Lock）合约广泛用于保护资金，确保只有在特定时间段之后才能提取。这种合约非常适用于 ICO 解锁、奖励分发等场景。

高级示例：使用时间单位设计多阶段锁定合约：

```solidity
pragma solidity ^0.8.0;
contract MultiStageTimeLock { 
    uint public stage1UnlockTime; 
    uint public stage2UnlockTime; 
    address public owner;

constructor(uint _stage1Lock, uint _stage2Lock) {
    owner = msg.sender;
    stage1UnlockTime = block.timestamp + _stage1Lock * 1 days;  // 第一阶段锁定天数
    stage2UnlockTime = block.timestamp + _stage2Lock * 1 days;  // 第二阶段锁定天数
}
function withdrawStage1() public {
    require(block.timestamp >= stage1UnlockTime, "Stage 1 funds are locked.");
    require(msg.sender == owner, "Only owner can withdraw.");
    // 提取第一阶段资金操作
}
function withdrawStage2() public {
    require(block.timestamp >= stage2UnlockTime, "Stage 2 funds are locked.");
    require(msg.sender == owner, "Only owner can withdraw.");
    // 提取第二阶段资金操作
}
```

}

这种多阶段时间锁定（Multi-Stage Time Lock）可以适用于不同的资金释放阶段。例如，某个项目可以设定两个阶段的锁定时间，分别用于不同的资金提取条件。

1. 基于区块时间戳的条件操作 在某些应用场景中，基于时间的操作可以被用来限制某些行为的发生频率，例如每 24 小时可以执行一次某个操作。这可以通过检查 block.timestamp 来实现。

示例：每天限制一次执行的合约

```solidity
pragma solidity ^0.8.0;
contract DailyAction {

    uint public lastActionTime; 
    address public owner;

constructor() {
    owner = msg.sender;
    lastActionTime = block.timestamp - 1 days;  // 初始化为可立即执行
}
function performAction() public {
    require(msg.sender == owner, "Only owner can perform this action.");
    require(block.timestamp >= lastActionTime + 1 days, "You can only perform this action once per day.");
    lastActionTime = block.timestamp;  // 更新最后一次执行时间
    // 执行具体操作
}
```

}

这种基于时间戳的条件操作适合频率限制型操作，比如某些奖励发放系统或者投票系统。
