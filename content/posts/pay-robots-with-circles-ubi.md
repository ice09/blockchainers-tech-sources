---
title: "Pay Robots with Crypto Money: Crypto Payable API (Circles UBI edition)"
date: 2021-10-19
draft: false
---

{{<blockquote author="Sam Harris">}}
In a world of true abundance you shouldn't have to work to justify your life.
{{</blockquote>}}

Last time, we introduced a [Crypto Payable API](http://blockchainers.tech/pay-robots-with-crypto-money/), which allows for paying Services and APIs with Stable Coins on the xDai chain. This works for any ERC-20 token as well and even more, it works for Universal Basic Income implementations like [Circles UBI](https://joincircles.net/).  

<figure>
   <img src="https://drive.google.com/uc?export=view&id=1jbx1ggzEPhQj9MVhxg6CLTxk11ts74oF"/>
   <figcaption>Stablecoin, Token and analog payments, same same...</figcaption>
</figure>

Circles UBI are ERC-20 Tokens which are individualized. So, each user has her own Circles (CRC) Token. As there might not be a path for a user, a Group Currency can be used as a "trust hub" between different local Circles communities. 

<figure>
   <img src="https://drive.google.com/uc?export=view&id=1Kf9dSnhPIz6jW_QxTYCJ12dQeIV-WlEK"/>
   <figcaption>Individual Circles Tokens (CRC) vs. Group Currencies</figcaption>
</figure>


As a Service or API, only the Group Currency Token (GCT) address has to be known. If a user has a path to the Group Currency contract, she can swap her own CRC for the Group Currency Token Owner (GCTO).



## But does it work?

Yes it does, we prepared a very simple demo for paying for (really cheap) Chuck Norris jokes which requires manual transaction identifier signing to correlate the payment and the joke request.

The development requires Java 11 (+Maven), some Spring Boot knowledge and internet access. The demo requires Docker.

* All sources and Demo instructions for this blog post: https://github.com/ice09/crypyapi-http-demo

### Next Step: crypyAPI. Crypto Payable APIs.

The demo should give you an impression on what is possible. What we actually want to have this runnable on any device, for any cryptocurrency, and useable as easy as:

```java
@Payable(currency=EUR, equivalentValue=0.1, accepted={DAI, CRC})
public byte[] createPicture() {
    return pictureService.create();
}
```

You can find running prototypes (for xDai/POA, but easily extendable to Stellar, Diem, and all Chains allowing for extra data in their transactions) here:

* Payment Receiver: https://github.com/ice09/crypyapi-receiver
* Payment Sender: https://github.com/ice09/crypyapi-sender

So you take your Services, add some annotations (we are in Java/Spring Boot environment here) and enjoy earning money. This can run everywhere, on-premise, in a private cloud, in a public cloud.  
No credentials, no credit cards, no bank accounts, just a plain 32 byte private key storing the money.

*Make money from your services, everywhere.*