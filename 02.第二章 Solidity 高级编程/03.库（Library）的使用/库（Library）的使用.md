# 库（Library）的使用

#### 课程目标：

1. 理解 Solidity 中库（Library）的概念及其在合约开发中的作用。
2. 学习库的两种使用方式：内嵌库与链接库。
3. 掌握 `using for` 语法，了解如何通过库扩展 Solidity 的数据类型功能。
4. 通过代码示例，学会如何创建、部署和使用库以实现代码复用。

#### 课程内容：

---

### **第一节：库的概念与作用**

1. **库的基本概念**

- 库（Library）是 Solidity 中用于封装常用函数的代码模块。它允许在多个合约中复用相同的函数代码，减少重复代码，提高代码的维护性和安全性。
- 库通过关键字 `library` 定义，通常用于封装数学运算、数组操作等常见功能。

2. **库的使用限制**

- 库仅由函数组成，不能定义状态变量。
- 库不能直接修改 `msg.sender` 或 `this`，因为它们没有自己独立的上下文。
- 库函数可以是内部函数，也可以是公共或外部函数，使用不同的函数类型决定库的使用方式。

3. **示例：SafeMath 库**

```solidity
pragma solidity >=0.5.0;
library SafeMath {
    function add(uint a, uint b) internal pure returns (uint) {
        uint c = a + b;
        require(c >= a, "SafeMath: addition overflow");
        return c;
    }
}
// 使用 SafeMath 库的合约
contract AddTest {
    function add(uint x, uint y) public pure returns (uint) {
        return SafeMath.add(x, y);
    }
}
```

- 上面的 `SafeMath` 库提供了一个安全加法函数 `add`，在 `AddTest` 合约中通过 `SafeMath.add()` 调用库函数。

---

### **第二节：内嵌库与链接库**

1. **内嵌库**

- 如果库函数都是内部函数（`internal`），编译器会将库函数的代码直接嵌入到调用合约中。库不会被单独部署，这种方式称为“内嵌库”。
- **示例：使用内嵌库的 AddTest 合约**

```solidity
import "./SafeMath.sol";
contract AddTest {
    function add(uint x, uint y) public pure returns (uint) {
        return SafeMath.add(x, y);  // 内嵌库函数调用
    }
}
```

2. **链接库**

- 当库函数是公共或外部函数时，库可以单独部署，称为“链接库”。
- 链接库在以太坊上有独立的地址，合约在调用时会通过委托调用的方式使用库函数。
- 链接库的部署可以减少重复部署的 Gas 消耗，但库函数的变量会使用主调合约的上下文。

3. **示例：使用链接库**

- 修改 `SafeMath` 库中的 `add` 函数为外部函数：

```solidity
pragma solidity >=0.5.0;
library SafeMath {
    function add(uint a, uint b) external pure returns (uint) {
        uint c = a + b;
        require(c >= a, "SafeMath: addition overflow");
        return c;
    }
}
```

- 链接库的合约：

```solidity
import "./SafeMath.sol";
contract AddTest {
    function add(uint x, uint y) public pure returns (uint) {
        return SafeMath.add(x, y);  // 链接库函数调用
    }
}
```

- 部署过程中，`AddTest` 合约的字节码会留出库地址的位置，合约部署时需要链接库的地址。

---

### **第三节：****using for**** 语法扩展库的使用**

#### **1. ****using for**** 语法的作用**

- `using for` 语法允许将库的函数绑定到特定的数据类型，使得库函数可以直接作用于该数据类型的变量，简化函数调用。

**示例：使用 ** **using for** **绑定类型**

- 通过 `using for` 语法将 `SafeMath` 库函数绑定到 `uint` 类型：

```solidity
contract TestLib {
    using SafeMath for uint;
    function add(uint x, uint y) public pure returns (uint) {
        return x.add(y);  // 直接在 uint 类型的变量上调用库函数
    }
}
```

#### **3.** **using for** **语法**

- `using for` 语法可以将库函数绑定到任意类型，类似于为任意类型扩展新的方法。
- **示例：为数组添加****indexOf****函数**

```solidity
pragma solidity >=0.4.16;
library Search {
    function indexOf(uint[] storage self, uint value) public view returns (uint) {
        for (uint i = 0; i < self.length; i++) {
            if (self[i] == value) return i;
        }
        return uint(-1);  // 如果找不到返回 -1
    }
}
contract C {
    using Search for uint[];  // 将库函数绑定到 uint[] 类型
    uint[] data;
    function append(uint value) public {
        data.push(value);
    }
    function replace(uint _old, uint _new) public {
        uint index = data.indexOf(_old);  // 调用库函数
        if (index == uint(-1)) {
            data.push(_new);
        } else {
            data[index] = _new;
        }
    }
}
```

- 在上面的例子中，`Search` 库提供了 `indexOf` 函数来查找数组中某个元素的位置，并使用 `using Search for uint[]` 语法绑定库函数到 `uint[]` 类型，使得数组可以直接调用库函数。

---

### **第四节：实践练习**

1. **基础库的实现**：

   - 编写一个库 `MathLib`，实现加法、减法和乘法函数，并在合约中使用该库。
2. **链接库的使用**：

   - 修改 `MathLib` 的乘法函数为外部函数，然后编写一个合约 `CalcTest` 使用链接库，并通过库地址调用乘法函数。
3. **using for** **的使用**：

   - 编写一个库，提供对数组的扩展功能（如 `contains` 函数检查数组是否包含某个元素），并在合约中使用 `using for` 语法扩展数组类型。
