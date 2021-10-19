---
title: "Pay Robots with Crypto Money: Crypto Payable API (Circles UBI edition)"
date: 2021-10-19
draft: false
---

{{<blockquote author="Sam Harris">}}
In a world of true abundance you shouldn't have to work to justify your life.
{{</blockquote>}}

Last time, we introduced a [Crypto Payable API](http://blockchainers.tech/pay-robots-with-crypto-money/), which allows for paying Services and APIs with Stablecoins on the xDai chain. This works for any ERC-20 token as well and even more, it works for Universal Basic Income implementations like [Circles UBI](https://joincircles.net/).  

<figure>
   <img src="https://drive.google.com/uc?export=view&id=1jbx1ggzEPhQj9MVhxg6CLTxk11ts74oF"/>
   <figcaption>Stablecoin, Token and analog payments, same same...</figcaption>
</figure>

Circles UBI are ERC-20 Tokens which are individualized. So, each user has her own Circles (CRC) Token, which are swapped 1:1 through the trust path, enabling untrusted parties to pay each other. As the Service or API does not want to deal with trust graphs, it can decide to accept well-known Group Currencies, which can be minted by community members and therefore be used as a "trust hubs" between different local Circles communities and external entities like Services or APIs.

<figure>
   <img src="https://drive.google.com/uc?export=view&id=1Kf9dSnhPIz6jW_QxTYCJ12dQeIV-WlEK"/>
   <figcaption>Individual Circles Tokens (CRC) vs. Group Currencies</figcaption>
</figure>

As a Service or API, only the Group Currency Token (GCT) address has to be known. If a user has a path to the Group Currency contract, she can swap her own CRC for the Group Currency Token with the Group Currency Token Owner (GCTO). With this GCT she can pay the Service or API.

<figure>
   <img src="https://drive.google.com/uc?export=view&id=15Mas10wGK6JsEMpPxWS0RxOPZypWvqPO"/>
   <figcaption>Call Sequence for Circles UBI GroupCurrency Payments</figcaption>
</figure>

## But does it work?

Yes it does, we prepared a simple demo for paying for (really cheap) Chuck Norris jokes with a Group Currency Token. The demo runs on Hardhat only, but could run on xDai with Circles UBI as well. The `OrgaHub`, which is necessary for transitive minting, is not deployed on the Mainnet for now.  

* All sources and Demo instructions for this blog post: https://github.com/ice09/token-paid-services

### Next Step: crypyAPI. Crypto Payable APIs.

The demo should give you an impression on what is possible. What we actually want to have this runnable on any device, for any cryptocurrency, and useable as easy as:

```java
@Payable(currency=EUR, equivalentValue=0.1, accepted={DAI, CRC})
public byte[] createPicture() {
    return pictureService.create();
}
```

So you take your Services, add some annotations (we are in Java/Spring Boot environment here) and enjoy earning money. This can run everywhere, on-premise, in a private cloud, in a public cloud.  
No credentials, no credit cards, no bank accounts, just a plain 32 byte private key storing the money.

*Make money from your services, everywhere.*