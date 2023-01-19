### [G-01] Use Immutable instead of constant for `keccak256`

Constant expressions are [re-calculated each time it is in use]([https://github.com/ethereum/solidity/issues/9232](https://github.com/ethereum/solidity/issues/9232)), costing an extra `97` gas than a constant every time they are called.

Instances include:

Mark these as  `immutable`  instead of  `constant`

*There are 4 instances of this issue:*

### Affected source code:

```solidity
/VaultImplementation.sol
44: bytes32 public constant STRATEGY_TYPEHASH = keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
47: bytes32 constant EIP_DOMAIN = keccak256("EIP712Domain(string version,uint256 chainId,address verifyingContract)");
51: bytes32 constant VERSION = keccak256("0");

/ERC20-Cloned.sol
15: bytes32 private constant PERMIT_TYPEHASH =keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
```

- [VaultImplementation.sol:44](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L44)

### [G-02] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

*There are 1 instances of this issue:*

```solidity
/BeaconProxy.sol
87: function _beforeFallback() internal virtual {}
```

### Affected source code:

- [Beaconproxy:87](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L87)

### [G-03] Defined Variables Used Only Once

Certain variables is defined even though they are used only once.Remove these unnecessary variables to save gas.For cases where it will reduce the readability, one can use comments to help describe what the code is doing.

*There are 2 instances of this issue*

### Affected source code:

- [ClearingHouse.sol:74-75](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L74-#L75)
- [WithdrawProxy.sol:153-156](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L153-#L156)

### [G-04] Splitting `require()` statements that use `&&` saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&The gas difference would only be realized if the revert condition is realized(met).

*There are 3 instances of this issue:*

### Affected source code:

- [Vault.sol:L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)
- [PublicVault.sol:687](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687)
- [ERC20-Cloned.sol:143](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143)

### [G-05] Duplicated `require() /revert()` checks should be refactored to a modifier or function(instances)

*There are 1 instances of this issue:*

### Affected source code:

- [VaultImplementation.sol:96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96)

### Mitigation:

```solidity
function is_owner() private view {
        require(msg.sender == owner(), "ONLY OWNER");
   }
```