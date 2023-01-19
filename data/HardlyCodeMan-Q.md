## 2023-01-astaria Low Severity Report 

### Use of block.timestamp is still vulnerable in PoS

[SWC-116](https://swcregistry.io/docs/SWC-116) demonstrates how block.timestamp is gamable and front runable by maliciouos miners in a PoW EVM. I have confirmed with [Sigma Prime](https://sigmaprime.io/) [Lighthouse](https://github.com/sigp/lighthouse) developers that ETH2 PoS EVM is still vulnerable to a similar attack vector.

PoS utilises blocks split into 32 by 12 second slots, each slot is sent to validators for block proposals. Should a valadator choose, they may skip a validation slot to which they have been assigned. Should a validator be assigned multiple consecutive slots, block.timestamp becomes vulnerable in a similar way through manipulation of slot contents, allowing for front running and other such attacks potentially affecting legitimate client calls for liquidation, withdrawals, epoch calculation and auctions.

Given the following example, a validator could have block proposals for slot1 and slot2, skip slot1 and insert their own tx into slot2.
```
Block 123456
slot00 = + 0 seconds
slot01 = + 12 seconds
slot02 = + 24 seconds
...
slot32 = + 372 seconds
```

### Uses of ```block.timestamp```: 32 results - 6 files

#### [src/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol):
- [439](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L439):     if (block.timestamp > commitment.lienRequest.strategy.deadline) {
- [617](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L617):     return (stack.point.end <= block.timestamp || 
- [698](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L698):     newLien.details.duration + block.timestamp >=
- [700](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L700):     (block.timestamp + newLien.details.duration - stack[position].point.end >=
- [738](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L738):     block.timestamp,

#### [src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol):
- [132](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L132):     if (block.timestamp < params.endTime) {
- [468](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L468):     startTime: uint256(block.timestamp),
- [469](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L469):     endTime: uint256(block.timestamp + maxDuration),

#### [src/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol):
- [112](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L112):     if (block.timestamp >= params.encumber.stack[params.position].point.end) {
- [156](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L156):     if (block.timestamp >= params.encumber.stack[j].point.end) {
- [247](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L247):     return _getInterest(stack, block.timestamp);
- [309](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L309):     uint88 owed = _getOwed(stack[i], block.timestamp);
- [335](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L335):     auctionData.startTime = block.timestamp.safeCastTo48();
- [336](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L336):     auctionData.endTime = (block.timestamp + auctionWindow).safeCastTo48();
- [452](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L452):     last: block.timestamp.safeCastTo40(),
- [453](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L453):     end: (block.timestamp + params.lien.details.duration).safeCastTo40()
- [475](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L475):     if (block.timestamp >= newStack[j].point.end) {
- [590](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L590):     owed = _getOwed(stack, block.timestamp);
- [744](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L744):     return _getOwed(stack, block.timestamp);
- [780](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L780):     uint256 delta_t = stack.point.end - block.timestamp;
- [805](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L805):     if (block.timestamp >= end) {
- [808](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L808):     uint256 owed = _getOwed(stack, block.timestamp);
- [827](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L827):     stack.point.last = block.timestamp.safeCastTo40();

#### [src/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol):
- [468](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L468):     s.last = block.timestamp.safeCastTo40();
- [491](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L491):     uint256 delta_t = block.timestamp - s.last;
- [625](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L625):     s.last = block.timestamp.safeCastTo40();
- [706](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L706):     if (block.timestamp >= epochEnd) {
- [710](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L710):     return epochEnd - block.timestamp;

#### [src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol):
- [250](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L250):     if (block.timestamp < s.finalAuctionEnd) {
- [314](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L314):     uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();


## 2023-01-astaria Quality Assurance Report

### ABIEncoverV2
ABIEncoderV2 enabled by default in pragma ^0.8.0 therefore not required to be explicitly calleding. [Solidity v0.8.0 Breaking Changes - ABIEncoderV2](https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html?highlight=ABIEncoderV2#silent-changes-of-the-semantics)

2 instances:
- [CollateralToken.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L16)
- [LienToken.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L16)
