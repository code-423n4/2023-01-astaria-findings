# 1) NatSpec
Important functions should have a @notice comment to describe what they perform.
Instances:
Almost no contract has been written with NatSpec comments.

Recommended mitigation steps:
Consider adding NatSpec comments


# 2) abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Unless there is a compelling reason, abi.encode should be preferred. 
If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. 
Instance:
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L123

Recommended mitigation steps:
Consider changing abi.encodePacked() to abi.encode()
