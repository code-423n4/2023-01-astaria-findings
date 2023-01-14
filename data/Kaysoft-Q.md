### [NC-01] SPDX license identifier not provided in source file.

File: 
- [src/interfaces/IERC721.sol#L1](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC721.sol#L1)


### [NC-02] Remove empty blocks or emit events.

File: 
- [src/BeaconProxy.sol#L87](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L87)
- `_beforeCommitToLien` in VaultImplementation.sol
- `_afterCommitToLien` in VaultImplementation.sol

## [NC-03] No NatSpec comments for some functions on the codebase
File: All functions in the `ClearingHouse.sol` file do not have Natspec comments
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol

## [NC-04] Solidity compiler optimization can be problematic
File: hardhat.config.ts
```jsx
const config: HardhatUserConfig = {
  solidity: {
    compilers: [
      {
        version: "0.8.17",
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
```

### Description

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [L-01] Lack of 2 step ownership change
Mistakenly setting the new owner with a wrong address will be irrecoverable.

File: [src/AuthInitializable.sol#L95](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AuthInitializable.sol#L95)

```
 function transferOwnership(address newOwner) public virtual requiresAuth {
    AuthStorage storage s = _getAuthSlot();
    s.owner = newOwner;

    emit OwnershipTransferred(msg.sender, newOwner);
  }

```

#### Recommended Mitigation steps
Use Openzeppelin's 2-step ownership change. see https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol#L35-L56

```
 function transferOwnership(address newOwner) public virtual override onlyOwner {
        _pendingOwner = newOwner;
        emit OwnershipTransferStarted(owner(), newOwner);
    }

    /**
     * @dev Transfers ownership of the contract to a new account (`newOwner`) and deletes any pending owner.
     * Internal function without access restriction.
     */
    function _transferOwnership(address newOwner) internal virtual override {
        delete _pendingOwner;
        super._transferOwnership(newOwner);
    }

    /**
     * @dev The new owner accepts the ownership transfer.
     */
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
```

### [L-02] The `balanceOf` function do not return the balance.

The `balanceOf` function does not return the balance of the account. The function instead always return `type(uint256).max`.
File: [src/ClearingHouse.sol#L82-L88](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L88)

```
function balanceOf(address account, uint256 id)
    external
    view
    returns (uint256)
  {
    return type(uint256).max;
  }
```

#### Recommended Mitigation steps
Implement the balanceOf functionality instead of returning the max uint value.

## [L-03] The `setApprovalForAll` function is not implemented
File: https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L104

```
function setApprovalForAll(address operator, bool approved) external {}
```
#### Recommended Mitigation steps
- Consider implementing the `setApprovalForall` functionality.

## [L-04] The `isApprovedforAll` always return true

File: https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L106

```
function isApprovedForAll(address account, address operator)
    external
    view
    returns (bool)
  {
    return true;
  }
```
#### Recommended Mitigation Steps
Consider implementing the functionality of `isApprovedforAll` function.

## [L-05] Remove unused parameters and variables
- The `account` and `id` parameters of the `balanceOf` function are unused
File: [src/ClearingHouse.sol#L82-L88](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L88)

- The `ids` paramater of balanceOfBatch function is unused
File: [src/ClearingHouse.sol#L90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90)

- All The parameters of the `setApprovalForAll` and the `isApprovalForAll` functions are unused.
File: 
- [src/ClearingHouse.sol#L104](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104)
- [src/ClearingHouse.sol#L106](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106)

- `tokenContract` and the `to` parameters of the `_execute` functions are unused
File: [src/ClearingHouse.sol#L115](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115)
- All the parameters of the `onERC721Received` function are unused.
File: [src/ClearingHouse.sol#L188](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L188)
- The `caller` and `offerer` parameters of the `isValidOrder` functions are unused
File: [CollateralToken.sol#L157](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L157)
- The `caller`, `priorOrderhashes` and `criteriaResolvers` parameters of the `isValidOrderIncludingExtraData` are unused
File: [CollateralToken.sol#L171](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L171)

- `tokenContract` local variable of the `releaseToAddress` function is unused
file: [CollateralToken.sol#L342](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L342)

- unused local variable `end` in the `_paymentAH` function
File: [LienToken.sol#L632](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L632)

- unused local variable `s` for the `_getRemainingInterest` function.
File: [LienToken.sol#L775](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L775)

- unused local variable `assets` in `deposit` function of PublicVault.sol
File: [PublicVault.sol#L262](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L262)

- unused function parameter  `param` in the function `_beforeCommitToLien` of PublicVault.sol
File: [PublicVault.sol#L413](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L413)
- unused function parameter `shares` in the function `afterDeposit` of the PublicVault.sol file
File: [PublicVault.sol#L413](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L575)

- Unused local variable `stack` and `currentOfferPrice` 
[ClearingHouse.sol#L139](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L139)

#### Recommended Mitigation Steps
Consider  updating the code not to allow unused variables

## [L-06] Shadowing local variables found in PublicVault.sol

The `onwer` variables in `redeem`, `withdraw`, `_redeemFutureEpoch` and `redeemFutureEpoch` functions SHADOW the `AstriaVaultBase.owner` variable.

#### Tool used
- Solhint

#### Recommended Mitigation Steps
Consider renaming the owner variable in those functions.


## [L-07] `currentWithdrawProxy` local variable in the function `transferWithdrawReserve` of PublicVault.sol file is declared twice.

Lines: 366 and 397
- [PublicVault.sol#L366](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366)
- [PublicVault.sol#L397](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L397)



