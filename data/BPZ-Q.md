## QA Report
 
 Total instances : 33
 
 ## External & Public Functions should use mixedCase withot underscore
 
 Total instances : 29
 
Affected Source Code

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L26

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L30

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L38

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L42

https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#377

https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L381

https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L105

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L43

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L47

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L51

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L28

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L32

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L50

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L54

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L58

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L62

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L229

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L234

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L239

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L244

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L252

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L259

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L345

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L352

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L141

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol#L25

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol#L27

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol#L29

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol#L31

 For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#function-names)
    2. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)


## Remove assembly for future updates
Total instances : 1

its better not to use assembly because it reduce readability & future updatability of the code even though assembly reduce gas.

Recommendation 

Consider removeing all assembly code and re-implement them in Solidity to make the code significantly more clean.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L30


## Revert without having any error message

Total instances : 2

https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65

https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L71

## Constants should be named with all capital letters with underscores separating words.(For Internal or private constants it should be started with underscore prefix)

Total instances : 1

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AuthInitializable.sol#L26


For more read...
    1. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)

