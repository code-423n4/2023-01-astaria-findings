## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Array lengths not checked | 1 | 
| [L&#x2011;02] | Signatures vulnerable to malleability attacks | 2 | 
| [L&#x2011;03] | `tokenURI()` does not follow EIP-721 | 1 | 
| [L&#x2011;04] | `_safeMint()` should be used rather than `_mint()` wherever possible | 2 | 

Total: 6 instances over 4 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Invalid/extraneous/optional function definitions in interface | 1 | 
| [N&#x2011;02] | Missing `initializer` modifier | 1 | 
| [N&#x2011;03] | Adding a `return` statement when the function defines a named return variable, is redundant | 1 | 
| [N&#x2011;04] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 8 | 
| [N&#x2011;05] | `constant`s should be defined rather than using magic numbers | 35 | 
| [N&#x2011;06] | Events that mark critical parameter changes should contain both the old and the new value | 3 | 
| [N&#x2011;07] | `pragma experimental ABIEncoderV2` is deprecated | 2 | 
| [N&#x2011;08] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 4 | 
| [N&#x2011;09] | Inconsistent spacing in comments | 26 | 
| [N&#x2011;10] | Lines are too long | 11 | 
| [N&#x2011;11] | Inconsistent method of specifying a floating pragma | 5 | 
| [N&#x2011;12] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 37 | 
| [N&#x2011;13] | Using `>`/`>=` without specifying an upper bound is unsafe | 5 | 
| [N&#x2011;14] | Typos | 6 | 
| [N&#x2011;15] | Misplaced SPDX identifier | 1 | 
| [N&#x2011;16] | File is missing NatSpec | 16 | 
| [N&#x2011;17] | NatSpec is incomplete | 57 | 
| [N&#x2011;18] | Not using the named return variables anywhere in the function is confusing | 25 | 
| [N&#x2011;19] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 6 | 
| [N&#x2011;20] | Consider using `delete` rather than assigning zero to clear values | 5 | 
| [N&#x2011;21] | Avoid the use of sensitive terms | 1 | 
| [N&#x2011;22] | Large assembly blocks should have extensive comments | 1 | 
| [N&#x2011;23] | Contracts should have full test coverage | 1 | 
| [N&#x2011;24] | Large or complicated code bases should implement fuzzing tests | 1 | 
| [N&#x2011;25] | Function ordering does not follow the Solidity style guide | 69 | 
| [N&#x2011;26] | Contract does not follow the Solidity style guide's suggested layout ordering | 21 | 

Total: 349 instances over 26 issues





## Low Risk Issues

### [L&#x2011;01]  Array lengths not checked
If the length of the arrays are not required to be of the same length, user operations may not be fully executed

*There is 1 instance of this issue:*

```solidity
File: src/ClearingHouse.sol

90      function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
91        external
92        view
93:       returns (uint256[] memory output)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L90-L93

### [L&#x2011;02]  Signatures vulnerable to malleability attacks
`ecrecover()` accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin's `ECDSA` library to perform the extra checks necessary in order to prevent this attack.

*There are 2 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

121         address recoveredAddress = ecrecover(
122           keccak256(
123             abi.encodePacked(
124               "\x19\x01",
125               DOMAIN_SEPARATOR(),
126               keccak256(
127                 abi.encode(
128                   PERMIT_TYPEHASH,
129                   owner,
130                   spender,
131                   value,
132                   _loadERC20Slot().nonces[owner]++,
133                   deadline
134                 )
135               )
136             )
137           ),
138           v,
139           r,
140           s
141:        );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L121-L141

```solidity
File: src/VaultImplementation.sol

246       address recovered = ecrecover(
247         keccak256(
248           _encodeStrategyData(
249             s,
250             params.lienRequest.strategy,
251             params.lienRequest.merkle.root
252           )
253         ),
254         params.lienRequest.v,
255         params.lienRequest.r,
256         params.lienRequest.s
257:      );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L246-L257

### [L&#x2011;03]  `tokenURI()` does not follow EIP-721
The [EIP](https://eips.ethereum.org/EIPS/eip-721) states that `tokenURI()` "Throws if `_tokenId` is not a valid NFT", which the code below does not do. If the NFT has not yet been minted, `tokenURI()` should revert

*There is 1 instance of this issue:*

```solidity
File: src/CollateralToken.sol

401     function tokenURI(uint256 collateralId)
402       public
403       view
404       virtual
405       override(ERC721, IERC721)
406       returns (string memory)
407     {
408       (address underlyingAsset, uint256 assetId) = getUnderlying(collateralId);
409       return ERC721(underlyingAsset).tokenURI(assetId);
410:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L401-L410

### [L&#x2011;04]  `_safeMint()` should be used rather than `_mint()` wherever possible
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function

*There are 2 instances of this issue:*

```solidity
File: src/CollateralToken.sol

588:        _mint(from_, collateralId);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L588

```solidity
File: src/LienToken.sol

455:      _mint(params.receiver, newLienId);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L455

## Non-critical Issues

### [N&#x2011;01]  Invalid/extraneous/optional function definitions in interface

*There is 1 instance of this issue:*

```solidity
File: src/interfaces/IERC721.sol

/// @audit tokenURI(uint256) isn't defined with those arguments in the standard IERC721 definition
20:     function tokenURI(uint256 id) external view returns (string memory);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC721.sol#L20

### [N&#x2011;02]  Missing `initializer` modifier
The contract extends `Initializable`/`ReentrancyGuardUpgradeable` but does not use the `initializer` modifier anywhere

*There is 1 instance of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

10:   abstract contract ERC721 is Initializable, IERC721 {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L10

### [N&#x2011;03]  Adding a `return` statement when the function defines a named return variable, is redundant

*There is 1 instance of this issue:*

```solidity
File: src/AstariaRouter.sol

758:      return vaultAddr;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L758

### [N&#x2011;04]  `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

*There are 8 instances of this issue:*

```solidity
File: src/ClearingHouse.sol

189:      address operator_,

190:      address from_,

191:      uint256 tokenId_,

192:      bytes calldata data_

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L189

```solidity
File: src/PublicVault.sol

413:    function _beforeCommitToLien(IAstariaRouter.Commitment calldata params)

575:    function afterDeposit(uint256 assets, uint256 shares)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L413

```solidity
File: src/WithdrawProxy.sol

143:    function deposit(uint256 assets, address receiver)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L143

### [N&#x2011;05]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 35 instances of this issue:*

```solidity
File: lib/gpl/src/ERC4626-Cloned.sol

/// @audit 10e18
132:      return supply == 0 ? 10e18 : shares.mulDivUp(totalAssets(), supply);

/// @audit 10e18
140:      return supply == 0 ? 10e18 : assets.mulDivUp(supply, totalAssets());

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626-Cloned.sol#L132

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit 0x01ffc9a7
190:        interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165

/// @audit 0x80ac58cd
191:        interfaceId == 0x80ac58cd || // ERC165 Interface ID for ERC721

/// @audit 0x5b5e139f
192:        interfaceId == 0x5b5e139f; // ERC165 Interface ID for ERC721Metadata

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L190

```solidity
File: src/AstariaRouter.sol

/// @audit 1000
111:      s.liquidationFeeDenominator = uint32(1000);

/// @audit 7
113:      s.minEpochLength = uint32(7 days);

/// @audit 100
117:      s.buyoutFeeNumerator = uint32(100);

/// @audit 1000
118:      s.buyoutFeeDenominator = uint32(1000);

/// @audit 5
119:      s.minDurationIncrease = uint32(5 days);

/// @audit 0
418:          mstore(0, OUTOFBOUND_ERROR_SELECTOR)

/// @audit 0
419:          revert(0, ONE_WORD)

/// @audit 1_000
645:          endingPrice: 1_000 wei

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L111

```solidity
File: src/BeaconProxy.sol

/// @audit 0
/// @audit 0
34:         calldatacopy(0, 0, calldatasize())

/// @audit 0
/// @audit 0
/// @audit 0
38:         let result := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)

/// @audit 0
/// @audit 0
41:         returndatacopy(0, 0, returndatasize())

/// @audit 0
46:           revert(0, returndatasize())

/// @audit 0
45:         case 0 {

/// @audit 0
49:           return(0, returndatasize())

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/BeaconProxy.sol#L34

```solidity
File: src/CollateralToken.sol

/// @audit 0xffffffff
167:          : bytes4(0xffffffff);

/// @audit 0xffffffff
182:          : bytes4(0xffffffff);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L167

```solidity
File: src/LienToken.sol

/// @audit 5
67:       s.maxLiens = uint8(5);

/// @audit 1000
342:      auctionData.endAmount = uint88(1000 wei);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L67

```solidity
File: src/PublicVault.sol

/// @audit 100
104:        return 100 gwei;

/// @audit 1e18
315:          .mulDivDown(1e18, totalSupply())

/// @audit 1e18
333:            totalAssets().mulDivDown(s.liquidationWithdrawRatio, 1e18)

/// @audit 10000
604:        uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L104

```solidity
File: src/VaultImplementation.sol

/// @audit 0x19
/// @audit 0x01
187:        abi.encodePacked(bytes1(0x19), bytes1(0x01), domainSeparator(), hash);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L187

```solidity
File: src/WithdrawProxy.sol

/// @audit 1e18
260:          (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)

/// @audit 1e18
264:          (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L260

### [N&#x2011;06]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 3 instances of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit setApprovalForAll()
103:      emit ApprovalForAll(msg.sender, operator, approved);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L103

```solidity
File: src/VaultImplementation.sol

/// @audit setDelegate()
214:      emit DelegateUpdated(delegate_);

/// @audit setDelegate()
215:      emit AllowListUpdated(delegate_, true);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L214

### [N&#x2011;07]  `pragma experimental ABIEncoderV2` is deprecated
Use `pragma abicoder v2` [instead](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics)

*There are 2 instances of this issue:*

```solidity
File: src/CollateralToken.sol

16:   pragma experimental ABIEncoderV2;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L16

```solidity
File: src/LienToken.sol

16:   pragma experimental ABIEncoderV2;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L16

### [N&#x2011;08]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it **doesn't save any gas** because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 4 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

15      bytes32 private constant PERMIT_TYPEHASH =
16        keccak256(
17          "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
18:       );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L15-L18

```solidity
File: src/VaultImplementation.sol

44      bytes32 public constant STRATEGY_TYPEHASH =
45:       keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");

47      bytes32 constant EIP_DOMAIN =
48        keccak256(
49          "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
50:       );

51:     bytes32 constant VERSION = keccak256("0");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L44-L45

### [N&#x2011;09]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 26 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

65:     // cast --to-bytes32 $(cast sig "OutOfBoundError()")

116:      //63419583966; // 200% apy / second

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L65

```solidity
File: src/ClearingHouse.sol

71:       //only execute from the conduit

117:      uint256 encodedMetaData, //retrieve token address from the encoded data

130:        roundUp: true //we are a consideration we round up

174:      bytes calldata data //empty from seaport

176:      //data is empty and useless

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L71

```solidity
File: src/CollateralToken.sol

120:        //revert no auction

126:        //revert auction params dont match

133:        //auction hasn't ended yet

291:      //look to see if we have a security handler for this asset

305:      //trigger the flash action on the receiver

507:      //get total Debt and ensure its being sold for more than that

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L120

```solidity
File: src/interfaces/IAstariaRouter.sol

74:       uint32 minInterestBPS; // was uint64

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L74

```solidity
File: src/LienToken.sol

155:        // should not be able to purchase lien if any lien in the stack is expired (and will be liquidated)

315:          // update the public vault state and get the liquidation accountant back if any

804:      // Blocking off payments for a lien that has exceeded the lien.end to prevent repayment unless the msg.sender() is the AuctionHouse

831:        //      // slope does not need to be updated if paying off the rest, since we neutralize slope in beforePayment()

838:          // since the openLiens count is only positive when there are liens that haven't been paid off

839:          // that should be liquidated, this lien should not be counted anymore

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L155

```solidity
File: src/PublicVault.sol

173:      //this will underflow if not enough balance

508:      require(msg.sender == owner()); //owner is "strategist"

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L173

```solidity
File: src/VaultImplementation.sol

123:      address, // operator_

124:      address, // from_

125:      uint256, // tokenId_

126:      bytes calldata // data_

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L123

### [N&#x2011;10]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 11 instances of this issue:*

```solidity
File: lib/gpl/src/interfaces/IERC4626RouterBase.sol

10:    The base router is a multicall style router inspired by Uniswap v3 with built-in features for permit, WETH9 wrap/unwrap, and ERC20 token pulling/sweeping/approving.

15:    NOTE the router is capable of pulling any approved token from your wallet. This is only possible when your address is msg.sender, but regardless be careful when interacting with the router or ERC4626 Vaults.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IERC4626RouterBase.sol#L10

```solidity
File: src/AstariaRouter.sol

75:      * @dev Setup transfer authority and set up addresses for deployed CollateralToken, LienToken, TransferProxy contracts, as well as PublicVault and SoloVault implementations to clone.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L75

```solidity
File: src/interfaces/ICollateralToken.sol

100:     * @notice Executes a FlashAction using locked collateral. A valid FlashAction performs a specified action with the collateral within a single transaction and must end with the collateral being returned to the Vault it was locked in.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L100

```solidity
File: src/interfaces/IWithdrawProxy.sol

27:      * @notice Called at epoch boundary, computes the ratio between the funds of withdrawing liquidity providers and the balance of the underlying PublicVault so that claim() proportionally pays optimized-out to all parties.

35:      * @param finalAuctionDelta The timestamp by which the auction being added is guaranteed to end. As new auctions are added to the WithdrawProxy, this value will strictly increase as all auctions have the same maximum duration.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IWithdrawProxy.sol#L27

```solidity
File: src/LienToken.sol

42:    * @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L42

```solidity
File: src/VaultImplementation.sol

282:     * Starts by depositing collateral and take optimized-out a lien against it. Next, verifies the merkle proof for a loan commitment. Vault owners are then rewarded fees for successful loan origination.

363:     * @notice Retrieves the recipient of loan repayments. For PublicVaults (VAULT_TYPE 2), this is always the vault address. For PrivateVaults, retrieves the owner() of the vault.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L282

```solidity
File: src/WithdrawProxy.sol

54:       uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.

56:       uint256 withdrawReserveReceived; // amount received from PublicVault. The WETH balance of this contract - withdrawReserveReceived = amount received from liquidations.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L54

### [N&#x2011;11]  Inconsistent method of specifying a floating pragma
Some files use `>=`, some use `^`. The instances below are examples of the method that has the fewest instances for a specific version. Note that using `>=` without also specifying `<=` will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so `^` should be preferred regardless of the instance counts

*There are 5 instances of this issue:*

```solidity
File: lib/gpl/src/ERC4626RouterBase.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626RouterBase.sol#L2

```solidity
File: lib/gpl/src/ERC4626Router.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626Router.sol#L2

```solidity
File: lib/gpl/src/ERC721.sol

2:    pragma solidity ^0.8.16;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L2

```solidity
File: lib/gpl/src/interfaces/IERC4626RouterBase.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IERC4626RouterBase.sol#L2

```solidity
File: lib/gpl/src/interfaces/IERC4626Router.sol

2:    pragma solidity ^0.8.17;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IERC4626Router.sol#L2

### [N&#x2011;12]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There are 37 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

84:       Authority _AUTHORITY,

85:       ICollateralToken _COLLATERAL_TOKEN,

86:       ILienToken _LIEN_TOKEN,

87:       ITransferProxy _TRANSFER_PROXY,

88:       address _VAULT_IMPL,

89:       address _SOLO_IMPL,

90:       address _WITHDRAW_IMPL,

91:       address _BEACON_PROXY_IMPL,

92:       address _CLEARING_HOUSE_IMPL

329:        (uint8 TYPE, address addr) = abi.decode(data, (uint8, address));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L84

```solidity
File: src/ClearingHouse.sol

221:      IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L221

```solidity
File: src/CollateralToken.sol

81:       Authority AUTHORITY_,

82:       ITransferProxy TRANSFER_PROXY_,

83:       ILienToken LIEN_TOKEN_,

84:       ConsiderationInterface SEAPORT_

93:       bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));

140:      ClearingHouse CH = ClearingHouse(payable(s.clearingHouse[collateralId]));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L81

```solidity
File: src/interfaces/IAstariaRouter.sol

67:       ERC20 WETH; //20

68:       ICollateralToken COLLATERAL_TOKEN; //20

69:       ILienToken LIEN_TOKEN; //20

70:       ITransferProxy TRANSFER_PROXY; //20

72:       address BEACON_PROXY_IMPLEMENTATION; //20

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L67

```solidity
File: src/interfaces/ICollateralToken.sol

52:       ITransferProxy TRANSFER_PROXY;

53:       ILienToken LIEN_TOKEN;

54:       IAstariaRouter ASTARIA_ROUTER;

55:       ConsiderationInterface SEAPORT;

56:       ConduitControllerInterface CONDUIT_CONTROLLER;

57:       address CONDUIT;

58:       bytes32 CONDUIT_KEY;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L52

```solidity
File: src/interfaces/ILienToken.sol

38:       address WETH;

39:       ITransferProxy TRANSFER_PROXY;

40:       IAstariaRouter ASTARIA_ROUTER;

41:       ICollateralToken COLLATERAL_TOKEN;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ILienToken.sol#L38

```solidity
File: src/LienToken.sol

59:     function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L59

```solidity
File: src/TransferProxy.sol

24:     constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/TransferProxy.sol#L24

```solidity
File: src/VaultImplementation.sol

234:      ERC721 CT = ERC721(address(COLLATERAL_TOKEN()));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L234

### [N&#x2011;13]  Using `>`/`>=` without specifying an upper bound is unsafe
There _will_ be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

*There are 5 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

2:    pragma solidity >=0.8.16;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L2

```solidity
File: lib/gpl/src/ERC4626-Cloned.sol

2:    pragma solidity >=0.8.16;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626-Cloned.sol#L2

```solidity
File: lib/gpl/src/interfaces/IMulticall.sol

4:    pragma solidity >=0.7.5;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IMulticall.sol#L4

```solidity
File: lib/gpl/src/interfaces/IUniswapV3Factory.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IUniswapV3Factory.sol#L2

```solidity
File: lib/gpl/src/interfaces/IUniswapV3PoolState.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IUniswapV3PoolState.sol#L2

### [N&#x2011;14]  Typos

*There are 6 instances of this issue:*

```solidity
File: lib/gpl/src/interfaces/IUniswapV3Factory.sol

/// @audit unenabled
38:       /// @param fee The enabled fee, denominated in hundredths of a bip. Returns 0 in case of unenabled fee

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IUniswapV3Factory.sol#L38

```solidity
File: src/interfaces/IERC4626.sol

/// @audit redeemption
230:     * @dev Allows an on-chain or off-chain user to simulate the effects of their redeemption at the current block,

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC4626.sol#L230

```solidity
File: src/interfaces/IV3PositionManager.sol

/// @audit acheive
122:    /// @return amount0 The amount of token0 to acheive resulting liquidity

/// @audit acheive
123:    /// @return amount1 The amount of token1 to acheive resulting liquidity

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IV3PositionManager.sol#L122

```solidity
File: src/PublicVault.sol

/// @audit calcualtion
307:      // reset liquidationWithdrawRatio to prepare for re calcualtion

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L307

```solidity
File: src/Vault.sol

/// @audit vautls
90:       //invalid action private vautls can only be the owner or strategist

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/Vault.sol#L90

### [N&#x2011;15]  Misplaced SPDX identifier
The SPDX identifier should be on the very first line of the file

*There is 1 instance of this issue:*

```solidity
File: lib/gpl/src/interfaces/IMulticall.sol

1:    // forked from https://github.com/Uniswap/v3-periphery/blob/main/contracts/interfaces/IMulticall.sol

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IMulticall.sol#L1

### [N&#x2011;16]  File is missing NatSpec

*There are 16 instances of this issue:*

```solidity
File: lib/gpl/src/ERC4626-Cloned.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626-Cloned.sol

```solidity
File: lib/gpl/src/ERC4626RouterBase.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626RouterBase.sol

```solidity
File: lib/gpl/src/ERC4626Router.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626Router.sol

```solidity
File: src/AstariaVaultBase.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaVaultBase.sol

```solidity
File: src/ClearingHouse.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol

```solidity
File: src/interfaces/IAstariaVaultBase.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaVaultBase.sol

```solidity
File: src/interfaces/IERC721.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC721.sol

```solidity
File: src/interfaces/IFlashAction.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IFlashAction.sol

```solidity
File: src/interfaces/IRouterBase.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IRouterBase.sol

```solidity
File: src/interfaces/ISecurityHook.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ISecurityHook.sol

```solidity
File: src/interfaces/IStrategyValidator.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IStrategyValidator.sol

```solidity
File: src/interfaces/ITransferProxy.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ITransferProxy.sol

```solidity
File: src/interfaces/IVaultImplementation.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IVaultImplementation.sol

```solidity
File: src/TransferProxy.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/TransferProxy.sol

```solidity
File: src/Vault.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/Vault.sol

```solidity
File: src/WithdrawVaultBase.sol


```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawVaultBase.sol

### [N&#x2011;17]  NatSpec is incomplete

*There are 57 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit Missing: '@param _WITHDRAW_IMPL'
/// @audit Missing: '@param _BEACON_PROXY_IMPL'
/// @audit Missing: '@param _CLEARING_HOUSE_IMPL'
74      /**
75       * @dev Setup transfer authority and set up addresses for deployed CollateralToken, LienToken, TransferProxy contracts, as well as PublicVault and SoloVault implementations to clone.
76       * @param _AUTHORITY The authority manager.
77       * @param _COLLATERAL_TOKEN The address of the deployed CollateralToken contract.
78       * @param _LIEN_TOKEN The address of the deployed LienToken contract.
79       * @param _TRANSFER_PROXY The address of the deployed TransferProxy contract.
80       * @param _VAULT_IMPL The address of a base implementation of VaultImplementation for cloning.
81       * @param _SOLO_IMPL The address of a base implementation of a PrivateVault for cloning.
82       */
83      function initialize(
84        Authority _AUTHORITY,
85        ICollateralToken _COLLATERAL_TOKEN,
86        ILienToken _LIEN_TOKEN,
87        ITransferProxy _TRANSFER_PROXY,
88        address _VAULT_IMPL,
89        address _SOLO_IMPL,
90        address _WITHDRAW_IMPL,
91        address _BEACON_PROXY_IMPL,
92        address _CLEARING_HOUSE_IMPL
93:     ) external initializer {

/// @audit Missing: '@param s'
/// @audit Missing: '@param underlying'
/// @audit Missing: '@param vaultFee'
/// @audit Missing: '@param allowList'
/// @audit Missing: '@param depositCap'
705     /**
706      * @dev Deploys a new Vault.
707      * @param epochLength The length of each epoch for a new PublicVault. If 0, deploys a PrivateVault.
708      * @param delegate The address of the Vault delegate.
709      * @param allowListEnabled Whether or not the Vault has an LP whitelist.
710      * @return vaultAddr The address for the new Vault.
711      */
712     function _newVault(
713       RouterStorage storage s,
714       address underlying,
715       uint256 epochLength,
716       address delegate,
717       uint256 vaultFee,
718       bool allowListEnabled,
719       address[] memory allowList,
720       uint256 depositCap
721:    ) internal returns (address vaultAddr) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L74-L93

```solidity
File: src/CollateralToken.sol

/// @audit Missing: '@param s'
/// @audit Missing: '@param underlyingAsset'
/// @audit Missing: '@param collateralId'
348     /**
349      * @dev Transfers locked collateral to a specified address and deletes the reference to the CollateralToken for that NFT.
350      * @param releaseTo The address to send the NFT to.
351      */
352     function _releaseToAddress(
353       CollateralStorage storage s,
354       Asset memory underlyingAsset,
355       uint256 collateralId,
356:      address releaseTo

/// @audit Missing: '@param address'
/// @audit Missing: '@param bytes'
547     /**
548      * @dev Mints a new CollateralToken wrapping an NFT.
549      * @param from_ the owner of the collateral deposited
550      * @param tokenId_ The NFT token ID
551      * @return a static return of the receive signature
552      */
553     function onERC721Received(
554       address, /* operator_ */
555       address from_,
556       uint256 tokenId_,
557       bytes calldata // calldata data_
558:    ) external override whenNotPaused returns (bytes4) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L348-L356

```solidity
File: src/interfaces/IAstariaRouter.sol

/// @audit Missing: '@param timeToSecondEpochEnd'
129     /**
130      * @notice Validates the incoming loan commitment.
131      * @param commitment The commitment proofs and requested loan data for each loan.
132      * @return lien the new Lien data.
133      */
134     function validateCommitment(
135       IAstariaRouter.Commitment calldata commitment,
136       uint256 timeToSecondEpochEnd
137:    ) external returns (ILienToken.Lien memory lien);

/// @audit Missing: '@return'
147      * @param depositCap the deposit cap for the vault if any
148      */
149     function newPublicVault(
150       uint256 epochLength,
151       address delegate,
152       address underlying,
153       uint256 vaultFee,
154       bool allowListEnabled,
155       address[] calldata allowList,
156       uint256 depositCap
157:    ) external returns (address);

/// @audit Missing: '@param recipient'
183     /**
184      * @notice Create a new lien against a CollateralToken.
185      * @param params The valid proof and lien details for the new loan.
186      * @return The ID of the created lien.
187      */
188     function requestLienPosition(
189       IAstariaRouter.Commitment calldata params,
190       address recipient
191     )
192       external
193       returns (
194         uint256,
195         ILienToken.Stack[] memory,
196:        uint256

/// @audit Missing: '@return'
209      * @param includeBuffer Adds the current auctionWindowBuffer if true.
210      */
211:    function getAuctionWindow(bool includeBuffer) external view returns (uint256);

/// @audit Missing: '@param implType'
274     /**
275      * @notice Returns the address for the current implementation of a contract from the ImplementationType enum.
276      * @return impl The address of the clone implementation.
277      */
278:    function getImpl(uint8 implType) external view returns (address impl);

/// @audit Missing: '@return'
286      * @param stack The Stack of existing Liens against the CollateralToken.
287      */
288     function isValidRefinance(
289       ILienToken.Lien calldata newLien,
290       uint8 position,
291       ILienToken.Stack[] calldata stack
292:    ) external view returns (bool);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L129-L137

```solidity
File: src/interfaces/ICollateralToken.sol

/// @audit Missing: '@return'
129      * @param params The auction data.
130      */
131     function auctionVault(AuctionVaultParams calldata params)
132       external
133:      returns (OrderParameters memory);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L129-L133

```solidity
File: src/interfaces/ILienToken.sol

/// @audit Missing: '@param auctionWindow'
/// @audit Missing: '@param stack'
/// @audit Missing: '@param liquidator'
117     /**
118      * @notice Stops accruing interest for all liens against a single CollateralToken.
119      * @param collateralId The ID for the  CollateralToken of the NFT used as collateral for the liens.
120      */
121     function stopLiens(
122       uint256 collateralId,
123       uint256 auctionWindow,
124       Stack[] calldata stack,
125:      address liquidator

/// @audit Missing: '@return'
130      * @param stack the lien
131      */
132     function getBuyout(Stack calldata stack)
133       external
134       view
135:      returns (uint256 owed, uint256 buyout);

/// @audit Missing: '@return'
157      * @param stack the Lien
158      */
159:    function getInterest(Stack calldata stack) external returns (uint256);

/// @audit Missing: '@return'
163      * @param collateralId the Lien to compute a point for
164      */
165     function getCollateralState(uint256 collateralId)
166       external
167       view
168:      returns (bytes32);

/// @audit Missing: '@return'
172      * @param stack the Lien to compute a point for
173      */
174     function getAmountOwingAtLiquidation(ILienToken.Stack calldata stack)
175       external
176       view
177:      returns (uint256);

/// @audit Missing: '@return'
181      * @param params LienActionEncumber data containing CollateralToken information and lien parameters (rate, duration, and amount, rate, and debt caps).
182      */
183     function createLien(LienActionEncumber memory params)
184       external
185       returns (
186         uint256 lienId,
187         Stack[] memory stack,
188:        uint256 slope

/// @audit Missing: '@return'
193      * @param params The LienActionBuyout data specifying the lien position, receiver address, and underlying CollateralToken information of the lien.
194      */
195     function buyoutLien(LienActionBuyout memory params)
196       external
197:      returns (Stack[] memory, Stack memory);

/// @audit Missing: '@param token'
/// @audit Missing: '@param auctionStack'
199     /**
200      * @notice Called by the ClearingHouse (through Seaport) to pay back debt with auction funds.
201      * @param collateralId The CollateralId of the liquidated NFT.
202      * @param payment The payment amount.
203      */
204     function payDebtViaClearingHouse(
205       address token,
206       uint256 collateralId,
207       uint256 payment,
208:      AuctionStack[] memory auctionStack

/// @audit Missing: '@param collateralId'
/// @audit Missing: '@return'
211     /**
212      * @notice Make a payment for the debt against a CollateralToken.
213      * @param stack the stack to pay against
214      * @param amount The amount to pay against the debt.
215      */
216     function makePayment(
217       uint256 collateralId,
218       Stack[] memory stack,
219       uint256 amount
220:    ) external returns (Stack[] memory newStack);

/// @audit Missing: '@return'
246      * @param collateralId The ID of the CollateralToken.
247      */
248     function getAuctionData(uint256 collateralId)
249       external
250       view
251:      returns (AuctionData memory);

/// @audit Missing: '@return'
255      * @param collateralId The ID of the CollateralToken.
256      */
257     function getAuctionLiquidator(uint256 collateralId)
258       external
259       view
260:      returns (address liquidator);

/// @audit Missing: '@return'
264      * @param stack The stack data for active liens against the CollateralToken.
265      */
266     function getMaxPotentialDebtForCollateral(ILienToken.Stack[] memory stack)
267       external
268       view
269:      returns (uint256);

/// @audit Missing: '@return'
274      * @param end The timestamp to accrue potential debt until.
275      */
276     function getMaxPotentialDebtForCollateral(
277       ILienToken.Stack[] memory stack,
278       uint256 end
279:    ) external view returns (uint256);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ILienToken.sol#L117-L125

```solidity
File: src/interfaces/IPublicVault.sol

/// @audit Missing: '@return'
92       * @param end time to compute the end for
93       */
94:     function getLienEpoch(uint64 end) external view returns (uint64);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IPublicVault.sol#L92-L94

```solidity
File: src/interfaces/IWithdrawProxy.sol

/// @audit Missing: '@return'
45       * @param withdrawProxy The address of the withdrawProxy to drain to.
46       */
47      function drain(uint256 amount, address withdrawProxy)
48        external
49:       returns (uint256);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IWithdrawProxy.sol#L45-L49

```solidity
File: src/LienToken.sol

/// @audit Missing: '@return'
253      * @param timestamp The timestamp at which to compute interest for.
254      */
255     function _getInterest(Stack memory stack, uint256 timestamp)
256       internal
257       pure
258:      returns (uint256)

/// @audit Missing: '@param s'
/// @audit Missing: '@return'
658     /**
659      * @dev Have a specified payer make a payment for the debt against a CollateralToken.
660      * @param stack the stack for the payment
661      * @param totalCapitalAvailable The amount to pay against the debts
662      */
663     function _makePayment(
664       LienStorage storage s,
665       Stack[] calldata stack,
666       uint256 totalCapitalAvailable
667:    ) internal returns (Stack[] memory newStack) {

/// @audit Missing: '@param timestamp'
756     /**
757      * @dev Computes the debt owed to a Lien at a specified timestamp.
758      * @param stack The specified Lien.
759      * @return The amount owed to the Lien at the specified timestamp.
760      */
761     function _getOwed(Stack memory stack, uint256 timestamp)
762       internal
763       pure
764:      returns (uint88)

/// @audit Missing: '@param s'
/// @audit Missing: '@param position'
/// @audit Missing: '@return'
784     /**
785      * @dev Make a payment from a payer to a specific lien against a CollateralToken.
786      * @param activeStack The stack
787      * @param amount The amount to pay against the debt.
788      * @param payer The address to make the payment.
789      */
790     function _payment(
791       LienStorage storage s,
792       Stack[] memory activeStack,
793       uint8 position,
794       uint256 amount,
795       address payer
796:    ) internal returns (Stack[] memory, uint256) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L253-L258

```solidity
File: src/PublicVault.sol

/// @audit Missing: '@return'
249      * @param receiver The receiver of the resulting VaultToken shares.
250      */
251     function deposit(uint256 amount, address receiver)
252       public
253       override(ERC4626Cloned)
254       whenNotPaused
255:      returns (uint256)

/// @audit Missing: '@param lienEnd'
/// @audit Missing: '@param lienSlope'
435     /**
436      * @dev Hook for updating the slope of the PublicVault after a LienToken is issued.
437      * @param lienId The ID of the lien.
438      */
439     function _afterCommitToLien(
440       uint40 lienEnd,
441       uint256 lienId,
442:      uint256 lienSlope

/// @audit Missing: '@param s'
592     /**
593      * @dev Handles the dilutive fees (on lien repayments) for strategists in VaultTokens.
594      * @param interestOwing the owingInterest for the lien
595      * @param amount The amount that was paid.
596      */
597     function _handleStrategistInterestReward(
598       VaultData storage s,
599       uint256 interestOwing,
600:      uint256 amount

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L249-L255

```solidity
File: src/VaultImplementation.sol

/// @audit Missing: '@param strategy'
/// @audit Missing: '@param root'
/// @audit Missing: '@return'
164     /*
165      * @notice encodes the data for a 712 signature
166      * @param tokenContract The address of the token contract
167      * @param tokenId The id of the token
168      * @param amount The amount of the token
169      */
170     function encodeStrategyData(
171       IAstariaRouter.StrategyDetailsParam calldata strategy,
172       bytes32 root
173:    ) external view returns (bytes memory) {

/// @audit Missing: '@param stack'
/// @audit Missing: '@return'
308     /**
309      * @notice Buy optimized-out a lien to replace it with new terms.
310      * @param position The position of the specified lien.
311      * @param incomingTerms The loan terms of the new lien.
312      */
313     function buyoutLien(
314       ILienToken.Stack[] calldata stack,
315       uint8 position,
316       IAstariaRouter.Commitment calldata incomingTerms
317     )
318       external
319       whenNotPaused
320:      returns (ILienToken.Stack[] memory, ILienToken.Stack memory)

/// @audit Missing: '@return'
377      * @param receiver The borrower requesting the loan.
378      */
379     function _requestLienAndIssuePayout(
380       IAstariaRouter.Commitment calldata c,
381       address receiver
382     )
383       internal
384       returns (
385         uint256 newLienId,
386         ILienToken.Stack[] memory stack,
387         uint256 slope,
388:        uint256 payout

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L164-L173

```solidity
File: src/WithdrawProxy.sol

/// @audit Missing: '@return'
130      * @param shares The number of shares to mint.
131      */
132     function mint(uint256 shares, address receiver)
133       public
134       virtual
135       override(ERC4626Cloned, IERC4626)
136:      returns (uint256 assets)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L130-L136

### [N&#x2011;18]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 25 instances of this issue:*

```solidity
File: lib/gpl/src/ERC4626Router.sol

/// @audit sharesOut
24      function depositToVault(
25        IERC4626 vault,
26        address to,
27        uint256 amount,
28        uint256 minSharesOut
29:     ) external payable override returns (uint256 sharesOut) {

/// @audit sharesOut
35      function depositMax(
36        IERC4626 vault,
37        address to,
38        uint256 minSharesOut
39:     ) public payable override returns (uint256 sharesOut) {

/// @audit amountOut
49      function redeemMax(
50        IERC4626 vault,
51        address to,
52        uint256 minAmountOut
53:     ) public payable override returns (uint256 amountOut) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626Router.sol#L24-L29

```solidity
File: src/AstariaRouter.sol

/// @audit amountIn
123     function mint(
124       IERC4626 vault,
125       address to,
126       uint256 shares,
127       uint256 maxAmountIn
128     )
129       public
130       payable
131       virtual
132       override
133       validVault(address(vault))
134:      returns (uint256 amountIn)

/// @audit sharesOut
139     function deposit(
140       IERC4626 vault,
141       address to,
142       uint256 amount,
143       uint256 minSharesOut
144     )
145       public
146       payable
147       virtual
148       override
149       validVault(address(vault))
150:      returns (uint256 sharesOut)

/// @audit sharesOut
155     function withdraw(
156       IERC4626 vault,
157       address to,
158       uint256 amount,
159       uint256 maxSharesOut
160     )
161       public
162       payable
163       virtual
164       override
165       validVault(address(vault))
166:      returns (uint256 sharesOut)

/// @audit amountOut
171     function redeem(
172       IERC4626 vault,
173       address to,
174       uint256 shares,
175       uint256 minAmountOut
176     )
177       public
178       payable
179       virtual
180       override
181       validVault(address(vault))
182:      returns (uint256 amountOut)

/// @audit assets
187     function redeemFutureEpoch(
188       IPublicVault vault,
189       uint256 shares,
190       address receiver,
191       uint64 epoch
192:    ) public virtual validVault(address(vault)) returns (uint256 assets) {

/// @audit lien
426     function validateCommitment(
427       IAstariaRouter.Commitment calldata commitment,
428       uint256 timeToSecondEpochEnd
429:    ) public view returns (ILienToken.Lien memory lien) {

/// @audit stack
/// @audit payout
761     function _executeCommitment(
762       RouterStorage storage s,
763       IAstariaRouter.Commitment memory c
764     )
765       internal
766       returns (
767         uint256,
768         ILienToken.Stack[] memory stack,
769:        uint256 payout

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L123-L134

```solidity
File: src/CollateralToken.sol

/// @audit validOrderMagicValue
157     function isValidOrder(
158       bytes32 orderHash,
159       address caller,
160       address offerer,
161       bytes32 zoneHash
162:    ) external view returns (bytes4 validOrderMagicValue) {

/// @audit validOrderMagicValue
171     function isValidOrderIncludingExtraData(
172       bytes32 orderHash,
173       address caller,
174       AdvancedOrder calldata order,
175       bytes32[] calldata priorOrderHashes,
176       CriteriaResolver[] calldata criteriaResolvers
177:    ) external view returns (bytes4 validOrderMagicValue) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L157-L162

```solidity
File: src/LienToken.sol

/// @audit newStack
107     function buyoutLien(ILienToken.LienActionBuyout calldata params)
108       external
109       validateStack(params.encumber.lien.collateralId, params.encumber.stack)
110:      returns (Stack[] memory, Stack memory newStack)

/// @audit newSlot
424     function _createLien(
425       LienStorage storage s,
426       ILienToken.LienActionEncumber memory params
427:    ) internal returns (uint256 newLienId, ILienToken.Stack memory newSlot) {

/// @audit owed
/// @audit buyout
577     function getBuyout(Stack calldata stack)
578       public
579       view
580:      returns (uint256 owed, uint256 buyout)

/// @audit newStack
596     function makePayment(
597       uint256 collateralId,
598       Stack[] calldata stack,
599       uint256 amount
600     )
601       public
602       validateStack(collateralId, stack)
603:      returns (Stack[] memory newStack)

/// @audit maxPotentialDebt
706     function getMaxPotentialDebtForCollateral(Stack[] memory stack)
707       public
708       view
709       validateStack(stack[0].lien.collateralId, stack)
710:      returns (uint256 maxPotentialDebt)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L107-L110

```solidity
File: src/PublicVault.sol

/// @audit assets
138     function redeemFutureEpoch(
139       uint256 shares,
140       address receiver,
141       address owner,
142       uint64 epoch
143:    ) public virtual returns (uint256 assets) {

/// @audit timeToSecondEpochEnd
713     function _timeToSecondEndIfPublic()
714       internal
715       view
716       override
717:      returns (uint256 timeToSecondEpochEnd)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L138-L143

```solidity
File: src/VaultImplementation.sol

/// @audit timeToSecondEpochEnd
353     function _timeToSecondEndIfPublic()
354       internal
355       view
356       virtual
357:      returns (uint256 timeToSecondEpochEnd)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L353-L357

```solidity
File: src/WithdrawProxy.sol

/// @audit assets
132     function mint(uint256 shares, address receiver)
133       public
134       virtual
135       override(ERC4626Cloned, IERC4626)
136:      returns (uint256 assets)

/// @audit shares
163     function withdraw(
164       uint256 assets,
165       address receiver,
166       address owner
167     )
168       public
169       virtual
170       override(ERC4626Cloned, IERC4626)
171       onlyWhenNoActiveAuction
172:      returns (uint256 shares)

/// @audit assets
184     function redeem(
185       uint256 shares,
186       address receiver,
187       address owner
188     )
189       public
190       virtual
191       override(ERC4626Cloned, IERC4626)
192       onlyWhenNoActiveAuction
193:      returns (uint256 assets)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L132-L136

### [N&#x2011;19]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 6 instances of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

200:      require(to != address(0), "INVALID_RECIPIENT");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L200

```solidity
File: src/AstariaRouter.sol

347:      require(msg.sender == s.guardian);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L347

```solidity
File: src/ClearingHouse.sol

216:      require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L216

```solidity
File: src/PublicVault.sol

259:        require(s.allowList[receiver]);

687       require(
688         currentEpoch != 0 &&
689           msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:      );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L259

```solidity
File: src/VaultImplementation.sol

96:       require(msg.sender == owner()); //owner is "strategist"

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L96

### [N&#x2011;20]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 5 instances of this issue:*

```solidity
File: src/PublicVault.sol

308:      s.liquidationWithdrawRatio = 0;

327:            s.withdrawReserve = 0;

377:          s.withdrawReserve = 0;

511:      s.strategistUnclaimedShares = 0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L308

```solidity
File: src/WithdrawProxy.sol

284:      s.finalAuctionEnd = 0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L284

### [N&#x2011;21]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There is 1 instance of this issue:*

```solidity
File: src/AstariaRouter.sol

709:     * @param allowListEnabled Whether or not the Vault has an LP whitelist.

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L709

### [N&#x2011;22]  Large assembly blocks should have extensive comments
Assembly blocks are take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code

*There is 1 instance of this issue:*

```solidity
File: src/AstariaRouter.sol

414       assembly {
415         let end := add(ONE_WORD, start)
416   
417         if lt(length, end) {
418           mstore(0, OUTOFBOUND_ERROR_SELECTOR)
419           revert(0, ONE_WORD)
420         }
421   
422         x := mload(add(bs, end))
423:      }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L414-L423

### [N&#x2011;23]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;24]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;25]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 69 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

/// @audit _loadERC20Slot() came earlier
35:     function balanceOf(address account) external view returns (uint256) {

/// @audit transfer() came earlier
67      function allowance(address owner, address spender)
68        external
69        view
70:       returns (uint256)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L35

```solidity
File: lib/gpl/src/ERC4626Router.sol

/// @audit pullToken() came earlier
24      function depositToVault(
25        IERC4626 vault,
26        address to,
27        uint256 amount,
28        uint256 minSharesOut
29:     ) external payable override returns (uint256 sharesOut) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626Router.sol#L24-L29

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit isApprovedForAll() came earlier
38:     function tokenURI(uint256 id) external view virtual returns (string memory);

/// @audit _loadERC721Slot() came earlier
52:     function ownerOf(uint256 id) public view virtual returns (address owner) {

/// @audit __initERC721() came earlier
79:     function name() public view returns (string memory) {

/// @audit symbol() came earlier
87:     function approve(address spender, uint256 id) external virtual {

/// @audit _transfer() came earlier
148     function safeTransferFrom(
149       address from,
150       address to,
151:      uint256 id

/// @audit _safeMint() came earlier
277     function onERC721Received(
278       address,
279       address,
280       uint256,
281       bytes calldata
282:    ) external virtual returns (bytes4) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L38

```solidity
File: src/AstariaRouter.sol

/// @audit _loadRouterSlot() came earlier
224:    function feeTo() public view returns (address) {

/// @audit COLLATERAL_TOKEN() came earlier
252:    function __emergencyPause() external requiresAuth whenNotPaused {

/// @audit _file() came earlier
339:    function setNewGuardian(address _guardian) external {

/// @audit _sliceUint() came earlier
426     function validateCommitment(
427       IAstariaRouter.Commitment calldata commitment,
428       uint256 timeToSecondEpochEnd
429:    ) public view returns (ILienToken.Lien memory lien) {

/// @audit _validateCommitment() came earlier
490     function commitToLiens(IAstariaRouter.Commitment[] memory commitments)
491       public
492       whenNotPaused
493:      returns (uint256[] memory lienIds, ILienToken.Stack[] memory stack)

/// @audit commitToLiens() came earlier
522     function newVault(address delegate, address underlying)
523       external
524       whenNotPaused
525:      returns (address)

/// @audit newPublicVault() came earlier
577     function requestLienPosition(
578       IAstariaRouter.Commitment calldata params,
579       address receiver
580     )
581       external
582       whenNotPaused
583       validVault(msg.sender)
584       returns (
585         uint256,
586         ILienToken.Stack[] memory,
587:        uint256

/// @audit liquidate() came earlier
650:    function getProtocolFee(uint256 amountIn) external view returns (uint256) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L224

```solidity
File: src/BeaconProxy.sol

/// @audit _fallback() came earlier
69      fallback() external payable virtual {
70:       _fallback();

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/BeaconProxy.sol#L69-L70

```solidity
File: src/ClearingHouse.sol

/// @audit _getStorage() came earlier
66:     function setAuctionData(ILienToken.AuctionData calldata auctionData)

/// @audit _execute() came earlier
169     function safeTransferFrom(
170       address from, // the from is the offerer
171       address to,
172       uint256 identifier,
173       uint256 amount,
174:      bytes calldata data //empty from seaport

/// @audit safeBatchTransferFrom() came earlier
188     function onERC721Received(
189       address operator_,
190       address from_,
191       uint256 tokenId_,
192       bytes calldata data_
193:    ) external override returns (bytes4) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L66

```solidity
File: src/CollateralToken.sol

/// @audit SEAPORT() came earlier
109:    function liquidatorNFTClaim(OrderParameters memory params) external {

/// @audit _loadCollateralSlot() came earlier
157     function isValidOrder(
158       bytes32 orderHash,
159       address caller,
160       address offerer,
161       bytes32 zoneHash
162:    ) external view returns (bytes4 validOrderMagicValue) {

/// @audit supportsInterface() came earlier
196:    function fileBatch(File[] calldata files) external requiresAuth {

/// @audit _file() came earlier
270     function flashAction(
271       IFlashAction receiver,
272       uint256 collateralId,
273       bytes calldata data
274:    ) external onlyOwner(collateralId) {

/// @audit _releaseToAddress() came earlier
370:    function getConduitKey() public view returns (bytes32) {

/// @audit securityHooks() came earlier
416     function getClearingHouse(uint256 collateralId)
417       external
418       view
419:      returns (ClearingHouse)

/// @audit _generateValidOrderParameters() came earlier
477     function auctionVault(AuctionVaultParams calldata params)
478       external
479       requiresAuth
480:      returns (OrderParameters memory orderParameters)

/// @audit _listUnderlyingOnSeaport() came earlier
524:    function settleAuction(uint256 collateralId) public {

/// @audit _settleAuction() came earlier
553     function onERC721Received(
554       address, /* operator_ */
555       address from_,
556       uint256 tokenId_,
557       bytes calldata // calldata data_
558:    ) external override whenNotPaused returns (bytes4) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L109

```solidity
File: src/LienToken.sol

/// @audit _loadLienStorageSlot() came earlier
82:     function file(File calldata incoming) external requiresAuth {

/// @audit supportsInterface() came earlier
107     function buyoutLien(ILienToken.LienActionBuyout calldata params)
108       external
109       validateStack(params.encumber.lien.collateralId, params.encumber.stack)
110:      returns (Stack[] memory, Stack memory newStack)

/// @audit _replaceStackAtPositionWithNewLien() came earlier
246:    function getInterest(Stack calldata stack) public view returns (uint256) {

/// @audit _getInterest() came earlier
277     function stopLiens(
278       uint256 collateralId,
279       uint256 auctionWindow,
280       Stack[] calldata stack,
281       address liquidator
282:    ) external validateStack(collateralId, stack) requiresAuth {

/// @audit _stopLiens() came earlier
348     function tokenURI(uint256 tokenId)
349       public
350       view
351       override(ERC721, IERC721)
352:      returns (string memory)

/// @audit _exists() came earlier
389     function createLien(ILienToken.LienActionEncumber memory params)
390       external
391       requiresAuth
392       validateStack(params.lien.collateralId, params.stack)
393       returns (
394         uint256 lienId,
395         Stack[] memory newStack,
396:        uint256 lienSlope

/// @audit _appendStack() came earlier
497     function payDebtViaClearingHouse(
498       address token,
499       uint256 collateralId,
500       uint256 payment,
501:      AuctionStack[] memory auctionStack

/// @audit _payDebt() came earlier
531     function getAuctionData(uint256 collateralId)
532       external
533       view
534:      returns (AuctionData memory)

/// @audit validateLien() came earlier
569     function getCollateralState(uint256 collateralId)
570       external
571       view
572:      returns (bytes32)

/// @audit _getBuyout() came earlier
596     function makePayment(
597       uint256 collateralId,
598       Stack[] calldata stack,
599       uint256 amount
600     )
601       public
602       validateStack(collateralId, stack)
603:      returns (Stack[] memory newStack)

/// @audit makePayment() came earlier
608     function makePayment(
609       uint256 collateralId,
610       Stack[] calldata stack,
611       uint8 position,
612       uint256 amount
613     )
614       external
615       validateStack(collateralId, stack)
616:      returns (Stack[] memory newStack)

/// @audit _updateCollateralStateHash() came earlier
702:    function calculateSlope(Stack memory stack) public pure returns (uint256) {

/// @audit _getMaxPotentialDebtForCollateralUpToNPositions() came earlier
727     function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
728       public
729       view
730       validateStack(stack[0].lien.collateralId, stack)
731:      returns (uint256 maxPotentialDebt)

/// @audit getMaxPotentialDebtForCollateral() came earlier
742:    function getOwed(Stack memory stack) external view returns (uint88) {

/// @audit _isPublicVault() came earlier
893:    function getPayee(uint256 lienId) public view returns (address) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L82

```solidity
File: src/PublicVault.sol

/// @audit _redeemFutureEpoch() came earlier
192:    function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {

/// @audit _deployWithdrawProxyIfNotDeployed() came earlier
233     function mint(uint256 shares, address receiver)
234       public
235       override(ERC4626Cloned)
236       whenNotPaused
237:      returns (uint256)

/// @audit computeDomainSeparator() came earlier
275     function processEpoch() public {
276       // check to make sure epoch is over
277:      if (timeToEpochEnd() > 0) {

/// @audit _afterCommitToLien() came earlier
461:    function accrue() public returns (uint256) {

/// @audit _accrue() came earlier
479     function totalAssets()
480       public
481       view
482       virtual
483       override(ERC4626Cloned)
484:      returns (uint256)

/// @audit _totalAssets() came earlier
495     function totalSupply()
496       public
497       view
498       virtual
499       override(IERC20, ERC20Cloned)
500:      returns (uint256)

/// @audit totalSupply() came earlier
507     function claim() external {
508:      require(msg.sender == owner()); //owner is "strategist"

/// @audit _setSlope() came earlier
534:    function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {

/// @audit _decreaseEpochLienCount() came earlier
546:    function getLienEpoch(uint64 end) public pure returns (uint64) {

/// @audit _increaseOpenLiens() came earlier
562:    function afterPayment(uint256 computedSlope) public onlyLienToken {

/// @audit _handleStrategistInterestReward() came earlier
611:    function LIEN_TOKEN() public view returns (ILienToken) {

/// @audit handleBuyoutLien() came earlier
632     function updateAfterLiquidationPayment(
633       LiquidationPaymentParams calldata params
634:    ) external onlyLienToken {

/// @audit _setYIntercept() came earlier
699:    function timeToEpochEnd() public view returns (uint256) {

/// @audit _timeToSecondEndIfPublic() came earlier
722:    function timeToSecondEpochEnd() public view returns (uint256) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L192

```solidity
File: src/VaultImplementation.sol

/// @audit _loadVISlot() came earlier
95:     function modifyAllowList(address depositor, bool enabled) external virtual {

/// @audit domainSeparator() came earlier
170     function encodeStrategyData(
171       IAstariaRouter.StrategyDetailsParam calldata strategy,
172       bytes32 root
173:    ) external view returns (bytes memory) {

/// @audit _encodeStrategyData() came earlier
190:    function init(InitParams calldata params) external virtual {

/// @audit _beforeCommitToLien() came earlier
287     function commitToLien(
288       IAstariaRouter.Commitment calldata params,
289       address receiver
290     )
291       external
292       whenNotPaused
293:      returns (uint256 lienId, ILienToken.Stack[] memory stack, uint256 payout)

/// @audit _timeToSecondEndIfPublic() came earlier
366:    function recipient() public view returns (address) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L95

```solidity
File: src/Vault.sol

/// @audit deposit() came earlier
70:     function withdraw(uint256 amount) external {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/Vault.sol#L70

```solidity
File: src/WithdrawProxy.sol

/// @audit redeem() came earlier
198     function supportsInterface(bytes4 interfaceId)
199       external
200       view
201       virtual
202:      returns (bool)

/// @audit _loadSlot() came earlier
215:    function getFinalAuctionEnd() public view returns (uint256) {

/// @audit getExpected() came earlier
235:    function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L198-L202

```solidity
File: src/WithdrawVaultBase.sol

/// @audit symbol() came earlier
26:     function ROUTER() external pure returns (IAstariaRouter) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawVaultBase.sol#L26

### [N&#x2011;26]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 21 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit function redeemFutureEpoch came earlier
196     modifier validVault(address targetVault) {
197       if (!isValidVault(targetVault)) {
198         revert InvalidVault(targetVault);
199       }
200       _;
201:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L196-L201

```solidity
File: src/CollateralToken.sol

/// @audit function _file came earlier
253     modifier releaseCheck(uint256 collateralId) {
254       CollateralStorage storage s = _loadCollateralSlot();
255   
256       if (s.LIEN_TOKEN.getCollateralState(collateralId) != bytes32(0)) {
257         revert InvalidCollateralState(InvalidCollateralStates.ACTIVE_LIENS);
258       }
259       if (s.collateralIdToAuction[collateralId] != bytes32(0)) {
260         revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
261       }
262       _;
263:    }

/// @audit function onERC721Received came earlier
602     modifier whenNotPaused() {
603       if (_loadCollateralSlot().ASTARIA_ROUTER.paused()) {
604         revert ProtocolPaused();
605       }
606       _;
607:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L253-L263

```solidity
File: src/interfaces/IAstariaRouter.sol

/// @audit event FileUpdated came earlier
87      enum ImplementationType {
88        PrivateVault,
89        PublicVault,
90        WithdrawProxy,
91        ClearingHouse
92:     }

/// @audit function isValidRefinance came earlier
294:    event Liquidation(uint256 collateralId, uint256 position);

/// @audit event NewVault came earlier
316     enum LienState {
317       HEALTHY,
318       AUCTION
319:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L87-L92

```solidity
File: src/interfaces/ICollateralToken.sol

/// @audit event ReleaseTo came earlier
74      enum FileType {
75        NotSupported,
76        AstariaRouter,
77        SecurityHook,
78        FlashEnabled,
79        Seaport
80:     }

/// @audit function liquidatorNFTClaim came earlier
175     enum InvalidCollateralStates {
176       NO_AUTHORITY,
177       NO_AUCTION,
178       FLASH_DISABLED,
179       AUCTION_ACTIVE,
180       INVALID_AUCTION_PARAMS,
181       ACTIVE_LIENS
182:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L74-L80

```solidity
File: src/interfaces/ILienToken.sol

/// @audit function file came earlier
294     event AddLien(
295       uint256 indexed collateralId,
296       uint8 position,
297       uint256 indexed lienId,
298       Stack stack
299:    );

/// @audit event AddLien came earlier
300     enum StackAction {
301       CLEAR,
302       ADD,
303       REMOVE,
304       REPLACE
305:    }

/// @audit event PayeeChanged came earlier
324     enum InvalidStates {
325       NO_AUTHORITY,
326       COLLATERAL_MISMATCH,
327       ASSET_MISMATCH,
328       NOT_ENOUGH_FUNDS,
329       INVALID_LIEN_ID,
330       COLLATERAL_AUCTION,
331       COLLATERAL_NOT_DEPOSITED,
332       LIEN_NO_DEBT,
333       EXPIRED_LIEN,
334       DEBT_LIMIT,
335       MAX_LIENS,
336       INVALID_HASH,
337       INVALID_LIQUIDATION_INITIAL_ASK,
338       INITIAL_ASK_EXCEEDED,
339       EMPTY_STATE,
340       PUBLIC_VAULT_RECIPIENT,
341       COLLATERAL_NOT_LIQUIDATED
342:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ILienToken.sol#L294-L299

```solidity
File: src/interfaces/IPublicVault.sol

/// @audit function updateVaultAfterLiquidation came earlier
157     enum InvalidStates {
158       EPOCH_TOO_LOW,
159       EPOCH_TOO_HIGH,
160       EPOCH_NOT_OVER,
161       WITHDRAW_RESERVE_NOT_ZERO,
162       LIENS_OPEN_FOR_EPOCH_NOT_ZERO,
163       LIQUIDATION_ACCOUNTANT_FINAL_AUCTION_OPEN,
164       LIQUIDATION_ACCOUNTANT_ALREADY_DEPLOYED_FOR_EPOCH,
165       DEPOSIT_CAP_EXCEEDED
166:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IPublicVault.sol#L157-L166

```solidity
File: src/LienToken.sol

/// @audit function _getInterest came earlier
265     modifier validateStack(uint256 collateralId, Stack[] memory stack) {
266       LienStorage storage s = _loadLienStorageSlot();
267       bytes32 stateHash = s.collateralStateHash[collateralId];
268       if (stateHash == bytes32(0) && stack.length != 0) {
269         revert InvalidState(InvalidStates.EMPTY_STATE);
270       }
271       if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
272         revert InvalidState(InvalidStates.INVALID_HASH);
273       }
274       _;
275:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L265-L275

```solidity
File: src/PublicVault.sol

/// @audit function _afterCommitToLien came earlier
459:    event SlopeUpdated(uint48 newSlope);

/// @audit function increaseYIntercept came earlier
679     modifier onlyLienToken() {
680       require(msg.sender == address(LIEN_TOKEN()));
681       _;
682:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L459

```solidity
File: src/VaultImplementation.sol

/// @audit function symbol came earlier
57      uint256 private constant VI_SLOT =
58:       uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;

/// @audit function onERC721Received came earlier
131     modifier whenNotPaused() {
132       if (ROUTER().paused()) {
133         revert InvalidRequest(InvalidRequestReason.PAUSED);
134       }
135   
136       if (_loadVISlot().isShutdown) {
137         revert InvalidRequest(InvalidRequestReason.SHUTDOWN);
138       }
139       _;
140:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L57-L58

```solidity
File: src/WithdrawProxy.sol

/// @audit event Claimed came earlier
49      uint256 private constant WITHDRAW_PROXY_SLOT =
50:       uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;

/// @audit variable WITHDRAW_PROXY_SLOT came earlier
59      enum InvalidStates {
60        PROCESS_EPOCH_NOT_COMPLETE,
61        FINAL_AUCTION_NOT_OVER,
62        NOT_CLAIMED,
63        CANT_CLAIM
64:     }

/// @audit function deposit came earlier
152     modifier onlyWhenNoActiveAuction() {
153       WPStorage storage s = _loadSlot();
154       // If auction funds have been collected to the WithdrawProxy
155       // but the PublicVault hasn't claimed its share, too much money will be sent to LPs
156       if (s.finalAuctionEnd != 0) {
157         // if finalAuctionEnd is 0, no auctions were added
158         revert InvalidState(InvalidStates.NOT_CLAIMED);
159       }
160       _;
161:    }

/// @audit function getExpected came earlier
230     modifier onlyVault() {
231       require(msg.sender == VAULT(), "only vault can call");
232       _;
233:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L49-L50


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `safeApprove()` is deprecated | 6 | 

Total: 6 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 29 | 
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 52 | 
| [N&#x2011;03] | `constant`s should be defined rather than using magic numbers | 21 | 
| [N&#x2011;04] | Event is missing `indexed` fields | 32 | 

Total: 134 instances over 4 issues





## Low Risk Issues

### [L&#x2011;01]  `safeApprove()` is deprecated
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead. The function may currently work, but if a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to no longer has this function, you'll encounter unnecessary delays in porting and testing replacement contracts.

*There are 6 instances of this issue:*

```solidity
File: lib/gpl/src/ERC4626RouterBase.sol

/// @audit (valid but excluded finding)
21:       ERC20(vault.asset()).safeApprove(address(vault), shares);

/// @audit (valid but excluded finding)
34:       ERC20(vault.asset()).safeApprove(address(vault), amount);

/// @audit (valid but excluded finding)
48:       ERC20(address(vault)).safeApprove(address(vault), amount);

/// @audit (valid but excluded finding)
62:       ERC20(address(vault)).safeApprove(address(vault), shares);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626RouterBase.sol#L21

```solidity
File: src/ClearingHouse.sol

/// @audit (valid but excluded finding)
148:      ERC20(paymentToken).safeApprove(

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L148

```solidity
File: src/VaultImplementation.sol

/// @audit (valid but excluded finding)
334:      ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L334

## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 29 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit (valid but excluded finding)
341:      require(msg.sender == s.guardian);

/// @audit (valid but excluded finding)
347:      require(msg.sender == s.guardian);

/// @audit (valid but excluded finding)
354:      require(msg.sender == s.newGuardian);

/// @audit (valid but excluded finding)
361:      require(msg.sender == address(s.guardian));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L341

```solidity
File: src/ClearingHouse.sol

/// @audit (valid but excluded finding)
72:       require(msg.sender == address(ASTARIA_ROUTER.LIEN_TOKEN()));

/// @audit (valid but excluded finding)
199:      require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

/// @audit (valid but excluded finding)
216:      require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

/// @audit (valid but excluded finding)
223:      require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L72

```solidity
File: src/CollateralToken.sol

/// @audit (valid but excluded finding)
266:      require(ownerOf(collateralId) == msg.sender);

/// @audit (valid but excluded finding)
535:      require(msg.sender == s.clearingHouse[collateralId]);

/// @audit (valid but excluded finding)
564:        require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));

/// @audit (valid but excluded finding)
598:        revert();

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L266

```solidity
File: src/LienToken.sol

/// @audit (valid but excluded finding)
504       require(
505         msg.sender == address(s.COLLATERAL_TOKEN.getClearingHouse(collateralId))
506:      );

/// @audit (valid but excluded finding)
860:      require(position < length);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L504-L506

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
241:        require(s.allowList[receiver]);

/// @audit (valid but excluded finding)
259:        require(s.allowList[receiver]);

/// @audit (valid but excluded finding)
508:      require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
672       require(
673         currentEpoch != 0 &&
674           msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
675:      );

/// @audit (valid but excluded finding)
680:      require(msg.sender == address(LIEN_TOKEN()));

/// @audit (valid but excluded finding)
687       require(
688         currentEpoch != 0 &&
689           msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:      );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L241

```solidity
File: src/VaultImplementation.sol

/// @audit (valid but excluded finding)
78:       require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
96:       require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
105:      require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
114:      require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
147:      require(msg.sender == owner()); //owner is "strategist"

/// @audit (valid but excluded finding)
191:      require(msg.sender == address(ROUTER()));

/// @audit (valid but excluded finding)
211:      require(msg.sender == owner()); //owner is "strategist"

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L78

```solidity
File: src/Vault.sol

/// @audit (valid but excluded finding)
65:       require(s.allowList[msg.sender] && receiver == owner());

/// @audit (valid but excluded finding)
71:       require(msg.sender == owner());

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/Vault.sol#L65

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 52 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit (valid but excluded finding)
224:    function feeTo() public view returns (address) {

/// @audit (valid but excluded finding)
229:    function BEACON_PROXY_IMPLEMENTATION() public view returns (address) {

/// @audit (valid but excluded finding)
234:    function LIEN_TOKEN() public view returns (ILienToken) {

/// @audit (valid but excluded finding)
239:    function TRANSFER_PROXY() public view returns (ITransferProxy) {

/// @audit (valid but excluded finding)
273:    function file(File calldata incoming) public requiresAuth {

/// @audit (valid but excluded finding)
402:    function getAuctionWindow(bool includeBuffer) public view returns (uint256) {

/// @audit (valid but excluded finding)
426     function validateCommitment(
427       IAstariaRouter.Commitment calldata commitment,
428       uint256 timeToSecondEpochEnd
429:    ) public view returns (ILienToken.Lien memory lien) {

/// @audit (valid but excluded finding)
490     function commitToLiens(IAstariaRouter.Commitment[] memory commitments)
491       public
492       whenNotPaused
493:      returns (uint256[] memory lienIds, ILienToken.Stack[] memory stack)

/// @audit (valid but excluded finding)
544     function newPublicVault(
545       uint256 epochLength,
546       address delegate,
547       address underlying,
548       uint256 vaultFee,
549       bool allowListEnabled,
550       address[] calldata allowList,
551       uint256 depositCap
552:    ) public whenNotPaused returns (address) {

/// @audit (valid but excluded finding)
621     function liquidate(ILienToken.Stack[] memory stack, uint8 position)
622       public
623:      returns (OrderParameters memory listedOrder)

/// @audit (valid but excluded finding)
684     function isValidRefinance(
685       ILienToken.Lien calldata newLien,
686       uint8 position,
687       ILienToken.Stack[] calldata stack
688:    ) public view returns (bool) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L224

```solidity
File: src/ClearingHouse.sol

/// @audit (valid but excluded finding)
169     function safeTransferFrom(
170       address from, // the from is the offerer
171       address to,
172       uint256 identifier,
173       uint256 amount,
174:      bytes calldata data //empty from seaport

/// @audit (valid but excluded finding)
180     function safeBatchTransferFrom(
181       address from,
182       address to,
183       uint256[] calldata ids,
184       uint256[] calldata amounts,
185:      bytes calldata data

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L169-L174

```solidity
File: src/CollateralToken.sol

/// @audit (valid but excluded finding)
80      function initialize(
81        Authority AUTHORITY_,
82        ITransferProxy TRANSFER_PROXY_,
83        ILienToken LIEN_TOKEN_,
84        ConsiderationInterface SEAPORT_
85:     ) public initializer {

/// @audit (valid but excluded finding)
105:    function SEAPORT() public view returns (ConsiderationInterface) {

/// @audit (valid but excluded finding)
206:    function file(File calldata incoming) public requiresAuth {

/// @audit (valid but excluded finding)
331     function releaseToAddress(uint256 collateralId, address releaseTo)
332       public
333       releaseCheck(collateralId)
334:      onlyOwner(collateralId)

/// @audit (valid but excluded finding)
370:    function getConduitKey() public view returns (bytes32) {

/// @audit (valid but excluded finding)
375:    function getConduit() public view returns (address) {

/// @audit (valid but excluded finding)
412:    function securityHooks(address target) public view returns (address) {

/// @audit (valid but excluded finding)
524:    function settleAuction(uint256 collateralId) public {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L80-L85

```solidity
File: src/LienToken.sol

/// @audit (valid but excluded finding)
59      function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
60        public
61:       initializer

/// @audit (valid but excluded finding)
246:    function getInterest(Stack calldata stack) public view returns (uint256) {

/// @audit (valid but excluded finding)
377:    function ASTARIA_ROUTER() public view returns (IAstariaRouter) {

/// @audit (valid but excluded finding)
550     function getAmountOwingAtLiquidation(ILienToken.Stack calldata stack)
551       public
552       view
553:      returns (uint256)

/// @audit (valid but excluded finding)
577     function getBuyout(Stack calldata stack)
578       public
579       view
580:      returns (uint256 owed, uint256 buyout)

/// @audit (valid but excluded finding)
596     function makePayment(
597       uint256 collateralId,
598       Stack[] calldata stack,
599       uint256 amount
600     )
601       public
602       validateStack(collateralId, stack)
603:      returns (Stack[] memory newStack)

/// @audit (valid but excluded finding)
706     function getMaxPotentialDebtForCollateral(Stack[] memory stack)
707       public
708       view
709       validateStack(stack[0].lien.collateralId, stack)
710:      returns (uint256 maxPotentialDebt)

/// @audit (valid but excluded finding)
727     function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
728       public
729       view
730       validateStack(stack[0].lien.collateralId, stack)
731:      returns (uint256 maxPotentialDebt)

/// @audit (valid but excluded finding)
893:    function getPayee(uint256 lienId) public view returns (address) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L59-L61

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
192:    function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {

/// @audit (valid but excluded finding)
196:    function getCurrentEpoch() public view returns (uint64) {

/// @audit (valid but excluded finding)
200:    function getSlope() public view returns (uint256) {

/// @audit (valid but excluded finding)
204:    function getWithdrawReserve() public view returns (uint256) {

/// @audit (valid but excluded finding)
208:    function getLiquidationWithdrawRatio() public view returns (uint256) {

/// @audit (valid but excluded finding)
212:    function getYIntercept() public view returns (uint256) {

/// @audit (valid but excluded finding)
461:    function accrue() public returns (uint256) {

/// @audit (valid but excluded finding)
534:    function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {

/// @audit (valid but excluded finding)
552:    function getEpochEnd(uint256 epoch) public pure returns (uint64) {

/// @audit (valid but excluded finding)
562:    function afterPayment(uint256 computedSlope) public onlyLienToken {

/// @audit (valid but excluded finding)
615     function handleBuyoutLien(BuyoutLienParams calldata params)
616       public
617:      onlyLienToken

/// @audit (valid but excluded finding)
640     function updateVaultAfterLiquidation(
641       uint256 maxAuctionWindow,
642       AfterLiquidationParams calldata params
643:    ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {

/// @audit (valid but excluded finding)
669:    function increaseYIntercept(uint256 amount) public {

/// @audit (valid but excluded finding)
684:    function decreaseYIntercept(uint256 amount) public {

/// @audit (valid but excluded finding)
722:    function timeToSecondEpochEnd() public view returns (uint256) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L192

```solidity
File: src/WithdrawProxy.sol

/// @audit (valid but excluded finding)
215:    function getFinalAuctionEnd() public view returns (uint256) {

/// @audit (valid but excluded finding)
220:    function getWithdrawRatio() public view returns (uint256) {

/// @audit (valid but excluded finding)
225:    function getExpected() public view returns (uint256) {

/// @audit (valid but excluded finding)
240     function claim() public {
241:      WPStorage storage s = _loadSlot();

/// @audit (valid but excluded finding)
289     function drain(uint256 amount, address withdrawProxy)
290       public
291       onlyVault
292:      returns (uint256)

/// @audit (valid but excluded finding)
302:    function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {

/// @audit (valid but excluded finding)
306     function handleNewLiquidation(
307       uint256 newLienExpectedValue,
308       uint256 finalAuctionDelta
309:    ) public onlyVault {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L215

### [N&#x2011;03]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 21 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit 130 - (valid but excluded finding)
110:      s.liquidationFeeNumerator = uint32(130);

/// @audit 1e15 - (valid but excluded finding)
/// @audit 5 - (valid but excluded finding)
112:      s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));

/// @audit 45 - (valid but excluded finding)
114:      s.maxEpochLength = uint32(45 days);

/// @audit 1e16 - (valid but excluded finding)
/// @audit 200 - (valid but excluded finding)
115:      s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L110

```solidity
File: src/AstariaVaultBase.sol

/// @audit 20 - (valid but excluded finding)
33:       return _getArgUint8(20); //ends at 21

/// @audit 21 - (valid but excluded finding)
37:       return _getArgAddress(21); //ends at 44

/// @audit 41 - (valid but excluded finding)
47:       return _getArgAddress(41); //ends at 64

/// @audit 61 - (valid but excluded finding)
51:       return _getArgUint256(61);

/// @audit 93 - (valid but excluded finding)
55:       return _getArgUint256(93); //ends at 116

/// @audit 125 - (valid but excluded finding)
59:       return _getArgUint256(125);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaVaultBase.sol#L33

```solidity
File: src/BeaconProxy.sol

/// @audit 20 - (valid but excluded finding)
62:       _delegate(_getBeacon().getImpl(_getArgUint8(20)));

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/BeaconProxy.sol#L62

```solidity
File: src/ClearingHouse.sol

/// @audit 21 - (valid but excluded finding)
48:       return _getArgUint256(21);

/// @audit 20 - (valid but excluded finding)
52:       return _getArgUint8(20);

/// @audit 21 - (valid but excluded finding)
136:      uint256 collateralId = _getArgUint256(21);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L48

```solidity
File: src/PublicVault.sol

/// @audit 18 - (valid but excluded finding)
103:      if (ERC20(asset()).decimals() == uint8(18)) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L103

```solidity
File: src/WithdrawVaultBase.sol

/// @audit 20 - (valid but excluded finding)
31:       return _getArgUint8(20);

/// @audit 21 - (valid but excluded finding)
35:       return _getArgAddress(21);

/// @audit 41 - (valid but excluded finding)
39:       return _getArgAddress(41);

/// @audit 61 - (valid but excluded finding)
43:       return _getArgUint64(61);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawVaultBase.sol#L31

### [N&#x2011;04]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 32 instances of this issue:*

```solidity
File: src/interfaces/IAstariaRouter.sol

/// @audit (valid but excluded finding)
54:     event FileUpdated(FileType what, bytes data);

/// @audit (valid but excluded finding)
294:    event Liquidation(uint256 collateralId, uint256 position);

/// @audit (valid but excluded finding)
295     event NewVault(
296       address strategist,
297       address delegate,
298       address vault,
299       uint8 vaultType
300:    );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L54

```solidity
File: src/interfaces/ICollateralToken.sol

/// @audit (valid but excluded finding)
32:     event ListedOnSeaport(uint256 collateralId, Order listingOrder);

/// @audit (valid but excluded finding)
33:     event FileUpdated(FileType what, bytes data);

/// @audit (valid but excluded finding)
40      event ReleaseTo(
41        address indexed underlyingAsset,
42        uint256 assetId,
43        address indexed to
44:     );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L32

```solidity
File: src/interfaces/IERC1155.sol

/// @audit (valid but excluded finding)
42      event ApprovalForAll(
43        address indexed account,
44        address indexed operator,
45        bool approved
46:     );

/// @audit (valid but excluded finding)
55:     event URI(string value, uint256 indexed id);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC1155.sol#L42-L46

```solidity
File: src/interfaces/IERC20.sol

/// @audit (valid but excluded finding)
16:     event Transfer(address indexed from, address indexed to, uint256 value);

/// @audit (valid but excluded finding)
22:     event Approval(address indexed owner, address indexed spender, uint256 value);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC20.sol#L16

```solidity
File: src/interfaces/IERC4626.sol

/// @audit (valid but excluded finding)
15      event Deposit(
16        address indexed sender,
17        address indexed owner,
18        uint256 assets,
19        uint256 shares
20:     );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC4626.sol#L15-L20

```solidity
File: src/interfaces/IERC721.sol

/// @audit (valid but excluded finding)
14      event ApprovalForAll(
15        address indexed owner,
16        address indexed operator,
17        bool approved
18:     );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IERC721.sol#L14-L18

```solidity
File: src/interfaces/ILienToken.sol

/// @audit (valid but excluded finding)
34:     event FileUpdated(FileType what, bytes data);

/// @audit (valid but excluded finding)
294     event AddLien(
295       uint256 indexed collateralId,
296       uint8 position,
297       uint256 indexed lienId,
298       Stack stack
299:    );

/// @audit (valid but excluded finding)
306     event LienStackUpdated(
307       uint256 indexed collateralId,
308       uint8 position,
309       StackAction action,
310       uint8 stackLength
311:    );

/// @audit (valid but excluded finding)
313:    event Payment(uint256 indexed lienId, uint256 amount);

/// @audit (valid but excluded finding)
314:    event BuyoutLien(address indexed buyer, uint256 lienId, uint256 buyout);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ILienToken.sol#L34

```solidity
File: src/interfaces/IPublicVault.sol

/// @audit (valid but excluded finding)
168:    event StrategistFee(uint88 feeInShares);

/// @audit (valid but excluded finding)
169:    event LiensOpenForEpochRemaining(uint64 epoch, uint256 liensOpenForEpoch);

/// @audit (valid but excluded finding)
170:    event YInterceptChanged(uint88 newYintercept);

/// @audit (valid but excluded finding)
171:    event WithdrawReserveTransferred(uint256 amount);

/// @audit (valid but excluded finding)
172:    event LienOpen(uint256 lienId, uint256 epoch);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IPublicVault.sol#L168

```solidity
File: src/interfaces/IV3PositionManager.sol

/// @audit (valid but excluded finding)
10      event IncreaseLiquidity(
11        uint256 indexed tokenId,
12        uint128 liquidity,
13        uint256 amount0,
14        uint256 amount1
15:     );

/// @audit (valid but excluded finding)
21      event DecreaseLiquidity(
22        uint256 indexed tokenId,
23        uint128 liquidity,
24        uint256 amount0,
25        uint256 amount1
26:     );

/// @audit (valid but excluded finding)
33      event Collect(
34        uint256 indexed tokenId,
35        address recipient,
36        uint256 amount0,
37        uint256 amount1
38:     );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IV3PositionManager.sol#L10-L15

```solidity
File: src/interfaces/IVaultImplementation.sol

/// @audit (valid but excluded finding)
52:     event AllowListUpdated(address, bool);

/// @audit (valid but excluded finding)
54:     event AllowListEnabled(bool);

/// @audit (valid but excluded finding)
56:     event DelegateUpdated(address);

/// @audit (valid but excluded finding)
58:     event NonceUpdated(uint256 nonce);

/// @audit (valid but excluded finding)
60:     event IncrementNonce(uint256 nonce);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IVaultImplementation.sol#L52

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
459:    event SlopeUpdated(uint48 newSlope);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L459

```solidity
File: src/WithdrawProxy.sol

/// @audit (valid but excluded finding)
42      event Claimed(
43        address withdrawProxy,
44        uint256 withdrawProxyAmount,
45        address publicVault,
46        uint256 publicVaultAmount
47:     );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L42-L47


