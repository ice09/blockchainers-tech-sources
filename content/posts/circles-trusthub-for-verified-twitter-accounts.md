---
title: "Circles Trusthub for Verified Twitter Accounts"
date: 2020-12-16T10:00:04+01:00
draft: true
---

{{<blockquote author="Warren Bennis">}}
Trust is the lubrication that makes it possible for organizations to work.
{{</blockquote>}}

<figure>
<img src="/images/Generic-Transitive-Payments-Circles-Twitter-Trusthub.png">
<figcaption>Transitive Payments via Trust Chains</figcaption>
</figure>

We won't go into details about [Circles UBI](https://joincircles.net/) here, just this: it's a great new concept which combines an Universal Basic Income with a social trust graph for transitive payments. 

In short, this concept allows users to become completely independent of centralized currency systems: a scarce resource (the user's Circles coins), which is steadily created solely based on algorithms, can be swapped between users which make sure this resource gets value due to the trust they have to the resource-owners.  

Trusted connections allow for transitive value transfers, which makes it possible to pay directly unknown users with transitive payments via Circle coin swaps with users along the trust path. This basically implements the [Six Degrees of Separation](https://en.wikipedia.org/wiki/Six_degrees_of_separation) theory.

# Trust in Real Life

Circles assumes that the currency will be used in local markets where people know each other. If I know someone, I can trust her (meaning I state that the token she uses belongs to her and is her main medium of exchange). This gets difficult if I want to trust someone I don't know, over the internet.

<figure >
    <img src="/images/Transitive-Payments-Circles-Twitter-Trusthub_RL.png"/>
    <figcaption>Trust in Real Life, Alice knows Bob who knows Carol.</figcaption> 
</figure>

# Trust in "Cyberspace"

This limitation might be ok for the early stages, but we don't have to live with it. We can create (semi-) automated identities on Circles which trust other entities with special characteristics. For this proof of concept, we chose "having a Twitter account" as characteristic. So if someone can prove that he owns a specific Twitter account and wants to associate this account with an Ethereum address, this will be done fully automated by the twitter-trusthub. 

<figure >
    <img src="/images/Transitive-Payments-Circles-Twitter-Trusthub_OFF.png"/>
    <figcaption>Trust in Cyberspace is difficult. Who knows if you are trusting a Dog? And what do you buy with DogCoins?</figcaption> 
</figure>

## So who am I trusting?

If you decide to trust the twitter_trusthub, you agree to trust people who 
* have proven that they have a Twitter account.
* have associated this Twitter account with an Ethereum address (which equals their Circles account). 
* have not submitted another Ethereum address for any other Twitter account.

No more than that, so this is a very limited trust mechanism, however it can get more strict easily.

## How does it work?

_Note: Using the prototype is quite simple, but you have to be aware that by using it, you are "opening" your trust graph. Though you can always revert the trust, you are always risking your Circles while you trust "unknown" entities._

In the overview below you see the communication between the several components, mostly smart contracts and some centralized glue functionality written in Java with web3j.  

### Steps to use Twitter Trusthub for Transitive Payments

<figure >
    <img src="/images/Circles-Twitter-Trusthub.png"/>
    <figcaption>Overview of Components Interaction for Twitter Verification and Automated Trust Creation.</figcaption> 
</figure>

1. Make sure you cannot pay trusthubmerchant with your Circles (CRC).
2. Trust the Twitter Trusthub.
3. Tweet the signed messaged.
4. Wait for mutual trust by Trusthub.
5. Pay trusthubmerchant via Trusthub.

## POAP as Trust Source

https://thegraph.com/explorer/subgraph/amxx/poap