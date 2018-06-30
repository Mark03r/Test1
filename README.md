---
title: ERC-20 Token crowdsale standard
author: Frank Bonnet <?> 
type: Standards Track
category: ERC 
status: Final
created: 30/06/2018
---

## Simple Summary

This is a standard interface to manage the crowdsale of an ERC-20 token on the Ethereum blockchain. This interface can be used by any venture (also non-crypto related ventures) to raise funds in a decentralized manner on the Ethereum blockchain.

This crowdsale interface allows the initiator of the crowdsale to make use of two distinct phases: a presale phase and a main sale phase. Furthermore, it allows the initiator of the crowdsale to incentivize contributors to contribute early on and to contribute more during the main sale while minimizing the risk of early exits.


## Description

A presale is a tokensale event that can be used prior to the actual main sale. The ether that is contributed during the presale phase is directly accessible to the initiator of the crowdsale and is often used for marketing and meetups prior to the main sale and to cover the expenses that were incurred in the run-up to the crowdsale. Investors that contribute during the presale often get a higher rate of tokens than the investors that contribute during the main sale. However, the contributions that are made during the presale phase are non-refundable. This means that if the presale does not raise the envisioned amount, investors during the presale phase will not get their contributions back. 

The ether that is contributed during the main sale stage is accessible to the initiator after the crowdsale and only when the crowdsale was successful. The investors that contribute during the main sale stage get a lower rate of tokens than the investors that contribute during the presale. However, their contributions are refundable in the case of a failed crowdsale. If the crowdsale does not raise the envisioned minimum amount, the main sale contributors will be refunded their complete funds (minus the transaction fees).  

This standard interface also allows the initiator to incentivize investors to contribute early on during the main sale. It does so by giving the initiator the possibility to assign different conversion rates to different timeframes within the main sale. For example, investors that contribute during the first 24 hours of the main sale could be rewarded with an allocation of 20% bonus tokens. Investors that contribute during the first week could for example be rewarded with an allocation of 10% bonus tokens, while the investors that contribute after the first week are not rewarded with any bonus tokens. This standard interface includes the possibility to temporarily allocate the bonus tokens and to release them at a later date, to prevent early exits with the bonus tokens.

This standard interface also allows the initiator of the crowdsale to incentivize investors to contribute more. It does so by giving the initiator the possibility to assign different volume multipliers to different investment amounts. The initiator can distinguish between different categories of investment amounts. For example, investors that contribute more than 100 ether could be rewarded with an allocation of 20% bonus tokens. Investors that contribute more than 20 ether could be rewarded with an allocation of 10% bonus tokens, while the investors that contribute less than 20 ether are not rewarded with any bonus tokens. This standard interface includes the possibility to temporarily allocate the bonus tokens and to release them at a later date, to prevent early exits with the bonus tokens.


## Specification

### Crowdsale metrics

#### baseRate

This integer shows the conversion rate from ether to tokens without any bonuses.

``` js
uint public baseRate 
```



#### minAmount

This integer shows the minimum amount of ether that has to be received during the crowdsale in order for the crowdsale to be considered successful. 

Note: the ether that was received during the presale also contributes towards this minimum amount. For example, if the minimum amount of ether is set at 1000 ether and if there has already been 500 ether received during the presale, only 500 ether has to be received during the main sale in order for the crowdsale to be considered successful.

``` js
uint public minAmount 
```



#### maxAmount

This integer shows the maximum amount of ether that can be received during the crowdsale. If this amount is reached, the crowdsale will stop.

``` js
uint public maxAmount 
```



#### minAcceptedAmount

This integer shows the minimum amount that is accepted per individual contribution during the main sale.

``` js
uint public minAcceptedAmount 
```



#### minAmountPresale

This integer shows the minimum amount of ether that has to be received during the presale in order for the presale to be considered successful. If this minimum amount is not received, the crowdsale will not be continued and there will be no main sale. 

``` js
uint public minAmountPresale 
```


#### maxAmountPresale

This integer shows the maximum amount of ether that can be received during the presale. If this amount is reached, the presale will stop.

``` js
uint public maxAmountPresale 
```



#### minAcceptedAmountPresale

This integer shows the minimum amount that is accepted per individual contribution during the presale.

``` js
uint public minAcceptedAmountPresale 
```


#### beneficiary

This is the address of the ‘beneficiary’. 

