---
title: "Blockchain: Deploy NFT Contract in Near Protocol"
date: 2022-09-03T22:54:04+07:00
draft: false
---

## Intro 

Hello guys, I hope everyone is doing well, in this post I am going to create an NFT (Non-Fungible Token) contract in Near protocol, have you ever heard of near protocol? near protocol is a layer 1 blockchain network, like an Ethereum network, near uses **Proof of Stake** (POS) instead of **Proof of Work** (POW), besides of that the Near protocol has excellent features like **Nightshade Sharding** that can validate a transaction really fast, personally, I really enjoy developing Dapps in near rather than another blockchain, but because near is relatively new, there are very few tutorials out there, so this is one the reason why I created this article, by the way, you can learn more about near on this website [near developer](https://near.org/developers/).

In this article, we will use **Rust** as a programming language, if you don't familiar with rust don’t worry it’s not scary as you think, I can say that if you want to build a custom smart contract you only need to learn the basics of Rust, is true because the smart contract is limited to do crazy things like async process or concurrency ( not in cross contract call ). Rust is only used to compile the logic into wasm binary, other than Rust, you can actually create a smart contract using assembly language & javascript [near-sdk-js](https://docs.near.org/tools/near-sdk-js), but I recommended to use rust to build a smart contract because it's safer and has a really good compiler.

## Install the Tools

First, you have to install **rustrc** to compile the rust code, as the official website said, it’s recommended to use rustup, [install rustup](https://www.rust-lang.org/tools/install), just follow the instructions on the website.
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Check the installation:
```
➜  ~ rustc --version
rustc 1.61.0 (fe5b13d68 2022-05-18)
➜  ~ cargo --version
cargo 1.61.0 (a028ae4 2022-04-29)
```

The second tool is Install **near-cli**, this tool is used to deploy the wasm binary into the near blockchain, near-cli is running on top of nodejs, install the nodejs if you haven’t installed it yet on your machine [download nodejs](https://nodejs.org/en/download/). 

Install near-cli as a global package using global argument
```
npm i -g near-cli
```
Check the installation:
```
near --version
```
## Compile the Smart contract

In this tutorial, we will use the NFT contract example from the official repository, like the Ethereum protocol Near also has its own standard for the NFT contract, near uses **NEP-171** for the core functionality of the interface method, its official standard that near provided and recommended to create the NFT smart-contract, if you want to learn more about that you can read all of the standards on [nomicon](https://nomicon.io).

Build the contract from the example project from their repository, the example project is very enough to cover all the NFTs contract's functionality, that example also uses the [near-contract-standards](https://github.com/near/near-sdk-rs/tree/master/near-contract-standards) 

Clone the repository
```
git clone https://github.com/near-examples/NFT.git
```
Build the contract
```
cd NFT
./scripts/build.sh
```
Let’s verify that the contract is has compiled successfully
```
➜  NFT git:(master) cd res && ls
README.md  approval_receiver.wasm  non_fungible_token.wasm  token_receiver.wasm
```
looks that we now have **non_fungible_token.wasm** in the res directory, this file is the wasm binary contract that will be deployed to the near network.

## Deploy Smart Contract Using near-dev 
Open your terminal, and go to the directory of the wasm file located.
```
cd res
near dev-deploy non_fungible_token.wasm
```
The dev-deploy command will automatically create a testnet account and will be deployed the contract to that account, the contract address will be shown if the deploying process is successfully processed, and the address will be like this **dev-\<timestamp>-\<random_number>**. dev-deploy is useful when you want to test your contract immediately in the testnet network.
```
➜  res git:(master) near dev-deploy --wasmFile non_fungible_token.wasm

Starting deployment. Account id: dev-1662209846446-10678431456293, node: https://rpc.testnet.near.org, helper: https://helper.testnet.near.org, file: non_fungible_token.wasm
Transaction Id Gph2kTpJ2DE7Wb2aN1RcPhJCFCNatDtCCFYUtnb6Rgby
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/Gph2kTpJ2DE7Wb2aN1RcPhJCFCNatDtCCFYUtnb6Rgby
Done deploying to dev-1662209846446-10678431456293
```
In this example the dev-deploy contract address is **dev-1662209846446-10678431456293**

## Deploy Smart Contract Within Your Account
Unlike other protocols, in the near, you can deploy the contract to your own account, your account will have executable code and perform transactions in the account’s name.

Before you deploy, you have to connect/login the near account into cli, if you haven’t had a near account then create it first, I recommend using the testnet account for this tutorial. The wallet url is [wallet testnet](https://wallet.testnet.near.org/create) and [wallet mainnet](https://wallet.near.org/create).

Okay, make sure you have logged your account into your web wallet, and type login on your terminal.
```
near login
```
Follow the instructions, and approve the login action on the web. By default near-cli will use the testnet RPC, 
```
➜  res git:(master) near login

Please authorize NEAR CLI on at least one of your accounts.

If your browser doesn't automatically open, please visit this URL
https://wallet.testnet.near.org/login/?referrer=NEAR+CLI&public_key=ed25519%3A5AdYZAcAVnt98&success_url=http%3A%2F%2F127.0.0.1%3A5000
Please authorize at least one account at the URL above.

Which account did you authorize for use with NEAR CLI?
Enter it here (if not redirected automatically):
```
If you want to switch to the mainnet network you have to **export NEAR_ENV=mainnet** before you do some action within near-cli.
```
export NEAR_ENV=mainnet near login
```
Make sure that the account has been added successfully
```
➜  ~ ls ~/.near-credentials/testnet
<your_account>.testnet.json
```
If everything is ready, now you can deploy the contract to your own account
```
near deploy –accountId <your_acccount.testnet> --wasmFile non_fungible_token.wasm
```
If the contract is successfully deployed, now your account has 2 functionality, as a **normal wallet** and as a **contract address** that can execute the smart contract methods. FYI 1 account address only support storing 1 smart contract, if you deployed another smart contract to the same account it will break the state, so be careful.

Congratulations you have successfully deployed the smart contract in the near protocol, let’s continue to test the contract methods.

## Test Smart Contract
To execute the methods in the contract, you need to use near-cli, the near-cli will call the methods via RPC network, it’s has 2 types of interaction, view method, and call method.

- **call**: makes a contract call that can invoke change or view methods.
- **view**: makes a contract call that can only invoke a view method.

The difference between them is, the call method needs to attach some near balance to cover the **gas fee** and the view method doesn’t, even though you can actually execute the view method via call method but it's not efficient.

```
near <call | view> –accountId <your_account_address> <the_contract_address> <method> <arg_json_formated>
```
- The accountId is not necessary when you do the view method.

### Init the contract (call)
```
near call --accountId <your_account> <the_contract_address_or_can_be_your_account_address> new_default_meta '{"owner_id": "<your_account_or_someone_else>"}'
```
```
➜  ~ near call --accountId dev-1662209846446-10678431456293 dev-1662209846446-10678431456293 new_default_meta '{"owner_id": "dev-1662209846446-10678431456293"}'
Scheduling a call: dev-1662209846446-10678431456293.new_default_meta({"owner_id": "dev-1662209846446-10678431456293"})
Doing account.functionCall()
Transaction Id 2erm5cdzDLwX8niT7gAmY9hnxTjmw7uzcfFU9vAtyTZb
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/2erm5cdzDLwX8niT7gAmY9hnxTjmw7uzcfFU9vAtyTZb
''
```
### Mint the new token (call)
```
near call  --accountId <your_account> <contract_address> nft_mint '{"token_id": "0", "receiver_id": "<your_account_or_someone_else>", "token_metadata": { "title": "Olympus Mons", "description": "Tallest mountain in charted solar system", "media": "https://upload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg", "copies": 1}}' --depositYocto 7430000000000000000000
```
- depositYocto is an argument to deposit some balance to the contract, usually, this needed to cover the user's storage
```
➜  ~ near call  --accountId dev-1662209846446-10678431456293 dev-1662209846446-10678431456293 nft_mint '{"token_id": "0", "receiver_id": "dev-1662209846446-10678431456293", "token_metadata": { "title": "Olympus Mons", "description": "Tallest mountain in charted solar system", "media": "https://upload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg", "copies": 1}}' --depositYocto 7430000000000000000000
Scheduling a call: dev-1662209846446-10678431456293.nft_mint({"token_id": "0", "receiver_id": "dev-1662209846446-10678431456293", "token_metadata": { "title": "Olympus Mons", "description": "Tallest mountain in charted solar system", "media": "https://upload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg", "copies": 1}}) with attached 0.00743 NEAR
Doing account.functionCall()
Receipt: 4ThsZ6CWgC9QL6E2EdFp4xLrsnvKahWABYe5A34KzGmH
        Log [dev-1662209846446-10678431456293]: EVENT_JSON:{"standard":"nep171","version":"1.0.0","event":"nft_mint","data":[{"owner_id":"dev-1662209846446-10678431456293","token_ids":["0"]}]}
Transaction Id DRMPmkW87vSAhofS6nRCYJrXzb4ZjzVS8oW7axWhNjrJ
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/DRMPmkW87vSAhofS6nRCYJrXzb4ZjzVS8oW7axWhNjrJ
{
  token_id: '0',
  owner_id: 'dev-1662209846446-10678431456293',
  metadata: {
    title: 'Olympus Mons',
    description: 'Tallest mountain in charted solar system',
    media: 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg',
    media_hash: null,
    copies: 1,
    issued_at: null,
    expires_at: null,
    starts_at: null,
    updated_at: null,
    extra: null,
    reference: null,
    reference_hash: null
  },
  approved_account_ids: {}
}
```
### View token (view)
Okey let's view the token that we have minted.
```
near view <contract_address> nft_token ‘{“token_id”: “1”}’
```
```
➜  ~ near view dev-1662209846446-10678431456293 nft_token '{"token_id": "1"}'
View call: dev-1662209846446-10678431456293.nft_token({"token_id": "1"})
{
  token_id: '1',
  owner_id: 'dev-1662209846446-10678431456293',
  metadata: {
    title: 'Olympus Mons',
    description: 'Tallest mountain in charted solar system',
    media: 'https://akjsdfpkajskdfjasoidfjiasdjfiasjdfoiajsdfoipjasoidfjasoidjfpaoisdjfpoiasjdfoijasdoifjasiojdfiosjdfupload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg',
    media_hash: null,
    copies: 1,
    issued_at: null,
    expires_at: null,
    starts_at: null,
    updated_at: null,
    extra: null,
    reference: null,
    reference_hash: null
  },
  approved_account_ids: {}
}
```
### Transfer the token to another user (call)
```
near call --accountId <your_account> <contract_address> nft_transfer '{"token_id": "1", "receiver_id": "<receiver_address>", "memo": "transfer ownership"}' --depositYocto 1

```
Change the owner of the token, the current owner is **dev-1662209846446-10678431456293**, let's transfer the token to **kangmalu.testnet**.
```
➜  ~ near call --accountId dev-1662209846446-10678431456293 dev-1662209846446-10678431456293 nft_transfer '{"token_id": "1", "receiver_id": "kangmalu.testnet", "memo": "transfer ownership"}' --depositYocto 1
Scheduling a call: dev-1662209846446-10678431456293.nft_transfer({"token_id": "1", "receiver_id": "kangmalu.testnet", "memo": "transfer ownership"}) with attached 0.000000000000000000000001 NEAR
Doing account.functionCall()
Receipt: mNd92jUgSuCuLKX1WyVk888u4oNRGd2WByMR7CqXMB4
        Log [dev-1662209846446-10678431456293]: EVENT_JSON:{"standard":"nep171","version":"1.0.0","event":"nft_transfer","data":[{"old_owner_id":"dev-1662209846446-10678431456293","new_owner_id":"kangmalu.testnet","token_ids":["1"],"memo":"transfer ownership"}]}
Transaction Id CrvL5yreX9XJUJbMcJXBG3nEVNB5MjT4c79DsGGvmbRV
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/CrvL5yreX9XJUJbMcJXBG3nEVNB5MjT4c79DsGGvmbRV
''
```

### View token after the owner changed (view)
Let's view the token after the owner is changed.
```
➜  ~ near view dev-1662209846446-10678431456293 nft_token '{"token_id": "1"}'
View call: dev-1662209846446-10678431456293.nft_token({"token_id": "1"})
{
  token_id: '1',
  owner_id: 'kangmalu.testnet',
  metadata: {
    title: 'Olympus Mons',
    description: 'Tallest mountain in charted solar system',
    media: 'https://akjsdfpkajskdfjasoidfjiasdjfiasjdfoiajsdfoipjasoidfjasoidjfpaoisdjfpoiasjdfoijasdoifjasiojdfiosjdfupload.wikimedia.org/wikipedia/commons/thumb/0/00/Olympus_Mons_alt.jpg/1024px-Olympus_Mons_alt.jpg',
    media_hash: null,
    copies: 1,
    issued_at: null,
    expires_at: null,
    starts_at: null,
    updated_at: null,
    extra: null,
    reference: null,
    reference_hash: null
  },
  approved_account_ids: {}
}
```

That’s all guys, I hope this article can help you when you want to learn more about near protocol, thank you very much, and have a nice day.
