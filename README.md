# jucs :ledger:

**j**ared's **u**nofficial **c**ardano **s**cratchpad

## :warning: Caveat Emptor :warning:

The best entry point for official Cardano documentation is
[Essential Cardano](https://www.essentialcardano.io/).

This repository, however, is just my personal scratchpad.
It might not stay up to date, and I am not an expert about all the topics involved.
It is, however, a total blast experimenting with the Cardano node on the **test** networks,
even if you are not an expert, and I invite you all to join in on the fun.

## Building the node

[Building the node with nix](https://github.com/input-output-hk/cardano-node/blob/master/doc/getting-started/building-the-node-using-nix.md)
is really convenient.

To discover which networks are available:

* load the Nix repl: `nix repl`
* load the node flake: `:lf .`
* use tab completion: `outputs.apps.x86_64-linux.`, then hit Tab, and look for the */node variants

In particular, `preview` is a great network for experimentation, which we can build with:

```
nix build .#preview/node -o some_name
```

## Running the node

We can run the node binary built above with:

```
./some_name/bin/cardano-node-preview
```

Using the cardano-cli, you can check the status of the node's progress with:
```
CARDANO_NODE_SOCKET_PATH=state-node-preview/node.socket cardano-cli query tip --testnet-magic 2
```

(I never know the testnet magic, I discover it by getting it wrong and reading the error.)

## Getting ledger snapshots with DB Analyze

https://github.com/input-output-hk/ouroboros-network/blob/master/ouroboros-consensus-cardano-tools/Documentation.md

You'll need to run a node to tip to build up the immutable db.

I like to build it by entering a nix shell in the ouroboros-network repo and then using cabal:
```
[nix-shell:~/io/ouroboros-network]$ cabal build cardano-ledger-state-converter
```

You can look at the shell script to get the config.

```
cat ../cardano-node/preview/bin/cardano-node-preview | grep config
```

```
cabal run db-analyser -- --db ~/io/cardano-node/state-node-preview/db-preview/ --only-immutable-db --minimum-block-validation  --store-ledger 8620 cardano --config /nix/store/z6iz3pjp29lnalcjmhz19avccyhwyf1q-config-0-0.json
```

### Convert to CBOR format from the ledger

https://github.com/input-output-hk/ouroboros-network/tree/jc/ledger-state-converter

```
cabal run cardano-ledger-state-converter -- --db ~/io/cardano-node/state-node-preview/db-preview/ --slot 8620
/nix/store/ymh82pwyxx4izg3wwnn3mv9pqv99ssx6-cabal-install-exe-cabal-3.8.1.0/bin/cabal --project-file=/home/jared/io/ouroboros-network/.nix-shell-cabal.project run cardano-ledger-state-converter -- --db /home/jared/io/cardano-node/state-node-preview/db-preview/ --slot 8620
```

which makes `newEpochState-71452796.cbor`.

## Ledger state tool

https://github.com/input-output-hk/cardano-ledger/tree/master/libs/ledger-state


## Foldblocks

See the stake credential history tool: https://github.com/input-output-hk/cardano-node/blob/master/cardano-client-demo/Stake-Credential-History.md

## Faucet

https://docs.cardano.org/cardano-testnet/tools/faucet
