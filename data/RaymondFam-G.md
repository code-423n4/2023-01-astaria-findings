## Unneeded functions
In ClearingHouse.sol, the function parameters of `balanceOf()` and `balanceOfBatch()` aren't utilized in the respective function logic to retrieve any state variables. The code blocks entailed are essentially associated with a pure function logic. Since these external functions are neither called internally nor inheritable in the child contracts, consider removing them to save gas on contract deployment. If need be, `type(uint256).max` can always be coded inline by the calling contracts/functions instead of being externally interacted with.

[File: ClearingHouse.sol#L90-L112](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-L112)

```solidity
  function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
    external
    view
    returns (uint256[] memory output)
  {
    output = new uint256[](accounts.length);
    for (uint256 i; i < accounts.length; ) {
      output[i] = type(uint256).max;
      unchecked {
        ++i;
      }
    }
  }

  function setApprovalForAll(address operator, bool approved) external {}

  function isApprovedForAll(address account, address operator)
    external
    view
    returns (bool)
  {
    return true;
  }
```
## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

```solidity
    require(s.allowList[msg.sender] && receiver == owner());
```
