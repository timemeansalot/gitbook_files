---
hidden: true
---

# How to Run a Validator node



This guide will help you set up and run a Cysic Network validator node, including system requirements, installation configuration, node startup, private key generation, staking operations, and node monitoring procedures.

### Table of Contents

1. System Requirements
2. Download Configuration Files
3. Get Docker Images
4. Start Node with Docker Compose
5. Generate Private Keys
6. Staking Operations to Increase Validator Power
7. Node Health Monitoring
8. Common Commands
9. Troubleshooting

### System Requirements

#### Minimum Hardware Requirements

* **CPU**: 4 cores (8 cores recommended)
* **Memory**: 8 GB RAM (16 GB recommended)
* **Storage**: 500 GB SSD (1TB recommended for long-term operation)
* **Network**: Stable internet connection with P2P communication support

#### Software Requirements

* **Operating System**: Linux (Ubuntu 20.04+ recommended) or macOS
* **Docker**: >= 20.10.0
* **Docker Compose**: >= 1.29.0
* **Git**: Latest version

#### Port Requirements

Ensure the following ports are available:

* `26657`: Tendermint RPC
* `26656`: Tendermint P2P
* `8545`: Ethereum JSON-RPC
* `8546`: Ethereum WebSocket
* `1317`: Cosmos REST API
* `9090`: gRPC API
* `6065`: Metrics (optional)

### Download Configuration Files

#### 1. Clone the Cysic Network Repository

```bash
git clone https://github.com/cysic-tech/cysic-network.git
cd cysic-network
```

#### 2. View Available Configurations

```bash
# View pre-configured local network settings
ls localnet-setup/
# Output: node0/ node1/ node2/ node3/ gentxs/
```

#### 3. Copy Configuration Template

```bash
# Create configuration directory for your validator node
mkdir -p ~/cysic-validator
cp -r localnet-setup/node0/cysicmintd ~/cysic-validator/
```

### Get Docker Images

#### 1. Pull Image from Docker Hub

```bash
# Pull the latest Cysic Network node image
docker pull cysicnetwork/cysicmintd:latest

# Or build local image
docker build -t cysicmintd/node .
```

#### 2. Verify Image

```bash
docker images | grep cysic
```

### Start Node with Docker Compose

#### 1. Configure Docker Compose

Modify the `docker-compose.yml` file according to your needs. Here's a single node configuration example:

```yaml
version: "3"

services:
  cysicmintd-validator:
    container_name: cysicmintd-validator
    image: "cysicmintd/node"
    ports:
      - "26657:26657"  # Tendermint RPC
      - "26656:26656"  # Tendermint P2P
      - "8545:8545"    # Ethereum JSON-RPC
      - "8546:8546"    # Ethereum WebSocket  
      - "1317:1317"    # Cosmos REST API
      - "9090:9090"    # gRPC API
      - "6065:6065"    # Metrics
    environment:
      - ID=0
      - LOG=${LOG:-cysicmintd.log}
      - LOGLEVEL=${LOGLEVEL:-info}
      - TRACE=${TRACE:-false}
    volumes:
      - ~/cysic-validator:/cysicmint:Z
    networks:
      - cysic-network
    restart: unless-stopped
    entrypoint: "bash start-docker.sh"

networks:
  cysic-network:
    driver: bridge
```

#### 2. Start the Node

```bash
# Start the validator node
docker-compose up -d cysicmintd-validator

# View node logs
docker-compose logs -f cysicmintd-validator
```

#### 3. Verify Node Running Status

```bash
# Check node status
curl http://localhost:26657/status

# Check node information
curl http://localhost:26657/abci_info
```

### Generate Private Keys

#### 1. Generate Keys Inside Container

```bash
# Enter the running container
docker exec -it cysicmintd-validator bash

# Generate new validator key
./cysicmintd keys add validator --keyring-backend test

# Generate node ID and validator public key
./cysicmintd tendermint show-node-id
./cysicmintd tendermint show-validator
```

#### 2. Backup Private Keys

```bash
# Export private key (Important: Keep it secure!)
./cysicmintd keys export validator --keyring-backend test

# Backup seed phrase
./cysicmintd keys mnemonic
```

#### 3. Get Validator Addresses

```bash
# Get validator address
./cysicmintd keys show validator --keyring-backend test -a

# Get validator operator address
./cysicmintd keys show validator --keyring-backend test --bech=val -a
```

### Staking Operations to Increase Validator Power

#### 1. Obtain Tokens

First, you need to obtain CYS tokens for staking:

```bash
# Check account balance
./cysicmintd query bank balances $(./cysicmintd keys show validator --keyring-backend test -a)
```

#### 2. Create Validator

Use the `stake-as-validator` command to create a validator and perform self-delegation:

```bash
# Create validator (example parameters)
./cysicmintd tx govtoken stake-as-validator \
    1000000000000000000000 \
    "My Validator" \
    "A reliable Cysic validator" \
    "0.05" \
    "0.20" \
    "0.01" \
    1000000000000000000 \
    $(./cysicmintd tendermint show-validator) \
    --from validator \
    --keyring-backend test \
    --chain-id cysic_7878-1 \
    --gas auto \
    --gas-adjustment 1.2 \
    --fees 20000000000000000CYS
```

Parameter explanations:

* `1000000000000000000000`: Staking amount (1000 CYS)
* `"My Validator"`: Validator name
* `"A reliable Cysic validator"`: Validator description
* `"0.05"`: Initial commission rate (5%)
* `"0.20"`: Maximum commission rate (20%)
* `"0.01"`: Commission change rate (1%)
* `1000000000000000000`: Minimum self-delegation (1 CYS)

#### 3. Delegate Additional Tokens

