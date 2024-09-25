# Solidity 结构体（Struct）

#### 教学目标：

- 理解 Solidity 中结构体（Struct）的定义与应用。
- 掌握结构体的初始化方法及其限制。
- 学会如何在结构体中使用数组、映射等复杂数据类型。
- 理解结构体在智能合约设计中的常见应用场景和最佳实践。

#### 教学大纲更新：

1. **结构体的定义**
   - **基本定义**：Solidity 中使用 `struct` 关键字定义结构体，可以将多种数据类型组合在一起形成自定义的复合数据类型。
   - **示例**：

```solidity

struct CustomType {
bool myBool;
uint myInt;
}
```
- **复杂类型**：结构体的成员不仅限于基本类型，还可以是数组、映射、甚至其他结构体。
```solidity
struct CustomType2 {
    CustomType[] cts;
    mapping(string => CustomType) indexs;
}
struct CustomType3 {
    string name;
    mapping(string => uint) score;
    int age;
}
```

2. **结构体的限制**
   - **不能包含自身类型作为成员**：结构体不能直接包含其自身类型作为成员，但可以通过映射来间接包含。

```solidity

struct CustomType2 {
CustomType[] cts;
mapping(string => CustomType2) indexs;
}

```

3. **结构体的声明与初始化**
	- **仅声明变量**：在声明结构体变量时，可以不进行初始化，此时Solidity会使用默认值为结构体成员赋值。
```solidity
CustomType ct1;  // 使用默认值初始化
```

- **按成员顺序初始化**：可以按照结构体成员声明的顺序进行初始化。此方法在状态变量和函数内均可使用，但要注意成员类型和数量的匹配。
```solidity
// 状态变量初始化
CustomType ct1 = CustomType(true, 2);

// 在函数内初始化
CustomType memory ct2 = CustomType(true, 2);

```
- **具名方式初始化**：这种方式不需要按照成员顺序，可以自由选择初始化的顺序，但必须忽略`mapping`类型的成员。
```solidity
CustomType memory ct = CustomType({ myBool: true, myInt: 2 });
```

- **复杂结构体的初始化**：对于包含`mapping`的结构体，只能对非`mapping`成员进行初始化。
```solidity
CustomType3 memory ct = CustomType3("tiny", 2);
```

4. **结构体的常见应用场景**
	- **用户信息管理**：可以使用结构体存储用户的详细信息，如姓名、年龄、得分等。
	- **订单管理**：在电商合约中，结构体可以用于存储订单的详细信息，如订单ID、产品列表、总价等。
	- **示例**：使用结构体管理用户信息
```solidity
struct User {
    string name;
    uint age;
    mapping(string => uint) score;
}

mapping(address => User) public users;

function createUser(string memory _name, uint _age) public {
    User storage user = users[msg.sender];
    user.name = _name;
    user.age = _age;
}

function updateUserScore(string memory _subject, uint _score) public {
    users[msg.sender].score[_subject] = _score;
}

function getUserScore(string memory _subject) public view returns (uint) {
    return users[msg.sender].score[_subject];
}
```

5. **结构体的最佳实践**

   - **结构体命名与设计**：结构体的名称应当具有描述性，成员字段名称应简洁且易于理解。避免结构体过于复杂，以免影响合约的可维护性。
   - **减少存储成本**：尽量减少结构体中存储的字段数量，以优化存储成本，尤其在频繁更新的场景下可以考虑使用指针（引用）。
   - **避免嵌套复杂性**：结构体的嵌套使用虽然灵活，但过度嵌套可能导致合约代码复杂度增加，应谨慎设计。
6. **总结**

   - 结构体是 Solidity 中管理复杂数据的强大工具，合理设计和使用结构体可以显著提高智能合约的灵活性和可维护性。
   - 在设计合约时，需要根据实际需求选择适当的结构体成员和初始化方式，并考虑到结构体的限制。
7. 作业：

   - 任务 1：创建一个合约，定义一个结构体用于存储产品信息，包括产品 ID、名称、价格和库存。实现增加、修改和查询产品信息的功能。
   - 任务 2：扩展合约，定义一个结构体用于存储订单信息，并实现订单的创建和查询功能，考虑如何设计结构体以便有效存储和访问订单数据。
   - 任务 3：设计一个用户管理合约，使用结构体记录用户的个人信息、余额及交易历史，探索如何优化结构体的设计以减少存储成本。

#### 相关代码参考：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
contract StructExample {
    struct Product {
        uint id;
        string name;
        uint price;
        uint stock;
    }
    mapping(uint => Product) public products;
    uint public productCount;
    function addProduct(string memory _name, uint _price, uint _stock) public {
        productCount++;
        products[productCount] = Product(productCount, _name, _price, _stock);
    }
    function updateProductStock(uint _productId, uint _newStock) public {
        Product storage product = products[_productId];
        product.stock = _newStock;
    }
    function getProduct(uint _productId) public view returns (string memory, uint, uint) {
        Product storage product = products[_productId];
        return (product.name, product.price, product.stock);
    }
}
```