Note: there are different beneficiaries in different contexts. 
Often, this refers to the address where the ether that is invested will eventually go to. This the address of the initiator of the crowdsale, not the address of the crowdsale contract. 
In other contexts, this most often refers to the address of an individual contributor to the crowdsale. Thus, in these cases the beneficiary is the individual crowdsale investor.

``` js
address public beneficiary 
```



#### start

This integer shows the start date of the crowdsale in a regular calendar date.

``` js
uint public start 
```



#### presaleEnd

This integer shows when the presale ends in a regular calendar date. 

``` js
uint public presaleEnd 
```



#### crowdsaleEnd

This integer shows when the presale ends in a regular calendar date.

``` js
uint public crowdsaleEnd 
```



#### raised

This integer shows the amount of ether that is raised so far during the crowdsale.

``` js
uint public raised 
```



#### allocatedEth

The crowdsale can be set up in such a way that ether can be temporarily allocated/locked up. It can be configured in such a way that the initiator of the crowdsale doesn’t receive all the ether immediately after the crowdsale. For example, ether can be temporarily allocated and be released in four steps with holding periods of three months in between. 

This function shows the total amount of ether that is still allocated/locked up.

``` js
uint public allocatedEth 
```

#### allocatedTokens

The crowdsale can also be set up in such a way that not all tokens are issued and distributed immediately during the crowdsale but that a certain amount will be temporarily allocated/locked up. 
For example, there could be a certain amount of tokens allocated to the team that has initiated the crowdsale. 
Another example would be to temporarily lock up the bonus tokens for the individual investors. Since larger investors might get a significant amount of bonus tokens, they might be incentivized to make an early exit for a small profit. By distributing the bonus tokens at a later date, you can still incentivize large investors to invest while minimizing the risk that these investors will make an early exit. 

This integer shows all the tokens that have not directly been issued during the crowdsale and that have currently been allocated/locked up. 

``` js
uint public allocatedTokens 
```



### Crowdsale deployment


#### stage

The stages are the different steps that the crowdsale goes through and can be seen as the internal states of the crowdsale. This is used by the rest of the code to exclude certain functions at certain stages. There are four different possible stages for the crowdsale. 

From the beginning, the crowdsale is in the `Deploying` stage. This means that the configuration of the crowdsale can still be adjusted. When the configuration of the crowdsale is complete (e.g. the whitelist is added, the bonus percentages are configured etc.) the crowdsale goes from the `Deploying` to the `Deployed` stage. Next, the beneficiary has to start the crowdsale. At the same time, the beneficiary has to prove that it is able to sign transactions. This is to prevent that there has been a mistake and that the beneficiary actually doesn’t have access to the crowdsale address. When the beneficiary starts the crowdsale and proves that it is able to sign transactions, the crowdsale goes from the `Deployed` stage to the `InProgress` stage. The last stage is when the crowdsale has ended. Here, the crowdsale goes from the `InProgress` stage to the `Ended` stage. 

``` js
Stages public stage 
```



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

This function shows the amount of release dates at which the balances are released to the beneficiary. Here, the beneficiary is the account that the balances are allocated for, which can both be the initiator of the crowdsale or an individual contributor to the crowdsale. 

``` js
function getReleaseDateCount(address _beneficiary) external view returns (uint)
```



#### getReleaseDateAtIndex

This functions shows the date at which a balance is released to the beneficiary, based on the values from `getReleaseDateCount`. This converts the index from `getReleaseDateCount` into a regular calendar date.

``` js
function getReleaseDateAtIndex(address _beneficiary, uint64 _index) external view returns (uint64)
```



#### getAllocatedBalanceAtReleaseDate

This shows the allocated token and ether balance of the beneficiary at a particular release date. Here, the beneficiary is the account that the balances are allocated for, which can both be the initiator of the crowdsale or an individual contributor to the crowdsale.

``` js
function getAllocatedBalanceAtReleaseDate(address _beneficiary, uint64 _releaseDate) external view returns (uint, uint)
```



#### getAllocatedBalance

This function shows the total amount of allocated tokens and ether of the beneficiary without taking the different release dates into account. Again, the beneficiary is the account that the balances are allocated for, which can both be the initiator of the crowdsale or an individual contributor to the crowdsale.

``` js
function getAllocatedBalance(address _beneficiary) external view returns (uint, uint)
```



