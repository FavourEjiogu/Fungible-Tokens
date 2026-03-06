# 🪙 Fungible Tokens on Sui (Move Tutorial)

If you already know a thing or two about Sui and Move, you can just jump straight into [Regular Coin Creation](./regular_coin).

But if you’re new here, welcome. Let's talk about creating your own cryptocurrency, token, or coin on the Sui blockchain. It might sound complicated at first, but once you break down how things work under the hood, it all starts to make sense.

In the crypto space, having a custom token is often the starting point for building decentralized applications (dApps), games, DeFi protocols, or even just loyalty reward systems. This guide takes you from an absolute beginner to successfully publishing and minting your very own token.

Before we start looking at code, let’s go over what we are actually building on.

---

## 🤔 So, What Exactly is Sui?

Sui (pronounced *swee*) is a Layer 1 blockchain. It handles digital assets in a way that is totally different from older blockchains like Bitcoin or Ethereum.

Most blockchains use an "account-based" model. Imagine a big ledger book that says: "Alice’s balance is 10 tokens. Bob’s balance is 5 tokens." When Alice sends Bob 5 tokens, the network updates those two balances in the ledger. Everyone waits in a single line for the ledger to update.

Sui uses an **object-centric model**.

In Sui, **everything is an object**. Instead of having a "balance" attached to your account, you literally own distinct, individual objects (think of them like unique digital items in your wallet: Token Object A, Token Object B).

Why should you care about this?
- **It’s Fast:** Because objects are independent, Sui doesn’t have to process transactions one by one in a single line. It can process transactions involving completely different objects at the exact same time.
- **True Ownership:** Objects have clear, undeniable owners. If you own an object, you are the only person who can interact with it (unless you explicitly decide to share it with everyone).
- **Customization:** Objects can hold other objects, and you can program them to contain any type of data you want.

---

## 🛠️ And What is Move?

Move is the programming language we use to write smart contracts on Sui.

If you've played around with Ethereum, Move is to Sui what Solidity is to Ethereum. The main difference is that Move was built strictly around safely managing digital assets.

### Why do we use Move?

- **It Treats Assets as Resources:** In Move, your digital assets are treated as "resources." A resource is special because it can never be accidentally copied or deleted out of thin air. It can only be moved from one place to another (which is why the language is called Move).
- **Abilities Control Everything:** Move uses things called "abilities" (`copy`, `drop`, `store`, `key`) to define exactly what you are allowed to do with a piece of data. We'll be using these abilities a lot when creating our coin.
- **Built for Security:** By making it very hard to accidentally copy or destroy assets, Move naturally prevents a lot of the common bugs and hacks that plague other blockchains.

---

## 🚀 The Course Structure

I've broken this guide down into parts so it's easier to digest. Make sure you follow them in order.

### 📚 Part 1: [Regular Coin Creation](./regular_coin)
This is where we actually write the Move code for a deflationary coin. You'll learn how to define your total supply, understand what decimals actually mean in crypto, set up type tags, and register your coin's metadata (like its name, symbol, and logo).

### 🪙 Part 2: [Publishing and Minting](./regular_coin_example)
Writing code is fun, but deploying it is better. In this section, we take the package you built in Part 1 and push it to the Sui network. We’ll look at what actually happens during a transaction and how to use Programmable Transaction Blocks (PTBs) to mint your initial supply straight to your wallet.

---

### What's Next?
Right now, this repo covers standard, regular coins. But the Sui ecosystem is huge. Once you finish these basics, the same concepts apply to:
- **Regulated Coins:** Tokens where you (the creator) control a "deny list" to block malicious addresses.
- **Closed-Loop Tokens:** Think in-game currencies or loyalty points that can't be traded outside of your specific app.
- **NFTs (Non-Fungible Tokens):** Unique, 1-of-1 digital assets.

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
