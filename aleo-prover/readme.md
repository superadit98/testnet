<p style="font-size:14px" align="left">
<a href="https://upasian.org/" target="_blank">Visit our website </a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/185994172-0b4e4ea8-f81a-48db-8020-9be619f485b7.png">
</p>

# ALEO PROVER TESTNET INCENTIVIZED

##  1. Overview

__snarkOS__ is a decentralized operating system for zero-knowledge applications. This code forms the backbone the [Aleo](https://aleo.org/) network, which verifies transactions and stores the encrypted state applications in a publicly-verifiable manner.

## 2. Requirements

The following are **minimum** requirements to run an Aleo node:
 - **CPU**: 16-cores (32-cores preferred)
 - **RAM**: 16GB of memory (32GB preferred)
 - **Storage**: 128GB of disk space
 - **Network**: 10 Mbps of upload **and** download bandwidth


# Automatic Install

```
wget -O prover.sh https://raw.githubusercontent.com/superadit98/testnet/main/aleo-prover/prover.sh && chmod +x prover.sh && ./prover.sh
```

Wait until instalation done.

# Run Prover

```
cd snarkOS
screen -R prover
```

```
./run-prover.sh
```
When prompted, enter your Aleo private key:
```
Enter the Aleo Prover account private key:
APrivateKey1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## Uninstal (Gunakan Jika Mau Menghapus Data Node)

```
rustup self uninstall
rm -rf prover.sh
rm -rf snarkOS
```
