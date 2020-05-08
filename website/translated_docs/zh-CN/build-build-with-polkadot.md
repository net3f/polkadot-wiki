---
id: build-build-with-polkadot
title: Polkadot 開發者入门指南
sidebar_label: Polkadot 開發者入门指南
---

_This article is the up-to-date version of the blog post: [Everything you Need to Know to Prepare for Polkadot](https://medium.com/polkadot-network/everything-you-need-to-know-to-prepare-for-polkadot-32d08b929735)._

Polkadot is a blockchain protocol with two goals: providing **shared security** among all connected parachains and allowing all connected chains to **interoperate** by using [XCMP](learn-crosschain). With the advent of [PDKs](build-pdk) like Parity Substrate and Cumulus, the time it takes to develop and launch a new chain has dropped significantly. Whereas before it would take years to launch a new chain, now it may only take weeks or even days.

This guide will walk you through the steps you can take today to get started building your vision with Polkadot. It will explain the difference between a parachain and a smart contract (and why one may be better suited for your application over the other). It will lay out the resources that are available now and the ones that are coming soon so that you can get started creating your application in anticipation of the Polkadot mainnet launch later this year.

## 我们处于哪里？

- Polkadot release: **Early 2020**
- Canary release: **Kusama**
- Current testnet: **Westend**
- Substrate: **2.0.0**
- Cumulus: **In development** ([Demo Available](https://github.com/paritytech/cumulus))
- ink!: **In development** ([Documentation](https://www.substrate.io/kb/smart-contracts))

## What you need to know

Polkadot is currently on the v0.7 release with a testnet called Westend and a value-bearing canary network called Kusama. Polkadot is being built with [implementations in various programming languages](learn-implementations) ranging from Rust to JavaScript. Currently the leading implementation is built in Rust and built on the Substrate framework. Substrate is a library that allows developers to develop entire blockchain applications with ease by bundling together a networking protocol, consensus, and Wasm interpreter. Cumulus, an extension to Substrate, will allow any Substrate built chain to connect to Polkadot and become a parachain. Substrate is currently nearing its official 2.0.0 tagged release that will solidify its API.

Polkadot does not natively support smart contracts, however there will be parachains that do. Substrate chains can include smart contract functionality by using the [Contracts](https://github.com/paritytech/substrate/tree/master/frame/contracts) pallet for Wasm contracts or the [EVM](https://github.com/paritytech/substrate/tree/master/frame/evm) pallet in FRAME. The contracts pallet enables a chain to use Wasm-compiled contracts that can be deployed permissionlessly by users or with specific rules dependent on the chain. To facilitate development of Wasm smart contracts, Parity is also developing [ink!](https://github.com/paritytech/ink), a domain specific language built in Rust for writing smart contracts.

Polkadot is planned to go live with an initial release early in 2020, depending on security audits and launch provisions outside of control of the team. Now that the tools have started to appear and stabilize, there has not been a better time to get your feet wet and start preparing for launch. But wait! Before you jump head-first into the code, you should think about the kind of decentralized application you want to make and understand the different paradigms available to developers who want to build on Polkadot.

## What is the difference between building a parachain, a parathread, or a smart contract?

Polkadot provides a few ways for you to deploy your application: as a smart contract on an existing parachain, as your own parachain, or as a parathread. There are trade-offs when working with each of these and reading this section will help you understand them.

Parachains are individual chains containing their own runtime logic that benefit from the shared security and the cross-chain messaging provided by the Polkadot relay chain. Parachains permit a high degree of flexibility and customization but will require more effort to create and maintain.

Parathreads are like parachains and enable the developer to have lower-level control of the logic of their application. The main difference between the two is economic, since parathreads will be much less expensive to secure than a parachain. The lower costs of parathreads are due to the fact that parathreads will only produce a block when they need to, unlike parachains which have secured a slot to produce a block at every block of the relay chain. When building a parathread, you will use the same tools (like PDKs) and you get all of the benefits of building a parachain, without the drawback of the cost.

On the Polkadot mainnet, there will be parachains that act as smart contract platforms. Smart contracts are executable programs that exist on only a single chain and are limited in complexity. Because they exist on a single chain, they can have smooth interoperability with other smart contracts on the same chain. However, they will always be constrained and limited by the inherent characteristics of their host chain.

If there is a need to have a large amount of control over the design and features of your application, a parachain is a better choice. Keep in mind, smart contracts can be used as a testing ground before later being turned into full-fledged parachains. Smart contract platforms will usually have more convenient tooling like IDEs to facilitate quick iterations. A smart contract MVP could be created to gauge user interest before putting in the work to build out a parachain.

Parachains grant the creators more space to build the monetary system and other aspects of the chain from the ground up. They will allow for more succinct and efficient execution of complex logic than could ever be offered by a smart contract platform. Parachains also offer more flexibility in the form of governance and can perform complete upgrades in a less controversial way than the current process of hard-forks.

在平行链或平行线程上的功能例子:

- 定制费用架构(例如: 固定费用交易费或按字节)。
- 定制货币政策用作原生币币和本地经济。
- 财政库通过状态转变函数获取资助。
- A governance mechanism that could manage a DAO that is responsible for allocating your on-chain treasury.

![build 1](assets/build-1.png)

Parachains open possibilities to construct complex runtime logic that would be too expensive to execute with smart contracts. However, unlike smart contracts, parachains lack a mandatory gas metering system entirely and could potentially be vulnerable to bugs that cause infinite loops (something that is prevented by design in smart contracts).

You may also decide to harness a combination of parachain, parathread, and smart contract. If you have certain logic that requires loops and it cannot be removed, use the native parachain runtime to handle all complex logic and the smart contract to call iteration. If you require off-chain data from an oracle, you may want to use a parathread as an oracle feed that only triggers once every 24 hours (this makes the most sense if the data is useful to other players in the Polkadot ecosystem too).

Most likely you’ve already realized that your application is better suited to be one or the other (or a hybrid of them both), but if you need a quick recap to digest the information, you can use this comparison chart as a cheat sheet:

![build 2](assets/build-2.png)

> **Note:** The image above does not include parathreads, but as we mentioned before all the benefits of parachains apply just as well to parathreads. Parathreads, however, _are_ cheaper to deploy and maintain. So if they had a column on the table above, it would look like the parachain column with "Ease of deployment" and "Maintenance overhead" changed to `+`.

This guide now splits into two sections depending on whether you’ve decided on a smart contract or a parachain to build your application. Feel free to read both sections, or just the one that is applicable to you.

- [你想建立平行链或平行线程!](#so-you-want-to-build-a-parachain-or-parathread)
- [你想建立智能合约!](#so-you-want-to-build-a-smart-contract)

## So you want to build a parachain or parathread...

Now that you have determined that building a parachain or parathread is the right approach for your new project, the next step is to decide which framework to use. Frameworks for building a parachain or parathread are known as parachain development kits (PDKs). Currently, the only PDK available is Substrate and Cumulus from Parity Technologies.

In the future, there will be many different PDKs available in different programming languages, just like there are multiple [implementations](learn-implementations.md) of the Polkadot Host.

> **Call to Action:** Do you want to build a Parachain Development Kit from scratch? The Web3 Foundation is giving grants to teams who are doing this, learn more and apply on the [W3F grants page](https://grants.web3.foundation).

### 开始使用 Substrate

Substrate is the underlying framework on which Polkadot itself is built. It is a toolset for blockchain innovators that provides the necessary building blocks for constructing a chain. It includes a library of modular plug-ins from which you can compose your chain logic and allows you to write your own pallets to use or publish to the community.

The best way to get started with Substrate is to explore the [Substrate Knowledge Base](https://www.substrate.io/kb/learn-substrate), an online resource built and maintained by Parity Technologies.

We recommend that you poke around in there to become familiar with the common patterns. Once you have a solid understanding, you can challenge yourself by progressing through the Substratekitties workshop then advancing to the TCR Dappchain tutorial or one of the others.

### 如何设定你的平行链

After creating your chain logic with Substrate, you will be able to compile it down to a Wasm executable. This Wasm code blob will contain the entire state transition function of your chain, and is what you will need to deploy your project to Polkadot as either a parachain or parathread.

Validators on Polkadot will use the submitted Wasm code to validate the state transitions of your chain or thread, but doing this requires some additional infrastructure. A validator needs some way to stay up to date with the most recent state transitions, since Polkadot nodes will not be required to also be nodes of your chain.

This is where the collator node comes into play. A collator is a maintainer of your parachain and performs the critical action of producing new block candidates for your chain and passing them to Polkadot validators for inclusion in the Relaychain.

Substrate comes with its own networking layer built-in but unfortunately only supports solo chains (that is, chains which do not connect to the relay chain). However, there is the Cumulus extension which does include a collator node and allows for your Substrate-built logic to be compatible with Polkadot as either a parachain or parathread.

#### Cumulus

The goal of [Cumulus](build-cumulus) is to be an extension of Substrate that will make any Substrate runtime compatible with Polkadot.

It handles the network compatibility overhead that any parachain would need to implement to be connected to Polkadot. This includes:

- Cross-chain message passing.
- 开箱即用的收集人节点
- 中继链的嵌入式轻客户端
- Polkadot 出块者兼容性。
- Integrating Cumulus with your Substrate chain will port it into a parachain capable of working on Polkadot with minimal modification, possibly as little work as importing a crate and adding a single line.

Rob Habermeier, a co-founder of Polkadot, last year gave a talk at EthCC that introduced Cumulus, which you can watch below.
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/thgtXq5YMOo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen mark="crwd-mark"></iframe> <iframe width="560" height="315" >

### 如何在 Polkadot 中部署平行链或平行线程。

#### 平行链

In order to include your parachain into the Polkadot network, you will need to acquire a parachain slot.

Parachain slots will be sold in open auctions, the mechanics of which can be found on the [parachain auction](learn-auction) page of the wiki.

#### 平行线程

Parathreads will not require a parachain slot, so you will not need to engage in the candle auction mechanism. Instead, you will be able to register your parathread code to the Relaychain for a fee and from then be able to start participating in the per-block auctions for inclusion of your state transition into the Relaychain.

For more information on how parathread per-block auctions work, see the more detailed [parathread](learn-parathreads) page.

## So you want to build a smart contract...

The Polkadot relay chain itself will not support smart contracts. However, since the parachains that connect to Polkadot can support arbitrary state transitions, they can support smart contracts. Builders of smart contracts can use these options when they become available. Today, it's possible to start development using a local development chain and later deploy to a live environment when the technology matures.

Substrate supports smart contracts out-of-the-box in two ways. One way is using the provided [Contracts](https://github.com/paritytech/substrate/tree/master/frame/contracts) pallet in the FRAME library. The second way is using the Substrate [EVM pallet](https://github.com/paritytech/substrate/tree/master/frame/evm) to deploy EVM-based bytecode compiled from Solidity or Vyper and using tools available from the Ethereum stack.

The experience of deploying to an EVM-based chain may be more familiar to developers that have written smart contract before. However, the Contracts pallet makes some notable improvements to the design of the EVM. Namely these are:

1. **Wasm**. The Contracts pallet uses WebAssembly as its compilation target. Any language that compiles to Wasm can potentially be used to write smart contracts. Although it's better to have a dedicated domain-specific-language and for that reason Parity offers the [ink!](#ink) language.

2. **Rent**. Contracts must pay rent or else hold a deposit suitably large enough in order to justify its existence on-chain. When a contract does not uphold this, it may create what's called a _tombstone_ which is a reference to the contract. In some conditions, the contract will be deleted outright along with its storage if it does not maintain these requirements.

3. **Caching**. Contracts are cached by default and therefore means they only need to be deployed once, and afterward be instantiated as many times as you want. This helps to keep the storage load on the chain down to the minimum. On top of this, when a contract is no longer being used and the _existential deposit_ is drained, the code will be erased from storage (known as reaping).

You will likely want to set up a local test environment to start writing your smart contracts. This can be done using a Substrate node with one of the two smart contracts pallets including. After development you will want to look into projects such as [Edgware])(#edgeware) for deploying your smart contract to a live environment.

### Edgeware

One project that is live today with the smart contracts pallet is [Edgeware](https://edgewa.re). Edgeware is a permissionless platform for smart contracts and is conducting experiments with on-chain governance. It is currently the best option for developers who have created their smart contracts and want to deploy to a live environment.

Edgeware intends to at some point connect to Polkadot as a parachain that allows for smart contracts. At this point, the smart contracts would be able to interact with other pieces of the Polkadot ecosystem through [XCMP](learn-crosschain).

Edgeware documentation can be found [here](https://docs.edgewa.re/).

### Ink

[ink!](https://github.com/paritytech/ink) is a domain specific language for writing smart contracts in Rust and compiles to Wasm code. As it states in its README, it is still in an experimental phase so brave developers should be aware that they might have a bumpy - but workable - development experience. There are some projects which have built projects in ink! with a decent level of complexity such as Plasm's [Plasma contracts](https://github.com/staketechnologies/Plasm), so it is mature enough to start building interesting things.

For interested developers, they can get started writing smart contracts using ink! by studying the [examples](https://github.com/paritytech/ink/tree/master/examples) that were already written. These can be used as guideposts to writing more complex logic which will be deployable on smart contract parachains.

ink! has laid much of the groundwork for a new smart contract stack that is based on a Wasm virtual machine and compatible with Substrate chains.

## Deploying your smart contract

A smart contract is simply some code that exists at an address on a chain and is callable by external actors. The key part is that you actually have to put the code on chain before anyone can start executing it!

Deploying your smart contract on chain will vary slightly for whichever specific parachain you will use, but generally you will send a special transaction that will create the smart contract on the ledger. You will likely need to pay an associated fee for the initialization logic and any storage that your contract consumes.

## Paying for your smart contract

Each platform will have a different way of paying for and maintaining the state of your smart contract.

你可能会看到支付智能合约不同的模式包括以下：

- 与部署交易关联的交易费用
- 订阅方式，你可以定期支付到该平台作费用
- An access token model for which you need to hold a threshold of native tokens to use the platform (EOS has something similar). Storage rent.
- 免费试用或开发者促销
- Most smart contract platforms use some form of gas to limit the number of operations a user can perform. Users will be required to pay for the gas upfront and will be refunded for what they don’t use.

You will need to consider the storage and complexity of your smart contract to ensure that gas usage stays within reasonable bounds. Storage will likely be expensive for whichever smart contract platform you use, so it is necessary to keep as much data off-chain as possible. You may consider using [IPFS](https://ipfs.io/) or [Storj](https://storj.io/) to keep the data and submitting only the content address on chain.

### 现在还处于早期

It’s still very early for smart contracts on Polkadot and the development is only now stabilizing. We are actively producing content to help developers get up to speed and will maintain the wiki with the latest resources. You should also keep up to date with the following links:

- [Edgeware](https://edgewa.re).
- [ink!](https://github.com/paritytech/ink). (Keep an eye out for content on the wiki tab.)
- [Substrate contracts pallet](https://github.com/paritytech/substrate/tree/master/frame/contracts).

## Conclusion

This guide has given you a mental model and shown the requisite resources to help you determine and start building your project as a parachain or smart contract today. Even though the tooling is still maturing, the advantage of being early will be the familiarity and head start on your project, allowing you to innovate and create something truly new.

If you have interesting ideas for parachains or smart contracts on Polkadot feel free to drop in to the [Polkadot Watercooler](https://riot.im/app/#/room/#polkadot-watercooler:matrix.org) to talk about them. Developers may be interested in joining the [Polkadot Beginners Lounge](https://riot.im/app/#/room/#polkadotnoobs:matrix.org) or [Substrate Technical](https://riot.im/app/#/room/#substrate-technical:matrix.org) to ask their questions. As always, keep up to date with Polkadot by following the [social channels](community).

祝你好运！