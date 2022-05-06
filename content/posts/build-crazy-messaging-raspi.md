---
title: "DIY: Build Stupid and Crazy Expensive Messaging for LED Switches"
date: 2022-05-04
draft: false
---

{{<blockquote author="John Steinbeck">}}
Sometimes a man wants to be stupid if it lets him do a thing his cleverness forbids.
{{</blockquote>}}

In this post you will learn how to create a LED Switch with the Raspberry PI in the most expensive way possible “via Blockchain”. Alternatively, a quite complicated way to do this with Google Sheets is shown as well. And, if you really want to, you can also use a Chatbot for switching on and off the LED from a Telegram chat.  

The simple, cheapest and intuitive option, a REST API, has been linked, but of course that’s for educational purposes only - we certainly advise the reader to use the weird, but way more interesting, options.

<figure>
<img src="https://raw.githubusercontent.com/ice09/ethereum-gsheets-telegram-raspi-messaging/main/docs/img/overview.png">
<figcaption>Setup for the LED-Switch Messaging Environment</figcaption>
</figure>

## Really, why?

Because it’s fun to do. You will learn about Blockchain or better Ethereum EVM Smart Contract usage in Java with Spring Boot and how to sync state using Google Sheets API (in Java as well). Even more, you can delegate the actions to a Telegram Bot that switches the LED on or off for you with a simple `/set on`. You can even have it blinking. This sounds weird and it is. But it’s fun - and switching on and off LEDs “on the Blockchain” is a cool party topic as well!

Choose wisely! Do you want it stupid, expensive, or both?

## Blockchain? Google Sheets API? Telegram? Why not just use a REST API?

<figure>
<img src="/images/raspi_rest.png"/>
<figcaption>The intuitive Setup with REST APIs</figcaption>
</figure>

Because to me it’s boring and it might also be dangerous. By reacting to messages using pulling mechanisms, the attack vector is much smaller than opening up your system for direct, external HTTP calls. But if you want to go that route, there is plenty of [great documentation]( https://webtechie.be/post/2019-05-23-pi4j-adding-a-rest-interface-with-spring-boot/) already.

<figure>
<img src="/images/raspi_messaging.png"/>
<figcaption>Messaging with open available Middleware (like Google Sheets, Telegram, Ethereum Blockchain)</figcaption>
</figure>

### Using the Raspberry PI from Java with Pi4J

In all out messaging implementations, we will use Pi4J to switch an LED on or off on the Raspi in Java.

In "Resources" below you will find all links necessary to build this LED Switch, it's really easy (and cheap). You can follow the bluej.org instructions, download the repo and start switching LEDs on and off.

<figure>
<img src="/images/circuit_raspi_led.png"/>
<figcaption>Our Raspi Setup for Controlling the LED with Java</figcaption>
</figure>

### Web3 Messaging

The Web3 Messaging works with a (dumb) Smart Contract, which has no internal storage and emits Events on function calls.

These Events can be used by any observer (we are using web3j RPC to query the EVM Gnosis Chain) to react to function calls by switching on/off the LED. 

We will then pull the changes with web3j JSON-RPC from the Ethereum Blockchain by listening to these LED-Switch Events.

<figure>
<img src="/images/blockscout_1.png"/>
<figcaption>Turning the Switch by Calling the Switch Smart Contract</figcaption>
</figure>

For more information about Web3 Messaging head to the [project repository](https://github.com/ice09/ethereum-gsheets-telegram-raspi-messaging)

### Google Sheets Messaging

Did you know that you can create a simple Google Form from a Google Sheet very easily? With this we can create a public accessible (or restricted to your needs) Web Form which can be used to set values in the Google Sheet.

We will then pull the changes from the changes from the Google Sheet via its API and use it as a dead simple *Messaging Queue* for out commands.

<figure>
<img src="/images/led_switch_view.png"/>
<figcaption>Turning the Switch by Using Google Forms for Google Sheets</figcaption>
</figure>

For more information about Google Sheets Messaging head to the [project repository](https://github.com/ice09/ethereum-gsheets-telegram-raspi-messaging)

### Telegram Bot Messaging

<figure>
<img src="/images/tg_led_chat.png"/>
<figcaption>Turning the Switch by Chatting with the Telegram Bot</figcaption>
</figure>

You can use a Telegram Bot to set the LED-Switch commands. This Bot will push these commands to a Telegram queue until it is pulled by our Java client.

For more information about Telegram Bot Messaging head to the [project repository](https://github.com/ice09/ethereum-gsheets-telegram-raspi-messaging)

### Resources

* https://medium.com/javarevisited/controlling-electronics-with-jbang-on-the-raspberry-pi-3c290c465abd
* https://pi4j.com/1.2/pins/model-3b-rev1.html
* https://www.bluej.org/raspberrypi/led.html
* https://pi4j.com/getting-started/minimal-example-application/
