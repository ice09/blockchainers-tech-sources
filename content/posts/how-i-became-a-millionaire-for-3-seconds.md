---
title: "How I Became a Millionaire for 3 Seconds"
date: 2020-07-10T10:01:08+02:00
draft: false
---

{{<blockquote author="Doc Brown">}}
...or two, the encounter could create a time paradox, the result of which could cause a chain reaction that would unravel the very fabric of the space-time continuum and destroy the entire universe!  
Granted, that's worst-case scenario. The destruction might in fact be very localized, limited to merely our own galaxy.
{{</blockquote>}}

If you could start a sequence of actions and - at the very end of it - could just cancel it from the beginning as if nothing ever happened, wouldn't that be cool? And if you can be sure, like very sure, as in low-of-nature sure, that what others do in such an action sequence, you will always be able to cancel what they did if it's not what you wanted - would you then borrow someone $1.000.000 if she either
* pays you back $1.000.900 at the end?
* gets reverted all actions in the transaction, including you borrowing her money?

In the strange world of DeFi, some people do exactly this and they are earning riskless money with it. Not much, but a nice "passive income". For just borrowing your money for 13 seconds and getting it back for sure, with or without interest.

# Enter "Flash Loans"

Though this sounds so crazy, it is actually just a side effect of decentralized, transactional Smart Contract programming. It is very easy and, with provably immutable blockchains, very secure.

## Why so cheap?

Usually for lending the _risk_ and the _opportunity cost_ (based on "risk-free" interest rates) determine the interest rate. As there are no opportunity costs with Flash Loans due to contract times in ranges of seconds and even more, no risk of losing any money due to deterministic, immutable logic, the fair rate naturally is very low.

## Really, no Risks?

There are many risks with Flash Loans involved, mainly because of the democritization aspect of it. Now everybody can be a crypto-whale, that has a lot of consequences, many of them [not that nice](https://medium.com/dragonfly-research/flash-loans-why-flash-attacks-will-be-the-new-normal-5144e23ac75a).

But for the user, this is actually really risk-free. The only money that can be lost are interest fees and gas costs.

## Ok, Gimme the Money!

Not so fast, let's do some coding before, it will neither be much nor complicated.
```javascript
contract BalanceLogger {
    address ropstenDAI = 
             0xf80a32a835f79d7787e8a8ee5721d0feafd78108;

    event HasBalanceOf(address _owner, uint _balance);

    function logBalance() public {
        uint balance = ERC20(ropstenDAI).balanceOf(msg.sender);
        emit HasBalanceOf(msg.sender, balance);
    }
}
```
This contract will take the callers address and log (ie. include into the Ethereum Blockchain for eternity) the balance of DAI Tokens for the callers address.

Let's deploy this contract, call the logBalance function and validate the result, it's not difficult.

### Prerequisites

* Installed [MetaMask](http://metamask.io/)
* Some Ropsten ETH from the [Ropsten faucet](https://faucet.ropsten.be/)

{{< warning >}}
Choose Ropsten Network in MetaMask before proceeding.
{{< /warning >}}

<img src="/images/MMRopsten.png" border=1 width=50%/>

* Go to [Aave](http://test.aave.com)
* Connect MetaMask Wallet and Account
* Choose DAI Faucet

<img src="/images/testaave.png" border=1 width=80%/>

<img src="/images/hasBalanceOf_es.png" border=1 width=80%/>

### Deploy BalanceLogger Contract
<img src="/images/FlashLoan.png" border=1 width=80%/>
### Deploy MyFlashloan Contract

* Approve the contract address at DAI Token Contract: https://ropsten.etherscan.io/address/0xf80A32A835F79D7787E8a8ee5721D0fEaFd78108
* Send some DAI to the contract (to pay fees of 0.09%)
* Send money to LendingPool if necessary (check MockDAI.balanceOf(LendingPool)): 0x4295Ee704716950A4dE7438086d6f0FBC0BA9472

https://docs.aave.com/developers/deployed-contracts/deployed-contract-instances
https://ipfs.io/ipfs/QmRJQJMVC6whKFKEpUR85Dv2Y4grg3bNiPiKJsQpsrZsCi
https://ipfs.io/ipfs/QmYZbrp4rNLn89U4GUWTnfuGqtQ6qYPBtBbkRAyovaqWzx


https://ropsten.etherscan.io/tx/0x6eb212e961ab7bc765557658cff16f6493a74894d946ec1cc8b8469d0cf33e1b

<img src="/images/copyFLaddress.png" border=1 width=80%/>
<img src="/images/DAIapproval.png" border=1 width=80%/>
<img src="/images/DAIapprovalES.png" border=1 width=80%/>
<img src="/images/editGas.png" border=1 width=80%/>
<img src="/images/customizeGas.png" border=1 width=80%/>
<img src="/images/executeFlashloan.png" border=1 width=80%/>