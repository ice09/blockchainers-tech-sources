---
title: "API-Driven Smart Contract Testing"
date: 2022-05-18
draft: false
---

{{<blockquote author="Brian Marick">}}
Testing a product is a learning process.
{{</blockquote>}}

Smart Contracts are difficult to get right and testing is even more important than with other technology stacks. Once a Smart Contract is deployed, it is always publicly accessible and not removable or modifiable - at least with no special precautions like proxy contracts or similar.

To support developers with testing, we are introducing a new approach for API-driven Smart Contract testing: after generating REST APIs from a Smart Contract Application Binary Interface (ABI), these REST APIs are used for REST client-based testing. 

![](https://i.imgur.com/g5bIHt4.png)

## Options for Smart Contracts Testing

### JSON/RPC (HTTP/JSON)

This testing is low-level and typically implemented in the language the web3 application is implemented in. So for JavaScript, the most common technology for accessing Ethereum/EVM Smart Contracts, the tests are written in JavaScript.  

Using JavaScript already is "asymmetric", as Smart Contracts are typically written in Solidity or Vyper, not JavaScript.

### REST (HTTP/JSON)

To enable testing on HTTP/REST level, a REST Adapter has to be created or generated for the Smart Contracts. As Ethereum/EVM Smart Contracts always implement an ABI, this interface can be used to generate the REST adapter automatically.  

This technique is implemented for Java with the [Web3j Gradle OpenAPI Plugin](https://github.com/web3j/web3j-openapi-gradle-plugin).

#### Advantages of REST Smart Contract Testing

* Separation of Concerns: Developers focus on EVM development, Testers use HTTP Client
* Different Teams: Experts in EVM focus on implementation, Testers focus on simple HTTP testing language
* Not another Testing Tool: Reuse of existing tools (Editor Plugins, Postman, SoapUI)
* Reusability of API: Generated REST adapter can be used for internal/external web3j service encapsulation

#### Disadvantages of REST Smart Contract Testing

* Abstracts away from Smart Contract Testing, no immediate connection between tests and implementation
* Testing on wrong layer (but code generation reduces translation errors)
* For advanced web3j developers new learning (if teams are not separated)
* Not CI/CD-automated out-of-the-box, but can be achieved (eg. https://httpx.sh/)

### Using REST Adapter Code Generation

see OpenAPI Plugin Sample: https://github.com/ice09/smart-contract-api-driven-testing

_Screenshot of automatically generated Swagger-UI_

![](https://i.imgur.com/JMhIR9A.png)

## Test Suite with IntelliJ REST Client

_Note: You can as well use the Visual Studio Code REST Client for Testing, but without Assertions._

* Assert with `client.assert`
* Store responses with `client.global.set`
* Log variables with `client.log`

```javascript
###
### Deploy Hub Smart Constract
###

POST http://localhost:9090/circles-ubi/contracts/hub
Content-Type: application/json

{
  "_inflation": 1,
  "_period": 1,
  "_symbol": "CRC",
  "_name": "Circles",
  "_signupBonus": 50000000000000000000,
  "_initialIssuance": 1,
  "_timeout": 1
}

> {%
    client.test("Request executed successfully", function() {
        client.assert(response.status === 200, "Response status is not 200");
    });
    client.global.set("hubContractAddress", response.body.contractAddress);
    client.log(client.global.get("hubContractAddress"));
%}
```

### Read Events to Verify Smart Contract Function Executions

* Call the function on the Smart Contract which emits the Event
* Store the complete response of the REST Service
* Use the stored response for Event retrieval

```javascript
###
### Signup with account 1
###

GET http://localhost:9091/circles-ubi/contracts/hub/{{hubContractAddress}}/signup

> {%
    client.test("Request executed successfully", function() {
        client.assert(response.status === 200, "Response status is not 200");
    });
    client.global.set("signupResponse", JSON.stringify(response.body));
    client.log(client.global.get("signupResponse"));
%}

###
### Get Token Address from Token Contract Deployment Logs
###
POST http://localhost:9090/circles-ubi/contracts/hub/{{hubContractAddress}}/events/signup
Content-Type: application/json

{{signupResponse}}

> {%
    client.test("Request executed successfully", function() {
        client.assert(response.status === 200, "Response status is not 200");
    });
    client.global.set("acct1TokenAddress", response.body[0].token);
    client.log(client.global.get("acct1TokenAddress"));
%}
```

## Test Suite with SoapUI

You can use any REST Service testing tool like Postman or SoapUI.

_Screenshot of SoapUI Sample Test Suite_

![](https://i.imgur.com/xKqaEQO.png)