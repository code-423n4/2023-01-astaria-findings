## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Add to `blacklist` function | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use of `ecrecover` is susceptible to signature malleability | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Init functions are susceptible to front-running | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Use `_safeMint` instead of `_mint` | 4 |
| [LOW&#x2011;5](#LOW&#x2011;5) | The Contract Should `approve(0) ` First | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Missing Checks for Address(0x0)  | 7 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Pragma Experimental ABIEncoderV2 is Deprecated | 2 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Protect your NFT from copying in POW forks | 2 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Remove unused code | 2 |
| [LOW&#x2011;10](#LOW&#x2011;10) | Use `safetransfer` Instead Of `transfer`  | 1 |
| [LOW&#x2011;11](#LOW&#x2011;11) | Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project | 2 |
| [LOW&#x2011;12](#LOW&#x2011;12) | Stack too deep  | 1 |

Total: 24 contexts over 12 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 9 |
| [NC&#x2011;2](#NC&#x2011;2) | Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 3 |
| [NC&#x2011;4](#NC&#x2011;4) | Critical Changes Should Use Two-step Procedure | 9 |
| [NC&#x2011;5](#NC&#x2011;5) | Event emit should emit a parameter | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | Event Is Missing Indexed Fields | 10 |
| [NC&#x2011;7](#NC&#x2011;7) | Function writing that does not comply with the Solidity Style Guide  | 34 |
| [NC&#x2011;8](#NC&#x2011;8) | Use `delete` to Clear Variables | 5 | 
| [NC&#x2011;9](#NC&#x2011;9) | NatSpec return parameters should be included in contracts | All in-scope contracts |
| [NC&#x2011;10](#NC&#x2011;10) | No need to initialize uints to zero | 3 |
| [NC&#x2011;11](#NC&#x2011;11) | Lines are too long | 1 |
| [NC&#x2011;12](#NC&#x2011;12) | Missing event for critical parameter change | 8 |
| [NC&#x2011;13](#NC&#x2011;13) | Implementation contract may not be initialized | 4 |
| [NC&#x2011;14](#NC&#x2011;14) | NatSpec comments should be increased in contracts | All in-scope contracts |
| [NC&#x2011;15](#NC&#x2011;15) | Omissions in Events | 2 |
| [NC&#x2011;16](#NC&#x2011;16) | Public Functions Not Called By The Contract Should Be Declared External Instead | 1 |
| [NC&#x2011;17](#NC&#x2011;17) | Redundant Cast | 1 |
| [NC&#x2011;18](#NC&#x2011;18) | Large multiples of ten should use scientific notation | 1 |
| [NC&#x2011;19](#NC&#x2011;19) | Use `bytes.concat()` | 10 |
| [NC&#x2011;20](#NC&#x2011;20) | Use of Block.Timestamp | 8 |
| [NC&#x2011;21](#NC&#x2011;21) | Use Underscores for Number Literals  | 1 |

Total: 205 contexts over 21 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Add to `blacklist` function

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```


#### <ins>Recommended Mitigation Steps</ins>
Add to Blacklist function and modifier.





### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use of `ecrecover` is susceptible to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  https://swcregistry.io/docs/SWC-117,  https://swcregistry.io/docs/SWC-121, and  https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### <ins>Proof Of Concept</ins>


```solidity
246: function _validateCommitment(
    IAstariaRouter.Commitment calldata params,
    address receiver
  ) internal view {
    uint256 collateralId = params.tokenContract.computeId(params.tokenId);
    ERC721 CT = ERC721(address(COLLATERAL_TOKEN()));
    address holder = CT.ownerOf(collateralId);
    address operator = CT.getApproved(collateralId);
    if (
      msg.sender != holder &&
      receiver != holder &&
      receiver != operator &&
      !CT.isApprovedForAll(holder, msg.sender)
    ) {
      revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
    }
    VIData storage s = _loadVISlot();
    address recovered = ecrecover(
      keccak256(
        _encodeStrategyData(
          s,
          params.lienRequest.strategy,
          params.lienRequest.merkle.root
        )
      ),
      params.lienRequest.v,
      params.lienRequest.r,
      params.lienRequest.s
    );
    if (
      (recovered != owner() && recovered != s.delegate) ||
      recovered == address(0)
    ) {
      revert IVaultImplementation.InvalidRequest(
        InvalidRequestReason.INVALID_SIGNATURE
      );
    }
  }
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L246



#### Recommended Mitigation Steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L138-L149




### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Init functions are susceptible to front-running

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: code-423n4/2021-09-sushimiso-findings#64)

#### <ins>Proof Of Concept</ins>


```solidity
function init(InitParams calldata params) external virtual {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L190



#### <ins>Recommended Mitigation Steps</ins>

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enforce prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.




### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
588: _mint(from_, collateralId);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L588

```solidity
455: _mint(params.receiver, newLienId);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L455

```solidity
512: _mint(msg.sender, unclaimed);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L512

```solidity
139: _mint(receiver, shares);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L139



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> The Contract Should `approve(0)` First

Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

#### <ins>Proof Of Concept</ins>


```solidity
203: ERC721(order.parameters.offer[0].token).approve(
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L203



#### <ins>Recommended Mitigation Steps</ins>

Approve with a zero amount first before setting the actual amount.



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```solidity
88: function initialize: address _VAULT_IMPL
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L88

```solidity
89: function initialize: address _SOLO_IMPL
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L89

```solidity
90: function initialize: address _WITHDRAW_IMPL
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L90

```solidity
91: function initialize: address _BEACON_PROXY_IMPL
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L91

```solidity
92: function initialize: address _CLEARING_HOUSE_IMPL
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L92

```solidity
339: function setNewGuardian: address _guardian
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L339

```solidity
210: function setDelegate: address delegate_
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L210



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Pragma Experimental ABIEncoderV2 is Deprecated

#### <ins>Proof Of Concept</ins>


```solidity
pragma experimental ABIEncoderV2
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L16

```solidity
pragma experimental ABIEncoderV2
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L16



#### <ins>Recommended Mitigation Steps</ins>

Use pragma abicoder v2 instead.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Protect your NFT from copying in POW forks
Ethereum has performed the long-awaited "merge" that will dramatically reduce the environmental impact of the network

There may be forked versions of Ethereum, which could cause confusion and lead to scams as duplicated NFT assets enter the market.

If the Ethereum Merge, which took place in September 2022, results in the Blockchain splitting into two Blockchains due to the 'THE DAO' attack in 2016, this could result in duplication of immutable tokens (NFTs).

In any case, duplicate NFTs will exist due to the ETH proof-of-work chain and other potential forks, and there’s likely to be some level of confusion around which assets are 'official' or 'authentic.'

Even so, there could be a frenzy for these copies, as NFT owners attempt to flip the proof-of-work versions of their valuable tokens.

As ETHPOW and any other forks spin off of the Ethereum mainnet, they will yield duplicate versions of Ethereum’s NFTs. An NFT is simply a blockchain token, and it can work as a deed of ownership to digital items like artwork and collectibles. A forked Ethereum chain will thus have duplicated deeds that point to the same tokenURI

About Merge Replay Attack: https://twitter.com/elerium115/status/1558471934924431363?s=20&t=RRheaYJwo-GmSnePwofgag

#### <ins>Proof Of Concept</ins>


```solidity
401: function tokenURI(uint256 collateralId)
    public
    view
    virtual
    override(ERC721, IERC721)
    returns (string memory)
  {

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L401

```solidity
348: function tokenURI(uint256 tokenId)
    public
    view
    override(ERC721, IERC721)
    returns (string memory)
  {

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L348



#### <ins>Recommended Mitigation Steps</ins>

Add the following check:
```solidity
if(block.chainid != 1) { 
    revert(); 
}
```



### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Remove unused code
This code is not used in the main project contract files, remove it or add event-emit
Code that is not in use, suggests that they should not be present and could potentially contain insecure functionalities.

#### <ins>Proof Of Concept</ins>


```solidity
function computeDomainSeparator
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L271

```solidity
function afterDeposit
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L575




### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Use `safetransfer` Instead Of `transfer`

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransfer`/`safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

For example, Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

#### <ins>Proof Of Concept</ins>


```solidity
374: super.transferFrom(from, to, id);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L374



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeTransfer`/`safeTransferFrom` or `require()` consistently.



### <a href="#Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project

The `owner` role has a single point of failure and `onlyOwner` can use critical a few functions.

`owner` role in the project:
Owner is not behind a multisig and changes are not behind a timelock.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.

#### Impact
Hacked owner or malicious owner can immediately use critical functions in the project.

#### <ins>Proof Of Concept</ins>

```solidity
270: function flashAction(
    IFlashAction receiver,
    uint256 collateralId,
    bytes calldata data
  ) external onlyOwner(collateralId) {

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L270

```solidity
331: function releaseToAddress(uint256 collateralId, address releaseTo)
    public
    releaseCheck(collateralId)
    onlyOwner(collateralId)
  {

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L331



#### <ins>Recommended Mitigation Steps</ins>
Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.
Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Allow only multi-signature wallets to call the function to reduce the likelihood of an attack.

https://twitter.com/danielvf/status/1572963475101556738?s=20&t=V1kvzfJlsx-D2hfnG0OmuQ

#### Reference
The status quo regarding significant centralization vectors has always been to award M severity, in order to warn users of the protocol of this category of risks. See <a href="https://gist.github.com/GalloDaSballo/881e7a45ac14481519fb88f34fdb8837">here</a> for list of centralization issues previously judged.


### <a href="#Summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> Stack too deep

When attempting to run `forge coverage`, the compilers runs into a `Stack too deep` compiler error.

```
CompilerError: Stack too deep. Try compiling with `--via-ir` (cli) or the equivalent `viaIR: true` (standard JSON) while enabling the optimizer. Otherwise, try removing local variables.
   --> lib/seaport/contracts/lib/FulfillmentApplier.sol:223:9:
    |
223 |         assembly {
    |         ^ (Relevant source part starts here and spans across multiple lines).
```


## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to the following functions:

#### <ins>Proof Of Concept</ins>


```solidity
339: function setNewGuardian(address _guardian) external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L339

```solidity
66: function setAuctionData(ILienToken.AuctionData calldata auctionData)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L66

```solidity
104: function setApprovalForAll(address operator, bool approved) external {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L104

```solidity
220: function settleLiquidatorNFTClaim() external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L220

```solidity
524: function settleAuction(uint256 collateralId) public {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L524

```solidity
210: function setDelegate(address delegate_) external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L210

```solidity
302: function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L302

```solidity
266: function setNewGuardian(address _guardian) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L266

```solidity
139: function settleAuction(uint256 collateralId) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L139







### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables

If the variable needs to be different based on which class it comes from, a view/pure function should be used instead.

#### <ins>Proof Of Concept</ins>

```solidity
140: ClearingHouse CH = ClearingHouse(payable(s.clearingHouse[collateralId]));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L140

```solidity
234: ERC721 CT = ERC721(address(COLLATERAL_TOKEN()));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L234







### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```solidity
44: bytes32 public constant STRATEGY_TYPEHASH =
    keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L44

```solidity
47: bytes32 constant EIP_DOMAIN =
    keccak256(
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L47

```solidity
51: bytes32 constant VERSION = keccak256("0");
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L51





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
339: function setNewGuardian(address _guardian) external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L339

```solidity
66: function setAuctionData(ILienToken.AuctionData calldata auctionData)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L66

```solidity
104: function setApprovalForAll(address operator, bool approved) external {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L104

```solidity
220: function settleLiquidatorNFTClaim() external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L220

```solidity
524: function settleAuction(uint256 collateralId) public {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L524

```solidity
210: function setDelegate(address delegate_) external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L210

```solidity
302: function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L302

```solidity
266: function setNewGuardian(address _guardian) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L266

```solidity
139: function settleAuction(uint256 collateralId) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L139



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Event emit should emit a parameter

Some emitted events do not have any emitted parameters. It is recommended to add some parameter such as state changes or value changes when events are emitted

#### <ins>Proof Of Concept</ins>


```solidity
149: emit VaultShutdown()

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L149




### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```solidity
event Claimed(
    address withdrawProxy,
    uint256 withdrawProxyAmount,
    address publicVault,
    uint256 publicVaultAmount
  );
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L42

```solidity
event FileUpdated(FileType what, bytes data);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L54

```solidity
event Liquidation(uint256 collateralId, uint256 position);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L294

```solidity
event NewVault(
    address strategist,
    address delegate,
    address vault,
    uint8 vaultType
  );
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L295

```solidity
event ListedOnSeaport(uint256 collateralId, Order listingOrder);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L32

```solidity
event FileUpdated(FileType what, bytes data);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L33

```solidity
event FileUpdated(FileType what, bytes data);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L34

```solidity
event LiensOpenForEpochRemaining(uint64 epoch, uint256 liensOpenForEpoch);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L169

```solidity
event LienOpen(uint256 lienId, uint256 epoch);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L172

```solidity
event AllowListUpdated(address, bool);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IVaultImplementation.sol#L52






### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

All in-scope contracts




### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

```solidity
308: s.liquidationWithdrawRatio = 0;
327: s.withdrawReserve = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L308

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L327



```solidity
377: s.withdrawReserve = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L377

```solidity
511: s.strategistUnclaimedShares = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L511

```solidity
284: s.finalAuctionEnd = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L284





### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```



### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
152: uint256 potentialDebt = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L152

```solidity
636: uint256 remaining = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L636

```solidity
254: uint256 transferAmount = 0;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L254






### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```solidity
54: // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L54





### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
339: function setNewGuardian(address _guardian) external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L339

```solidity
66: function setAuctionData(ILienToken.AuctionData calldata auctionData)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L66

```solidity
104: function setApprovalForAll(address operator, bool approved) external {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L104

```solidity
220: function settleLiquidatorNFTClaim() external {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L220

```solidity
524: function settleAuction(uint256 collateralId) public {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L524

```solidity
302: function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L302

```solidity
266: function setNewGuardian(address _guardian) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L266

```solidity
139: function settleAuction(uint256 collateralId) external;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L139





### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
70: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L70

```solidity
76: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L76

```solidity
55: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L55

```solidity
24: constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/TransferProxy.sol#L24





### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Use a single file for all system-wide constants

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

#### <ins>Proof Of Concept</ins>


```solidity
459: event SlopeUpdated(uint48 newSlope);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L459

```solidity
56: event DelegateUpdated(address);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IVaultImplementation.sol#L56




#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.



### <a href="#Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```solidity
function pullToken(
    address token,
    uint256 amount,
    address recipient
  ) public payable override {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L203





### <a href="#Summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


```solidity
210: address(token)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L210





### <a href="#Summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

#### <ins>Proof Of Concept</ins>


```solidity
604: uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L604





### <a href="#Summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
733: abi.encodePacked(
        address(this)

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L733

```solidity
569: abi.encodePacked(
            address(s.ASTARIA_ROUTER)

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L569

```solidity
83: return string(abi.encodePacked("AST-Vault-", ERC20(asset()
93: return string(abi.encodePacked("AST-V-", ERC20(asset()
222: abi.encodePacked(
          address(ROUTER()

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L83

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L93

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L222



```solidity
35: return string(abi.encodePacked("AST-Vault-", ERC20(asset()
46: string(abi.encodePacked("AST-V", owner()

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol#L35

https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol#L46



```solidity
187: abi.encodePacked(bytes1(0x19)

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L187

```solidity
110: string(abi.encodePacked("AST-WithdrawVault-", ERC20(asset()
124: string(abi.encodePacked("AST-W", VAULT()

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L110

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L124






#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
439: if (block.timestamp > commitment.lienRequest.strategy.deadline) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L439

```solidity
132: if (block.timestamp < params.endTime) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L132

```solidity
112: if (block.timestamp >= params.encumber.stack[params.position].point.end) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L112

```solidity
156: if (block.timestamp >= params.encumber.stack[j].point.end) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L156

```solidity
475: if (block.timestamp >= newStack[j].point.end) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L475

```solidity
805: if (block.timestamp >= end) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L805

```solidity
706: if (block.timestamp >= epochEnd) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L706

```solidity
250: if (block.timestamp < s.finalAuctionEnd) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L250



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.





### <a href="#Summary">[NC&#x2011;21]</a><a name="NC&#x2011;21"> Use Underscores for Number Literals

#### <ins>Proof Of Concept</ins>


```solidity
604: uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L604






