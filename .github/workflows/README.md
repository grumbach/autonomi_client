# GitHub Actions Workflows

This directory contains GitHub Actions workflows for continuous integration and testing of the Autonomi client components.

## Workflows Overview

### Core CI Workflows

#### `ci.yml` - Basic CI Pipeline
- **Triggers**: Push/PR to main, merge group
- **Jobs**:
  - `lint-and-format`: Code formatting and linting checks
  - `unit-tests`: Run unit tests on all platforms
  - `build-check`: Verify builds work on all platforms
- **Platforms**: Ubuntu, Windows, macOS

#### `ant-cli-tests.yml` - CLI Integration Tests
- **Purpose**: Test the `ant` CLI against a live network
- **Network**: Uses `maidsafe/autonomi-actions` to spawn test network
- **Tests**:
  - File upload/download operations
  - Register creation, editing, and retrieval
  - Vault operations (create, load, sync)
  - Wallet operations
- **Platforms**: Ubuntu, macOS, Windows

#### `file-upload-download-tests.yml` - Comprehensive File Tests
- **Purpose**: Extensive testing of file operations
- **Test Cases**:
  - Public/private files and directories
  - With/without archive functionality
  - Various file sizes (small: 10MB, medium: 50MB)
  - Directory structures with nested files
- **Platforms**: Ubuntu, macOS, Windows

#### `api-integration-tests.yml` - Rust API Tests  
- **Purpose**: Test the autonomi Rust library integration
- **Tests**: Run integration tests from the autonomi crate
- **Network**: Uses spawned test network
- **Platforms**: Ubuntu, Windows, macOS

### Node.js Workflows

#### `nodejs.yml` - Node.js Bindings CI
- **Purpose**: Build and test Node.js bindings
- **Platforms**: Multiple architectures and operating systems
- **Output**: Native binary modules for each platform

#### `nodejs-publish.yml` - Node.js Package Publishing
- **Purpose**: Publish autonomi Node.js package to NPM
- **Trigger**: Manual workflow dispatch
- **Requirements**: NPM_TOKEN secret

## Network Testing Architecture

### Autonomi Actions Integration

The workflows use `maidsafe/autonomi-actions` GitHub Actions for network management:

```yaml
- name: Start Autonomi Network
  uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
  with:
    autonomi-version: 'stable-2025.7.1.3'
    node-count: '25'
    enable-evm: 'true'

- name: Cleanup Autonomi Network
  if: always()
  uses: maidsafe/autonomi-actions/cleanup-autonomi-network@v1
  with:
    upload-logs: 'true'
    keep-directories: 'false'
```

### Environment Variables

Test workflows automatically receive:
- `ANT_PEERS`: Network peer addresses
- `EVM_NETWORK`: EVM network configuration
- `SECRET_KEY`: Test wallet private key (set in workflows)

### Test Data Management

- **Upload tests**: Create random test files of various sizes
- **Download verification**: Compare file sizes and content integrity
- **Directory tests**: Test nested directory structures
- **Archive handling**: Test both archived and non-archived uploads

## Local Development

To run similar tests locally:

1. **Set up test network** (requires main autonomi repository):
   ```bash
   git clone https://github.com/maidsafe/autonomi.git
   cd autonomi
   cargo run --bin evm-testnet
   cargo run --bin antctl -- local run --build --clean --rewards-address <ADDRESS>
   ```

2. **Run client tests**:
   ```bash
   cd autonomi_client
   export SECRET_KEY="0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80"
   cargo test --package autonomi --tests
   ./target/release/ant --local file upload test_file.txt
   ```

## Maintenance

### Updating Network Versions
Update the `autonomi-version` parameter in workflow files when new stable versions are released.

### Adding New Tests
1. Follow existing patterns in test workflows
2. Use the same network setup actions
3. Include proper cleanup steps
4. Test on all supported platforms

### Secrets Required
- `NPM_TOKEN`: For Node.js package publishing
- Standard GitHub Actions secrets are automatically available