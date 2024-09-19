
# Arbitrum BoLD Validator Setup and Configuration

This documentation covers the setup, configuration, and best practices for running an **Arbitrum BoLD** validator. The validator is deployed on the public BoLD testnet using Ethereum Sepolia as the parent chain. You will also learn how to troubleshoot common issues and ensure optimal performance.

## Table of Contents
1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Environment Variables](#environment-variables)
4. [Repository Setup](#repository-setup)
5. [Funding Your Validator](#funding-your-validator)
6. [Running a BoLD Validator](#running-a-bold-validator)
    - [Honest Validator](#honest-validator)
    - [Evil Validator](#evil-validator)
7. [Interpreting Log Lines](#interpreting-log-lines)
8. [Troubleshooting](#troubleshooting)
9. [Best Practices](#best-practices)

---

## Overview

This guide will help you set up and run an Arbitrum BoLD validator. You'll be able to participate in validating transactions, posting assertions, and challenging invalid state assertions on the testnet. Two modes are available: **Honest** (validating correctly) and **Evil** (intentionally invalid).

The BoLD validator interacts with the **BoLD testnet**, which is an L2 using Ethereum Sepolia as the parent chain.

Testnet Resources:
- **Block explorer**: [boldv2-explorer.arbitrum.io](https://boldv2-explorer.arbitrum.io/)
- **Sequencer RPC**: [https://boldv2.arbitrum.io/rpc](https://boldv2.arbitrum.io/rpc)
- **Sequencer Feed**: `wss://boldv2.arbitrum.io/feed`

## Prerequisites

Before proceeding with the setup, ensure that you have the following requirements in place:

- **Docker** installed locally. Follow the [Docker installation guide](https://docs.docker.com/get-docker/) for your OS.
- **Docker Compose** installed standalone. Verify installation by running `docker compose version`.
- **JQ** (JSON processor) installed on your system.
- An **Ethereum Sepolia testnet account** with at least 150 Sepolia ETH for staking and challenges.
- An **RPC connection** to the Ethereum Sepolia network (running your own node is highly recommended to avoid rate limits).
- A machine with at least **8 GB of RAM** and **4 CPU cores** (e.g., AWS t3 xLarge).

> **Note**: Ensure you have the correct permissions for running Docker without root access, especially on Linux. Follow the [Docker post-installation guide](https://docs.docker.com/engine/install/linux-postinstall/).

## Environment Variables

You'll need to define specific environment variables to configure your BoLD validator:

- `SEPOLIA_ENDPOINT`: Your Ethereum Sepolia RPC endpoint.
- `HONEST_PRIVATE_KEY`: The private key of your validator in **honest** mode (without `0x` prefix).
- `EVIL_PRIVATE_KEY`: The private key of your validator in **evil** mode (without `0x` prefix).

> **Note**: If running only one validator (either honest or evil), you can use the same private key for both `HONEST_PRIVATE_KEY` and `EVIL_PRIVATE_KEY`.

## Repository Setup

Follow the steps below to set up the necessary repository and Docker images for your BoLD validator.

### Step 1: Clone the Repository

```bash
git clone https://github.com/OffchainLabs/bold-validator-starter-kit.git
cd bold-validator-starter-kit
```

### Step 2: Pull the Docker Images

```bash
docker pull ghcr.io/rauljordan/nitro:bold
docker pull ghcr.io/rauljordan/bold-utils:latest
```

Alternatively, you can build the Docker image from source:

```bash
git clone https://github.com/OffchainLabs/nitro.git
cd nitro
git checkout sepolia-tooling-merge
git submodule update --init --recursive --force
docker build . -t nitro-node-dev --target nitro-node-dev
docker tag nitro-node-dev:latest nitro-node-dev-testnode
```

You can now use this image with the `honest-validator/docker-compose.yml` or `evil-validator/docker-compose.yml` files.

## Funding Your Validator

Before running the validator, you need to mint the necessary staking tokens for assertions and challenges.

### Mint Stake Tokens

To mint the ERC20 staking token using Sepolia ETH, run:

```bash
./mint_stake_token.sh --private-key $PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

You will need:

- **100 Sepolia WETH** to become an assertion poster.
- **50 Sepolia WETH** to open and resolve a challenge claim.

## Running a BoLD Validator

You can choose to run your validator in **honest** or **evil** mode, depending on the type of tests you wish to run.

### Honest Validator

To start an honest validator:

```bash
./validator.sh --private-key $HONEST_PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

### Evil Validator

To run the validator in evil mode:

```bash
./validator.sh --evil --private-key $EVIL_PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

> **Note**: It may take some time for the validator to sync with the testnet. Expect to see some initial error messages, which should disappear once the node has caught up.

## Interpreting Log Lines

As the validator runs, log lines will provide insights into its current operations. Below are some common logs and their meanings:

- **Posting assertion for batch we agree with**: The validator is posting a correct state assertion.
- **Disagreed with an observed assertion onchain**: The validator disagrees with another validator's assertion.
- **Opening a challenge on an observed assertion**: A dispute is opened over a state assertion.
- **Successfully submitted assertion**: The validator has successfully posted an assertion as part of a dispute.
- **Observed an evil edge created onchain**: An adversary has made a move, and the validator will respond accordingly.
- **Identified single step of disagreement at the execution of a block**: The validator has isolated the point of disagreement and is preparing a one-step proof.

## Troubleshooting

Below are common issues you may encounter and how to resolve them:

- **chain catching up**: This error occurs when the validator is still syncing with the latest state of the chain. This is normal upon startup and should resolve within a few minutes.
- **could not add edge to challenge tree**: This issue is likely due to the validator not being fully synced. Allow the validator time to catch up.
- **invalid hex character in private key**: Ensure your private key does not contain the `0x` prefix.
- **ERC20: insufficient allowance**: This happens if your validator has exhausted its staking tokens. Re-run the `mint_stake_token.sh` script to mint more tokens.

### Wiping the Validator Database

If the validator database becomes corrupted, you can wipe it by removing the Docker volumes:

```bash
docker volume rm $(docker volume ls -q | grep "honest-validator") 2>/dev/null
docker volume rm $(docker volume ls -q | grep "evil-validator") 2>/dev/null
```

## Best Practices

To ensure optimal performance and security when running a BoLD validator, follow these best practices:

- **Run your own Ethereum Sepolia node**: Avoid rate limits by using your own RPC connection.
- **Monitor log lines**: Stay updated on the validator's progress, especially during disputes.
- **Stake sufficient tokens**: Ensure you have enough Sepolia WETH to cover the costs of assertions and challenges.
- **Use separate keys for honest and evil modes**: When running multiple validators, use distinct private keys for each.
- **Automate**: Consider automating logs and performance monitoring to catch errors early.

---

By following this guide, you should now have a fully operational BoLD validator, capable of interacting with the Arbitrum BoLD testnet, posting assertions, and engaging in challenges. For more in-depth exploration and updates, check the official [BoLD repository](https://github.com/OffchainLabs) and related resources.

