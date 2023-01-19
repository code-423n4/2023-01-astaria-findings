## [GAS-01] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
if a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

File: [src/AstariaRouter.sol#L263](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L263)
```
263: function fileBatch(File[] calldata files) external requiresAuth {

273:   function file(File calldata incoming) public requiresAuth {
```

## [GAS-02] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS
File: [src/AstariaRouter.sol#L192-L193](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L192-L193)

```
function redeemFutureEpoch(
    IPublicVault vault,
    uint256 shares,
    address receiver,
    uint64 epoch
  ) public virtual validVault(address(vault)) returns (uint256 assets) {
    return vault.redeemFutureEpoch(shares, receiver, msg.sender, epoch);
  }
```