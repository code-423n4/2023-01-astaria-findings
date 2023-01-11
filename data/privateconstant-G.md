# Gas Optimizations

## [G-01] REORDER STRUCTURE LAYOUR TO SAVE SLOT STORAGES

The layout of the following structs could be optimized by rearranging the positions of certain values in order to reduce the number of storage slots required.

This would lead to a more efficient use of storage resources and potentially lower gas costs for interactions involving these structs.

_instance (1)_:
```diff
file: src/interfaces/IAstariaRouter.sol, line 101 - 105

    struct StrategyDetailsParam {
        uint8 version;
+       address vault;
        uint256 deadline;
-       address vault;
    }
```

[Link to code](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L101)