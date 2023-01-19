## GAS-1: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas

### Affected file

* CollateralToken.sol (Line: 124, 520)
* LienToken.sol (Line: 229, 271, 406, 448, 563, 698)

## GAS-2: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* ClearingHouse.sol (Line: 134)
* LienToken.sol (Line: 112, 156, 464, 475, 805)
* PublicVault.sol (Line: 375, 586, 706)

## GAS-3: Empty blocks should be removed or emit something

### Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Affected file

* BeaconProxy.sol (Line: 87)
* ClearingHouse.sol (Line: 104, 180)

## GAS-4: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

### Affected file

* PublicVault.sol (Line: 271, 413, 439, 575)

## GAS-5: Internal functions only called once can be inlined to save gas

### Description

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Affected file

* AstariaRouter.sol (Line: 407, 761, 788)
* BeaconProxy.sol (Line: 20, 29, 87)
* ClearingHouse.sol (Line: 114)
* CollateralToken.sol (Line: 425, 502, 541)
* LienToken.sol (Line: 122, 233, 292, 459, 512, 623, 663, 715, 775, 855, 911)
* PublicVault.sol (Line: 556, 597, 713)

## GAS-6: Length calculated at each iteration in For-loop

### Description

Caching the array length outside a loop saves reading it on each iteration, as long as the array’s length is not changed during the loop.

### Affected file

* AstariaRouter.sol (Line: 265, 364, 506)
* ClearingHouse.sol (Line: 96)
* CollateralToken.sol (Line: 198)
* LienToken.sol (Line: 304, 520, 669, 734)

## GAS-7: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* AstariaRouter.sol (Line: 135, 151, 167, 183, 192, 429, 721, 771)
* CollateralToken.sol (Line: 162, 177)
* LienToken.sol (Line: 111, 427, 581, 604, 711)
* PublicVault.sol (Line: 143, 718)
* WithdrawProxy.sol (Line: 137, 173, 194)

## GAS-8: Variable initialized with their default value

### Description

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it’s default value costs unnecesary gas.

### Affected file

* LienToken.sol (Line: 152, 636)
* WithdrawProxy.sol (Line: 254)

## GAS-9: Public functions not called by the contract should be declared external instead

### Description

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

### Affected file

* AstariaRouter.sol (Line: 203, 224, 229, 234, 239, 244, 402, 490, 544, 621, 684)
* ClearingHouse.sol (Line: 43, 51, 180)
* CollateralToken.sol (Line: 80, 105, 370, 412)
* LienToken.sol (Line: 59, 348, 377, 381, 550, 706, 727)
* PublicVault.sol (Line: 76, 96, 117, 126, 192, 196, 200, 204, 208, 212, 345, 552, 562, 615, 640, 669, 684, 722)
* Vault.sol (Line: 28, 49, 59)
* WithdrawProxy.sol (Line: 67, 90, 103, 143, 215, 220, 225, 240, 289, 302, 306)

## GAS-10: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* AstariaRouter.sol (Line: 196)
* CollateralToken.sol (Line: 253, 265, 602)
* LienToken.sol (Line: 265)
* PublicVault.sol (Line: 679)
* WithdrawProxy.sol (Line: 152, 230)

## GAS-11: Splitting require() statements that use && saves gas

### Description

Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

### Affected file

* PublicVault.sol (Line: 672, 687)
* Vault.sol (Line: 65)

## GAS-12: Use custom errors rather than revert()/require() strings to save gas

### Description

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Affected file

* AstariaRouter.sol (Line: 198, 333, 341, 347, 354, 361, 384, 398, 440, 445, 459, 463, 473, 555, 560, 626, 694, 775, 778)
* ClearingHouse.sol (Line: 72, 134, 199, 216, 223)
* CollateralToken.sol (Line: 121, 127, 134, 248, 257, 260, 266, 281, 287, 312, 319, 327, 339, 510, 513, 533, 535, 564, 585, 598, 604)
* LienToken.sol (Line: 91, 113, 117, 135, 141, 149, 157, 169, 214, 221, 269, 272, 355, 367, 370, 429, 435, 440, 444, 465, 476, 483, 493, 504, 546, 565, 801, 806, 860, 895)
* PublicVault.sol (Line: 168, 170, 241, 259, 278, 283, 304, 508, 587, 672, 680, 687)
* Vault.sol (Line: 65, 71, 77, 82, 91)
* WithdrawProxy.sol (Line: 138, 149, 158, 231, 244, 248, 251)

## GAS-13: Use elementary types or a user-defined type instead of a struct that has only one member

### Affected file

* ClearingHouse.sol (Line: 36)

## GAS-14: Using private rather than public for constants saves gas

### Description

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Affected file

* LienToken.sol (Line: 53)
