# `autonomi` - Autonomi client API

[![Crates.io](https://img.shields.io/crates/v/autonomi.svg)](https://crates.io/crates/autonomi)
[![docs.rs](https://img.shields.io/badge/api-rustdoc-blue.svg)](https://docs.rs/autonomi)

Connect to and build on the Autonomi network.

## Usage

Add the `autonomi` crate to your project with `cargo add`:

```sh
cargo add autonomi
```

### Example

```rust
use autonomi::{Bytes, Client, Wallet};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::init().await?;

    // Default wallet of testnet.
    let key = "0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80";
    let wallet = Wallet::new_from_private_key(Default::default(), key)?;

    // Put and fetch data.
    let data_addr = client
        .data_put_public(Bytes::from("Hello, World"), (&wallet).into())
        .await?;
    let _data_fetched = client.data_get_public(&data_addr).await?;

    // Put and fetch directory from local file system.
    let dir_addr = client.dir_upload_public("files/to/upload".into(), &wallet).await?;
    client
        .dir_download_public(dir_addr, "files/downloaded".into())
        .await?;

    Ok(())
}
```

In the above example the wallet is setup to use the default EVM network (Arbitrum One). Instead we can use a different
network:

```rust
use autonomi::{EvmNetwork, Wallet};
// Arbitrum Sepolia
let wallet = Wallet::new_from_private_key(EvmNetwork::ArbitrumSepoliaTest, key) ?;
// Custom (e.g. local testnet)
let wallet = Wallet::new_from_private_key(EvmNetwork::new_custom("<rpc URL>", "<payment token address>", "<data payment address>"), key) ?;
```

## Running tests

To run the unit tests for the autonomi crate:

```sh
cargo test --package autonomi --lib
```

### Integration testing with local network

For full integration testing that involves network operations, you'll need to set up a local Autonomi network using the [main repository](https://github.com/maidsafe/autonomi):

1. Clone and set up the main repository:
   ```sh
   git clone https://github.com/maidsafe/autonomi.git
   cd autonomi
   ```

2. Run a local EVM node (Foundry required):
   ```sh
   cargo run --bin evm-testnet
   ```

3. Run a local network:
   ```sh
   cargo run --bin antctl -- local run --build --clean --rewards-address <ETHEREUM_ADDRESS>
   ```

4. Run integration tests from the client repository:
   ```sh
   EVM_NETWORK=local cargo test --package autonomi
   ```

## Using funds from the Deployer Wallet

You can use the `Deployer wallet private key` printed in the EVM node output to initialise a wallet from with almost
infinite gas and payment tokens. Example:

```rust
let rpc_url = "http://localhost:54370/";
let payment_token_address = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
let data_payments_address = "0x8464135c8F25Da09e49BC8782676a84730C318bC";
let private_key = "0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80";

let network = Network::Custom(CustomNetwork::new(
rpc_url,
payment_token_address,
data_payments_address,
));

let deployer_wallet = Wallet::new_from_private_key(network, private_key).unwrap();
let receiving_wallet = Wallet::new_with_random_wallet(network);

// Send 10 payment tokens (atto)
let _ = deployer_wallet
.transfer_tokens(receiving_wallet.address(), Amount::from(10))
.await;
```

Alternatively, you can provide the wallet address that should own all the gas and payment tokens to the EVM testnet
startup command using the `--genesis-wallet` flag:

```sh
cargo run --bin evm-testnet -- --genesis-wallet <ETHEREUM_ADDRESS>
```

```shell
*************************
* Ethereum node started *
*************************
RPC URL: http://localhost:60093/
Payment token address: 0x5FbDB2315678afecb367f032d93F642f64180aa3
Chunk payments address: 0x8464135c8F25Da09e49BC8782676a84730C318bC
Deployer wallet private key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
Genesis wallet balance: (tokens: 20000000000000000000000000, gas: 9998998011366954730202)
```

# Python

For documentation on the Python bindings, see [./README_PYTHON.md].
