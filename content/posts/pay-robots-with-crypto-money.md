---
title: "Pay Robots with Crypto Money: Crypto Payable API (xDai edition)"
date: 2020-10-08
draft: false
---

{{<blockquote author="T-1000">}}
I need your clothes, your boots, and your motorcycle.
{{</blockquote>}}

<figure>
  <img src="/images/resties_s.png"/>
  <figcaption>Wouldn't it be great? Make money simply by annotating services.</figcaption>
</figure>

You have a valuable data source and want to make money with it? You have sensors, machines, cameras or even robots which could earn you money if people could use them, just by calling services with simple HTTP-Requests?  

Or *are* you a robot or autonomous car and want to pay for something you need - maintenance, fuel or electricity and just noboby wants to open a bank account for you because KYC turns out to be difficult?

Do not despair: xDai to the rescue. Really everybody and even every-nobody like robots, cars or AIs can pay now for everything digital: cheap, fast (enough) and in digital dollars.

<figure>
   <img src="/images/crypyAPIsample_.png"/>
   <figcaption>Digital and analog payments, same same....</figcaption>
</figure>

How does it work? Technically, we are using xDai as a _Stablecoin on a Ethereum side-chain with a [DAI-xDai-Bridge](https://dai-bridge.poa.network/) to the Ethereum Mainnet_. 

> xDAI is a cryptocurrency that is pegged to the US Dollar, has super low transaction fees, and fast transaction times. These features make it an ideal cryptocurrency to use for every day transactions like retail stores or peer to peer transfers ([more information about xDai](https://medium.com/@jaredstauffer/what-is-xdai-how-do-i-use-xdai-a-simple-explanation-7440cbaf1df6)).

What does it mean, how do you pay me dollars without me or you having a bank account?

It's easy, you can buy xDai for dollars 1:1 and pay for anything by transferring xDai directly to the receiver. You can always convert xDai back to Dollar if necessary, it's a Stablecoin and therefore 1 xDai will always be worth $1. 

### Let's go shopping!

We will use [Sokol POA Network as a Test Network](https://www.poa.network/for-developers/developer-resourses) for xDai. There are subtle differences between POA and xDai Networks, but for testing purposes POA Network is good enough.  

### Get a wallet first

Creating a wallet has no prerequisites, but it is simplified a lot by Browser plugins like [MetaMask](https://metamask.io/), [Nifty Wallet](https://www.poa.network/for-users/nifty-wallet) or others. We will use Nifty Wallet as it is optimized for xDai/POA Network use.

<figure>
   <img style="width:350px" border=1 src="/images/nifty.png"/>
   <figcaption>Install Nifty or MetaMask Wallet for xDai fun.</figcaption>
</figure>

{{<note>}}
Remember to choose Sokol Testnet in Nifty Wallet before proceeding.
{{</note>}}

Read more on the [comparison of xDai and POA Network](https://www.xdaichain.com/about-xdai/comparisons/poa) if you really want to use xDai to understand the risks. For POA there are no risks involved, we operate on the Testnet only.

### Get Rich Quick (...with valueless Testnet-Coins)

* Get Sokol-POA (SPOA) for free at the faucet: [https://faucet.poa.network/](https://faucet.poa.network/)
* A transaction hash will be displayed and within ~5 seconds your wallet will have 100 SPOA.
* You can now transfer SPOA to any Ethereum address. 
  * The receiver however first has to connect to Sokol to see the amount.

As you see, transferring money between parties is incredibly easy and fast. Pay with xDai, receive xDai and if necessary, convert it to DAI (via the DAI-xDai-Bridge) and even back to USD/EUR eg. via CEX like Kraken.

# Human to Machine Payments with Digital Dollars

With crypto human-to-machine payments, it's almost as easy as with analog human-to-human ones, it is just more difficult to relate payments to trades. We have to find some linking identifier which relates the payment to the respective trade.

<figure>
<img src="/images/PaymentAPIxDaiBold.png"/>
<figcaption>Paying for services: Overview of Service Request and Payment.</figcaption>
</figure>

The crucial transaction step is the correlation of the service request and the payment by the client. This step must prevent double spents, sybil attacks, and other means of fraud. For this, we have to make sure that the payment was executed for exactly one request by the client who now requests the service. This is complex as there is no connection between the technical payment provider (the xDai Blockchain) and the service provider (the HTTP response). This connection has to be established securely and easy by correlating the payment and the service request.

## Correlation with unique transaction identifiers, digital signatures and sender addresses

<figure>
<img src="/images/payflow.png"/>
<figcaption>Paying for services: Correlation of payments and service requests.</figcaption>
</figure>

* The Service Client Payment Sender sends the address it wants to transfer from. 
* The Service Payment Receiver creates an unique identifier (TRXID), adds it to the "outstanding" payments storage together with the submitted address with state "REQUESTED" and returns it to the client (steps 1 and 2).
* The Service Client Payment Sender sends a transaction to the receivers address with the TRXID as payload (extra data) (step 3). 
* The Service Payment Receiver listens to transactions to its address and stores the TRXHASH (steps 4 and 5) for the TRXID and the sender's address (from) in the outstanding payments storage, changing the state to "PAID".
* The Service Client Payment Sender sends the HTTP Request with the TRXHASH (retrieved in step 6) and SIGNED_TRXID to the Service Payment Receiver (step 7).
* The Service Payment Receiver ecrecovers the address from the SIGNED_TRXID and checks if it matches the payments storage address (step 8).
* If the addresses match, the Service Client Payment Sender must have the same private key as the sender of the transaction.
* The content is delivered and the TRXID is removed from the outstanding payments storage.

## But does it work?

Yes it does, we prepared a very simple demo for paying for (really cheap) Chuck Norris jokes which requires manual transaction identifier signing to correlate the payment and the joke request.

Any half-assed software developer can see that this is not rocket science to use. The development requires Java 11 (+Maven), some Spring Boot knowledge and internet access. The demo requires Docker.

* All sources and Demo instructions for this blog post: https://github.com/ice09/crypyapi-http-demo

### Next Step: crypyAPI. Crypto Payments APIs.

The demo should give you an impression on what is possible. What we actually want to have this runnable on any device which runs Java:

```java
@Payable(currency=EUR, equivalentValue=0.1, accepted={DAI, LIBRA})
public byte[] createPicture() {
    return pictureService.create();
}
```

So you take your Endpoints/REST Controllers/Services, add some annotations (we are in Java/Spring Boot environment here) and enjoy earning money. This can run everywhere, on-premise, in a private cloud, in a public cloud.  
No credentials, no credit cards, no bank accounts, just a plain 32 byte private key storing the money.

*Make money from your services, everywhere.*