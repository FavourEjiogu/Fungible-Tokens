# Creating a Regular Deflationary Coin on Sui (Move Tutorial)

>This guide shows you how to create a deflationary coin on the Sui blockchain using Move. Upon initialization, it mints the total supply and gives up the TreasuryCap to make the supply deflationary (meaning no new coins can ever be minted, but existing ones can still be burned).
It also keeps the ability to update your coin’s currency metadata (name, symbol, icon, and description) whenever you want.
You’ll understand how decimals, type tags, and coin metadata all come together step by step.
---

## 🧠 Before You Begin

**Estimated Time to Completion:** 15-25 minutes  

**Prerequisites**
- Basic understanding of what Sui and Move are  
- The [Sui CLI](https://docs.sui.io/guides/developer/getting-started/sui-install) installed and configured  
- A GitHub account (optional, but useful for hosting your coin icon)  
- A little curiosity, patience, and ☕  

---

## ⚙️ Setup
### 🧩 Step 1: Create the package

If you don’t already have one, create a new package for your coin:

```
sui move new regular_coin

```

This command creates a folder structure like this:

```
regular_coin/
│
├── Move.toml
└── sources/
    └── regular_coin.move

```

### 🧩 Step 2: Populate the code

Paste the code from **[here](https://github.com/FavourEjiogu/Currency/blob/main/regular_coin/sources/regular_coin.move)** inside:

```
📂 regular_coin/sources/regular_coin.move
```

That’s where your actual Move code for this guide should live.

Once you’ve added the code, verify everything is fine by building your package:

```
sui move build
```

If no errors show up, you’re ready to go!

The rest of this README explains how and why that code works, line by line, concept by concept.

---

## Understanding What’s Going On

In line 7, i know you're probably wondering why theres way more than 9 zeros if the total supply is going to be just 1 billion. if you are, that means you are inquisitive just like me :)

You see in crypto, especially when in context of currency and currency creation, we have something called decimals, they are not exactly what you were taught in school (1.59, 2.87, etc) but not entirely different.

A decimal in crypto refers to the number of decimal places a cryptocurrency can be divided into. Unlike traditional fiat currencies, which typically use two decimal places (e.g., dollars and cents) where the smallest unit is usually 1/100 of the smallest note (e.g., 1 cent is 1/100 of 1$), most cryptocurrencies like Bitcoin (BTC) and Sui (SUI) are divisible down to 8 decimal places or more.

This high degree of divisibility is crucial for several reasons:

* **Microtransactions:** It allows for the valuation and transaction of tiny fractions of a cryptocurrency, making it suitable for small purchases and microtransactions.
* **Accessibility:** It ensures that transactions remain accessible regardless of the asset's value, even as the price appreciates significantly.
* **Precision:** It enables greater precision in transaction handling, particularly important for complex smart contract operations and gas fee calculations.

Now dont get me wrong, figures like 2.82 SUI still exist, but this is actually represented as 2.820000000 SUI (and ofcourse we know that the trailing zeros behind a decimal point doesn't matter)

This is because SUI supports up to 9 decimal places (because 1 SUI = 1,000,000,000 MIST).
This means you can see amounts like:
1.123456789 SUI in your wallet

It is generally considered best practice to create coins that follow the same decimal standard as the parent chain (in Sui’s case, 9 decimals), especially for tokens that are meant to be widely used, traded, or paired with SUI.

**Why Match the Decimal Standard?**

* User Experience: Consistency with SUI makes it easier for users to understand and compare values.
* Interoperability: DEXs, wallets, and other dApps expect 9 decimals for SUI and may assume the same for other coins, reducing the risk of display or calculation errors.
* Integration: Many DeFi protocols and infrastructure tools are optimized for the native decimal standard.

### So in our example:

If you write:

```move
const TOTAL_SUPPLY: u64 = 1000000000;
```

This means the value of `TOTAL_SUPPLY` is exactly **one billion** (1,000,000,000) with **no extra zeros** for decimal places.

#### What’s the difference?

* `const TOTAL_SUPPLY: u64 = 1000000000_000000000;`

  * Value: 1,000,000,000,000,000,000 (one quintillion)
  * If your coin uses 9 decimals, this represents **1 billion coins** (1,000,000,000.000000000 in human-readable form).
* `const TOTAL_SUPPLY: u64 = 1000000000;`

  * Value: 1,000,000,000 (one billion)
  * If your coin uses 9 decimals, this represents **one coin** (1.000000000 in human-readable form).

#### So now that means:

* The number you set for `TOTAL_SUPPLY` should match the number of coins you want to mint, including the number of decimals.
* If your coin has 9 decimals, and you want a total supply of "1,000,000,000.000000000" coins, you must use `1000000000_000000000` (1,000,000,000,000,000,000).
* If you use just `1000000000`, you are only minting "1.000000000" coins (if displayed with 9 decimals).

#### Summary:

The value should also include all the zeros for the decimal places.

---

#### One question i usually get asked frequently at developer workshops is:

> "Is the Underscore basically representation of a decimal point?"

The answer is **No**

**Please note that** (`_`) **does not** represent a decimal point. Instead, Sui Move uses an underscore (`_`) as a digit separator for readability, similar to how you might write `1,000,000` for one million. This is a common pattern in Move (and many other languages) to make large numbers easier to read.

#### So that means:

* `1000000000_000000000` is exactly the same as `1,000,000,000,000,000,000`.
* The underscore is ignored by the compiler; it’s just for humans.
* The underscore is just to improve readability.

#### For example:

```move
const TOTAL_SUPPLY: u64 = 1000000000_000000000; // 1B supply (if decimals == 9)
```

---

When you look up transaction data or a digest on Sui, you often see references to object types, especially for coins. For example, the comment you saw on line 10-11:

/* The type identifier of coin. The coin will have a type
tag of kind: `Coin<package_object::module::struct>`. */

**What does this mean?**

* **Type Tag**: In Sui, every object (including coins) has a type tag that uniquely identifies what kind of object it is. For coins, the type tag looks like `0x2::coin::Coin<package_object::module::struct>`.
* **Why does it matter?**

  * When you query transaction data or look up an object by its digest, you will see this type tag in the response.
  * This tag tells you exactly what kind of coin or object you are dealing with. For example, SUI itself is `0x2::coin::Coin<0x2::sui::SUI>`, but a custom coin would have its own type, like `Coin<0x123...::my_module::my_struct>`.
  * If you are building dApps, wallets, or explorers, you need to filter, display, or process objects based on their type tags.

**Use cases:**

* **Identifying Coins**: If you want to know if an object is a coin, you check its type tag.
* **Custom Coins**: If you create your own coin, it will have a unique type tag.
* **APIs and Queries**: When using Sui APIs (like GraphQL or RPC), you often filter or search for objects by their type tag.

**Summary:**
So basically, the type tag is how Sui (and you) know what kind of object or coin you are dealing with. It is essential for filtering, displaying, and interacting with coins and other objects on Sui.

**Should the struct be named after the coin?**

Yes, it is best practice to name the struct after the actual name of the coin or asset it represents.

**Why?**

* **Clarity:** When someone sees `struct MyCoin`, it’s immediately clear what the struct represents.
* **Consistency:** It matches the type tag, which will be `Coin<package::module::MyCoin>`, making it easier to reason about types and avoid confusion.
* **Ecosystem Standards:** The [Sui Move conventions](https://docs.sui.io/concepts/sui-move-concepts/conventions) recommend clear, descriptive names for structs, especially for key objects like coins.
* This improves code readability, maintainability, and clarity for anyone using or reviewing your code.

#### For example:

```move
public struct MyCoin has key, store {
    id: UID
}
```

This is a **good** practice.

**Does it matter technically?**

**Well, Technically:** No, the Move language and Sui do not require the struct name to match the coin’s branding or intended use. The type tag will always be `Coin<package::ModuleName::StructName>`, regardless of what you call `StructName`. However, using a descriptive name is **highly recommended** for the reasons above.

---

In line 12-14:

```
public struct MyCoin has key, store { 
    id: UID 
}
```

This defines the **type** of your coin `MyCoin`.
It’s the identity that every Coin `<MyCoin>` will be based on.

`id: UID` gives it a unique identity on-chain.

In Sui Move, when you define a struct for your coin (or any object), you control its abilities using the `has` keyword.

**`key`** basically means it is an **object**

**`store`** basically means it is **transferrable**

The most common abilities in coin creation are `key` and `store`, because all coins have to be ***objects***, and you'd typically expect all coins to be ***transferrable***, right? Well in turns out, on sui, there are special kind of coins that can be created to only exist within a particular system, think of loyalty points or in-game money (like Call of Duty's CP) that can't be used outside the app/game or even transferrable to other users/players, and on sui these coins and their owners are actually publicly verifiable on the blockchain. They are called **Closed-Loop Tokens**. *For more info about Closed-Loop Tokens, click **here***.

#### Now back to business:

When creating regular coins, you actually do not need to explicitly add "store" if you already have "key". This is because, in Move, the `key` ability automatically implies `store` for all fields inside the struct.

**From the official documentation:**

> If a value has `key`, all values contained inside of that value have `store`. This is the only ability with this sort of asymmetry.
> — [Source](https://github.com/MystenLabs/sui/blob/319fe085a2387747dc19d798895d77bd6d594063/external-crates/move/documentation/book/src/abilities.md)

#### Example

```move
public struct MyCoin has key {
    id: UID,
    // other fields...
}
```

* Here, `MyCoin` has the `key` ability.
* This is enough for your struct to be transferable and storable as an object.
* So you do not need to write `has key, store`, and in fact it might actually just be redundant but personally, i like to include it because it's not harmful and it just makes more sense to do so (especially for beginners, it improves code readability and it will help you understand abilities better), and it's just 1 extra word bro.

---

### **Quick Recap**:

* **Module (`regular_coin`)**: This is the Move module where your coin logic lives.
* **Struct (`MyCoin`)**: This is the type identifier for your coin.
* **Type Tag**: The full type tag for your coin is `Coin<package_object::ModuleName::StructName>`.
* **Coin Name**: The ***display name*** of the coin (what users see, e.g., "Scallop" or "SCA") is set in the metadata when you call `create_currency` or similar functions. It is **not** automatically the same as the struct name.

**So, note that:**

* The struct name (`MyCoin`) and module (`regular_coin`) are for type identification.
* The coin’s ***display name*** is set in metadata and can be anything you want.

| What you see on chain                          | What it means                                   |
| ---------------------------------------------- | ----------------------------------------------- |
| `Coin<package_object::ModuleName::StructName>` | Coin Type, defined by struct & module           |
| Coin name in wallet/explorer                   | Coin Name, defined in metadata, not struct name |

---

### Here, a new currency is created without a [OTW (One Time Witness) proof of uniqueness]():

```
 public fun new_currency(registry: &mut CoinRegistry, ctx: &mut TxContext): Coin<MyCoin> {
    let (mut currency, mut treasury_cap) = coin_registry::new_currency(
        registry,
        9, // This is where the actual decimal value is stated
        b"MYC".to_string(), // Symbol (Abbreviation) of your coin (e.g., SCA, NAVX, SUI) 
        b"MyCoin".to_string(), // Name of your coin
        b"Standard Unregulated Coin".to_string(), // Description of your coin
        b"https://example.com/my_coin.png".to_string(), // Icon URL (The link to your logo, check the ReadMe for more info)
        ctx,
    );

    let total_supply = treasury_cap.mint(TOTAL_SUPPLY, ctx); // This line mints the initial supply of your coin (Check the ReadMe to understand HOW it works).
    currency.make_supply_burn_only(treasury_cap); // This line makes the coin's supply "burn-only" (Check the ReadMe to understand HOW it works).

    let metadata_cap = currency.finalize(ctx); // This line finalizes the creation of your coin
    
    /// Here, the recipient is the sender of the transaction. The linter would normally warn you, but the #[allow(lint(self_transfer))] suppresses that warning.
    transfer::public_transfer(metadata_cap, ctx.sender()); // This line transfers the MetadataCap object to the transaction sender. 
    
    total_supply
}   
```

Now, as a first timer, i know this looks crazy, but trust me it's not. By the end of this guide, you'll see why Move just makes sense.

If you haven't already, consider following me on [Github](https://github.com/FavourEjiogu) and [X/Twitter](https://twitter.com/intent/follow?screen_name=MelloTheTrader_), i'd really appreciate it! It motivates me to write more free guides like this... ouu and leave a star on [this repo](https://github.com/FavourEjiogu/Fungible-Tokens/tree/main/regular_coin), thanks!

## Now back to business:

### 1. The Function: `new_currency`

```
public fun new_currency(registry: &mut CoinRegistry, ctx: &mut TxContext): Coin<MyCoin> {
```

This function does everything. It:

* Registers your coin with Sui’s Coin Registry.
* Creates minting authority (treasury cap).
* Mints supply.
* Locks the supply.
* Sends ownership of metadata to you.
* Returns your newly minted supply.

Now, you might be wondering, in this function, does `<MyCoin>` refer to the struct or the symbol or the name of the coin?

In the function signature:

```move
public fun new_currency(registry: &mut CoinRegistry, ctx: &mut TxContext): Coin<MyCoin>
```

the `<MyCoin>` refers to the ***struct***, not the symbol.

**Explanation:**

* In Move (and Sui), when you see a type like `Coin<MyCoin>`, the part inside the angle brackets (`<...>`) is a **type parameter**.
* `MyCoin` here is a struct that you have defined elsewhere in your module, for example:

  ```move
  public struct MyCoin has key, store {
      id: UID
  }
  ```
* The symbol (like `"MYC"`) is just a string used for display and metadata purposes, not a type.

**So, for example:**

* `Coin<MelloCoin>` would mean "a coin whose underlying type is the struct `MelloCoin`".
* The symbol, name, description, etc., are set in the coin's metadata, not in the type system.

---

### 2. Creating a New Currency

```
let (mut currency, mut treasury_cap) = coin_registry::new_currency(
    registry,
    9,
    b"MYC".to_string(),
    b"MyCoin".to_string(),
    b"Standard Unregulated Coin".to_string(),
    b"https://example.com/my_coin.png".to_string(),
    ctx,
);
```

**What Happens**:

* `coin_registry::new_currency(...)` creates a new coin type in Sui’s system.
* It sets its metadata:

  * Decimals (explained above): 9
  * Symbol: `b"MYC".to_string()` (Symbol or Abbreviation of your coin, e.g., SCA, NAVX, SUI)
  * Name: `b"MyCoin".to_string()` (The actual name of your Coin, this is what would be displayed in browsers, wallets, exchanges, etc)
  * Description: `b"I love you Mello".to_string()` (i know you do haha)
  * Icon URL: `b"https://example.com/my_coin.png".to_string()`(This is where you would put the link to your coin’s logo).

**What You Get:**
It returns two important resources:

* `currency`: the setup/config object for your coin.
* `treasury_cap`: your minting authority (the only thing that allows you to legally print coins).

Remember to always include `b"...".to_string()` when filling in your coin's metadata, except for the decimal part.

---

Someone asked me this during a developer workshop:

> In
> `b"https://example.com/my_coin.png".to_string()`
> What if i want to point to a local path?

So, in Sui Move, the `icon_url` for a coin is expected to be a URL string (usually an HTTP(S) link) that points to an image accessible by wallets, explorers, or other clients.

**That means:**

* A file on your own computer (e.g., `file:///Users/you/my_coin.png`) will not work because wallets and explorers **cannot** access your local filesystem. They need a URL that is accessible over the internet.
* If you want others to see the icon, upload it to a public image host (like GitHub, IPFS, or your own web server).

**Here are some easy ways to host your coin icon image so you can use a public URL in your Sui Move project:**

---

**1. GitHub (Free & Easy)**

* Upload your image to a public GitHub repository.
* After uploading, click on the image file and copy the “Raw” file URL.
* Example:

  ```
  https://raw.githubusercontent.com/yourusername/yourrepo/main/my_coin.png
  ```
* Use this URL in your Move code.

---

**2. Image Hosting Services**

* Use free image hosts like [imgur.com](https://imgur.com/) or [postimages.org](https://postimages.org/).
* Upload your image and copy the direct link.
* Example:

  ```
  https://i.imgur.com/yourimage.png
  ```

---

**3. Your Own Web Server**

* If you have a website, upload the image to your server.
* Use the direct URL:

  ```
  https://yourdomain.com/path/to/my_coin.png
  ```

---

### 3. Minting the Total Supply

```move
let total_supply = treasury_cap.mint(TOTAL_SUPPLY, ctx);
```

* **What it does:** This line mints the initial supply of your custom coin.
* **How:**

  * `treasury_cap` is a special capability that allows minting and burning of coins of a specific type.
  * `mint(TOTAL_SUPPLY, ctx)` creates a new `Coin` object with the specified amount (`TOTAL_SUPPLY`) and assigns it to the publisher (or whoever is running this function).
* **Result:**

  * `total_supply` now holds the minted coins (as a `Coin` object).

---

### 4. Making the Supply Burn-Only

```move
currency.make_supply_burn_only(treasury_cap);
```

* **What it does:** This line makes the coin's supply "burn-only" (Deflationary).
* **How:**

  * `currency` is the `Currency` object representing your coin in the registry.
  * `make_supply_burn_only(treasury_cap)` consumes the `TreasuryCap`, which means:

    * **No more minting is possible** (since minting requires the `TreasuryCap`).
    * **Burning is still allowed** (holders can destroy their coins, reducing total supply).
* **Result:**

  * The coin becomes **deflationary**: supply can **only decrease** (via burning), ***never*** **increase**.

### But why should i do this?

* **Deflationary coins:** This pattern is used for coins where you want a fixed initial supply, but allow holders to burn (destroy) their coins, reducing the total supply over time.
* **No more minting:** By giving up the `TreasuryCap`, you ensure that no one (not even the original publisher) can mint more coins.

---

### 5. Finalizing the Currency

```move
let metadata_cap = currency.finalize(ctx);
```

* **What it does:** This line finalizes the creation of your custom coin.
* **How:**

  * `currency` here is a `CurrencyInitializer` object, which is a temporary "builder" for your coin.
  * Calling `.finalize(ctx)` completes the registration process for your coin and returns a `MetadataCap` object.
  * The `MetadataCap` is a special capability that allows the holder to update the coin’s metadata (name, symbol, description, icon, etc.).
* **Result:**

  * The coin is now fully registered and ready to use.
  * You receive a `MetadataCap` for future metadata management.

---

### 6. Transferring the MetadataCap

```move
transfer::public_transfer(metadata_cap, ctx.sender());
```

* **What it does:**
  This line transfers the `MetadataCap` object to the transaction sender.
* **How:**

  * `transfer::public_transfer` is a function that moves an object (here, the `MetadataCap`) to a specified address (here, `ctx.sender()`, which is the address that published or initialized the coin).
* **Result:**

  * The sender (usually the coin creator) now owns the `MetadataCap` and can update the coin’s metadata in the future.

### Why is this important?

* **Metadata management:** Only the holder of the `MetadataCap` can update the coin’s metadata. By transferring it to yourself (the sender), you retain control over your coin’s branding and information.
* **Security:** If you ever want to make the metadata immutable, you can destroy the `MetadataCap`. [(See how)]().

---

Okay, so here’s what happens conceptually:

| Step | Function                 | What it does                                               |
| ---- | ------------------------ | ---------------------------------------------------------- |
| 1    | `treasury_cap.mint(...)` | Creates actual coins (your total supply).                  |
| 2    | `make_supply_burn_only`  | Locks the treasury so no new minting can occur (optional). |
| 3    | `finalize(...)`          | Finalizes and registers metadata (symbol, name, icon).     |
| 4    | `transfer(...)`          | Sends ownership of metadata to you (the creator).          |
| 5    | `return total_supply`    | Gives you the minted coins.                                |

---

### Common Beginner Issues (CBIs) and their solutions:

> **My coins and objects got wiped!**

This is an expected behavior on Devnet/Testnet.

**Devnet data is wiped regularly** as part of scheduled software updates. When this happens, all balances, objects, and accounts are reset, and your previous balance will disappear.

From the official documentation:

> Devnet data is wiped regularly as part of scheduled software updates. The data on Testnet persists through the regular update process, but might be wiped when necessary. Testnet data wipes are announced ahead of time.
>
> For more information about the release schedule of Sui networks, see [Sui Network Release](https://sui.io/networkinfo/).

**What can you do?**

* You can request new test SUI from the Devnet faucet.
* If you need more persistent balances, consider using Testnet instead, as its data is more stable (but still not guaranteed forever).

---

> **How do i publish my package?**

To publish your Move package to Sui Devnet, Testnet, or Mainnet, you **should use the Sui framework as a dependency** in your `Move.toml`.

**How to set the Sui framework as a dependency:**

You should set the Sui framework as a dependency **exactly as shown** below, unless you have a local copy of the Sui framework you want to use instead:

```toml
[dependencies]
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "framework/devnet" }
```

Remember to replace `devnet` with your desired network.

**Quick breakdown**:

* `git`: Points to the official Sui repository.
* `subdir`: Specifies the path to the Sui framework package inside the repo.
* `rev`: `"framework/devnet"` ensures you are using the version compatible with the current devnet.

This setup is the most reliable for devnet, as it matches the framework version running on the network.

---

**What if you want to use a local framework?**

If you have a local copy of the Sui framework (for example, if you cloned the repo), you can use:

```toml
[dependencies]
Sui = { local = "/path/to/sui/crates/sui-framework/packages/sui-framework" }
```

But for most users, the **git dependency is preferred** for devnet.

---

**Final Checklist for Publishing to Devnet**

1. Add the dependency as shown above.
2. Make sure you didn't tamper with the `edition` in your Move.toml file. If you did, make sure it is set to `"2024.beta"`.
3. Build your package:

   ```sh
   sui move build
   ```
4. Publish to devnet:

   ```sh
   sui client publish
   ```

---

> **I'm having issues with the devnet**
>
> [warning] Client/Server api version mismatch, client api version : 1.59.0, server api version : 1.58.0
>
> How can i fix this?

If you receive a warning about a client and server API version mismatch, update Sui using the version in the relevant branch (`mainnet`, `testnet`, `devnet`) of the Sui repo.

Update your Sui CLI to match the network version:

```
cargo install --locked --git https://github.com/MystenLabs/sui.git --branch devnet sui
```
Replace `devnet` with your target network branch.

---

## 📘 Check Out the Next Guide

Continue learning more about Fungible Tokens, Move, and Sui smart contracts [in the next guide in this series](https://github.com/FavourEjiogu/Fungible-Tokens/tree/main/regular_coin_example) on my GitHub. Here, i would show you how to mint the token you just published, and you'll learn about PTBs, Sui's object model and Transaction flow, and how they (alongside other concepts) all come together to create a fungible token (coin). Stay tuned for more hands-on examples that build directly on top of this one.

---

## 📜 References

* [The Official Sui Documentation](https://docs.sui.io)
* [The Move Book](https://move-book.com/)

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
