
# Gas Optimizations Report

This report focuses on Astaria contest, in context of various improvements that can be made in terms of gas cost.

Some of the opportunities identified for improving gas efficiency throughout the codebase of Astaria NFT lending protocol are categorised into 5 main areas; with further multiple instances in each of the category.


# Summary

[G-01] Modifier can be used instead of require(01 Instance)
[G-02] Immutable has more gas efficiency than constant (17 Instances)
[G-03] Wastage of deployed gas when return is not present for returns function (05 Instances)
[G-04] 0perator assignment is more gas efficient than compound assignment (28 Instances)
[G-05] Uint/Int lower than 32 bytes consumes more gas (109 Instances)
 

# [G-01] Modifier can be used instead of require(01 Instance)


Modifier onlyVault() can be used instead of require() as it’s giving out same message which can reduce gas consumption by the contract.


Link to the Code:

1.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L138


# [G-02] Immutable has more gas efficiency than constant (17 Instances)

Using immutable instead of constant, save more gas due to avoiding storage access for state variables.

Variable values are set through constructor when using immutable, which also eliminates the need of assigning initial values to state variable making them more efficient in terms of gas cost.

Link to the Code:

2.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L40
3.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L49
4.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73
5.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L53
6.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L62
7.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L66
8.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L68
9.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L50
10.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L53
11.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L13
12.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L15
13.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L15
14.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L44
15.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L47
16.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L51
17.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L57


# [G-03] Wastage of deployed gas when return is not present for returns function (05 Instances)

Wastage of gas during deployment; when return is absent for named variable when function returns.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L121
2.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L130
3.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L154
4.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L643
5.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L588

# [G-04] 0perator assignment is more gas efficient than compound assignment (28 Instances)

Compound assignment operators (+= / -=) are more expensive in terms of gas consumption and needs to be avoided.

Operator assignment (a = a + b / a - b) is preferable in terms of increasing gas optimization.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L277
2.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L174
3.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L179
4.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L380
5.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L405
6.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L565
7.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L583
8.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606
9.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L624
10.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L512
11.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L160
12.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L210
13.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L480
14.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L524
15.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L525
16.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L679
17.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L720
18.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L735
19.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L830
20.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L54
21.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L59
22.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L93
23.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L98
24.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L174
25.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L179
26.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L187
27.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L192
28.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L404


# [G-05] Uint/Int lower than 32 bytes consumes more gas (109 Instances)

Contract gas usage increases as EVM standard operation are of 32 bytes. If any element is smaller than 32 bytes (i.e.; 256 bits) it will cause EVM to consume more gas for reducing the size to given output like uint8.

Throughout the codebase many returns functions, function arguments, variables, event and struct have values that are below 32 bytes in type of uint/int. These all elements below 32 bytes create a significant gas consumption overhead for extra work.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L51
2.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L53
3.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54
4.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L55
5.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L77
6.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L571
7.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L71
8.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L103
9.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L142
10.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L192
11.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L196
12.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L216
13.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L224
14.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L362
15.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L440
16.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L449
17.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L453
18.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L459
19.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L523
20.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L529
21.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L534
22.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L538
23.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L546
24.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L548
25.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L552
26.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L556
27.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L622
28.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L654
29.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L671
30.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L686
31.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L191
32.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L329
33.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L368
34.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L395
35.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L442
36.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L621
37.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L686
38.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L722
39.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L309
40.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L342
41.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L412
42.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L418
43.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L420
44.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L742
45.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L750
46.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L764
47.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L793
48.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L803
49.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L855
50.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L879
51.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L30
52.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L42
53.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L32
54.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L269
55.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L315
56.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L367
57.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IBeacon.sol#L22
58.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IRouterBase.sol#L21
59.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC20Metadata.sol#L18
60.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L24
61.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L18
62.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L29
63.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L40
64.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L48
65.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L62
66.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L77
67.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L44
68.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L48
69.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L64
70.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L79
71.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L88
72.	https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L110
73.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L20
74.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L25
75.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L48
76.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L70
77.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L88
78.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L94
79.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L168
80.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L169
81.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L170
82.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L10
83.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L21
84.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L58
85.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L73
86.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L98
87.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L127
88.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L133
89.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L154
90.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L56
91.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L101
92.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L112
93.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L234
94.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L278
95.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L288
96.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L295
97.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L312
98.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L36
99.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L60
100.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L69
101.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L88
102.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L142
103.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L153
104.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L159
105.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L222
106.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L229
107.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L235
108.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L294
109.	https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L306
