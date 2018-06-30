---
title: Standard for ERC-20 Token with additional functionalities
author: Frank Bonnet <?>,
type: Standards Track
category: ERC
status: Final
created: 30/06/2018 
---

## Simple Summary

This is a standard interface that adds the following functionalities to the basic ERC-20 token:
-	Locking;
-	Issuing;
-	Burning.


## Description
Note: it is important to recognize that there is a difference between an owner of tokens and the owner(s) of the token contract. The functions described in this interface can only be executed by the owner(s) of the token contract.

With the regular ERC-20 token standard, it is not possible to change certain functions or characteristics after the token has been published However, with this interface the owner of an ERC-20 token contract is able to add additional functionalities that allow them to manage tokens after they have been published. 

This interface enables the owner(s) of an ERC-20 token contract to lock tokens, issue additional tokens or burn tokens after the token has been published. Only the addresses in the list of owners can choose to execute these functions and only under certain conditions that are decided upon ahead of time. 

This interface can for example be used to let the address of a crowdsale contract manage the token contract. Then, the crowdsale address is the only one that is able to unlock, issue or burn tokens. In this example, it could be decided that the token will remain locked until a crowdsale has been completed successfully. Only when the crowdsale has been completed successfully, can the crowdsale contract unlock the token. It is up to the initiator of the crowdsale contract to decide if the crowdsale contract is owned by one or several parties.


## Specification


### IManagedToken 

#### isLocked

This function shows whether the token is locked or not.

``` js
function isLocked() external view returns (bool) 
```



#### lock

This functions locks the token so that the transferring of value is disabled and shows whether the locking was successful or not. 

``` js
function lock() external returns (bool) 
```



#### unlock

This function unlocks the token so that the transferring of value is enabled and shows whether the unlocking was successful or not.

``` js
function unlock() external returns (bool) 
```



#### issue

This function issues `_value` new tokens to the destination address and shows if the tokens were successfully issued or not. 

``` js
function issue(address _to, uint _value) external returns (bool) 
```



#### burn

This function burns tokens from the address `_from` and shows whether the tokens were successfully burned or not.

``` js
function burn(address _from, uint _value) external returns (bool) 
```


