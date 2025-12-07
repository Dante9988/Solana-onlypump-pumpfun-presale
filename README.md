# OnlyPump Presale - Anchor Program

A Solana program built with Anchor framework for presale functionality.

## Prerequisites

### 1. Install Solana CLI

**Important:** This install script fixes Cargo lockfile version issues:

```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```

After installation, add Solana to your PATH:
```bash
export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"
```

### 2. Install Anchor

```bash
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
avm install latest
avm use latest
```

### 3. Install Node.js Dependencies

```bash
yarn install
```

## Quick Start

### Build the Program

```bash
anchor build
```

This compiles the Rust program and generates TypeScript types in `target/types/`.

## Testing (Hardhat-Style)

**Just like Hardhat, running tests automatically:**
- ✅ Starts a local Solana validator
- ✅ Deploys your program to localnet
- ✅ Runs all tests
- ✅ Cleans up when done

### Run Tests

```bash
anchor test
```

That's it! No manual validator setup needed. The test command will:
1. Start a fresh local validator on port 8899
2. Deploy the program automatically
3. Run all tests in `tests/**/*.ts`
4. Shut down the validator when complete

### Run Specific Tests

You can modify test files to run specific tests using `.only()`:

```typescript
// In tests/onlypump-presale.ts
it.only("My specific test", async () => {
  // test code
});
```

## Manual Validator (Optional)

If you want to run a persistent local validator for debugging:

### Terminal 1: Start Validator

```bash
solana-test-validator --reset
```

This starts a validator on:
- RPC: `http://localhost:8899`
- WebSocket: `ws://localhost:8900`

### Terminal 2: Run Tests Against Existing Validator

```bash
anchor test --skip-local-validator
```

This will:
- Skip starting a new validator
- Deploy to your existing validator
- Run tests
- Keep the validator running for inspection

### Stop the Validator

Press `Ctrl+C` in the validator terminal, or:

```bash
pkill -f solana-test-validator
```

## Deployment

### Deploy to Localnet

```bash
# Ensure validator is running (or use anchor test which does this automatically)
anchor deploy --provider.cluster localnet
```

### Deploy to Devnet

```bash
# Switch to devnet
solana config set --url devnet

# Get devnet SOL
solana airdrop 2 $(solana address)

# Deploy
anchor deploy --provider.cluster devnet
```

### Deploy to Mainnet

⚠️ **WARNING: Mainnet deployment is permanent and costs real SOL**

```bash
# Switch to mainnet
solana config set --url mainnet-beta

# Ensure you have enough SOL (2-3 SOL recommended)
solana balance

# Deploy
anchor deploy --provider.cluster mainnet
```

## Project Structure

```
onlypump-presale/
├── programs/
│   └── onlypump-presale/
│       └── src/
│           └── lib.rs          # Main program logic
├── tests/
│   └── onlypump-presale.ts     # Test suite
├── migrations/
│   └── deploy.ts               # Deployment script
├── Anchor.toml                  # Anchor configuration
└── Cargo.toml                   # Rust workspace config
```

## Configuration

### Anchor.toml

The `Anchor.toml` file contains:
- Program IDs for different clusters
- Test configuration
- Provider settings (wallet, cluster)

### Program ID

The program ID is defined in:
- `programs/onlypump-presale/src/lib.rs`: `declare_id!("...")`
- `Anchor.toml`: `[programs.localnet]` section

## Common Commands

```bash
# Build
anchor build

# Test (auto-starts validator)
anchor test

# Test against existing validator
anchor test --skip-local-validator

# Deploy
anchor deploy --provider.cluster localnet
anchor deploy --provider.cluster devnet
anchor deploy --provider.cluster mainnet

# Clean build artifacts
anchor clean

# Generate new program keypair
anchor keys list
```

## Troubleshooting

### "Cargo.lock version 4 requires `-Znext-lockfile-bump`"

Run the Solana install script (see Prerequisites):
```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```

### "Program is not deployed"

The test script automatically deploys the program. If you see this error:
- Ensure the validator is running
- Check that port 8899 is available
- Try running `anchor test` again (it will restart the validator)

### "Port 8899 is already in use"

Kill any existing validators:
```bash
pkill -f solana-test-validator
```

### "Insufficient funds"

Get SOL for your wallet:
```bash
# Localnet (automatic with test validator)
solana airdrop 10 $(solana address)

# Devnet
solana airdrop 2 $(solana address) --url devnet
```

### "Websocket error"

This usually means the validator isn't ready yet. The test script includes a wait time, but if issues persist:
- Increase `startup_wait` in `Anchor.toml` `[test]` section
- Manually start validator and use `--skip-local-validator`

## Development Workflow

1. **Make changes** to Rust code in `programs/onlypump-presale/src/lib.rs`
2. **Build**: `anchor build`
3. **Test**: `anchor test` (automatically handles validator + deployment)
4. **Fix issues** and repeat
5. **Deploy to devnet** when ready: `anchor deploy --provider.cluster devnet`
6. **Test on devnet** with real transactions
7. **Deploy to mainnet** after thorough testing

## Resources

- [Anchor Documentation](https://www.anchor-lang.com/)
- [Solana Cookbook](https://solanacookbook.com/)
- [Solana Web3.js Docs](https://solana-labs.github.io/solana-web3.js/)

