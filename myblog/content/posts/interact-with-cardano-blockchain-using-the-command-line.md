+++
title = 'The most simple way to interact with the Cardano blockchain using the command line'
date = 2022-07-16
draft = false
tags = ['Cardano', 'Blockchain', 'CLI']
canonicalURL = 'https://caike.medium.com/the-most-simple-way-to-interact-with-the-cardano-blockchain-using-the-command-line-652efd1c5319'
+++

In this post we'll learn one the most simple approaches to interacting with the Cardano blockchain using the command line. The command line is the lowest level for interacting with Cardano before using one of its [programming language libraries](https://github.com/input-output-hk/cardano-node/tree/master/cardano-cli).

With the cardano command line, we can send commands directly to a cardano **node** using the **cardano-cli** on Linux, Mac or Windows.

## The Cardano node

The Cardano **node** is the most important and fundamental element of the network.

The node communicates with other nodes.

**The blockchain is a network of nodes** which communicate with [each other](https://en.wikipedia.org/wiki/Peer-to-peer) and execute different roles, such as:

1- Running the [protocol](https://cardano.org/ouroboros/).

2- Validating and propagating blocks.

3- Producing blocks (just a particular subset of nodes do this)

4- Executing network queries.

> ***"The blockchain is a network of nodes which communicate with each other and execute different roles"***

Since each node has a copy of the blockchain, they can return queries 🔍

There are a few different ways to ***run*** a node. Those with systems administration and IT knowledge can follow the [manual process](https://developers.cardano.org/docs/get-started/running-cardano/). This is certainly the most recommended way to build real-world applications such as the backend to a [dApp](https://en.wikipedia.org/wiki/Decentralized_application) or to operate a [Stake Pool](https://cardano.org/stake-pool-operation/#stake-pool).

On the other hand, there is a way that's much more approachable and practical, and particularly useful for running tests and basic operations: using the [Daedalus desktop](https://daedaluswallet.io/) wallet.

The **Daelalus wallet** is a project by IOG, one of the companies behind the development of Cardano. It is one of the most secure and one of the easiest wallets to install and use. This wallet includes a dedicated blockchain node, allowing each individual who uses the wallet to also participate in the Cardano network through the aforementioned roles.

In fact, referring to Daedalus simply as a wallet is an understatement. In the words of [Charles Hoskinson](https://en.wikipedia.org/wiki/Charles_Hoskinson) himself,

> "Daedalus is a lot of software. It's not a wallet. It's a server, it's a database. **Daedalus is the entire cardano protocol.**"

When installed and run for the first time, Daedalus will need to sync the entire blockchain history since [its first block](https://explorer.cardano.org/en/block?id=5f20df933584822601f9e3f8c024eb5eb252fe8cefb24d1317dc3d432e940ebb). This can take a few hours ⏳

Only once the sync is complete, will the wallet be available for use.

For now, let's skip the commands to use the wallet and jump straight to the command line 💪 The **Terminal.app** is used to run the commands.

## Communicating with the Testnet

Before commands can be run against the Cardano node, the following environment variable needs to be set.

`CARDANO_NODE_SOCKET_PATH`

As the name implies, this environment variable points to the socket used to communicate with the node. To communicate with "Testnet", confirm that the following file,

`cardano-node.socket`

exists on the following directory,

`~/Library/Application\ Support/Daedalus\ Testnet/`

Run this command to populate the environment variable:

```
export CARDANO_NODE_SOCKET_PATH=~/Library/Application\ Support/Daedalus\ Testnet/cardano-node.socket
```

Make sure the value is properly set:

```
echo $CARDANO_NODE_SOCKET_PATH
```

Switch folders to where Daedalus stores the **cardano-cli** and other executables for the testnet. Run the following:

```
cd /Applications/Daedalus\ Testnet.app/Contents/MacOS/
```

Inside the new folder, look for **cardano-cli**.

This is the program used to interact with the node. From this folder, run the following command to query real-time information from the blockchain:

```
./cardano-cli query tip --testnet-magic 1097911063
```

The value for the testnet magic is read [from this file](https://hydra.iohk.io/build/12179730/download/1/testnet-shelley-genesis.json). This parameter is used by cryptographic functions implemented as part of the protocol and differentiates communication between test and main network.

The query tip command returns a summarized snapshot of the current state of the network as known by the current node. One example of result is the following JSON:

```json
{
  "era": "Alonzo",
  "epoch": 185,
  "hash": "886250a606c2d9968c5b1508e56cc2cd79f5abf99cb848695e8ecfb84c7b60dc",
  "block": 3299613,
  "slot": 49830257,
  "syncProgress": "100.00"
}
```

Try running this command yourself and if you see a result similar to this one, congratulations! You have officially interacted with Cardano **Testnet!** 👏

Now let's see how to interact with the main network.

## Communicating with Mainnet

Check for **cardano-node.socket** on the following directory:

`~/Library/Application\ Support/Daedalus\ Mainnet/`

Run this command to populate the environment variable for mainnet:

```
export CARDANO_NODE_SOCKET_PATH=~/Library/Application\ Support/Daedalus\ Mainnet/cardano-node.socket
```

Make sure the value is properly set:

```
echo $CARDANO_NODE_SOCKET_PATH
```

Change directories to where Daedalus stores the **cardano-cli** and other executables for the **main** network with the following command:

```
cd /Applications/Daedalus\ Mainnet.app/Contents/MacOS/
```

Inside this folder, look for **cardano-cli**.

Run the following command:

```
./cardano-cli query tip --mainnet
```

Note the option `--mainnet` instead of `--testnet-magic`. With this option, **cardano-cli** commands run on the **main** network and no longer on the test network. One example result is the following JSON:

```json
{
  "era": "Alonzo",
  "epoch": 319,
  "hash": "869ed5d7788e5518ef7195c3731a2d37b9bd2e861820b377ef87f6f062edf640",
  "block": 6852938,
  "slot": 52675349,
  "syncProgress": "100.00"
}
```

You have now interacted with Cardano **Mainnet!** 👏

The `query tip` command is one of the many available commands. One could say it is just the tip… of the iceberg

A comprehensive list of commands to create wallets, manage transactions, operate stake pools and publish smart contracts to the network can be found on the [official documentation for the cardano-cli](https://github.com/input-output-hk/cardano-node/blob/master/doc/reference/cardano-node-cli-reference.md).

## Conclusion

A simple and effective way to run a node and interact with the Cardano blockchain is through Daedalus. The Daedalus wallet is a Desktop wallet that includes its own node of the blockchain. This allows individual users to participate on the network and run commands using the **cardano-cli**.

To learn more about Cardano, checkout [https://workwithcardano.com/](https://workwithcardano.com/) and follow us on [Twitter.com/workwithcardano](https://twitter.com/workwithcardano).

---

*Originally published on [Medium](https://caike.medium.com/the-most-simple-way-to-interact-with-the-cardano-blockchain-using-the-command-line-652efd1c5319).*
