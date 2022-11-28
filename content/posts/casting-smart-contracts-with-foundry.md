---
title: "Casting Smart Contracts with Foundry"
date: 2022-11-28
draft: false
---

# Casting Smart Contracts with Foundry

{{<blockquote author="Luis J. Rodriguez">}}
I worked in a steel mill, I worked in a foundry, I worked in a paper mill, I worked in a chemical refinery, construction, I did all that. It was great work, it was good. I learned welding, mechanic, carpentry, but it saved me from going back to prison because that's helpful. It's really sad because those jobs are gone.
{{</blockquote>}}

## Foundry, a Practical Guide

There are many great introductions to Foundry and good [comparisons with Truffle and Hardhat](https://www.youtube.com/watch?v=d6Uvt0u6FQc).

In this post, we will refactor a smart contract from an existing Truffle repository to a brand-new Foundry repository and on the way using some cool features: _blazingly fast builds, EVM-testing, and Mainnet forking_.

<figure>
<img src="https://i.imgur.com/ZwwdQEih.png" border=0 />
</figure>

## Motivation

When development started on the `GroupCurrencyToken` smart contracts, we created a simple prototype first. This prototype was only runnable in the context of Circles UBI, ie. with access to an existing `Hub` smart contract.

Even on creation a dependency on `Hub` does exist, as the function `organizationSignup` is called.

```javascript
constructor(address _hub, address _treasury, address _owner, uint8 _mintFeePerThousand, string memory _name, string memory _symbol) ERC20(_name, _symbol) {
    owner = _owner;
    hub = _hub;
    treasury = _treasury;
    mintFeePerThousand = _mintFeePerThousand;
    IHub(hub).organizationSignup();
}
```

Our goal was to have a thoroughly tested and _self-contained_ version of the smart contract which can be tested in isolation (_for unit testing_) and ideally in context of a running environment as well (_for integration testing_). 

We wanted to use Foundry to implement these features, to have minimal dependencies (on other languages, environments, etc.)

### Extracting a Smart Contract using Interfaces 

Though Foundry already supports a [sophisticated mocking concept](https://book.getfoundry.sh/cheatcodes/mock-call), we decided for creating dedicated mock classes: `MockToken` and `MockHub` with very limited and easy to understand functionality.

<details>
  <summary>MockHub: IHub which maps user to token addresses and registers trusts</summary>

```javascript
contract MockHub is IHub {

    mapping (address => mapping (address => uint256)) public limits;
    mapping (address => address) public tokenToUser;
    mapping (address => address) public userToToken;

    function setTokenToUser(address token, address user) external {
        tokenToUser[token] = user;
        userToToken[user] = token;
    }

    function signup() external {
        emit Signup(msg.sender, 0x0000000000000000000000000000000000000001);
    }

    function organizationSignup() external {
        emit OrganizationSignup(msg.sender);
    }
    
    function trust(address trustee, uint256 amount) external {
        limits[msg.sender][trustee] = amount;
        emit Trust(msg.sender, trustee, amount);
    }

}
```
</details>

<details>
    <summary>MockToken: ERC20 which mints "amount" to deployer</summary>

```javascript
contract MockToken is ERC20 {

    constructor(string memory _name, string memory _symbol, uint256 _amount) ERC20 (_name, _symbol) {
        _mint(msg.sender, _amount);
    }
}
```
</details>


With these simple mocks, it is very easy to test for events and with tokens _as they are available to the deploying account immediately_.

## Unit Tests for the `GroupCurrencyToken` Contract

First we redefine the events we are interested in in the test itself. Though this started as a workaround for not being able to import events (_Solidity doesn't support qualifying events by contract_), it has advantages as it is easier to read and manage.

```javascript
contract GroupCurrencyTokenTest is Test {

    event Trust(address indexed _canSendTo, address indexed _user, uint256 _limit);
    event OrganizationSignup(address indexed _organization);

    [...]
```

### Deploying the Contract under Test

To deploy a real `GroupCurrencyToken` which we want to test with a mock `IHub`, in Solidity the commands are straightforward:

```javascript
MockHub mockHub = new MockHub();
GroupCurrencyToken gct = 
    new GroupCurrencyToken(address(mockHub), address(this), address(this), 0, "GCT", "GCT");
```

The call `IHub.organizationSignup()` is then executed on the `MockHub` which emits the event `emit OrganizationSignup(msg.sender)` we can later assert for.

### Assertions in Solidity

Assertions are super easy to implement with help of the Solidity lib `Test.sol` which ships with Foundry as a standard lib (`forge-std`).

```javascript
assertFalse(gct.onlyOwnerCanMint(), "onlyOwnerCanMint should be false.");
assertFalse(gct.onlyTrustedCanMint(), "onlyTrustedCanMint should be false.");
```

[More complex assertions](https://book.getfoundry.sh/reference/ds-test#asserting) are supported as well.

### Asserting for Events

A great feature of Foundry is the simplicity for asserting for events. If we want to check that a certain call `addMemberToken(...)` should emit two events, it it as easy as:

```javascript
vm.expectEmit(true, true, false, true, address(mockHub));
emit Trust(address(gct), user, 100);
vm.expectEmit(true, true, false, true, address(gct));
emit MemberTokenAdded(user);

gct.addMemberToken(address(mockToken));
```

with `vm` being a special [cheatcode](https://book.getfoundry.sh/cheatcodes/#cheatcodes-reference) which simplifies testing a lot. In this case we want to make sure the events `Trust` and `MemberTokenAdded` will be called in this sequence _on the next function invocation_.

After `addMemberToken` has finished, _these events must have been emitted_. The parameters config what exactly will be checked of the event (which parameters, which invoker).

### Test For Failure

With pragmatic conventions it is very easy to test for failure: if your function name starts with **testFail** instead of **test**, the function is _expected to fail_ and will really fail otherwise. 

> Unit tests always have to start with **test**, otherwise they will not be executed during testing.

```javascript
function testFailMintingNoMemberToken() external {
    MockHub mockHub = new MockHub();
    GroupCurrencyToken gct = new GroupCurrencyToken(address(mockHub), address(this), address(this), 0, "GCT", "GCT");
    MockToken mockToken = new MockToken("GCT", "GCT", 50);

    address[] memory cols = new address[](1);
    cols[0] = address(mockToken);
    uint256[] memory tokens = new uint256[](1);
    tokens[0] = 50;
    mockToken.transfer(address(gct), 50);
    gct.mint(cols, tokens);
}
```

The execution of `gct.mint` should fail in the scenario above, hence **testFailMinting...**.

### How to Prank in Solidity

Pranking in Solidity is fun & easy! 

In the following example we want to call `trust` _in the name of_ (ie. with `msg.sender`) `0xf39...`, we can just use `vm.prank(...)`. If we want to do this for multiple calls, we can use `vm.startPrank(...)` and `vm.stopPrank()`.

```javascript
MockToken mockToken = new MockToken("GCT", "GCT", 50);
vm.prank(0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266);
mockHub.trust(address(this), 100);
```

## Integration Testing

Though this term is not used in the Foundry documentation, the [_scripting feature_](https://book.getfoundry.sh/tutorials/solidity-scripting) will often be used for this use case.

Our scenario is: We want to test our new contract against the existing `Hub` smart contract. There are two options:

1. Deploy the `Hub` in our test chain with all existing dependencies and test data
2. Fork the target chain and use our smart contract against all data present in that chain

Options 2. sounds just crazy - and again is incredible easy to do in Foundry (it is supported by other frameworks as well).

If we start the script `script/GroupCurrencyTest.s.sol` with 

`forge script script/GroupCurrencyToken.s.sol -vvvv --fork-url=
https://rpc.gnosischain.com`

we can instantiate `IHub` with `IHub(0x29b9a7fBb8995b2423a71cC17cf9810798F6C543)` and our calls will be executed as if our contract was deployed in target chain already (jep - crazy).

## References

* Old GCT "Combined" Repo: https://github.com/ice09/circles-contracts/blob/hub-v1-comp/contracts/GroupCurrencyToken.sol
* New Refactored, Standalone Repo: https://github.com/ice09/circles-group-currency-token
* Image: _Epic futuristic mysterious foundry, sparks and flashes, middle ages artistic style, dark colors_ (Stable Diffusion via Dreamstudio)