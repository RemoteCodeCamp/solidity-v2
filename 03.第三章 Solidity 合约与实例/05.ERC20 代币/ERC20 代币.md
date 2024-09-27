# ERC20 代币

ERC20 Token 是目前最为广泛使用的代币标准，所有的钱包和交易所都是按照这个标准对代币进行支持的。ERC20 标准约定了代币名称、总量及相关的交易函数。

```solidity
pragma solidity ^0.5.0; 
interface IERC20 { 
function name() public view returns (string); 
function symbol() public view returns (string); 
function decimals() public view returns (uint8); 
function totalSupply() external view returns (uint256); 
function balanceOf(address account) external view returns (uint256); 
function transfer(address recipient, uint256 amount) externalreturns (bool); 
function allowance(address owner, address spender) external viewreturns (uint256); 
function approve(address spender, uint256 amount) external returns(bool); 
function transferFrom(address sender, address recipient, uint256amount) external returns (bool); 
event Transfer(address indexed from, address indexed to, uint256value); 
event Approval(address indexed owner, address indexed spender,uint256 value); }
```

ERC20 接口定义中，有一些接口是不强制要求实现的（下面的解释说明中标记了可选的接口），ERC20 接口各函数说明如下。

- name()：（可选）函数返回代币的名称，如“MyToken”。
- symbol()：（可选）函数返回代币符号，如“MT”。
- decimals：（可选）函数返回代币小数点位数。
- totalSupply()：发行代币总量。
- balanceOf()：查看对应账号的代币余额。
- transfer()：实现代币转账交易，成功转账必须触发事件 Transfer。
- transferFrom()：给被授权的用户（合约）使用，成功转账必须触发 Transfer 事件。
- allowance()：返回授权给某用户（合约）的代币使用额度。
- approve()：授权用户可代表操作者花费多少代币，必须触发 Approval 事件。

OpenZeppelin 实现代码如下：

```solidity
pragma solidity ^0.5.0; 
import "./IERC20.sol"; 
import "../../math/SafeMath.sol"; 
contract ERC20 is IERC20 { 
using SafeMath for uint256; 
mapping (address => uint256) private _balances; 
mapping (address => mapping (address => uint256)) private _allowances; 
uint256 private _totalSupply; 
function totalSupply() public view returns (uint256) { 
return _totalSupply; 
} 
function balanceOf(address account) public view returns (uint256){ 
return _balances[account]; 
} 
function transfer(address recipient, uint256 amount) publicreturns (bool) { _transfer(msg.sender, recipient, amount); 
return true; 
} 
function allowance(address owner, address spender) public viewreturns (uint256) { 
return _allowances[owner][spender]; 
} 
function approve(address spender, uint256 value) public returns(bool) { _approve(msg.sender, spender, value); 
return true; 
} 
function transferFrom(address sender, address recipient, uint256amount) public returns (bool) { 
_transfer(sender, recipient, amount); 
_approve(sender, msg.sender, _allowances[sender][msg.sender].sub(amount)); 
return true; 
} 
function _transfer(address sender, address recipient, uint256amount) internal { require(sender != address(0), "ERC20: transfer from the zeroaddress"); 
require(recipient != address(0), "ERC20: transfer to the zeroaddress"); 
_balances[sender] = _balances[sender].sub(amount); 
_balances[recipient] = _balances[recipient].add(amount);
emit Transfer(sender, recipient, amount); 
} 
function _mint(address account, uint256 amount) internal {
require(account != address(0), "ERC20: mint to the zeroaddress"); 
_totalSupply = _totalSupply.add(amount); 
_balances[account] = _balances[account].add(amount); 
emit Transfer(address(0), account, amount); 
} 
function _burn(address account, uint256 value) internal { 
require(account != address(0), "ERC20: burn from the zeroaddress"); 
_balances[account] = _balances[account].sub(value); 
_totalSupply = _totalSupply.sub(value); 
emit Transfer(account, address(0), value); 
} 
function _approve(address owner, address spender, uint256 value)internal { 
require(owner != address(0), "ERC20: approve from the zeroaddress"); 
require(spender != address(0), "ERC20: approve to the zeroaddress");
 _allowances[owner][spender] = value; 
 emit Approval(owner, spender, value); 
 } 
 }
```

ERC20.sol 是实现了标准中所有的必须要实现的函数，可选的函数则放在另一个文件 ERC20Detailed.sol 中（后面也会贴出代码）。

ERC20 实现的关键是使用了两个 mapping:_balances 和_allowances，_balances 用来保存某个地址的余额，_allowances 用来保存某个地址授权给另一个地址可使用的余额。