#### getRefundableBalance

This function shows the refundable ether balance of the beneficiary, where the beneficiary is the individual contributor to the crowdsale.

``` js
function getRefundableBalance(address _beneficiary) external view returns (uint)
```



#### getCurrentPhase

This function shows the current phase of the crowdsale based on the current time. As mentioned in the ‘Description’ above, this function allows the initiator to incentivize investors to contribute early on during the main sale. It does so by giving the initiator the possibility to assign different conversion rates to different phases within the crowdsale. 

``` js
function getCurrentPhase() external view returns (uint)
```



#### getRate

This function shows the rate at which ether will be converted into tokens, based on the phase of the crowdsale and the amount of ether that is contributed. If this shows 0, then it is currently not possible to invest. 

``` js
function getRate(uint _phase, uint _volume) external view returns (uint)
```



#### toTokens

This function converts the amount of ether sent by the contributor into tokens using the `_rate` from the `getRate` function.

``` js
function toTokens(uint _wei, uint _rate) external view returns (uint)
```



#### ()

This function receives the ether from the sender (a contributor to the crowdsale) and issues the tokens to the sender in return. 

Note: this function requires that the sender is not a contract. This is required because it’s not possible for a contract to specify a gas amount when calling the (internal) send() function. Solidity imposes a maximum amount of gas (2300 gas at the time of writing). Instead, contracts can call the `contribute` function. 

``` js
Function () external payable
```



#### contribute

This function receives the ether from the sender and issues the tokens to the sender in return, in case the sender is a contract. Afterwards, it shows the accepted amount of ether.

``` js
function contribute() external payable returns (uint)
```



#### contributeFor

This function allows someone to invest on behalf of someone else. Here, the ether is received from the sender, but the tokens are issued to a different address (the beneficiary). 
This can for example be used by a smart contract that invests on behalf of a group of investors. If 100 investors decide to invest together through the use of a smart contract, this function makes it possible for the tokens to eventually end up at the 100 individual investors. 

``` js 
function contributeFor(address _beneficiary) external payable returns (uint)
```



#### withdrawTokens

This function withdraws allocated tokens. When this function is executed by an address that has a balance of allocated tokens that is larger than 0, the tokens are created and the amount is subtracted from the allocated balance for that address. Afterwards, the tokens are sent to the address that has executed the `withdrawTokens` function.  

``` js
function withdrawTokens() external 
```



#### withdrawTokensTo

This function allows someone to withdraws allocated tokens on behalf of someone else. Here, the tokens are issued to a different address (the beneficiary) than to the address that executes the ‘withdrawTokensTo` function. This for example enables smart contracts to interact with the crowdsale and invest on behalf of a large group of investors. 

``` js
function withdrawTokensTo(address _beneficiary) external 
```



#### withdrawEther

This function withdraws ether. When this function is executed by an address that has a balance of allocated ether that is larger than 0, the ether is subtracted from the allocated balance for that address and sent to the address that has executed the `withdrawTokens` function.  

``` js
function withdrawEther() external
```



#### withdrawEtherTo

This function allows someone to withdraw allocated ether on behalf of someone else. Here, the ether is sent to a different address (the beneficiary) than to the address that executes the ‘withdrawTokensTo` function. This for example enables smart contracts to interact with the crowdsale and invest on behalf of a large group of investors. 

``` js
function withdrawEtherTo(address _beneficiary) external 
```



#### refund

Investors that have contributed during the main sale and that have a refundable balance can execute this function if the crowdsale was unsuccessful. The crowdsale is considered unsuccessful if  the minimum amount was not raised before the end of the crowdsale. 

This function refunds the amount of ether that was contributed (in the case of an unsuccessful crowdsale) and has to be executed by the same address as the address that the contribution was made from.

``` js
function refund() external 
```



#### refundTo

Investors that have contributed during the main sale and that have a refundable balance can execute this function if the crowdsale was unsuccessful. The crowdsale is considered unsuccessful if  the minimum amount was not raised before the end of the crowdsale. 

This function enables someone to refund the amount of ether that was contributed (in the case of an unsuccessful crowdsale) and send the refund to a different address. This function still has to be executed by the same address as the address that the contribution was made from.
This for example enables smart contracts to interact with the crowdsale and invest on behalf of a large group of investors.

``` js
function refundTo(address _beneficiary) external
```


