# SafeMath 安全算数运算

SafeMath 针对 256 位整数进行加减乘除运算添加了额外的异常处理，避免整型溢出漏铜，SafeMath 的代码如下：

```solidity
pragma solidity ^0.5.0; 
library SafeMath { 
// 加法运算，溢出时抛出异常 
function add(uint256 a, uint256 b) internal pure returns (uint256){ 
uint256 c = a + b; 
require(c >= a, "SafeMath: addition overflow"); 
return c; 
} 
// 减法运算，溢出时抛出异常 
function sub(uint256 a, uint256 b) internal pure returns (uint256){ 
require(b <= a, "SafeMath: subtraction overflow"); 
uint256 c = a - b; 
return c; 
} 
// 乘法运算，溢出时抛出异常 
function mul(uint256 a, uint256 b) internal pure returns (uint256){ 
if (a == 0) { 
return 0; 
} 
uint256 c = a * b; 
require(c / a == b, "SafeMath: multiplication overflow");
return c; 
} 
// 除法运算，除 0 异常 
function div(uint256 a, uint256 b) internal pure returns (uint256){ 
require(b > 0, "SafeMath: division by zero"); 
uint256 c = a / b; 
return c; 
} 
}
```

由于 SafeMath 是一个库，可以使用 using...for...把这几个函数关联到 uint256 上：

```solidity
pragma solidity ^0.5.0; 
import "@openzeppelin/contracts/contracts/math/SafeMath.sol"; 
contract MyContract { 
using SafeMath for uint256; 
uint counter; 
function add(uint i) public { 
// 使用 SafeMath 的 add 方法 
counter = counter.add(i); 
} 
}
```
