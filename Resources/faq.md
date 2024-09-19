## Frequently Asked Questions (FAQs)

### 1. **What is the purpose of running a BoLD validator?**
   - A BoLD validator ensures that only valid state assertions are posted to the blockchain. Validators participate in disputes and challenge incorrect state assertions on the BoLD testnet.

### 2. **Do I need my own Ethereum Sepolia node?**
   - It is highly recommended to run your own Ethereum Sepolia node to avoid rate limits from third-party providers. This ensures faster and more reliable validator operations.

### 3. **How much Sepolia ETH is required to run a validator?**
   - You need a minimum of 150 Sepolia ETH. The base stake for posting assertions is 100 Sepolia ETH, and 50 Sepolia ETH is required to open and resolve challenges.

### 4. **What is the difference between an Honest and Evil Validator?**
   - An honest validator posts valid assertions, while an evil validator intentionally submits incorrect assertions to trigger challenges and test the dispute resolution process.

### 5. **What should I do if my validator isn’t posting assertions?**
   - Ensure your node is fully synced with the testnet. Check logs for any synchronization issues. If the issue persists, verify your Sepolia ETH balance and RPC connection.

### 6. **Can I run both Honest and Evil validators simultaneously?**
   - Yes, you can run both, but it’s recommended to use separate private keys for each validator to avoid potential conflicts.

### 7. **How do I mint more staking tokens if I run out?**
   - Simply re-run the `mint_stake_token.sh` script with your private key and RPC endpoint to mint more tokens.

### 8. **How can I monitor challenges and validator performance?**
   - The logs provide real-time updates on challenges, assertions, and validator performance. Key logs to watch include `Posting assertion`, `Opening a challenge`, and `Successfully submitted assertion`.

---