```bash
# Delegate additional tokens to validator
./cysicmintd tx staking delegate \
    $(./cysicmintd keys show validator --keyring-backend test --bech=val -a) \
    1000000000000000000000CGT \
    --from validator \
    --keyring-backend test \
    --chain-id cysic_7878-1 \
    --gas auto \
    --gas-adjustment 1.2 \
    --fees 20000000000000000CYS
```

#### 4. View Validator Status

```bash
# View validator information
./cysicmintd query staking validator $(./cysicmintd keys show validator --keyring-backend test --bech=val -a)

# View validator set
./cysicmintd query staking validators

# View delegation information
./cysicmintd query staking delegations $(./cysicmintd keys show validator --keyring-backend test -a)
```

### Node Health Monitoring

#### 1. Enable Prometheus Monitoring

Add the `--metrics` parameter when starting the node:

```bash
# Modify start-docker.sh script
./cysicmintd start --metrics \
    --home /cysicmint \
    --log_level $LOGLEVEL \
    --keyring-backend test \
    --pruning=nothing \
    --json-rpc.api eth,txpool,personal,net,debug,web3,miner \
    --api.enable
```

#### 2. Monitoring Endpoints

* **Node Status**: `http://localhost:26657/status`
* **Network Information**: `http://localhost:26657/net_info`
* **Validator Information**: `http://localhost:26657/validators`
* **Prometheus Metrics**: `http://localhost:6065/debug/metrics/prometheus`

#### 3. Key Monitoring Metrics

```bash
# Check node synchronization status
curl -s http://localhost:26657/status | jq '.result.sync_info'

# Check latest block height
curl -s http://localhost:26657/status | jq '.result.sync_info.latest_block_height'

# Check validator voting power
curl -s http://localhost:26657/validators | jq '.result.validators[] | select(.address=="YOUR_VALIDATOR_ADDRESS")'

# Check number of connected peers
curl -s http://localhost:26657/net_info | jq '.result.n_peers'
```

#### 4. Automated Monitoring Script

Create a simple monitoring script:

```bash
#!/bin/bash
# monitor.sh

echo "=== Cysic Node Health Check ==="
echo "Timestamp: $(date)"

# Check container status
if docker ps | grep -q cysicmintd-validator; then
    echo "✅ Container is running"
else
    echo "❌ Container is not running"
    exit 1
fi

# Check synchronization status
SYNC_INFO=$(curl -s http://localhost:26657/status | jq '.result.sync_info')
CATCHING_UP=$(echo $SYNC_INFO | jq -r '.catching_up')
LATEST_HEIGHT=$(echo $SYNC_INFO | jq -r '.latest_block_height')

echo "Block Height: $LATEST_HEIGHT"
if [ "$CATCHING_UP" = "false" ]; then
    echo "✅ Node is synchronized"
else
    echo "⚠️  Node is still syncing"
fi

# Check peers
PEERS=$(curl -s http://localhost:26657/net_info | jq -r '.result.n_peers')
echo "Connected Peers: $PEERS"

if [ "$PEERS" -gt 0 ]; then
    echo "✅ Network connectivity OK"
else
    echo "❌ No peers connected"
fi
```

### Common Commands

#### Node Operations

```bash
# Stop node
docker-compose down

# Restart node
docker-compose restart cysicmintd-validator

# View node logs
docker-compose logs -f cysicmintd-validator

# Clean data and resync
docker-compose down
sudo rm -rf ~/cysic-validator/data
docker-compose up -d
```

#### Key Management

```bash
# List all keys
./cysicmintd keys list --keyring-backend test

# Recover key
./cysicmintd keys add validator --recover --keyring-backend test

# Delete key
./cysicmintd keys delete validator --keyring-backend test
```

#### Staking Management

```bash
# Undelegate
./cysicmintd tx staking unbond \
    $(./cysicmintd keys show validator --keyring-backend test --bech=val -a) \
    1000000000000000000CGT \
    --from validator \
    --keyring-backend test \
    --chain-id cysic_7878-1

# Withdraw rewards
./cysicmintd tx distribution withdraw-rewards \
    $(./cysicmintd keys show validator --keyring-backend test --bech=val -a) \
    --from validator \
    --keyring-backend test \
    --chain-id cysic_7878-1

# Withdraw commission
./cysicmintd tx distribution withdraw-rewards \
    $(./cysicmintd keys show validator --keyring-backend test --bech=val -a) \
    --commission \
    --from validator \
    --keyring-backend test \
    --chain-id cysic_7878-1
```

### Troubleshooting

#### Common Issues

1. **Node Cannot Sync**
   * Check network connection
   * Verify configuration files
   * Clean data and resync
2. **Validator Not in Active Set**
   * Check if staking amount is sufficient
   * Confirm validator is not jailed
   * Review staking situation of other validators in the network
3. **Container Fails to Start**
   * Check if ports are occupied
   * Verify configuration file paths
   * Review container logs

#### Log Analysis

```bash
# View detailed logs
docker-compose logs --tail=100 cysicmintd-validator

# Filter error logs
docker-compose logs cysicmintd-validator | grep -i error

# Real-time log monitoring
docker-compose logs -f cysicmintd-validator | grep -E "(error|failed|panic)"
```

#### Emergency Recovery

If the node encounters problems, use the following steps for recovery:

1. Backup important data
2. Stop the node
3. Check configuration files
4. Restart the node
5. Verify synchronization status

***

### Summary

Through this guide, you should be able to:

* Set up a complete Cysic validator node environment
* Manage nodes using Docker Compose
* Generate and manage validator keys
* Execute staking operations to increase validator power
* Monitor node health status
* Handle common issues

Running a validator node requires continuous maintenance and monitoring. It's recommended to set up automated monitoring and alerting systems to ensure stable node operation.

For more help, please refer to the official documentation or contact the Cysic community.
