https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L19-L52

The deposit() and mint() functions look a lot alike, but there are some slight differences. This could cause some confusion and mistakes, and above all use up more gas.

Combining them would save some gas, save some work and make the code run more smoothly