---
title: Job Scheduling
description: Job Schedulers are services that allow scheduling transactions to be executed at a later time on the ethereum blockchain
lang: en
sidebar: true
incomplete: true
---

Job Scheduling services are comprised of off-chain agents that trigger the execution of smart contract functions on behalf of a protocol or individual user so that certain processes are ensured to take place on-chain. For example, overcollateralised lending protocols require such services to watch over debt positions created by their users in order to make sure sufficient collateral is present. If the collateral present on-chain becomes insufficient, job scheduling services will execute liquidiation transactions on-chain to wipe out the bad debt of the protocol.

## Prerequisites {#prerequisites}

Make sure you're familiar with [nodes](/developers/docs/nodes-and-clients/), and [smart contract anatomy](/developers/docs/smart-contracts/anatomy/), specifically events.

## What is a Job Scheduling Service {#what-is-an-job-scheduling-service}

Job Scheduling services are networks comprised of Executors, also sometimes known as Keepers. Executors are crypto-economically incentivised off-chain agents that form the execution layer that make smart contracts act "smart". They constantly check whether certain smart contract functions can get executed off-chain and if they can, they will do the heavy lifting of paying the necssary gas to miners and getting a tranasction mined, usually to get compensated by a reward from whoever built the smart contract.

## Why are they needed? {#why-are-they-needed}

On a blockchain like Ethereum, smart contract functions are not self-executing, they require an external impulse in form of a transaction sent by an Externally Owned Account (EOA) in order to actually facilitate the state change that a smart contract function defines. To avoid having to call functions every block on-chain and pay the associated gas fees, Job Scheduling services conduct these checks off-chain and only send tranasctions on-chain once they have a high probability of not reverting.

This reduces the congestion on Ethereum and saves Gas for the respective party who aims to have a certain smart contract function automatically executed at certain times in the future.

### Security {#security}

Smart contract functions which should be executable by Executors / Keepers usually should validate and check all the necessary security on-chain, thus no trust assumptions have to be given to Job Scheduling services other than that they should be operating reliably and triggering the execution of transactions when they are due.

## Usage {#usage}

Using services like [Gelato Network](https://gelato.network/), [Keeper Network](https://keep3r.network/), [Open Zeppelin Defender](https://www.openzeppelin.com/defender) and [Chainlink Keepers](https://keepers.chain.link/), you can communicate to Executors that you would like them to start monitoring your smart contract functions so that they start to execute them whenever possible. This is usually done by depositing some e.g. ETH into a pre-paid gas tank, which gets deducted for every transactions these services execute on your behalf.

Here is an example of a Counter smart contract which increments a counter every 300 seconds:

```solidity
pragma solidity ^0.8.0;

contract Counter {

    uint256 public lastExecuted;

    /**
     * Increments a counter every 300 seconds
     */
    function increaseCount() external {
        require(
            ((block.timestamp - lastExecuted) > 300),
            "Counter: increaseCount: Time not elapsed"
        );

        count += 1;
        lastExecuted = block.timestamp;
    }
}
```

In order to get this function executed every 300 seconds, you have to 1) deploy & verify this smart contract, 2) go to a job scheduling service such as [Gelato](https://app.gelato.network/), 3) input the contracts address, 4) deposit some ETH to pay for transaction fees and 5) create a task which will emit an event so that all Executors of your chosen Job Scheduling Network will start monitoring and executing this function whenever they can.

[View the docs](https://docs.gelato.network/developer-products/gelato-ops-smart-contract-automation-hub)

## Further reading {#further-reading}

**Videos**

- [How to automate smart contract functions](https://www.youtube.com/watch?v=YQGRIT8aV5c&t=184s&ab_channel=GelatoNetwork) - _Gelato_
- [Writing Smart Contract Resolvers](https://www.youtube.com/watch?v=CTgmVLQlo1Q&t=134s&ab_channel=SarahAmann) - _Gelato_
- [Automate the harvesting of yield farming Vaults](https://youtu.be/F3wg91DFDyU?t=1260) - _Gelato_

**Tutorials**

- [How to automate buying tokens on Uniswap](https://medium.com/gelato-network/how-to-automate-your-smart-contract-executions-with-gelato-network-9bf632b3ceab) - _Gelato_
