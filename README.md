# Autonomi Client API and Tools

[Autonomi.com](https://autonomi.com/) | [Documentation](https://docs.autonomi.com/)

Critical infrastructure of the next web client components: APIs, CLI, and Node.js bindings for the Autonomi Network.

This repository contains the client-side components for interacting with the Autonomi Network:
- **Lifetime storage** with one-time payment
- **Private by design** with multilayered encryption
- **Decentralized infrastructure** built from everyday devices
- **Content-addressable storage** using Kademlia and libp2p

## Components

### For Users
- **[CLI](ant-cli/README.md)** - The `ant` command line interface that enables users to interact with the network from their terminal
- **[Autonomi API](autonomi/README.md)** - The Rust client API library for developers building applications
- **[Node.js Bindings](autonomi-nodejs/README.md)** - JavaScript/TypeScript bindings for the Autonomi client API

## Quick Start

### Installing the CLI

#### Option 1: Download Binary
1. Go to the [Releases](https://github.com/maidsafe/autonomi_client/releases) page
2. Download the latest release for your operating system
3. Extract and add to your PATH

#### Option 2: Build from Source
```bash
git clone https://github.com/maidsafe/autonomi_client.git
cd autonomi_client
cargo build --release --bin ant
```

### Using the Rust API

Add to your `Cargo.toml`:
```toml
[dependencies]
autonomi = "0.6"
```

Basic usage:
```rust
use autonomi::{Bytes, Client, Wallet};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::init().await?;
    
    let key = "0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80";
    let wallet = Wallet::new_from_private_key(Default::default(), key)?;
    
    // Upload data
    let data_addr = client
        .data_put_public(Bytes::from("Hello, World"), (&wallet).into())
        .await?;
    
    // Download data
    let _data_fetched = client.data_get_public(&data_addr).await?;
    
    Ok(())
}
```

### Using the Node.js API

```bash
npm install @withautonomi/autonomi
```

```javascript
import { Client } from '@withautonomi/autonomi'

const client = await Client.initLocal()
// Ready to use the network!
```

## Development

### Prerequisites
- [Rust](https://www.rust-lang.org/) (latest stable)
- [Node.js](https://nodejs.org/) (for Node.js bindings)

### Building
```bash
# Build all components
cargo build --release

# Build CLI only
cargo build --release --bin ant

# Build Node.js bindings
cd autonomi-nodejs
npm install
npm run build
```

### Testing
```bash
# Run unit tests
cargo test --lib

# Format code
cargo fmt --all

# Run linter
cargo clippy --all-targets --all-features -- -D warnings
```

## Using with Local Network

For development and testing, you can connect to a local Autonomi network:

1. **Set up the main Autonomi repository** for running nodes:
   ```bash
   git clone https://github.com/maidsafe/autonomi.git
   cd autonomi
   ```

2. **Start local EVM testnet**:
   ```bash
   cargo run --bin evm-testnet
   ```

3. **Start local network**:
   ```bash
   cargo run --bin antctl -- local run --build --clean --rewards-address <YOUR_ETHEREUM_ADDRESS>
   ```

4. **Use the client tools** from this repository:
   ```bash
   # Using CLI
   SECRET_KEY=<YOUR_EVM_SECRET_KEY> ./target/release/ant --local file upload <path>
   
   # Using Rust API with local network
   EVM_NETWORK=local cargo test --package autonomi
   ```

## Architecture

This repository focuses on client-side components:

- **`autonomi`** - Core Rust client library providing high-level APIs for data operations, payments, and network interaction
- **`ant-cli`** - Command-line interface wrapping the autonomi library for end-users  
- **`autonomi-nodejs`** - Node.js bindings using napi-rs for JavaScript/TypeScript applications

### Dependencies

These components depend on core network infrastructure from the [main Autonomi repository](https://github.com/maidsafe/autonomi):
- `ant-bootstrap` - Network bootstrap peer discovery
- `ant-evm` - Payment processing and blockchain integration
- `ant-protocol` - Network protocol definitions
- `evmlib` - EVM blockchain integration
- `ant-logging` - Centralized logging infrastructure

## Documentation

- **[Developer Documentation](https://docs.autonomi.com/developers)** - Comprehensive guides for building with Autonomi
- **[API Reference](https://docs.rs/autonomi)** - Rust API documentation
- **Individual component READMEs** - Detailed usage for each component

## Community

Join our community:
- **Discord**: https://discord.gg/autonomi
- **Forum**: https://forum.autonomi.community/
- **X (Twitter)**: https://x.com/WithAutonomi
- **Reddit**: https://www.reddit.com/r/autonomi/
- **LinkedIn**: https://uk.linkedin.com/company/withautonomi

## Contributing

Contributions are welcome! Please read our [contribution guidelines](https://github.com/maidsafe/autonomi/blob/main/CONTRIBUTING.md).

### Pull Request Process
1. Target the `main` branch
2. Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages
3. Ensure all CI tests pass
4. Keep PRs under 200 lines of changes when possible
5. Reference relevant issues using [GitHub keywords](https://help.github.com/articles/closing-issues-using-keywords)

## License

This Autonomi Client repository is licensed under the General Public License (GPL), version 3 ([LICENSE](http://www.gnu.org/licenses/gpl-3.0.en.html)).