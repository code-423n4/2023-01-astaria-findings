# Low risk / QA findings

## QA-1: Lack of documentation in `initialize()` of AstariaRouter

Function `initalize()` of AstariaRouter.sol lacks in documentation for the following arguments:

- `_WITHDRAW_IMPL`
- `_CLEARING_HOUSE_IMPL`
- `_BEACON_PROXY_IMPL`

## QA-2: ETH accidentally sent to AstariaRouter cannot be recovered

AstariaRouter has multiple payable public methods: `mint()`, `deposit()`, `withdraw()`, `redeem()`, and `pullToken()`. ETH accidentally sent to this contract via these methods cannot be recovered unless the contract is selfdestructed or upgraded.

## QA-3: Remove unused variables in `liquidatorNFTClaim` of CollateralToken.sol

The following variables are not used and hence can be removed.

```solidity
address tokenContract = underlying.tokenContract;
uint256 tokenId = underlying.tokenId;
```

## QA-4: Provide descriptive error info in PublicVault.sol

In multiple locations in PublicVault.sol, `require` statements lack in descriptive error information. 

```solidity
require(s.allowList[receiver]);  // in mint()
```

```solidity
require(s.allowList[receiver]); // in deposit()
```

Add an error description in the second argument, or create a custom error type such as `error NotInAllowList()` and throw it with `revert`.

## QA-5: Provide descriptive error info in VaultImplementation.sol

In multiple locations in VaultImplementation.sol, `require` statements lack in descriptive error messages.

```solidity
require(msg.sender == owner()); //owner is "strategist"
```

Add an error description in the second argument, or create a custom error type and throw it with `revert`. 

## QA-6: Unused field in `RouterStorage` of IAstariaRouter.sol

The field `ERC20 WETH` in the `RouterStorage` struct is not being used anywhere in the code. This can be removed to reduce the storage space.

## QA-7: Implicitly assumed non-inclusivity of `minDepositAmount()` in ERC4626-Cloned.sol

The following snippet

```solidity
require(assets > minDepositAmount(), "VALUE_TOO_SMALL");
```

in the `deposit` and `mint` functions implicitly assumes that `minDepositAmount` is non-inclusive. Clarify this in the documentation.

## QA-8: Remove unused variable in `deposit` of PublicVault.sol

```solidity
uint256 assets = totalAssets();
```

is not used and hence can be removed.