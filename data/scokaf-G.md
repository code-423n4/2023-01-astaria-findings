# Number 1: Poorly Ordered Structs
Vulnerability details

## Impact

In some contracts, structs are poorly ordered which leads to more gas consumption.

## Proof of Concept

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L52 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IVaultImplementation.sol#L36 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IVaultImplementation.sol#L43 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L56 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L101 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L60 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L69 



## Tools Used

Remix IDE

## Recommended Mitigation Steps

Pack structs tightly by using the right order when adding struct properties e.g from this 👇

struct WPStorage {
    uint88 withdrawRatio;
    uint88 expected;
    uint40 finalAuctionEnd;
    uint256 withdrawReserveReceived;
  }
 
To this  👇


struct WPStorage {
    uint256 withdrawRatio;
    uint88 expected;
    uint88 finalAuctionEnd;
    uint40 withdrawReserveReceived;
  }
