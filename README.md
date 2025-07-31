# Autonomi GitHub Actions

Reusable GitHub Actions for testing and managing Autonomi networks. These actions provide an easy way to spin up local Autonomi testnets with EVM support for CI/CD pipelines.

## Actions

### 🚀 spawn-autonomi-network

Starts a local Autonomi testnet with optional EVM support. This action handles all prerequisites, downloads binaries, and configures the network automatically.

**Features:**
- Downloads and caches Autonomi binaries
- Builds EVM testnet from source if needed
- Configurable node count and network settings
- Automatic peer discovery and network verification
- Support for custom rewards addresses
- Automatically extracts and sets SECRET_KEY for ant CLI operations

**Usage:**
```yaml
- name: Start Autonomi Network
  uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
  with:
    autonomi-version: 'stable-2025.7.1.3'
    node-count: '25'
    enable-evm: 'true'
    rewards-address: '0x03B770D9cD32077cC0bF330c13C114a87643B124'
```

**Inputs:**
| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `autonomi-version` | Autonomi version to use | `stable-2025.7.1.3` | No |
| `node-count` | Number of nodes to start | `25` | No |
| `rewards-address` | EVM address for rewards | `0x03B770D9cD32077cC0bF330c13C114a87643B124` | No |
| `enable-evm` | Start EVM testnet alongside network | `true` | No |
| `wait-for-network` | Time to wait for network start (seconds) | `15` | No |

**Outputs:**
| Output | Description |
|--------|-------------|
| `peer-address` | First peer address from the started network |
| `network-status` | Status of the network startup (`running`/`failed`) |

### 🧹 cleanup-autonomi-network

Stops Autonomi network processes and cleans up resources. Always use this action to ensure proper cleanup of network resources.

**Features:**
- Graceful network shutdown
- Process cleanup with fallback mechanisms
- Optional log collection and upload
- Configurable directory preservation
- Comprehensive cleanup verification

**Usage:**
```yaml
- name: Cleanup Autonomi Network
  if: always()
  uses: maidsafe/autonomi-actions/cleanup-autonomi-network@v1
  with:
    upload-logs: 'true'
    keep-directories: 'true'
```

**Inputs:**
| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `upload-logs` | Upload logs as artifacts | `false` | No |
| `keep-directories` | Keep network directories after cleanup | `true` | No |
| `timeout-minutes` | Maximum cleanup time | `5` | No |

**Outputs:**
| Output | Description |
|--------|-------------|
| `cleanup-status` | Status of cleanup (`success`/`partial`) |

## Complete Example

Here's a complete workflow that demonstrates both actions:

```yaml
name: Test with Autonomi Network

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test-with-autonomi:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Start Autonomi Network
        uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
        with:
          autonomi-version: 'stable-2025.7.1.3'
          node-count: '25'
          enable-evm: 'true'
      
      - name: Run your tests
        run: |
          # Your test commands here using ant CLI
          # Use --local flag to connect to the local network automatically
          
          # Example: Upload a file
          echo "Test data" > test.txt
          ant --local file upload test.txt --public
          
          # The network peer address is also available as: ${{ steps.start-network.outputs.peer-address }}
          echo "Network is running, peer: ${{ steps.start-network.outputs.peer-address }}"
          
      - name: Cleanup Autonomi Network
        if: always()
        uses: maidsafe/autonomi-actions/cleanup-autonomi-network@v1
        with:
          upload-logs: 'true'
          keep-directories: 'false'
```

## Environment Variables

The actions set up several environment variables that your tests can use:

- `ANT_PEERS`: The peer address of the first node in the network
- `SECRET_KEY`: The deployer secret key for ant CLI payment operations (when EVM is enabled)
- `EVM_NETWORK`: Set to `local` when EVM is enabled
- `ANT_LOG`: Set to `v` for verbose logging

**Note:** When using the ant CLI with these actions, you can use the `--local` flag which automatically connects to the local network setup.

## Requirements

- **OS**: Ubuntu (latest versions recommended)
- **Runner**: These actions install system dependencies and require `sudo` access
- **Rust**: The spawn action installs the Rust toolchain automatically
- **Foundry**: Installed automatically when EVM support is enabled

## Advanced Usage

### Custom Binary Versions

```yaml
- uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
  with:
    autonomi-version: 'stable-2025.7.2.0'  # Use a specific version
    node-count: '50'                        # Larger network
```

### Minimal Network (No EVM)

```yaml
- uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
  with:
    enable-evm: 'false'
    node-count: '10'
```

### Debug with Log Upload

```yaml
- uses: maidsafe/autonomi-actions/cleanup-autonomi-network@v1
  if: always()
  with:
    upload-logs: 'true'
    keep-directories: 'true'  # Preserve for debugging
```

## Troubleshooting

### Network Startup Issues

1. Check the action logs for specific error messages
2. Verify the Autonomi version exists in the releases
3. Ensure sufficient resources (CPU/memory) for the node count

### Cleanup Issues

1. The cleanup action reports `partial` status when some processes couldn't be stopped gracefully
2. Enable log upload to diagnose issues: `upload-logs: 'true'`
3. Check artifact uploads for detailed cleanup logs

### Binary Download Failures

1. Verify the `autonomi-version` parameter matches a valid GitHub release
2. Check network connectivity in the runner environment
3. The action supports both `stable-X.X.X` and `X.X.X` version formats

## Contributing

This repository contains reusable GitHub Actions for the Autonomi ecosystem. When contributing:

1. Test changes thoroughly with different configurations
2. Update documentation for any new features or parameters
3. Follow semantic versioning for releases
4. Ensure backward compatibility when possible

## License

These actions are provided under the same license as the Autonomi project.