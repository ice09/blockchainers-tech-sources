---
title: "How To Become a Millionaire for 15 Seconds"
date: 2020-07-10
draft: false
---

{{<blockquote author="Doc Brown">}}
...or two, the encounter could create a time paradox, the result of which could cause a chain reaction that would unravel the very fabric of the space-time continuum and destroy the entire universe!  
Granted, that's worst-case scenario. The destruction might in fact be very localized, limited to merely our own galaxy.
{{</blockquote>}}

If you could start a sequence of actions and - at the very end of it - could just cancel it from the beginning as if nothing ever happened, wouldn't that be cool? And if you can be sure that what others do in such an action sequence will always be cancelled if certain specified criteria, like you getting back the lended money, are not met - would you then lend someone $1.000.000 if she either
* pays you back $1.000.900 at the end?
* gets reverted all actions that happened in the transaction, including you lending her money in the first place?

In the strange world of DeFi, some people do exactly this and they are earning riskless money with it. Just for lending money for 15 seconds and getting it back guaranteed, with (almost) no risk (but also low interest).

# Enter "Flash Loans"

Though this sounds so crazy, it is actually just a side effect of decentralized, transactional Smart Contract programming. It is very easy and, with provably immutable blockchains, very secure.

<figure>
<img src="/images/FlashLoan_2.png" size=200% border=0 />
<figcaption>Flash Loan transaction call sequence</figcaption>
</figure>

{{< warning >}}
This walk-through works for the Ropsten Testnet. Do not just transfer this to the Mainnet, this can have all kind of legal and tax implications when executed with real money!
{{< /warning >}}

## Why so cheap?

Usually for lending the _risk_ and the _opportunity cost_ (based on "risk-free" interest rates) determine the interest rate. As there are no opportunity costs with Flash Loans due to contract times in ranges of seconds and even more, no risk of losing any money due to deterministic, immutable logic, the fair rate is very low.

## Really, no Risks?

