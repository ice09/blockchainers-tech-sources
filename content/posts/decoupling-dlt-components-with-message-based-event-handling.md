---
title: "Decoupling DLT Components with Message-Based Event Handling"
date: 2023-02-17
draft: false
---

{{<blockquote author="Edsger W. Dijkstra">}}
This is what I mean by "focusing one's attention upon some aspect": it does not mean ignoring the other aspects, it is just doing justice to the fact that from this aspect's point of view, the other is irrelevant.
{{</blockquote>}}

<figure>
<img src="https://i.imgur.com/FNVbNtp.png" size=200% border=0 />
</figure>

DLT applications often require multiple components to interact with each other. These components might be part of the DLT network, such as smart contracts or nodes, or they might be external components such as web applications or databases. One way to make these interactions more manageable and scalable is by building an event-driven architecture using message brokers like RabbitMQ.

### 🔊 Producers and 👂 Consumers

In this blog post, we will explore how to build a **decoupled event-driven architecture for DLT applications** using web3j and RabbitMQ. Web3j is a Java library that allows you to interact with Ethereum networks, and RabbitMQ is a message broker that enables message-based communication between components. 

By using these tools together, we can create a loosely coupled architecture that allows components to **communicate via messages, rather than being strongly coupled with direct method calls**.

<figure>
<img src="https://i.imgur.com/rLOTfEj.png" size=200% border=0 />
<figcaption>Message-Based Publishing of DLT Events</figcaption>
</figure>

With this architecture, you can **decouple the DLT parts** of the system, like the Spring Boot Java application which **reads the Events and publishes them** to the Message Broker, from the **consumer that listens** to this Broker and that can be implemented in any other language, like Python, JavaScript, TypeScript, Rust, or .NET languages.

### 🚶‍♀️ Walk-Through a Simple Switch Dumb Contract

In our example, we will create a complete application:

1. Smart Contract on Ethereum that emits Events
2. Spring Boot Java component which subscribe to these Events with web3j
3. Spring Boot Java component which publishes JSON-serialized Events to RabbitMQ

#### 📖 Why Dumb Contracts?

Dumb Smart Contracts are a pattern name for Smart Contracts which have no storage but only emit events. We use these kind of contracts for simplicity as we want to focus on events, however this pattern is often used to reduce gas costs.

### 🔬 How does it work?

To complete the walk-through, you will use multiple, completely unrelated tech stacks. The examples and setups themselves are simple, so you need admin access to your computer and some time to install all the pre-requisites.

### ⚙️ Step-by-Step

You can use the modules README will help with the sequence of setup steps:

* Deploy the Switch contract on your local chain
* Listen to the Events of the Switch contract
* Transact with the contract to "turn the switch"
* See the queued messages with the Event payload in the RabbitMQ Admin Dashboard

#### 1. Deploy the Switch contract on your local chain

* [Switch Dumb Contract README](https://github.com/ice09/dlt-event-monitor/blob/main/dumb-contract/README.md)

#### 2. Listen to Events of the Switch contract

* [DLT-Monitor README](https://github.com/ice09/dlt-event-monitor/blob/main/dlt-event-monitor/README.md)

#### 3. Transact with the contract to turn the switch

* Use `cast` to call the function `turnSwitch(1)` on the deployed smart contract. See the step in [Switch Dumb Contract README](https://github.com/ice09/dlt-event-monitor/blob/main/dumb-contract/README.md#get-deployed-contract-address)

#### 4. See the queued messages with the Event payload in RabbitMQ

* Open http://localhost:15672 (login with guest/guest) and navigate to queue **Switch_SwitchTurned**
* Watch the events being queued if you execute the function

<figure>
<img src="https://i.imgur.com/4oOx2Ly.png" size=200% border=0 />
<figcaption>Event-Messages in Queues in RabbitMQ</figcaption>
</figure>

### 🛠️ Troubleshoot

#### Read current configuration

![](https://i.imgur.com/pAk2lHr.png)