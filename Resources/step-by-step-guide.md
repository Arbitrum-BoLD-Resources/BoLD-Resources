
# Arbitrum BoLD Validator Step-by-Step Guide

This guide provides a thorough, step-by-step walkthrough on how to set up and run a BoLD validator on the Arbitrum testnet, using **Ethereum Sepolia** as the parent chain.

---

## Table of Contents

1. [Step 1: Prerequisites](#step-1-prerequisites)
2. [Step 2: Setting Up Environment Variables](#step-2-setting-up-environment-variables)
3. [Step 3: Repository Cloning and Docker Setup](#step-3-repository-cloning-and-docker-setup)
4. [Step 4: Funding the Validator](#step-4-funding-the-validator)
5. [Step 5: Running the Validator](#step-5-running-the-validator)
   - [Honest Validator](#honest-validator)
   - [Evil Validator](#evil-validator)
6. [Step 6: Monitoring and Interpreting Logs](#step-6-monitoring-and-interpreting-logs)
7. [Step 7: Troubleshooting](#step-7-troubleshooting)
8. [Best Practices](#best-practices)
9. [Frequently Asked Questions (FAQs)](#frequently-asked-questions-faqs)

---

## Step 1: Prerequisites

### Tools and Software Requirements

Before starting the validator setup, ensure the following software and hardware prerequisites are met:

1. **Docker**:
   - Install Docker on your system following the [Docker installation guide](https://docs.docker.com/get-docker/).
   
2. **Docker Compose**:
   - Docker Compose should be installed as a standalone tool. To verify the installation, run the following command in your terminal:
     ```bash
     docker compose version
     ```
   - If using Linux, you might need to give executable permissions:
     ```bash
     sudo chmod +x /usr/bin/docker-compose
     ```

3. **JQ**:
   - JQ is a lightweight and flexible command-line JSON processor. Install it using the appropriate package manager for your operating system.

4. **Ethereum Sepolia Testnet Account**:
   - You need an Ethereum Sepolia account with at least **150 Sepolia ETH**. This is required to stake on assertions and open challenges. The **base stake** to become an assertion poster is **100 Sepolia ETH**.

5. **Ethereum Sepolia Node**:
   - Running your own Ethereum Sepolia node is highly recommended to avoid rate limits imposed by third-party RPC providers.

6. **Hardware Requirements**:
   - Minimum **8 GB RAM** and **4 CPU cores** are required. For cloud-based setups, such as AWS, use a **t3.xLarge** instance for optimal performance.

---

## Step 2: Setting Up Environment Variables

After installing the required software, you must define the following environment variables. These variables will be used to configure your BoLD validator.

### Required Environment Variables:

- **SEPOLIA_ENDPOINT**: This is the RPC endpoint of your Ethereum Sepolia node. If you are running your node on **localhost**, the endpoint will look like this:
  ```bash
  http://host.docker.internal:$PORT
  ```
  Where `$PORT` is the port number for your node (usually 8545).

- **HONEST_PRIVATE_KEY**: Your Ethereum private key (used for running an honest validator) without the `0x` prefix. For example, if your private key is `0xabc123`, set the variable like this:
  ```bash
  HONEST_PRIVATE_KEY=abc123
  ```

- **EVIL_PRIVATE_KEY**: Your Ethereum private key (used for running an evil validator) without the `0x` prefix. You may use the same private key as the honest validator, but it is recommended to use two separate keys if running both simultaneously.

> **Note**: Make sure these private keys are kept secure as they provide access to your funds.

---

## Step 3: Repository Cloning and Docker Setup

### Clone the Repository

First, clone the **BoLD Validator Starter Kit** repository to your local machine:

```bash
git clone https://github.com/OffchainLabs/bold-validator-starter-kit.git
cd bold-validator-starter-kit
```

### Pull Docker Images

Next, pull the Docker images necessary to run the validator:

```bash
docker pull ghcr.io/rauljordan/nitro:bold
docker pull ghcr.io/rauljordan/bold-utils:latest
```

#### Alternatively: Build Docker Image from Source

If you prefer, you can build the Docker image from the source code. Run the following commands:

```bash
git clone https://github.com/OffchainLabs/nitro.git
cd nitro
git checkout sepolia-tooling-merge
git submodule update --init --recursive --force
docker build . -t nitro-node-dev --target nitro-node-dev
docker tag nitro-node-dev:latest nitro-node-dev-testnode
```

Once built, use this image with the configuration files located at `honest-validator/docker-compose.yml` or `evil-validator/docker-compose.yml`.

---

## Step 4: Funding the Validator

To stake on assertions or open challenges, you need to mint ERC20 staking tokens using the Sepolia ETH in your account.

### Mint Stake Tokens

Run the following script to mint stake tokens:

```bash
./mint_stake_token.sh --private-key $PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

- You need **100 Sepolia WETH** to become an assertion poster on the testnet.
- It costs **50 Sepolia WETH** to open and resolve a challenge claim by an adversary.

---

## Step 5: Running the Validator

You can choose between running a **Honest** validator (which behaves correctly) or an **Evil** validator (which intentionally submits incorrect assertions).

### Honest Validator

To run a **honest** validator, use the following command:

```bash
./validator.sh --private-key $HONEST_PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

This will start the validator in honest mode. You’ll see log outputs indicating its activity (e.g., posting assertions, challenges, etc.).

### Evil Validator

To run an **evil** validator (which submits incorrect assertions), use the same command but with the `--evil` flag:

```bash
./validator.sh --evil --private-key $EVIL_PRIVATE_KEY --eth-rpc-endpoint $SEPOLIA_ENDPOINT
```

This validator will attempt to post incorrect assertions and trigger challenges.

> **Note**: Initially, you may see error log lines. These indicate that the node is catching up with the testnet’s latest state, and they will disappear once synchronization is complete.

---

## Step 6: Monitoring and Interpreting Logs

### Key Log Lines to Watch

- **Posting assertion for batch we agree with**: The validator is posting a correct state assertion.
- **Disagreed with an observed assertion onchain**: The validator has encountered an incorrect assertion posted by another validator.
- **Opening a challenge on an observed assertion**: The validator is challenging an incorrect assertion.
- **Successfully submitted assertion**: The validator successfully posted an assertion.
- **Observed an evil edge created onchain from an adversary**: The validator has observed a malicious assertion and is responding accordingly.
- **Identified single step of disagreement at the execution of a block**: The validator has pinpointed the disagreement between itself and another validator, preparing for a one-step proof.

These logs help monitor validator behavior and ensure that assertions and challenges are processed correctly.

---

## Step 7: Troubleshooting

Here are some common issues you might encounter and how to resolve them:

### Common Errors

1. **chain catching up**:
   - This is a normal log when the validator is syncing with the latest state of the testnet. It will disappear after a few minutes.

2. **could not add edge to challenge tree**:
   - This error typically occurs when the validator is not fully synced. Let the node catch up, and the issue will resolve itself.

3. **invalid hex character in private key**:
   - Ensure your private key does not include the `0x` prefix.

4. **ERC20: insufficient allowance**:
   - If you run out of staking tokens, simply re-run the `mint_stake_token.sh` script to mint more.

5. **execution reverted: PREV_NOT_LATEST_CONFIRMED**:
   - This error occurs when multiple validators race to confirm the same assertion. Only one validator will succeed, and others will receive this error. This is normal.

### Wiping the Validator Database

If your validator database becomes corrupted (due to an unexpected shutdown, for example), you may need to wipe the database and start fresh:

```bash
docker volume rm $(docker volume ls -q | grep "honest-validator") 2>/dev/null
docker volume rm $(docker volume ls -q | grep "evil-validator") 2>/dev/null
```

---

## Best Practices

To ensure a smooth and efficient validator setup, follow these best practices:

1. **Run Your Own Ethereum Sepolia Node**:
   - Avoid rate limits by running your own Ethereum Sepolia node rather than relying on third-party providers.

2. **Monitor Logs Regularly**:
   - Regularly monitor the logs to ensure that the validator is operating correctly and catching challenges as expected.

3. **Stake Sufficient Funds**:


   - Ensure you have enough Sepolia WETH to cover the staking and challenge costs. Always maintain a buffer in case of extended activity.

4. **Use Separate Private Keys for Honest and Evil Validators**:
   - If you are testing both honest and evil validators simultaneously, it’s best to use separate private keys to avoid conflicts.

5. **Automation**:
   - Consider automating monitoring processes to catch potential issues early, ensuring the validator runs efficiently without interruptions.

---


By following this step-by-step guide, you will be fully prepared to set up, run, and monitor your BoLD validator. This documentation ensures you can handle both honest and malicious behavior in the testnet environment while contributing to the validation of Arbitrum’s state assertions.