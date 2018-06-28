---
title: ERC-20 Token crowdsale standard
author: Frank Bonnet <?>,
type: ?
category: ?
status: Final
created: 28/06/2018
---

## Simple Summary

This is a standard interface to manage the crowdsale of an ERC-20 token on the Ethereum blockchain. This interface can be used by any venture(also non-crypto related ventures) to raise funds in a decentralized manner on the Ethereum blockchain.


## Description
This crowdsale mechanism allows the initiator of the crowdsale (also called the beneficiary) to make use of two distinct stages: a presale stage and a main sale stage. There are a few important distinctions between a presale stage and main sale stage.
The purpose of the presale is to ….. The ether that is contributed during the presale stage is directly accessible to the beneficiary of the crowdsale and is used for…. Investors that contribute during the presale get a higher rate of tokens than the investors that contribute during the main sale. However, the contributions that are made during the presale stage are non-refundable. This means that if the presale does not raise the envisioned amount, investors during the presale phase will not get their ether back. 
The purpose of the main sale is to …… The ether that is contributed during the main sale stage is accessible to the beneficiary after the crowdsale and only when the main sale was successful. The ether that is raised during the main sale is used for… The investors that contribute during the main sale stage get a lower rate of tokens than the investors that contribute during the presale. However, their contributions are refundable in the case of a failed main sale. If the main sale does not raise the envisioned minimum amount, the main sale contributors will be refunded their complete funds (minus the transaction fees). 

This standard interface also allows the initiator to incentivize investors to contribute early on during the main sale. It does so by giving the initiator the possibility to assign different conversion rates to different timeframes within the main sale. For example, investors that contribute during the first 24 hours of the main sale could be rewarded with an allocation of 20% bonus tokens. Investors that contribute during the first week could for example be rewarded with an allocation of 10% bonus tokens, while the investors that contribute after the first week are not rewarded with any bonus tokens. 
Maybe explain here the mechanism that prevents early investors from dumping their tokens?    

This standard interface also allows the initiator of the crowdsale to incentivize investors to contribute more (as much as possible?). It does so by giving the initiator the possibility to assign different volume multipliers to different investment amounts. The initiator can distinguish between different categories of investment amounts. For example, investors that contribute more than 100 ether could be rewarded with an allocation of 20% bonus tokens. Investors that contribute more than 20 ether could be rewarded with an allocation of 10% bonus tokens, while the investors that contribute less than 20 ether are not rewarded with any bonus tokens.


## Specification

## Crowdsale
### Crowdsale details

#### baseRate

This integer shows the conversion rate from wei to tokens without any bonuses.

``` js
uint public baseRate 
```



#### minAmount

This integer shows the minimum amount of ether that has to be received during the main sale in order for the main sale to be considered successful. 

``` js
uint public minAmount 
```



#### maxAmount

This integer shows the maximum amount of ether that can be received during the main sale. If this amount is reached, the crowdsale will stop.

``` js
uint public maxAmount 
```



#### minAcceptedAmount

What’s the difference between minAcceptedAmount and minAmount?

``` js
uint public minAcceptedAmount 
```



#### minAmountPresale

This integer shows the minimum amount of ether that has to be received during the presale in order for the presale to be considered successful.

``` js
uint public minAmountPresale 
```


#### maxAmountPresale

This integer shows the maximum amount of ether that can be received during the presale. If this amount is reached, the presale will stop.

``` js
uint public maxAmountPresale 
```



#### minAcceptedAmountPresale

What’s the difference between minAcceptedAmountPresale and minAmountPresale?

``` js
uint public minAcceptedAmountPresale 
```


#### beneficiary

This address shows the address of the initiator of the crowdsale. Contributions to the crowdsale have to be sent here.

``` js
address public beneficiary 
```



#### start

This integer shows the timestamp of the start date of the crowdsale.

``` js
uint public start 
```


#### presaleEnd

I’m not sure what this integer shows. Does it show when the presale ends? Or IF the presale has ended?

``` js
uint public presaleEnd 
```

#### crowdsaleEnd

I’m not sure what this integer shows. Does it show when the crowdsale ends? Or IF the crowdsale has ended?

``` js
uint public crowdsaleEnd 
```



#### raised

I’m not sure what the difference is with allocatedEth.

``` js
uint public raised 
```



#### allocatedEth

I’m not sure what the difference is with raised.

