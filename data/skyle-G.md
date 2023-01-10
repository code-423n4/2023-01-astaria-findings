[G-1] Cache array length in for loops can save gas
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Instances include:
src/AstariaRouter.sol line 256
src/AstariaRouter.sol line 364
src/AstariaRouter.sol line 506
src/ClearingHouse.sol line 96