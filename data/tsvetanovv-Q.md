## [QA-01] Use stable pragma statement

Using a floating pragma `^0.8.17` statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
***

## [QA-02] Use latest Solidity version

Solidity pragma versioning should be upgraded to latest available version.
***

## [QA-03] Different pragma directives are used

Use one Solidity version on each contract.
***

## [QA-04] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

```
ClearingHouse.sol:
 40: uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =
 41:  uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
 
 WithdrawProxy.sol:
 49: uint256 private constant WITHDRAW_PROXY_SLOT =
 50:  uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
 
 CollateralToken.sol:
 73: uint256 private constant COLLATERAL_TOKEN_SLOT =
 74:  uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
 
 PublicVault.sol:
 53: uint256 private constant PUBLIC_VAULT_SLOT =
 54:   uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
 
 AstariaRouter.sol:
 62: uint256 private constant ROUTER_SLOT =
 63:   uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
 
LienToken.sol:
50: uint256 private constant LIEN_SLOT =
51:    uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;

ERC20-Cloned.sol:
15: bytes32 private constant PERMIT_TYPEHASH =
16:    keccak256(
17:      "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"

ERC721.sol:
15: uint256 private constant ERC721_SLOT =
16:    uint256(keccak256("xyz.astaria.ERC721.storage.location")) - 1;

VaultImplementation.sol:
44:  bytes32 public constant STRATEGY_TYPEHASH =
45:    keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");

47:  bytes32 constant EIP_DOMAIN =
48:    keccak256(
      "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
    );
51:  bytes32 constant VERSION = keccak256("0");

57:  uint256 private constant VI_SLOT =
58:    uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
```
***

## [QA-05] Empty blocks should be removed or Emit something

**Description**: Code contains empty block
```
ClearingHouse.sol:

104:  function setApprovalForAll(address operator, bool approved) external {}
180:  function safeBatchTransferFrom(
```

**Recommendation**: The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

***

## [QA-06] REMOVE PRAGMA ABIEncoderV2
`CollateralToken.sol:`
```
16: pragma experimental ABIEncoderV2;
```
`LienToken.sol:`
```
16: pragma experimental ABIEncoderV2;
```
You are using solidity v0.8 since that version and above the ABIEncoderV2 is not experimental anymore - it is actually enabled by default by the compiler.
***

## [QA-07] MISSING CHECKS FOR ADDRESS(0X0)
Many places in the code are missing CHECKS FOR ADDRESS(0X0).
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### Recommended Mitigation Steps

Consider adding explicit zero-address validation on input parameters of address type.
***

## [QA-08] Unused local variables
Not used local variables can be removed:
```
PublicVault.sol:
262: uint256 assets = totalAssets();
```

***