### [LOW-1] initialize() function can be called by anybody
initialize() function can be called anybody when the contract is not initialized.

More importantly, if someone else runs this function, they will have full authority because of the __initAuth() function. Also, there is no 0 address check in the address arguments of the initialize() function, which must be defined.

Here is a definition of initialize() function.
consider validation check
``` solidity
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
}
```

``` solidity
File: c:\Users\pc\Desktop\code\astria\2023-01-astaria\src\LienToken.sol
59:   function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
60:     public
61:     initializer
62:   {
63:     __initAuth(msg.sender, address(_AUTHORITY));
64:     __initERC721("Astaria Lien Token", "ALT");
65:     LienStorage storage s = _loadLienStorageSlot();
66:     s.TRANSFER_PROXY = _TRANSFER_PROXY;
67:     s.maxLiens = uint8(5);
68:   } 
```

``` solidity
File: c:\Users\pc\Desktop\code\astria\2023-01-astaria\src\CollateralToken.sol
80:   function initialize(
81:     Authority AUTHORITY_,
82:     ITransferProxy TRANSFER_PROXY_,
83:     ILienToken LIEN_TOKEN_,
84:     ConsiderationInterface SEAPORT_
85:   ) public initializer {
86:     __initAuth(msg.sender, address(AUTHORITY_));
87:     __initERC721("Astaria Collateral Token", "ACT");
88:     CollateralStorage storage s = _loadCollateralSlot();
89:     s.TRANSFER_PROXY = TRANSFER_PROXY_;
90:     s.LIEN_TOKEN = LIEN_TOKEN_;
91:     s.SEAPORT = SEAPORT_;
92:     (, , address conduitController) = s.SEAPORT.information();
93:     bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
94:     s.CONDUIT_KEY = CONDUIT_KEY;
95:     s.CONDUIT_CONTROLLER = ConduitControllerInterface(conduitController);
96: 
97:     s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(CONDUIT_KEY, address(this));
98:     s.CONDUIT_CONTROLLER.updateChannel(
99:       address(s.CONDUIT),
100:       address(SEAPORT_),
101:       true
102:     );
103:   }
```

``` solidity

File: c:\Users\pc\Desktop\code\astria\2023-01-astaria\src\AstariaRouter.sol
83:   function initialize(
84:     Authority _AUTHORITY,
85:     ICollateralToken _COLLATERAL_TOKEN,
86:     ILienToken _LIEN_TOKEN,
87:     ITransferProxy _TRANSFER_PROXY,
88:     address _VAULT_IMPL,
89:     address _SOLO_IMPL,
90:     address _WITHDRAW_IMPL,
91:     address _BEACON_PROXY_IMPL,
92:     address _CLEARING_HOUSE_IMPL
93:   ) external initializer {
94:     __initAuth(msg.sender, address(_AUTHORITY));
95:     RouterStorage storage s = _loadRouterSlot();
96: 
97:     s.COLLATERAL_TOKEN = _COLLATERAL_TOKEN;
98:     s.LIEN_TOKEN = _LIEN_TOKEN;
99:     s.TRANSFER_PROXY = _TRANSFER_PROXY;
100:     s.implementations[uint8(ImplementationType.PrivateVault)] = _SOLO_IMPL;
101:     s.implementations[uint8(ImplementationType.PublicVault)] = _VAULT_IMPL;
102:     s.implementations[uint8(ImplementationType.WithdrawProxy)] = _WITHDRAW_IMPL;
103:     s.implementations[
104:       uint8(ImplementationType.ClearingHouse)
105:     ] = _CLEARING_HOUSE_IMPL;
106:     s.BEACON_PROXY_IMPLEMENTATION = _BEACON_PROXY_IMPL;
107:     s.auctionWindow = uint32(2 days);
108:     s.auctionWindowBuffer = uint32(1 days);
109: 
110:     s.liquidationFeeNumerator = uint32(130);
111:     s.liquidationFeeDenominator = uint32(1000);
112:     s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
113:     s.minEpochLength = uint32(7 days);
114:     s.maxEpochLength = uint32(45 days);
115:     s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
116:     //63419583966; // 200% apy / second
117:     s.buyoutFeeNumerator = uint32(100);
118:     s.buyoutFeeDenominator = uint32(1000);
119:     s.minDurationIncrease = uint32(5 days);
120:     s.guardian = msg.sender;
121:   }

```

