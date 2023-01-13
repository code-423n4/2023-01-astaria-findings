- [Low](#low)
    - [**1. Allows malleable SECP256K1 signatures**](#1-allows-malleable-secp256k1-signatures)
    - [**2. Wrong transfer amount**](#2-wrong-transfer-amount)
    - [**3. Reentrancy pattern**](#3-reentrancy-pattern)
- [Non critical](#non-critical)
    - [**4. Mixing and Outdated compiler**](#4-mixing-and-outdated-compiler)
    - [**5. Lack of initialize protections**](#5-lack-of-initialize-protections)
    - [**6. Avoid hardcoded values**](#6-avoid-hardcoded-values)
    - [**7. Token compatibility**](#7-token-compatibility)
    - [**8. Improve code style**](#8-improve-code-style)

# Low

## **1. Allows malleable `SECP256K1` signatures**

Here, the `ecrecover()` method doesn't check the `s` range.

Homestead ([EIP-2](https://eips.ethereum.org/EIPS/eip-2)) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.

Since an order can only be confirmed once and its hash is saved, there doesn't seem to be a serious danger in existing use cases.

**Reference:**

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149

**Affected source code:**

- [ERC20-Cloned.sol:121](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L121)
- [VaultImplementation.sol:246](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L246)

## **2. Wrong transfer amount**

The amount set by the user when calling `transferFrom` is not taken into account in the internal method of `_execute`, so it should be forced to be `1`.

```js
  function safeTransferFrom(
    address from, // the from is the offerer
    address to,
    uint256 identifier,
    uint256 amount,
    bytes calldata data //empty from seaport
  ) public {
    //data is empty and useless
    _execute(from, to, identifier, amount);
  }
```

**Affected source code:**

- [ClearingHouse.sol:173](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L173)

## **3. Reentrancy pattern**

Although it has not been possible to exploit the reentrancy attack, the logic of the methods named below, make the changes of the validation flags after a method that allows reentrancy, it is convenient to modify the flags before the external calls to contracts.

In the `WithdrawProxy.claim` method, the `s.finalAuctionEnd` flag is set after an external contract is called, so if that contract allows a reentrancy attack, the attack vector is possible. It's better to apply the following changes:

```diff
  function claim() public {
    WPStorage storage s = _loadSlot();

    if (s.finalAuctionEnd == 0) {
      revert InvalidState(InvalidStates.CANT_CLAIM);
    }
+   s.finalAuctionEnd = 0;
    ...
-   s.finalAuctionEnd = 0;
    emit Claimed(address(this), transferAmount, VAULT(), balance);
  }
```

**Affected source code:**

- [WithdrawProxy.sol:284](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L284)

---

# Non critical

## **4. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity >=0.5.0;
pragma solidity >=0.7.5;
pragma solidity ^0.8.13;
pragma solidity ^0.8.17;
pragma solidity =0.8.17;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

## **5. Lack of `initialize` protections**

If you want to use the modifier in the class that implements the base class, you don't need to define the `Initializable` inheritance in the base class.

The following contracts are updateable, and follow the update pattern, these contracts contain an `initialize` method to set the contract once, but anyone can call this method, this will spend project fuel if someone tries to initialize it before the project.

It is recommended to check the sender when initializing the contract.

```diff
- function __initERC721(string memory _name, string memory _symbol) internal {
+ function __initERC721(string memory _name, string memory _symbol) internal initializer {
    ERC721Storage storage s = _loadERC721Slot();
    s.name = _name;
    s.symbol = _symbol;
  }
```

**Affected source code:**

- [ERC721.sol:69](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L69)
- [VaultImplementation.sol:190](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L190)

## **6. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

Use the selector instead of the raw value:

- [ERC721.sol:190-192](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L190-L192)

## **7. Token compatibility**

The current logic does not allow a token that does not have decimals to be used as a `PublicVault` asset.

```js
return 10**(ERC20(asset()).decimals() - 1);
```

**Affected source code:**

- [PublicVault.sol:106](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L106)

## **8. Improve code style**

Normally the arguments and local variables are written in lower case, however in many contracts it has been found that upper case is used, typically associated with constants, a good code style improves its readability.

**Affected source code:**

- [AstariaRouter.sol:84](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L84)

Using ' _' at the beginning of public or external methods is not a common practice, and is discouraged, worsening the readability and auditability of the code.

**Affected source code:**

- [AstariaRouter.sol:345](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L345)
- [AstariaRouter.sol:352](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L352)
