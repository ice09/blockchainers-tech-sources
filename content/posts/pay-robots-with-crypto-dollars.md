---
title: "Pay Robots With Crypto Dollars"
date: 2020-05-18T10:05:56+02:00
draft: false
---

TODO: Post is still incomplete, Images and Diagrams are missing. 

You have a valuable data source and want to make money with it? You have sensors, cameras or even robots which could earn you money if people could use them, just by calling your API? Or are you a robot or autonomous car and want to pay for something you need - maintenance, fuel or electricity and just noboby wants to open a bank account for you because KYC turns out to be difficult?

Do not despair: xDai to the rescue. Really everybody and even every-nobody like robots, cars or AIs can pay now for everything digital: cheap, fast (enough) and in digital dollars.

How does it work? Technically, we are using xDai as a Stablecoin on a Ethereum side-chain with a Dai-Bridge to the Ethereum Mainnet. 

xDAI is a cryptocurrency that is pegged to the US Dollar, has super low transaction fees, and fast transaction times. These features make it an ideal cryptocurrency to use for every day transactions like retail stores or peer to peer transfers. [Link]

What does it mean, how do you pay me dollars without me or you having a bank account?

It's easy, you can buy xDai for dollars 1:1 and pay for anything. You can always transfer it back if necessary, it's a Stablecoin and therefore 1 DAI will always be worth $1. 


Both trading scenarios, analog and digital, with dollars and the digital quivalents, xDai
Let me try it, what do I need?
A valid credit card number
(optional) Metamask browser plugin configured for xDai chain
About 30 minutes of your time
Note: We are using carbon.money, this might not be the cheapest solution, but it has a great UX and you can buy xDai directly. There are other, cheaper possibilities, especially when first buying Daiand converting it afterwards.

Buying xDai with your credit card
Install and Configure Metamask Plugin
Go to https://buy.carbon.money/
Follow instructions from xdaichain for buying xDai for USD/EUR

Congratulations, you have digital dollars now!
Nice, let's go shopping!
Go to Burner Wallet and link to Metamask Wallet.
Burner Wallet is usable also without Metamask, eg. on your mobile.
It's perfect for having a simple wallet for paying at certain events, eg. conferences, meetups and other gatherings
Try it out, install another Burner Wallet somewhere and transfer money back and forth
See the transactions in the Explorer
Compare the transfer fees
Understand how secure you money is (read & understand the documentation at pao.network and xdaichain to evaluate how secure your money is and what the differences to the Ethereum Mainnet are)
As you see, transferring money between parties is incredibly easy and fast. Pay with xDai, receive xDai and if necessary, convert it to DAI and even back to USD/EUR.

HUMAN-TO-MACHINE PAYMENTS WITH DIGITAL DOLLARS
With crypto human-to-machine payments, it's almost as easy as with analog human-to-human ones, it is just more difficult to relate payments to trades. We have to find some linking identifier which relates the payment to the respective trade.

We prepared a very simple demo for paying for (cheap) Chuck Norris jokes which requires manual transaction identifier signing.

This demo should give you an impression of what is possible, a more realistic request/response flow is depicted here:


Payment Flow for payable APIs
Any half-assed software developer can see that this is not rocket science to implement. For a demo we created to different versions which you can run with just having Java 8+ installed. These versions do not use the above mentioned process, but a simplified version for ease of understanding.

For getting started, use https://github.com/ice09/crypyapi-demo#chuck-norris-joke-machine-proxy-version

For the detailed version, use https://github.com/ice09/crypyapi-demo#chuck-norris-joke-machine-manual-version 

Next Step: crypyAPI. Crypto Payments for APIs.
The demo should give you an impression on what is possible. What we actually want to have is this:

@RestController
public class PayableAPIEndpoints<T> {

    @Payable(currency = USD, equivalentValue = 0.1, accepted =...)
    public byte[] createPicture() {
        return pictureService.create();
    }
So you take your existing Endpoints/REST Controllers, add some annotations (we are in Java/Spring MVC/Spring Boot here) and enjoy earning money. This can run everywhere, on-premise, in a private cloud, in a public cloud. No credentials, no credit cards, no bank accounts, just a plain 32 byte private key storing the money.

Make money from your services, everywhere.