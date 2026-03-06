# 🪙 Fungible Tokens on Sui (Move Tutorial)

Welcome! If you're here, you're probably curious about creating your own cryptocurrency, token, or coin on the Sui blockchain. You're in the right place!

This repository is designed to take you from knowing absolutely nothing about coin creation on Sui, to successfully deploying and minting your own token.

But before we dive into the code, let's take a moment to understand the tools we're working with.

---

## 🤔 What is Sui?

Sui (pronounced *swee*) is a next-generation Layer 1 blockchain designed from the ground up to make digital asset ownership fast, private, secure, and accessible to everyone.

Unlike traditional blockchains like Ethereum or Bitcoin, which record transactions in a big sequence of blocks (like pages in a ledger book), Sui takes a completely different approach.

### The Object-Centric Model

In Sui, **everything is an object**.

Think of it this way: On most blockchains, your account has a "balance" (e.g., Alice has 10 tokens). The blockchain just updates that number.
On Sui, you actually own distinct, individual objects (e.g., Alice owns Token Object A, Token Object B).

Why does this matter?
- **Speed:** Because objects are independent, Sui can process transactions involving different objects at the same time (in parallel). It doesn't have to wait in a single line!
- **Ownership:** Objects have clear owners. If you own an object, you are the only one who can interact with it (unless you make it shared).
- **Flexibility:** Objects can own other objects, and objects can be customized to hold different types of data.

---

## 🛠️ What is Move?

Move is the programming language we use to write smart contracts on Sui.

If you're familiar with Ethereum, Move is to Sui what Solidity is to Ethereum. But Move was designed specifically to handle digital assets safely.

### Why Move?

- **Safety First:** Move was created with security as its primary goal. It prevents common bugs like reentrancy attacks (which have caused billions of dollars to be stolen on other blockchains).
- **Resources:** In Move, digital assets are treated as "resources". A resource can never be copied or accidentally deleted. It can only be moved from one place to another (hence the name "Move"!).
- **Abilities:** Move uses something called "abilities" (`copy`, `drop`, `store`, `key`) to strictly control what you can do with a type. You'll see this in action when we define our coin.

---

## 🚀 The Course Structure

This guide is broken down into bite-sized, easy-to-follow parts. I highly recommend going through them in order.

### 📚 Part 1: [Regular Coin Creation](./regular_coin)
Here, you'll learn how to write the Move code to create a deflationary coin. You'll understand decimals, type tags, and how to set up your coin's metadata (name, symbol, icon).

### 🪙 Part 2: [Publishing and Minting](./regular_coin_example)
Once your code is ready, you need to actually deploy it to the blockchain. In this part, we'll cover how to publish your package, what happens during a transaction, and how to use Programmable Transaction Blocks (PTBs) to mint and transfer your shiny new coins to your wallet!

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
