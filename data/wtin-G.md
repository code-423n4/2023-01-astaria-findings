----

### UNUSED FUNCTION PARAMETER ON NON-VIEW FUNCTIONS

Unused function parameters are declared inside non-view functions, thus affecting the gas cost.

**Proof of concept (without optimizations):**

```solidity
pragma solidity 0.8.16;

contract TesterA {
  function testParam(uint a, address b) public returns (uint) { return a / 2; }
}

contract TesterB {
  function testParam(uint a) public returns (uint) { return a / 2; }
}
```

Gas saving executing: 79 per entry

```
TesterA.testParam: 396
TesterB.testParam: 317
```

**Affected source code:**

 - src/ClearingHouse.sol:115:5
 - src/ClearingHouse.sol:116:5


----

### PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE CHANGED TO EXTERNAL

Functions declared with public visibility do require more gas than functions declared with external visibility. Functions declared as public were meant to be used by outsider entities and also by the contract itself. Thus if the contract does not call its public functions then there is no reason to declare them public, except in case of extensibility of the contract in future.

**Proof of concept (without optimizations):**

```solidity
pragma solidity 0.8.16;

contract Example {
  function testParam1(uint a) external returns (uint) { return a / 2; }
  function testParam2(uint a) public returns (uint) { return a / 2; }
}

contract Tester {

  Example addr;

  constructor() {
    addr = new Example(); 
  }

  function testParam1(uint a) external returns (uint) {
    uint ret = addr.testParam1(a);
    return ret;
  }

  function testParam2(uint a) external returns (uint) {
    uint ret = addr.testParam2(a);
    return ret;
  }

}
```

Gas saving executing: 55 per entry

```
Tester.testParam1: 5551
Tester.testParam2: 5606
```

**Affected source code:**

 - src/WithdrawProxy.sol:240
 - src/WithdrawProxy.sol:302
 - src/PublicVault.sol:275
 - src/PublicVault.sol:359
 - src/PublicVault.sol:461
 - src/PublicVault.sol:534
 - src/PublicVault.sol:562
 - src/PublicVault.sol:669
 - src/PublicVault.sol:684
 - src/AstariaRouter.sol:273
 - src/CollateralToken.sol:206
 - src/CollateralToken.sol:524
