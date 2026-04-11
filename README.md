# Fungible Tokens on Sui (Move Tutorial)

In the crypto space, having a custom token is often the starting point for building decentralized applications (dApps), games, DeFi protocols, or even just loyalty reward systems. This repo is designed to take you from knowing absolutely nothing about token creation on Sui, to successfully deploying and minting your own token.

If you already know a thing or two about Sui and Move, you can just jump straight into [Regular Coin Creation](./regular_coin).

But if you’re new here, welcome. Let's talk about creating your own token on the Sui blockchain. It might sound complicated at first, but once i break down how things work under the hood, it all starts to make sense.


Before we start looking at code, let’s go over what we are actually building on.

---

## 🤔 So, What Exactly is Sui?

Sui (pronounced *swee*) is a next-generation Layer 1 blockchain designed from the ground up to make digital asset ownership fast, private, secure, and accessible to everyone.

Unlike traditional blockchains like Ethereum or Bitcoin, which record transactions in a big sequence of blocks (like pages in a ledger book), Sui takes a completely different approach.

Sui uses an **object-centric model**.

Most blockchains use an "account-based" model. Imagine a big ledger book that says: "Alice’s balance is 10 tokens. Bob’s balance is 5 tokens." When Alice sends Bob 5 tokens, the network updates those two balances in the ledger. Everyone waits in a single line for the ledger to update.

In Sui, **everything is an object**. Instead of having a "balance" attached to your account, you literally own distinct, individual objects (think of them like unique digital items in your wallet: Token Object A, Token Object B).

Why does this matter?
- **Speed:** Because objects are independent, Sui doesn’t have to process transactions one by one in a single line. It can process transactions involving completely different objects at the exact same time (in parallel).
- **True Ownership:** Objects have clear, undeniable owners. If you own an object, you are the only person who can interact with it (unless you explicitly decide to share it with people).
- **Flexibility:** Objects can own other objects, and objects can be customized to hold different types of data.

---

## And What is Move?

Move is the programming language we use to write smart contracts on Sui.

If you're familiar with Ethereum, Move is to Sui what Solidity is to Ethereum. But Move was designed specifically to handle digital assets safely. 

### Why do we use Move?

- **It Treats Assets as Resources:** In Move, digital assets are treated as "resources". A resource can never be copied or accidentally deleted. It can only be moved from one place to another (hence the name "Move"!).
- **Abilities Control Everything:** Move uses things called "abilities" (`copy`, `drop`, `store`, `key`) to define exactly what you are allowed to do with a piece of data. We'll be using these abilities a lot when creating our coin.
- **Built for Security:** Move was created with security as its primary goal. It prevents common bugs like reentrancy attacks (which have caused billions of dollars to be stolen on other blockchains).

---

## The Course Structure

I've broken this guide down into parts so it's easier to digest. Make sure you follow them in order.

### Part 1: [Regular Coin Creation](./regular_coin)
This is where we actually write the Move code for a deflationary coin. You'll learn how to define your total supply, understand what decimals actually mean in crypto, set up type tags, and register your coin's metadata (like its name, symbol, and logo).

### Part 2: [Publishing and Minting](./regular_coin_example)
Writing code is fun, but deploying it is better. In this section, we take the package you built in Part 1 and push it to the Sui network. We’ll look at what actually happens during a transaction and how to use Programmable Transaction Blocks (PTBs) to mint your initial supply straight to your wallet.

---

### What's Next?
Right now, this repo covers standard, regular coins. But the Sui ecosystem is huge. Once you finish these basics, the same high-level concepts apply to:
- **Regulated Coins:** Tokens where you (the creator) control a "deny list" to block malicious addresses.
- **Closed-Loop Tokens:** Think of in-game currencies (like CODM CP) that can't be traded outside of your specific app (CODM CP is completely useless outside of CODM).

---

## ❤️ Support the Author

If you found this guide helpful, please follow me for more free Move/Sui content:

🐦 **X (Twitter):** [Mello](https://twitter.com/intent/follow?screen_name=MelloTheTrader_)

⭐ **GitHub:** [Favour Ejiogu](https://github.com/FavourEjiogu)

Leaving a **star** on the repository helps motivate me to write more beginner-friendly tutorials like this. Thank you!

---

## ⚖️ License

This guide is released under the **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** license.

That means:

* ✅ You can **share** (copy, redistribute) the material in any medium or format
* ✅ You can **adapt** (remix, transform, and build upon) it
* ❌ You **cannot** use it for commercial purposes
* ⚠️ You **must** give appropriate credit (e.g., “Written by [Favour Ejiogu](https://github.com/FavourEjiogu)”)

For full details, see the [license text here](https://creativecommons.org/licenses/by-nc/4.0/).

---

⭐ **Star this repository if you enjoyed the guide!** ⭐

**Made with ❤️ and lots of ☕ by [Mello](https://twitter.com/intent/follow?screen_name=MelloTheTrader_) for the [Sui Community](https://x.com/SuiCommunity)**