Actually, there are many risks with Flash Loans involved, mainly because of the democratization aspect of it, meaning it can be used by anyone from anywhere almost anonymously, including the bad guys and brillant hackers. Besides the non-foreseeable system hacks, as now everybody can be a crypto-whale, that has a lot of consequences, many of them [not that nice](https://medium.com/dragonfly-research/flash-loans-why-flash-attacks-will-be-the-new-normal-5144e23ac75a).

But for the user, ie. the borrower of a huge amount of money for very short time, this is really risk-free. The only money that will be lost are interest fees and gas costs.

## Ok, Gimme the Money!

Not so fast, let's do some coding before, it will neither be much nor complicated.

#### Deployed Smart Contracts

These Smart Contracts will be used in our Flash Loan transaction. `MockDAI` and `LendingPool` are 3rd party contracts, for this demo we deployed `BalanceLogger` and `MyFlashloan`. For replaying this demo on Ropsten, no contract deployment has to be performed. 

| Smart Contract | IPFS | Etherscan |
|---|---|---|
| BalanceLogger | [IPFS](https://ipfs.io/ipfs/QmYZbrp4rNLn89U4GUWTnfuGqtQ6qYPBtBbkRAyovaqWzx) | [Ropsten](https://ropsten.etherscan.io/address/0xebc2b6ac8257571a5f7e03bfbe89715e938f43dd#code) |
| MyFlashloan |  [IPFS](https://ipfs.io/ipfs/QmRJQJMVC6whKFKEpUR85Dv2Y4grg3bNiPiKJsQpsrZsCi) | [Ropsten](https://ropsten.etherscan.io/address/0xe0c6c13572aba5bf29640ee348d2fb4f22ab4531) |
| MockDAI | | [Ropsten](https://ropsten.etherscan.io/address/0xf80a32a835f79d7787e8a8ee5721d0feafd78108#code) |
| LendingPool | | [Ropsten](https://ropsten.etherscan.io/address/0x4295Ee704716950A4dE7438086d6f0FBC0BA9472#code) |

#### Notarizing the current DAI Balance: the BalanceLogger contract

The `BalanceLogger` contract is crazy simple: it reads the balance of DAI (MockDAI here) of `tx.origin`, which is the actual transaction sender and logs it, ie. writes it as an event into the Ethereum Blockchain.

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
Let's load this deployed contract on Ropsten, call the `logBalance` function and validate the result, it's not difficult.

### Prerequisites

* Installed [MetaMask](http://metamask.io/)
* Some Ropsten ETH from the [Ropsten faucet](https://faucet.ropsten.be/)

{{< warning >}}
Choose Ropsten Network in MetaMask before proceeding.
{{< /warning >}}

#### Warming Up: Minting DAI on Ropsten

We will need some DAI for the fees, therefore we are going to mint some (thanks Ropsten!).

<img src="/images/MMRopsten.png" border=1 width=50%/>

* Go to [Aave](http://test.aave.com)
* Connect MetaMask Wallet and Account
* Choose DAI Faucet

<img src="/images/testaave.png" border=1 width=60%/>

Check `BalanceLogger` for minted Ropsten DAI in Etherscan:
* Open Etherscan with [BalanceLogger contract](https://ropsten.etherscan.io/address/0xebc2b6ac8257571a5f7e03bfbe89715e938f43dd#writeContract)
* Connect Wallet with minted DAI account
* Call `logBalance` function of contract
* `logBalance` will log the Ropsten DAI balance of the origin account
* Check that event `HasBalanceOf` has been emitted for 10.000 DAI

<img src="/images/logBalance_es.png" border=1/>
<img src="/images/hasBalanceOf_es.png" border=1 width=80%/>

We successfully minted 10.000 DAI (Ropsten or MockDAI) and used the `BalanceLogger` to log our balance. We now want to borrow 1.000.000 DAI, transfer it to the original sender account, log the new balance and return the DAI + fees. Obviously, instead of logging the balance, you could do all kind of transactions with the loan, like margin trading, token swapping, etc. 

### Prepare for the Money

* Give the contract permission to transfer DAI on your behalf
  * Approve the contract address at [MockDAI Token Contract](https://ropsten.etherscan.io/address/0xf80A32A835F79D7787E8a8ee5721D0fEaFd78108)

<img src="/images/DAIapproval.png" border=1/>
<img src="/images/DAIapprovalES.png" border=1/>

* As all DAI has to be transfered back to the lending pool, the fees have to be submitted beforehand to the contract
  * Send some DAI to the contract (to pay fees of 0.09%)
    * On Ropsten: Send money to LendingPool if necessary (check `MockDAI.balanceOf(LendingPool)` at [deployed contracts](https://docs.aave.com/developers/deployed-contracts/deployed-contract-instances))

_The last step sounds weird, but as Ropsten is a Testnet, there might not be enough DAI in the Lending Pool. This won't be the case on the Mainnet._

### Getting Real: Request 1.000.000 DAI from the Lending Pool

As everything is prepared now, we can call the main contract. Flash Loans use a callback mechanism which allows us to define what actually will happen with the money. This callback function will be called inside the transaction, giving us this process flow:

(OUR CONTRACT,`flashloan`) --calls--> (LENDING POOL,`flashloan`) --transfers_asset,calls--> (OUR CONTRACT,`executeOperation`) --> logs the balance, returns money afterwards <-- (LENDING POOL,`flashloan`) <--checks_balance,succeeds_or_fails-- (OUR CONTRACT,`flashloan`) -- end of transaction

That's it, the transaction will fail if the assertion in the LENDING POOL contract (was borrowed amount+fees transferred back) fails. 

```javascript
    function executeOperation(
        address _reserve,
        uint256 _amount,
        uint256 _fee,
        bytes calldata _params
    )
        external
    {
        require(_amount <= getBalanceInternal(address(this), _reserve), "Invalid balance, was the flashLoan successful?");
        
        // transfer loan to tx.origin address
        IERC20(0xf80A32A835F79D7787E8a8ee5721D0fEaFd78108).transfer(tx.origin, _amount);

        IBalanceLogger(0xEbc2B6aC8257571a5f7E03bFBe89715E938F43dd).logBalance();
    
        // transfer money back to contract to pay back. Fee have to be transferred to this contract before. 
        // Approval for contract address with tx.origin at MockDAI must be set for _amount
        IERC20(0xf80A32A835F79D7787E8a8ee5721D0fEaFd78108).transferFrom(tx.origin, address(this), _amount);

        // Time to transfer the funds back
        uint totalDebt = _amount.add(_fee);
        transferFundsBackToPoolInternal(_reserve, totalDebt);
    }

    function flashloan(uint amount) public onlyOwner {
        bytes memory data = "";
        address asset = address(0xf80A32A835F79D7787E8a8ee5721D0fEaFd78108); // ropsten DAI
        ILendingPool lendingPool = ILendingPool(addressesProvider.getLendingPool());
        lendingPool.flashLoan(address(this), asset, amount*(10**18), data);
    }
```

### Step-by-Step Borrowing

* Go to https://remix.ethereum.org and import the [Smart Contract IPFS resource](https://ipfs.io/ipfs/QmRJQJMVC6whKFKEpUR85Dv2Y4grg3bNiPiKJsQpsrZsCi).

<img src="/images/remix_ipfs.png" border=1/>

* Rename the file: add extension _sol_ for Solidity, so Remix recognizes the file.

<img src="/images/remix_rename.png" width=300 border=1/>

* Load the contract from the Ropsten chain. This step will link the sources in the editor to the contract address.

<img src="/images/executeFlashloan.png" border=1/>

* Execute the `flashloan` function with the amount of DAI you want to borrow (and gave approval in the MockDAI contract for).
  * Customize the gas price (this step might not be necessary, customize only if the transaction fails otherwise).

<img src="/images/editGas.png" border=1/>
<img src="/images/customizeGas.png" border="1"/>

### Validating the Transaction

You can easily validate the transaction by connecting with web3js or web3j or any other client you trust, or more easily [in Etherscan](https://ropsten.etherscan.io/tx/0xfa702b7e54bfed224d1ae35b0ba4ddade1160d3026d0c945b2c941c3099d9ffe#eventlog).  
There will be step 5 which logs the current balance of the `trx.origin` account. As this contract is immutable and deployed to the Ethereum Blockchain, there is no way to fake this log message.

<img src="/images/proof_etherscan.png" border=1>

So for logging out the balance this would be a costly thing to do on the Mainnet. But as you can do everything that fits in the `executeOperation` function, there might be more profitable things to do than logging.

#### Resources
* [Original Transaction for this post](https://ropsten.etherscan.io/tx/0xfa702b7e54bfed224d1ae35b0ba4ddade1160d3026d0c945b2c941c3099d9ffe#eventlog)
* [Log current DAI balance](https://ropsten.etherscan.io/address/0xebc2b6ac8257571a5f7e03bfbe89715e938f43dd#writeContract)
* [MyFlashloanContract on IPFS](https://ipfs.io/ipfs/QmRJQJMVC6whKFKEpUR85Dv2Y4grg3bNiPiKJsQpsrZsCi)