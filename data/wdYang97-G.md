Topic: Optimize Memory Caching
#1:  Variables should not be declared in a for . loop
Link: https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol
- Line 152 
- Line 472
- Line 521
- Line 670, 671
    uint256 potentialDebt = 0;
    for (uint256 i = params.encumber.stack.length; i > 0; ) {
      uint256 j = i - 1;
   ....
    }
When you include uint256 j = i -1 in the loop, it will make your function heavier and use more gas.
- Line 304
You should declare the variable address `withdrawProxyIfNearBoundary = address(0)` before entering the for . loop.Similar to owed, payee 
 variables.

Link: https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol
- Line 510
Example:
    function loop1() public {
        for (uint256 i = 500; i > 0; i--) {
          uint256 j = i - 1;
      }
    }
Gas : 110778 ! Transaction cost: 110778
-------------------------------------
    function loop2() public {
        uint256 j = 0;
        for (uint256 i = 500; i > 0; i--) {
           j = i - 1;
      }
Gas : 108239 ! Transaction cost: 108239

#2: Avoid changing storage data
- Link: https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol
- Line 199
You can create a temporary storage variable and after the loop ends you set it back to local. This will help you reduce gas significantly.
Example:
    uint256 number = 1;

    function getNumber() public {
        for(uint8 i = 0; i < 100; i++) {
            number += 1;
        }
    }
Gas : 210016 ! Transaction cost: 208668
--------------------------
    function getNumberOptimal() public {
        uint256 _number = number;

        for(uint8 i = 0; i < 100; i++) {
            _number += 1;
        }

        number = _number;
Gas : 47933 ! Transaction cost: 47933

#3: Reasonable use of memory and calldata to reduce gas costs
Link: https://github.com/code-423n4/2023-01-astaria/blob/main/src/utils/Initializable.sol
- Line 88, 104, 105, 143, 145, 162, 179, 180, 193, 213, 214, 230, 231
When you do not perform data manipulation, you should use `call data` to reduce gas costs
