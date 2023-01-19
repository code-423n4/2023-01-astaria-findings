## Title
Missing sanity check for amount input at the function drain()
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L289

## Tools Used
Manual

## Recommended Mitigation Steps
Consider Add require(amount > 0, "amount connot be 0 or negative")

## Title
repepetive require statment that could be implement in a modifier

## Proof of Concept
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L69
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L72
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L198
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L215
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216
## Tools Used
Manual
## Recommended Mitigation Steps
Consider moving IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0)); locally from each function in the contract
to the global scope of the contract. Additionally creating a modifier with require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN())), and implement it when needed.
```
 modifier OnlyXYZ {
  require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  _;
```