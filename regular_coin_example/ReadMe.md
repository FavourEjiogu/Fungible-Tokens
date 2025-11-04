# Creating a Regular Deflationary Coin on Sui (Move Tutorial) - Part 2: Publishing and Minting

>This guide shows you how to mint a deflationary coin on the Sui blockchain using Move. It mints the total supply, and keeps the ability to update your coin’s currency metadata (name, symbol, icon, and description) whenever you want. 
>
> You'll learn about PTBs, Sui's object model and Transaction flow, and how they (alongside other concepts) all come together to create a fungible token (coin).

---

## 🧠 Before You Begin

**Estimated Time to Completion:** 15-25 minutes  

**Prerequisites**
- Successfully Completed [Part 1](https://github.com/FavourEjiogu/Fungible-Tokens/tree/main/regular_coin)
- A little curiosity, patience, and ☕  

---

## Understanding What’s Going On

I know you're probably wondering why your coin doesn't exist yet, well that's because all we did was run `sui move build`

What does that do? Just know that it basically compiles your code and checks for any errors

#### To actually create the coin, we need to do 2 key things:

1. Publish it
2. Call the function `new_currency`

---

So, here we are actually going to **"create"** the coin:

#### Before we continue:
- The name of the package in this example is `regular_coin_example`
- The name of the coin is `MelloCoin`
- The name of the struct is also `MelloCoin` (remember why)
- The name of the module is `regular_coin`
- Check out the source code [here]()

---

After building with `sui move build`, if you followed this guide correctly, there shouldn't be any errror, but if you see an error, go through your code to see if you made any mistake or typo

#### Your result should look like:

```bash
INCLUDING DEPENDENCY Bridge
INCLUDING DEPENDENCY SuiSystem
INCLUDING DEPENDENCY Sui
INCLUDING DEPENDENCY MoveStdlib
BUILDING regular_coin_example
Total number of linter warnings suppressed: 1 (unique lints: 1)
```
![Regular coin build image](../images/regular%20coin%20build.png)

---

Now that we have successfully built our package, the next step now is to publish our package to the sui network.

#### To do that:

**Run:** `sui client publish`

**Scroll to:** ```Object Changes```

#### It should look like this:

![sui client publish example](../images/sui%20client%20publish%20example.png)

---

### Now lets disect what happened:

#### 1. **Creation of the `UpgradeCap` Object**
First we can see that a new object was created, the `UpgradeCap`.

When you publish a package, Sui creates an `UpgradeCap` object.  

- **Purpose:** This object gives the publisher the ability to upgrade the package in the future.

- **Details you can inspect:**  
  - **ObjectID:** Unique identifier for the `UpgradeCap`.
  - **Owner:** The account that owns the capability.
  - **ObjectType:** Always `0x2::package::UpgradeCap` for this object.
  - **Version:** Tracks if the capability has changed.
  - **Digest:** Hash of the object’s state.

---

#### 2. **Mutation of the SUI Coin Object**

Secondly we can see that an object was mutated, `SUI`.

Publishing a package costs gas, which is paid in SUI. The SUI coin object in your wallet is mutated to reflect the deduction.

- **Details you can inspect:**  

  - **ObjectType:** `0x2::coin::Coin<0x2::sui::SUI>`
  - **Owner:** Your account.
  - **Version:** Increments after mutation.
  - **Digest:** New hash after the transaction.

---

#### 3. **Publishing the Package**

Lastly, we can see that an object was published, the Package (which was `regular_coin_example`), now exists on the sui network, on-chain (whenever you see/hear "on-chain", it basically means that whatever they're referring to now has an address that can be used to verify the authenticity of the action through an explorer, in Sui's case, they're indicating that the item is an object and can be verified through an ID ), and its module is also onchain.

- **Details you can inspect:**

  - **PackageID:** Unique identifier for the package.
  - **Version:** Starts at 1.
  - **Digest:** Hash of the package.
  - **Modules:** List of modules published (e.g., `regular_coin_example`).

---

#### **On-Chain Verification**

So now this basically means that i can pick out any of the IDs and **verify** who the **sender** or the **owner** of the **object** is, the **type of object** it is (is it a coin? is it a capability? is it a package? etc), the **version of the object** (has it been altered/upgraded?), and lastly the **transaction digest** (to dig deeper into the object)

---

Disecting what happened during a transaction is very crucial for beginners, because it will help you understand the Sui object model and transaction flow.

If you want to see these details for any object, you can use any Sui explorer or even the Sui CLI to inspect by object ID.

---



> **But how do i actually see the coin i created? At the moment, i can't see it, it's not in my wallet**.

When you run `sui client publish`, **you are only deploying your Move package** (the smart contract code).  
**This does NOT automatically create or mint any coins.**

To do that, we would have to `call` the function that creates or mints the coins.

But before that, for context, Devnet was reset at time i was making this guide, so i had to re-publish the package which gave me a different PackageID. For the rest of this guide, my new PackageID is `0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb`

### Now, there are two main ways to `call` the function:

#### 1. Using `sui client call`:

```bash
sui client call --package 0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb --module regular_coin --function new_currency --args @0xc
```

But because we are just returning an object (`total_supply`) inside a function (`new_currency`), we can't use this method. Using it would result to an error like:

``` bash
failure due to UnusedValueWithoutDrop
```

This is because Move is strict about resource management. 

Every value must be used, moved, stored, returned, or dropped (if it has the drop ability).

If you just “leave” something unused at the end of a function, Move panics with `UnusedValueWithoutDrop`.

But in our case, we **DID** return something, which was `total_supply`, so what could actually be the **real issue** here?

So, you see, by calling `new_currency`, we are trying to mint coins right? But ask yourself, **WHERE** are these coins going to? 

Exactly! So we need to somehow mint **AND** transfer the minted coins to our address, at the **same** time.

That can only be done with **PTBs**

You can try it to see for yourself :)

---

#### 2. Using PTBs (Programmable Transaction Blocks):

```bash
sui client ptb --move-call 0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::new_currency @0xc --assign "total_supply" --transfer-objects "[total_supply]" @0x53e18124ca06bf820af
73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707
```

A PTB (Programmable Transaction Block) in Sui is a way to **compose** and **execute multiple actions** (like Move calls, object transfers, coin splits, etc.) in a **single transaction**.

What can you do with PTBs?

- `Publish` Move packages
- Call Move functions (`move-call`)
- `Assign` results to variables
- `Transfer` objects
- `Split`/`Merge` coins
- `Create vectors` of Move values

**So, let's breakdown what happened in our PTB command step-by-step:**

```bash
sui client ptb \
  --move-call 0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::new_currency @0xc \
  --assign "total_supply" \
  --transfer-objects "[total_supply]" @0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707
```

#### What each part does:

1. **`--move-call 0x2d08...::regular_coin::new_currency @0xc`**
   - This calls the `new_currency` function in the `regular_coin` module at the specified address(the **PackageID** of `regular_coin_example`).
   - `@0xc` is the argument passed to the function (which is the address of **`CoinRegistry`**).

2. **`--assign "total_supply"`**
   - The result of the `move-call` is assigned to a variable named `total_supply`.
   - This means whatever object or value that is returned by `new_currency` **will now be referenced as** `total_supply` in the next steps.

3. **`--transfer-objects "[total_supply]" @0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707`**
   - This transfers the object(s) in the list `[total_supply]` to the address `0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707`.
   - Here, the list contains only one value, which is the variable `total_supply` that we got earlier, and transfers it to "`0x53e18...707`" which is my address.

#### What happened in this transaction?
 
- We called a function to create a new currency.

- The result (which is an object (`total_supply`, in line 41) representing the total coins minted with the value that we set in line 7 (`TOTAL_SUPPLY`),  was assigned to `total_supply`).

- We then transferred this `total_supply` object to a specific address (Which is my address).

---

#### Your result should like this:


```bash
Transaction Digest: En3NeSNwayQcURfEcoeV7HpfXsc2VvGRSQxWwEKkYM7f
╭──────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Transaction Data                                                                                             │
├──────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                   │
│ Gas Owner: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                │
│ Gas Budget: 12159832 MIST                                                                                    │
│ Gas Price: 1000 MIST                                                                                         │
│ Gas Payment:                                                                                                 │
│  ┌──                                                                                                         │
│  │ ID: 0x1c6bbe2a9b8f001357e12255ab0418b571841de4824060ea7dc4d60c7f65e16b                                    │
│  │ Version: 20                                                                                               │
│  │ Digest: 5RJoRNH9hwGXBQZVTcKYBp1FVUbPWcDKcZQASKWpomGW                                                      │
│  └──                                                                                                         │
│                                                                                                              │
│ Transaction Kind: Programmable                                                                               │
│ ╭──────────────────────────────────────────────────────────────────────────────────────────────────────────╮ │
│ │ Input Objects                                                                                            │ │
│ ├──────────────────────────────────────────────────────────────────────────────────────────────────────────┤ │
│ │ 0   Shared Object    ID: 0x000000000000000000000000000000000000000000000000000000000000000c              │ │
│ │ 1   Pure Arg: Type: address, Value: "0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707" │ │
│ ╰──────────────────────────────────────────────────────────────────────────────────────────────────────────╯ │
│ ╭──────────────────────────────────────────────────────────────────────────────────╮                         │
│ │ Commands                                                                         │                         │
│ ├──────────────────────────────────────────────────────────────────────────────────┤                         │
│ │ 0  MoveCall:                                                                     │                         │
│ │  ┌                                                                               │                         │
│ │  │ Function:  new_currency                                                       │                         │
│ │  │ Module:    regular_coin                                                       │                         │
│ │  │ Package:   0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb │                         │
│ │  │ Arguments:                                                                    │                         │
│ │  │   Input  0                                                                    │                         │
│ │  └                                                                               │                         │
│ │                                                                                  │                         │
│ │ 1  TransferObjects:                                                              │                         │
│ │  ┌                                                                               │                         │
│ │  │ Arguments:                                                                    │                         │
│ │  │   Result 0                                                                    │                         │
│ │  │ Address: Input  1                                                             │                         │
│ │  └                                                                               │                         │
│ ╰──────────────────────────────────────────────────────────────────────────────────╯                         │
│                                                                                                              │
│ Signatures:                                                                                                  │
│    Eme9dP5OLBBBetc9xfk7Su4MG0rbJsNk0rbxfuW/Ljj8eqyG8NUgK1SoWDubA1Q6nTfj0jCrxU3qIKCdQ3VbCw==                  │
│                                                                                                              │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
╭───────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Transaction Effects                                                                               │
├───────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Digest: En3NeSNwayQcURfEcoeV7HpfXsc2VvGRSQxWwEKkYM7f                                              │
│ Status: Success                                                                                   │
│ Executed Epoch: 83                                                                                │
│                                                                                                   │
│ Created Objects:                                                                                  │
│  ┌──                                                                                              │
│  │ ID: 0x54f362c253d10e8ef0561669acbcea18b974d7fdc4d42791ae7874a2e04a856e                         │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )  │
│  │ Version: 21                                                                                    │
│  │ Digest: C3dpj4RDwj8nzmaUwJJpHSts5SgWnPPfbmcmcgsdfPEa                                           │
│  └──                                                                                              │
│  ┌──                                                                                              │
│  │ ID: 0x7b51434eb5dbac7837ec02d34dfaf7f730c6200c86e981012195ddcbaf033071                         │
│  │ Owner: Shared( 21 )                                                                            │
│  │ Version: 21                                                                                    │
│  │ Digest: Fctx1ZBHutxYxcbPcMNDQ8FMAXTWTe7HhpAw4P9WC4g3                                           │
│  └──                                                                                              │
│  ┌──                                                                                              │
│  │ ID: 0xac62bcba24d2ea456e2dc82626f8e3aca1f311fe34a90543834214c224f46db4                         │
│  │ Owner: Object ID: ( 0x000000000000000000000000000000000000000000000000000000000000000c )       │
│  │ Version: 21                                                                                    │
│  │ Digest: FivTwsdSBtURPCudrcGFF9hV9MMNiRWK2oHZxhMoj3Ge                                           │
│  └──                                                                                              │
│  ┌──                                                                                              │
│  │ ID: 0xe0e88385a78cb111738a0b19c3eae57a28da7f9df36d2efa68247896ddccdf9c                         │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )  │
│  │ Version: 21                                                                                    │
│  │ Digest: 5vxeAzwS6TsF1r1VtcCKDpDwo7H7syBQn5XJaKNB2gJL                                           │
│  └──                                                                                              │
│ Mutated Objects:                                                                                  │
│  ┌──                                                                                              │
│  │ ID: 0x000000000000000000000000000000000000000000000000000000000000000c                         │
│  │ Owner: Shared( 1 )                                                                             │
│  │ Version: 21                                                                                    │
│  │ Digest: FD91e15gcNkqdfjccDLSnLsqy8J6opyvLV6FxkBoboSN                                           │
│  └──                                                                                              │
│  ┌──                                                                                              │
│  │ ID: 0x1c6bbe2a9b8f001357e12255ab0418b571841de4824060ea7dc4d60c7f65e16b                         │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )  │
│  │ Version: 21                                                                                    │
│  │ Digest: 5P9PNPiuXhy1MzNb1hLnDJLEX25r1DK8TsBhA64eKWsQ                                           │
│  └──                                                                                              │
│ Shared Objects:                                                                                   │
│  ┌──                                                                                              │
│  │ ID: 0x000000000000000000000000000000000000000000000000000000000000000c                         │
│  │ Version: 13                                                                                    │
│  │ Digest: 9nhSbyzucpewLktgVydqHybwZATTZPrjgATpAVG6cwaL                                           │
│  └──                                                                                              │
│ Gas Object:                                                                                       │
│  ┌──                                                                                              │
│  │ ID: 0x1c6bbe2a9b8f001357e12255ab0418b571841de4824060ea7dc4d60c7f65e16b                         │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )  │
│  │ Version: 21                                                                                    │
│  │ Digest: 5P9PNPiuXhy1MzNb1hLnDJLEX25r1DK8TsBhA64eKWsQ                                           │
│  └──                                                                                              │
│ Gas Cost Summary:                                                                                 │
│    Storage Cost: 11529200 MIST                                                                    │
│    Computation Cost: 1000000 MIST                                                                 │
│    Storage Rebate: 2347488 MIST                                                                   │
│    Non-refundable Storage Fee: 23712 MIST                                                         │
│                                                                                                   │
│ Transaction Dependencies:                                                                         │
│    DFpnmGjCFSPgzLnwCWcbGMuSmcwnKxNB9LXvgzdY4k16                                                   │
│    EG2pG1feAncZBay4GLLjK4bUhBtRdvDCBYgp3ct1AxEz                                                   │
│    FN2HvWfzV95DN1JGiS1QzWFjVRqJwUoswn6aqWQ79uYG                                                   │
╰───────────────────────────────────────────────────────────────────────────────────────────────────╯
╭─────────────────────────────╮
│ No transaction block events │
╰─────────────────────────────╯

╭──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Object Changes                                                                                                                               │
├──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Created Objects:                                                                                                                             │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0x54f362c253d10e8ef0561669acbcea18b974d7fdc4d42791ae7874a2e04a856e                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )                                             │
│  │ ObjectType: 0x2::coin::Coin<0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::MelloCoin>                  │
│  │ Version: 21                                                                                                                               │
│  │ Digest: C3dpj4RDwj8nzmaUwJJpHSts5SgWnPPfbmcmcgsdfPEa                                                                                      │
│  └──                                                                                                                                         │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0x7b51434eb5dbac7837ec02d34dfaf7f730c6200c86e981012195ddcbaf033071                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Shared( 21 )                                                                                                                       │
│  │ ObjectType: 0x2::coin_registry::Currency<0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::MelloCoin>     │
│  │ Version: 21                                                                                                                               │
│  │ Digest: Fctx1ZBHutxYxcbPcMNDQ8FMAXTWTe7HhpAw4P9WC4g3                                                                                      │
│  └──                                                                                                                                         │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0xac62bcba24d2ea456e2dc82626f8e3aca1f311fe34a90543834214c224f46db4                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Object ID: ( 0x000000000000000000000000000000000000000000000000000000000000000c )                                                  │
│  │ ObjectType: 0x2::dynamic_field::Field<0x2::derived_object::Claimed, 0x2::derived_object::ClaimedStatus>                                   │
│  │ Version: 21                                                                                                                               │
│  │ Digest: FivTwsdSBtURPCudrcGFF9hV9MMNiRWK2oHZxhMoj3Ge                                                                                      │
│  └──                                                                                                                                         │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0xe0e88385a78cb111738a0b19c3eae57a28da7f9df36d2efa68247896ddccdf9c                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )                                             │
│  │ ObjectType: 0x2::coin_registry::MetadataCap<0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::MelloCoin>  │
│  │ Version: 21                                                                                                                               │
│  │ Digest: 5vxeAzwS6TsF1r1VtcCKDpDwo7H7syBQn5XJaKNB2gJL                                                                                      │
│  └──                                                                                                                                         │
│ Mutated Objects:                                                                                                                             │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0x000000000000000000000000000000000000000000000000000000000000000c                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Shared( 1 )                                                                                                                        │
│  │ ObjectType: 0x2::coin_registry::CoinRegistry                                                                                              │
│  │ Version: 21                                                                                                                               │
│  │ Digest: FD91e15gcNkqdfjccDLSnLsqy8J6opyvLV6FxkBoboSN                                                                                      │
│  └──                                                                                                                                         │
│  ┌──                                                                                                                                         │
│  │ ObjectID: 0x1c6bbe2a9b8f001357e12255ab0418b571841de4824060ea7dc4d60c7f65e16b                                                              │
│  │ Sender: 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707                                                                │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )                                             │
│  │ ObjectType: 0x2::coin::Coin<0x2::sui::SUI>                                                                                                │
│  │ Version: 21                                                                                                                               │
│  │ Digest: 5P9PNPiuXhy1MzNb1hLnDJLEX25r1DK8TsBhA64eKWsQ                                                                                      │
│  └──                                                                                                                                         │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
╭───────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Balance Changes                                                                                           │
├───────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│  ┌──                                                                                                      │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )          │
│  │ CoinType: 0x2::sui::SUI                                                                                │
│  │ Amount: -10181712                                                                                      │
│  └──                                                                                                      │
│  ┌──                                                                                                      │
│  │ Owner: Account Address ( 0x53e18124ca06bf820af73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707 )          │
│  │ CoinType: 0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::MelloCoin  │
│  │ Amount: 1000000000000000000                                                                            │
│  └──                                                                                                      │
╰───────────────────────────────────────────────────────────────────────────────────────────────────────────╯


```

#### And you can confirm by running `sui client balance`

```bash
 ╭────────────────────────────────────────────────────╮
│ Balance of coins owned by this address             │
├────────────────────────────────────────────────────┤
│ ╭────────────────────────────────────────────────╮ │
│ │ coin       balance (raw)        balance        │ │
│ ├────────────────────────────────────────────────┤ │
│ │ Sui        39898795436          39.89 SUI      │ │
│ │ MelloCoin  1000000000000000000  1000.00M MLC   │ │
│ ╰────────────────────────────────────────────────╯ │
╰────────────────────────────────────────────────────╯
```

From my experience, you might need a stable internet connection for this to work.

And just look at that! We have **SUCCESSFULLY** created a deflationary coin with a fixed supply!! 

You are now officially a **ROOKIE BLOCKCHAIN DEVELOPER!**

---

### Quick Recap:

- **Resource Management:** In Sui Move, objects that do not have the `drop` ability, **must** be transfered, destroyed, or otherwise consumed. If you just let them go out of scope, you'll get an `UnusedValueWithoutDrop` error.
- **PTB Usage:** PTB lets you **chain** these actions together, **ensuring** that **ALL** resources are properly handled in a **single transaction**. 
- The PTB approach is also necessary for any function that returns a resource without the drop ability, not just for coins.

---

### What to check before using PTBs

- **Function Signature:** Make sure your function returns an object that can be transferred.
- **Recipient Address:** The address you transfer to, **must** be able to receive the object (i.e., it must be a valid Sui address).

---

#### Tip:

Use a **dry run** to see the intended effects of your code without spending gas or deploying to a live network, which is vital for catching mistakes in an irreversible environment. 

You can acheive this by adding `--dry-run` to the end of your PTB command chain (Or even at the end of a `sui client call` command).


---

### Common Beginner Issues (CBIs):

> **I don't know what's wrong, i did everything right, but i got an error saying:** 
```bash
Error executing transaction `abc...xyz`: CommandArgumentError {arg_idx: 0, kind InvalidUsageOfPureArg } in command 0
```

My brother (or sister), please ensure you used **`@`** before the addresses, **yes** `0xc` is an address. The only exception to this, is in the `PackageID` field.

**For example:**

```bash
sui client ptb --move-call 0x2d081f04e119f6a35e9a1e154513cf94be267845283b00c591c5344fb6e902eb::regular_coin::new_currency 0xc --assign "total_supply" --transfer-objects "[total_supply]" @0x53e18124ca06bf820af
73d64254e852e2e0801ec1a44dd07b1c0ef39c6ab2707
```

Will **definitely** throw you a `CommandArgumentError`.


Alright, that's all for now, i'll be adding more CBIs after developer workshops. 


## 📘 Check Out the Next Guide

**[Here](https://github.com/FavourEjiogu/Non-Fungible-Tokens)**, i will introduce you to the concept of non-fungibility, and how you can create your very own NFT.

I’m also currently working on other kinds of fungible tokens, and I’ll add references here once I’m done, hence the need for you to star the repo.

---

## ❤️ Support the Author

If you found this guide helpful, please follow me for more **free** Move/Sui content:

🐦 **X (Twitter):** [Mello](https://x.com/mellothetrader_)

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

**Made with ❤️ and lots of ☕ by [Mello](https://x.com/mellothetrader_) for the [Sui Community](https://x.com/SuiCommunity)**

