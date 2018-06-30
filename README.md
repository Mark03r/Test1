---
title: ERC-20 Token Standard with alternative `approve` function. 
author: Fabian Vogelsteller <fabian@ethereum.org>, Vitalik Buterin <vitalik.buterin@ethereum.org>, with additions by Frank Bonnet <?> 
type: Standards Track
category: ERC
status: Final
created: 30/06/2018
---

## Simple Summary
This is a standard interface for ERC-20 compatible tokens that is very similar to the original standard interface by Vogelsteller and Buterin. The standard allows for the implementation of a standard API for tokens within smart contracts and provides basic functionality to transfer tokens, as well as allow tokens to be approved so they can be spent by another on-chain third party. However, the original `approve` function in the ERC-20 token standard is relatively susceptible to attack vectors. This token standard attempts to solve this problem by adding an alternative `approve` function. 

## Motivation
The original `approve` function in the ERC-20 token standard is relatively susceptible to attack vectors. This token standard attempts to solve this problem by adding an alternative `approve` function.

## Specification


### IToken 

#### totalSupply

This function shows the total supply of tokens.

``` js
function totalSupply() external view return (uint)
```



#### balanceOf

This function shows the token balance of another account with address `_owner`. 

``` js
function balanceOf(address _owner) external view returns (uint) 
```



#### transfer

This function transfers `_value` tokens from the address of the account that signed the transaction to the address of the recipient. Afterwards it shows if the transaction was successful or not.

``` js
function transfer(address _to, uint _value) external returns (bool)
```



#### transferFrom

This function allows the signer of the transaction to transfer `_value` tokens on behalf of the address `_from` to the address of the recipient (`_to`). This is under the condition that the address `_from` has set aside an allowance for the spender. Afterwards it shows if the transaction was successful or not. 

``` js
function transferFrom(address _from, address _to, uint _value) external returns (bool)
```



#### approve

This function allows `_spender` to withdraw from an account multiple times, up to the `_value’ amount. Afterwards it shows if the approval was successful or not. 

Note: this is the original `approve` function. However, this function is relatively susceptible to attack vectors, like the ones [described here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729)
. 
This is why this standard interface includes an alternative ‘approve’ function. 

``` js
function approve(address _spender, uint _value) external returns (bool) 
```


#### approve

This function allows `_spender` to withdraw from an account multiple times, up to the `_value’ amount. Afterwards it shows if the approval was successful or not. 

Note: this is the alternative `approve` function. This is to prevent an attack vector, which is accomplished by adding an expected value to the function . 

``` js
function approve(address _spender, uint _value, uint _expected) external returns (bool); 
```



#### allowance

This function shows the amount of remaining tokens that `_spender` is still allowed to withdraw from `_owner`.

``` js
function allowance(address _owner, address _spender) external view returns (uint)
```


