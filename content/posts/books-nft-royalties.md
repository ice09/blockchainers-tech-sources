---
title: "Books as NFT with Royalties on Stablecoin Chains"
date: 2022-08-27
draft: false
---

{{<blockquote author="Friedrich Dürrenmatt - Die Physiker">}}
Was einmal gedacht wurde, kann nicht mehr zurückgenommen werden.
{{</blockquote>}}

<figure>
<img src="https://i.imgur.com/mrs2FaO.png" size=200% border=0 />
<figcaption>Two Book Lovers trading Books, Stable Diffusion Style</figcaption>
</figure>

[Pearson announced it wants to use NFTs](https://www.theverge.com/2022/8/3/23290335/pearson-textbook-publisher-nft-blockchain-secondhand-ebook-sales) for earning on all trades of a book and the Blockchain-crowd did not like it.
There are many good reasons against this kind of money harvesting, but in this post, we ignore them and just want to understand how to implement this feature (on an EVM chain with a native Stablecoin currency, like [Gnosis Chain](https://www.gnosischain.com/)).

<figure>
<img src="https://i.imgur.com/XBSeMBy.png" size=200% border=0 />
<figcaption>Context of this Post</figcaption>
</figure>

### Algorithmically Ensured Royalty Payments

Why are Smart Contracts a great solution for paying Royalties? Because the author or publisher can make sure that payments will be executed, _without having to trust the buyer or the seller_.

With a physical book, if the seller (Sally) sells the book to the buyer (Bob), the author has no way to verify that Royalties are paid.

With Smart Contracts, the author can _prohibit_ the transfer of the NFT from Sally to Bob if the royalties are not paid.

### Stablecoin Chains for Business-oriented Implementations

For this example, we will use the Stablecoin Gnosis Chain. It is a EVM-chain which uses a Stablecoin (xDai which is bridged to DAI on Ethereum) as native currency as Ethereum uses ETH as native currency.

With this we can pay gas (and royalty) fees in US dollars.

### Using the ERC 721 Interface

In the [ERC 721](https://erc721.org/), which is used to implement NFTs on EVM chains, the interface specifies two methods which we can use:

```solidity
function approve(address _approved, uint256 _tokenId) external payable;
```

```solidity
function safeTransferFrom(address _from, address _to, uint256 _tokenId) external payable;
```

Both functions are `payable` which means we can send value with the transaction (which will be accessible with `msg.value` in the Smart Contract).

#### Sally `transfers` to Bob and pays Royalty Fees

The easiest solution is having Sally calling `safeTransferFrom` from her own account and adding the royalty as `msg.value` to the transaction.

#### Bob `transfers` to Sally and pays Royalty Fees

Sally can also `approve` Bob or someone else to transfer the NFT on behalf of her. She can then add the royalty fee to the `approve` transaction (which means that she pays the fee) or Bob has to add the fee to the `safeTransfer` transaction which he or someone else calls who was approved by Sally. Both options require to add the fee amount as `msg.value` to the transactions.

_Note: If we decide to offer both options (approve and safeTransferFrom), we will have to track the payment as a state in the Smart Contract._

### Implementation of Smart Contract-based Royalty Fees

```solidity
/**
  * @dev See {IERC721-safeTransferFrom}.
  */
function safeTransferFrom(
  address from,
  address to,
  uint256 tokenId,
  bytes memory data
) public virtual override {
  require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: caller is not token owner or approved");
  _safeTransfer(from, to, tokenId, data);
}
```

`isApprovedOrOwner` checks if the sender is either `approved` by the owner or the owner herself.
Our idea is to add a `payRoyaltyFee` here which deducts the pre-defined amount from the transaction amount:

```solidity
/**
  * @dev See {IERC721-safeTransferFrom}.
  */
function safeTransferFrom(
  address from,
  address to,
  uint256 tokenId,
  bytes memory data
) public virtual override {
  require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: caller is not token owner or approved");
  require(_payRoyaltyFee(), "ERC721: Royalty Fee was not paid");
  _safeTransfer(from, to, tokenId, data);
}

function _payRoyaltyFee() internal {
  require(msg.value >= _royaltyFee, "Royalty Fee is not included in transaction");
  _royaltyReceiver.transfer(_royaltyFee);
}
```

And that's it! We will have to somehow set `_royaltyFee` and `_royaltyReceiver`, eg. on NFT deployment.

Now the `safeTransferFrom` function will revert if the transaction did not include the royalty fee or could not transfer the fee to the `_royaltyReceiver`. 

With this simple implementation, we can make sure that the owner of an NFT cannot change if the royalty fee was not paid without having to trust anyone, neither buyer or seller or intermediaries on behalf - the payment cannot be circumvented as it is "hard wired" into the code of the NFT Smart Contract.

These are all implementation steps to implement this feature in a Stablecoin chain. For a non-Stablecoin chain, Token transfers would have to be used, which increases the complexity a bit, though the logic would stay exactly the same.