``` js
uint public allocatedEth 
```

#### allocatedTokens

This integer shows the amount of tokens that have been allocated to contributors of the crowdsale so far. Note: to prevent whales… By distributing the tokens at a later date, you minimize the risk for the smaller investor.

``` js
uint public allocatedTokens 
```



#### stage

I am not sure what to do with this.

``` js
Stages public stage 
```


### Crowdsale deployment



#### isInPresalePhase

This function shows if the crowdsale contract is currently in the presale phase or not. 

``` js
function isInPresalePhase() external view returns (bool)
```



#### isEnded

This function shows if the crowdsale contract has ended or not.

``` js
function isEnded() external view returns (bool)
```



#### getReleaseDateCount

This function shows the amount of release dates for the beneficiary at which the balances are released.

``` js
function getReleaseDateCount(address _beneficiary) external view returns (uint)
```



#### getReleaseDateAtIndex

I’m not sure how this works.

``` js
function getReleaseDateAtIndex(address _beneficiary, uint64 _index) external view returns (uint64)
```



#### getAllocatedBalanceAtReleaseDate

This shows the allocated token and ether balance of the beneficiary at the release date, which is the date after which the balance can be withdrawn.

``` js
function getAllocatedBalanceAtReleaseDate(address _beneficiary, uint64 _releaseDate) external view returns (uint, uint)
```



#### getAllocatedBalance

This function shows the allocated token and ether balance of the beneficiary without taking the different release dates into account.

``` js
function getAllocatedBalance(address _beneficiary) external view returns (uint, uint)
```



#### getRefundableBalance

This function shows the refundable ether balance of the beneficiary.

``` js
function getRefundableBalance(address _beneficiary) external view returns (uint)
```



#### getCurrentPhase

This function shows the current phase of the crowdsale based on the current time.

``` js
function getCurrentPhase() external view returns (uint)
```



#### getRate

This function shows the rate at which wei will be converted into ERC-20 tokens, based on the phase of the crowdsale and the amount of wei that is contributed. If this shows 0, then it is currently not possible to invest. 

``` js
function getRate(uint _phase, uint _volume) external view returns (uint)
```



#### toTokens

This function converts the amount of wei sent by the contributor into tokens using the `_rate` from the getRate function.

``` js
function toTokens(uint _wei, uint _rate) external view returns (uint)
```



#### 

This function receives the ether from the sender and issues the tokens to the sender in return. 
Note: this function requires that the sender is not a contract. This is required because it’s not possible for a contract to specify a gas amount when calling the (internal) send() function. Solidity imposes a maximum amount of gas (2300 gas at the time of writing). Instead, contracts can call the `contribute` function. 

``` js
function () external payable
```



#### contribute

This function receives the ether from the sender and issues the tokens to the sender in return, in case the sender is a contract. Afterwards, it shows the accepted amount of ether.

``` js
function contribute() external payable returns (uint)
```



#### contributeFor

Does this receive the same amount of either as the two functions above. But instead, does it issue an extra amount of tokens that are allocated for the team holding the crowdsale?

``` function contributeFor(address _beneficiary) external payable returns (uint)
```



#### withdrawTokens

I’m not yet sure what this function does. Where does it withdraw the allocated tokens from?

``` js
function withdrawTokens() external 
```



#### withdrawTokensto

I’m not yet sure what this function does. Where does it withdraw the allocated tokens from? What’s the difference with the previous function?

``` js
function withdrawTokensTo(address _beneficiary) external 
```



#### withdrawEther

I’m not yet sure what this function does. Where does it withdraw the allocated ether from?

``` js
function withdrawEther() external
```



#### withdrawEtherTo

I’m not yet sure what this function does. Where does it withdraw the allocated ether from? What’s the difference with the previous function?

``` js
function withdrawEtherTo(address _beneficiary) external 
```



#### refund

This function refunds the amount of wei that was contributed, in the case of an unsuccessful crowdsale. The crowdsale is considered unsuccessful if minAmount was not raised before the end of the crowdsale.

``` js
function refund() external 
```



#### refundTo

This function refunds the amount of wei that was contributed, in the case of an unsuccessful crowdsale. The crowdsale is considered unsuccessful if minAmount was not raised before the end of the crowdsale. How does this differ from the previous function?

``` js
function refundTo(address _beneficiary) external
```