transfer()用来实现代币转账的转账，有两个参数：转账的目标（接收者）及数量。在执行 transfer()的时候（对照_transfer()的实现），主要是修改控制账号余额的_balances 变量，修改方法为：发送方账号（即交易的发起人）的余额减去相应的金额，同时目标账号的余额加上相应的金额，加减法使用了 safemath 来防止溢出，transfer()的实现需要触发 Transfer 事件。approve()函数和 transferFrom()函数需要配合使用，使用场景是这样的：操作者先通过 approve() 授权第三方可以转移操作者的币，然后第三方通过 transferFrom()去转移币。举一个通俗的例子：假如使用代币来发送工资，总经理就可以授权财务使用部分代币（使用 approve()函数），财务把代币发放给员工（使用 transferFrom()函数）。

目前最常用的一个场景是去中心化交易（以下简称 DEX，它使用智能合约来处理代币之间的兑换）。假如 Bob 要使用 DEX 智能合约用 100 个代币 A 购买 150 个代币 B，那么通常操作步骤是：Bob 先把 100 个 A 授权给 DEX，然后调用 DEX 的兑换函数，在兑换函数里使用 transferFrom()函数把 Bob 的 100 个 A 转走，之后再转给 Bob150 个 B。

approve()函数通过修改_allowances 变量来控制被授权人及授权代币数量（请对照上面代码的_approve()函数），_allowances[owner][spender]=value;的意思是：owner 账号授权 spender 账号可消费数量为 value 的代币。 transferFrom()是由被授权人发起调用，transferFrom()的第一个参数 sender 是真正扣除代币的账号（也就是_allowances 中的 owner）。

ERC20Detailed 的实现比较简单，仅仅初始化代币名称、代币符号、小数位数这 3 个变量，代码如下：

```solidity
pragma solidity ^0.5.0; 
import "./IERC20.sol"; 
contract ERC20Detailed is IERC20 { 
string private _name; 
string private _symbol; 
uint8 private _decimals; 
constructor (string memory name, string memory symbol, uint8decimals) public { 
_name = name; 
_symbol = symbol; 
_decimals = decimals; 
} 
function name() public view returns (string memory) { 
return _name; 
} 
function symbol() public view returns (string memory) { 
return _symbol; 
} 
function decimals() public view returns (uint8) { 
return _decimals; 
} 
}
```

### 3.5.1 标准 ERC20 实现

有了 ERC20.sol 和 ERC20Detailed.sol，实现一个自己的代币就很简单了，现在尝试实现一个有 4 位小数、名称为 My Token 的代币，只需要以下几行代码：

```solidity
pragma solidity ^0.5.0; 
import "@openzeppelin/contracts/ERC20Detailed.sol" 
import "@openzeppelin/contracts/ERC20.sol" 
contract MyToken is ERC20 , ERC20Detailed("My Token", "MT", 4){
constructor() public { 
_mint(msg.sender, 1000000000 * 10 ** 4); 
} 
}
```

第 6 行的_mint()函数在 ERC20.sol 中实现的，用来初始化代币发行量。

### 3.5.2 WETH 实现

在标准 ERC20 基础之上，还可以根据自己的需求追加一些定制功能，比如：WETH 代币用来实现 ETH 和 ERC20 的双向 1：1 兑换。当向 WETH 代币存入 ETH 时，可以收到对等数量的 WETH 代币，同样使用 WETH 代币可以赎回对等的 ETH。

基于 OpenZepplin 实现的 WETH 代币如下：

```solidity
pragma solidity ^0.5.0; 
import "@openzeppelin/contracts/ERC20Detailed.sol" 
import "@openzeppelin/contracts/ERC20.sol" 
contract MyToken is ERC20 , ERC20Detailed("Wrapped Ether", "WETH",18) { 
event Deposit(address indexed dst, uint wad); 
event Withdrawal(address indexed src, uint wad); 
constructor() public { } function totalSupply() public view returns (uint) { 
return address(this).balance; 
} 
receive() public payable { 
deposit(); 
} 
function deposit() public payable { 
_mint(msg.sender, msg.value); 
emit Deposit(msg.sender, msg.value); 
} 
function withdraw(uint wad) public { 
_burn(msg.sender, wad); 
msg.sender.transfer(wad); 
emit Withdrawal(msg.sender, wad); 
} 
}
```

WETH 已经在以太坊网络上大量采用，当前使用最多的版本 WETH9, 其合约可以通过 [https://cn.etherscan.com/address/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#code](https://cn.etherscan.com/address/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#code) 参看到。