### [LOW-2]  Don't uncheck balance updates
``` solidity
src\PublicVault.sol
178:     unchecked {
179:       es.balanceOf[address(this)] += shares;
180:     }

564:     unchecked {
565:       s.slope += computedSlope.safeCastTo48();
566:     }

582:     unchecked {
583:       s.yIntercept += assets.safeCastTo88();
584:     }

621:     unchecked {
622:       uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
623:       _setSlope(s, newSlope);
624:       s.yIntercept += params.increaseYIntercept.safeCastTo88();
625:       s.last = block.timestamp.safeCastTo40();
626:     }

379:     unchecked {
380:         s.withdrawReserve -= withdrawBalance.safeCastTo88();
381:     }

404:     unchecked {
405:         s.withdrawReserve -= drainBalance.safeCastTo88();
406:     }

```

### [L-3] Functions return bool but cannot return true
Some functions return bool but only return true unless reverting. This means the return value is meaningless. This may cause problems if they are elsewhere expected to return true rather than revert.

``` solidity
src\Vault.sol
56:     return false;
```

### [NC-1] consider providing protection for the liquidator liquidatorNFTClaim(),securityHooks(),getClearingHouse()
function to claim auctioned assets and issue funds without sufficient verification. This can cause security and financial problems to the contract and insufficient parties can take unwanted funds.
EG added
This will cancel the transaction and return all funds sent to the contract and give the message "Invalid liquidator" if the function call does not match the address of the liquidator entrusted.
``` solidity
if (msg.sender != liquidator) {
   revert("Invalid liquidator");
}
```

``` solidity
src\CollateralToken.sol
109: function liquidatorNFTClaim(OrderParameters memory params) external {
110:     CollateralStorage storage s = _loadCollateralSlot();
111: 
112:     uint256 collateralId = params.offer[0].token.computeId(
113:       params.offer[0].identifierOrCriteria
114:     );
115:     address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
116:     if (
117:       s.collateralIdToAuction[collateralId] == bytes32(0) ||
118:       liquidator == address(0)
119:     ) {
120:       //revert no auction
121:       revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
122:     }
123:     if (
124:       s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
125:     ) {
126:       //revert auction params dont match
127:       revert InvalidCollateralState(
128:         InvalidCollateralStates.INVALID_AUCTION_PARAMS
129:       );
130:     }
131: 
132:     if (block.timestamp < params.endTime) {
133:       //auction hasn't ended yet
134:       revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
135:     }
136: 
137:     Asset memory underlying = s.idToUnderlying[collateralId];
138:     address tokenContract = underlying.tokenContract;
139:     uint256 tokenId = underlying.tokenId;
140:     ClearingHouse CH = ClearingHouse(payable(s.clearingHouse[collateralId]));
141:     CH.settleLiquidatorNFTClaim();
142:     _releaseToAddress(s, underlying, collateralId, liquidator);
143:   }
``` 

### [NC-2] Unsafe use of assembly in _loadRouterSlot() function
can cause security problems because the assembly code can be changed by an unauthorized user to take over the contract or cause other damage. User assembly code must ensure that it is secure and cannot be modified by unauthorized users. It is a good idea to replace the use of assembly with a more secure function or to add a security mechanism to prevent unauthorized changes.
``` solidity
src\AstariaRouter.sol
217:   function _loadRouterSlot() internal pure returns (RouterStorage storage rs) {
218:     uint256 slot = ROUTER_SLOT;
219:     assembly {
220:       rs.slot := slot
221:     }
222:   }
```