# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Core Project Overview

This repository contains the Autonomi client components extracted from the main Autonomi monorepo:

- **autonomi**: Main client API library with high-level data operations
- **ant-cli**: Command-line interface for end users  
- **autonomi-nodejs**: Node.js bindings for the client API

The Autonomi Network is a fully autonomous data and communications network providing:
- **Lifetime Storage**: One-time payment for permanent data storage
- **Private by Design**: Multilayered encryption and self-encryption
- **Decentralized Infrastructure**: Built from everyday devices forming a peer-to-peer network

For comprehensive documentation, visit: https://docs.autonomi.com/

## Essential Development Commands

### Building and Testing
```bash
# Build all workspace members
cargo build --release

# Run unit tests for the autonomi library
cargo test --package autonomi --lib

# Check code formatting
cargo fmt --all -- --check

# Run clippy linter
cargo clippy --all-targets --all-features -- -Dwarnings

# Check documentation
RUSTDOCFLAGS="--deny=warnings" cargo doc --no-deps --workspace
```

### Client Usage
```bash
# Build the CLI
cargo build --release --bin ant

# Upload files (requires SECRET_KEY environment variable and running network)
SECRET_KEY=<YOUR_EVM_SECRET_KEY> ./target/release/ant --local file upload <path>

# Download files
./target/release/ant --local file download <addr> <dest_path>
```

### Node.js Development
```bash
# Build Node.js bindings
cd autonomi-nodejs
npm install
npm run build

# Run Node.js tests
npm test
```

## Workspace Architecture

### Core Components
- **autonomi**: Main client API library that handles data operations, payments, and networking
- **ant-cli**: Command-line interface that wraps the autonomi library for end users
- **autonomi-nodejs**: Node.js bindings using napi-rs for JavaScript/TypeScript integration

## Key Development Patterns

### Dependencies
This repository depends on several components from the main Autonomi repository:
- `ant-bootstrap`: Network bootstrap peer discovery
- `ant-evm`: Payment processing and blockchain integration  
- `ant-protocol`: Network protocol definitions
- `evmlib`: EVM blockchain integration
- `ant-logging`: Centralized logging infrastructure
- `test-utils`: Shared testing utilities

These are pulled in as git dependencies from the main repository.

### Payment System Integration
All data operations require EVM payments. When developing:
- Set SECRET_KEY environment variable for client operations
- Payments are handled through the ant-evm and evmlib crates
- A running network with EVM testnet is required for integration testing

### Testing Strategy
- Unit tests focus on the autonomi library components
- Integration tests require a running local network
- Node.js tests are run separately within the autonomi-nodejs directory

### Code Quality Standards
- All code must pass clippy with no warnings (`-Dwarnings`)
- Formatting enforced via rustfmt
- Documentation required (checked with `--deny=warnings`)
- Unwrap is allowed in tests (see .clippy.toml)

## Branch and Release Process
- Main development happens on the `main` branch
- PRs should target `main`
- Use Conventional Commits specification for commit messages
- Release process should be coordinated with main Autonomi repository