# How to Run a Validator node

**During Testnet Phase III, validators will be admitted in a permissioned way.**

This guide will help you set up and run a Cysic Network validator node, including system requirements, installation configuration, node startup, private key generation, staking operations, and node monitoring.

### System Requirements

#### Minimum Hardware Requirements

* **CPU**: 4 cores (8 cores recommended)
* **Memory**: 8 GB RAM (16 GB recommended)
* **Storage**: 200 GB SSD (500 GB recommended for long-term operation)
* **Network**: Stable internet connection with P2P communication support

#### Software Requirements

* **Operating System**: Linux (Ubuntu 20.04+ recommended)
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

```bash
# Create data directory
mkdir -p /data && cd /data
# Download configuration files
wget https://statics.prover.xyz/testnet_node.tar.gz
# Extract configuration files
tar -zxvf testnet_node.tar.gz
```

#### Download Data Snapshot

```bash
# Download snapshot
wget https://statics.prover.xyz/data.tar.gz
# Remove existing data directory
rm -rf node/cysicmintd/data
# Extract snapshot to data directory
tar -zxvf data.tar.gz -C node/cysicmintd
```

### Pull Docker Image

```bash
docker-compose pull
```

### Start Node with Docker Compose

#### 1. Configure Docker Compose

Modify the `docker-compose.yml` file according to your needs. Here's a single-node configuration example:

```yaml
version: "3"

services:
  node:
    container_name: node
    image: "ghcr.io/cysic-tech/cysic-network:testnet"
    ports:
      - "26657:26657"  # Tendermint RPC
      - "26656:26656"  # Tendermint P2P
      - "8545:8545"    # Ethereum JSON-RPC
      - "8546:8546"    # Ethereum WebSocket  
      - "1317:1317"    # Cosmos REST API
      - "9090:9090"    # gRPC API
      - "6065:6065"    # Metrics
    environment:
      - ID=xxx
      - LOG=${LOG:-cysicmintd.log}
      - LOGLEVEL=${LOGLEVEL:-info}
      - TRACE=${TRACE:-false}
    volumes:
        - ./node/cysicmintd:/cysicmint
    networks:
      - cysic-network
    restart: unless-stopped
    entrypoint: "bash start-docker.sh"

networks:
  cysic-network:
    driver: bridge
```

#### 2. Start Node

\*\*\* Before starting the node, you need to send the node's exit IP address to the Cysic team for whitelist configuration \*\*\*

```bash
# Start validator node
docker-compose up -d node

# View node logs
docker-compose logs -f node
```

#### 3. Verify Node Running Status

```bash
# Check node status
curl http://localhost:26657/status

# Check node information
curl http://localhost:26657/abci_info
```

### Generate Private Key

#### 1. Generate Key in Container

```bash
# Enter running container
docker exec -it node bash

# Generate new validator key
./cysicmintd keys add validator-xxx --home ./cysicmint --keyring-backend test

**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

rocket athlete cage target walnut behave slow short fire fiscal neither apology reason boy stem lawsuit bird vessel arm among sugar jealous clerk exclude

# Verify generated node ID and validator public key
./cysicmintd tendermint show-node-id --home ./cysicmint --keyring-backend test
./cysicmintd tendermint show-validator --home ./cysicmint --keyring-backend test
```

#### 2. Backup Private Key

```bash
# Export private key (Important: Keep it safe!)
./cysicmintd keys export validator-xxx --home ./cysicmint --keyring-backend test
```

#### 3. Get Validator Address

```bash
# Get validator address
./cysicmintd keys show validator-xxx --home ./cysicmint --keyring-backend test -a

# Get validator operator address
./cysicmintd keys show validator-xxx --home ./cysicmint --keyring-backend test --bech=val -a
```

### Staking Operations to Increase Validator Power

#### 1. Get Tokens

Check balance

```bash
# Check account balance
./cysicmintd query bank balances $(./cysicmintd keys show validator-xxx --home ./cysicmint --keyring-backend test -a)
```

\*\*\* Please contact the Cysic team to obtain testnet staking tokens \*\*\*

#### 2. Create Validator

Use the `stake-as-validator` command to create a validator and self-stake:

```bash
# Create validator (example parameters)
./cysicmintd tx staking create-validator \
    --amount=100000000000000000000CGT \
    --moniker="james" \
    --details="james's validator"
    --chain-id cysicmint_9001-1 \
    --commission-rate="0.05" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation=1000000000000000000 \
    --pubkey=$(./cysicmintd tendermint show-validator --home ./cysicmint) \
    --from validator-xxx \
    --home=./cysicmint \
    --keyring-backend test \
    --gas auto \
    --gas-adjustment 1.2 \
    --gas-prices=300000CYS \
    --yes
```

Parameter description:

* `10000000000000000000`: Staking amount (10 CGT)
* `"My Validator"`: Validator name
* `"A reliable Cysic validator"`: Validator description
* `"0.05"`: Initial commission rate (5%)
* `"0.20"`: Maximum commission rate (20%)
* `"0.01"`: Commission change rate (1%)
* `1000000000000000000`: Minimum self-stake (1 CGT)

#### 3. Delegate More Tokens

```bash
# Delegate additional tokens to validator
./cysicmintd tx staking delegate \
    $(./cysicmintd keys show validator-xxx --home ./cysicmint --keyring-backend test --bech=val -a) \
    10000000000000000000CGT \
    --from validator-xxx \
    --home=./cysicmint \
    --keyring-backend test \
    --chain-id cysicmint_9001-1 \
    --gas auto \
    --gas-adjustment 1.2 \
    --gas-prices=300000CYS
```

#### 4. View Validator Status

```bash
# View validator information
./cysicmintd query staking validator $(./cysicmintd keys show validator-xxx --home ./cysicmint  --keyring-backend test --bech=val -a)

# View validator set
./cysicmintd query staking validators

# View delegation information
./cysicmintd query staking delegations $(./cysicmintd keys show validator-xxx --home ./cysicmint  --keyring-backend test -a)
```

### Node Health Monitoring

#### 1. Monitoring Endpoints

* **Node Status**: `http://localhost:26657/status`
* **Network Information**: `http://localhost:26657/net_info`
* **Validator Information**: `http://localhost:26657/validators`
* **Prometheus Metrics**: `http://localhost:6065/debug/metrics/prometheus`

#### 2. Key Monitoring Metrics

```bash
# Check node sync status
curl -s http://localhost:26657/status | jq '.result.sync_info'

# Check latest block height
curl -s http://localhost:26657/status | jq '.result.sync_info.latest_block_height'

# Check validator voting power
curl -s http://localhost:26657/validators | jq '.result.validators[] | select(.address=="YOUR_VALIDATOR_ADDRESS")'

# Check number of peer connections
curl -s http://localhost:26657/net_info | jq '.result.n_peers'
```

### Common Commands

#### Node Operations

```bash
# Stop node
docker-compose down

# Restart node
docker-compose restart node

# View node logs
docker-compose logs -f node

# Start node
docker-compose up -d
```

#### Key Management

```bash
# List all keys
./cysicmintd keys list --keyring-backend test

# Recover key
./cysicmintd keys add validator-xxx --recover --keyring-backend test

# Delete key
./cysicmintd keys delete validator-xxx --keyring-backend test
```

#### Staking Management

```bash
# Unbond stake
./cysicmintd tx staking unbond \
    $(./cysicmintd keys show validator-xxx --keyring-backend test --bech=val -a) \
    1000000000000000000CGT \
    --from validator \
    --keyring-backend test \
    --chain-id cysicmint_9001-1

# Withdraw rewards
./cysicmintd tx distribution withdraw-all-rewards \
    $(./cysicmintd keys show validator-xxx --keyring-backend test --bech=val -a) \
    --from validator \
    --keyring-backend test \
    --chain-id cysicmint_9001-1
```

### Troubleshooting

#### Common Issues

1. **Node Cannot Sync**
   * Check network connection
   * Verify configuration files
   * Clear data and resync
2. **Validator Not in Active Set**
   * Check if staking amount is sufficient
   * Confirm validator is not jailed
   * Check staking status of other validators in the network
3. **Container Startup Failure**
   * Check if ports are occupied
   * Verify configuration file paths
   * Check container logs

#### Log Analysis

```bash
# View detailed logs
docker-compose logs --tail=100 node

# Filter error logs
docker-compose logs node | grep -i error

# Real-time log monitoring
docker-compose logs -f node | grep -E "(error|failed|panic)"
```

#### Emergency Recovery

If the node encounters issues, follow these steps to recover:

1. Backup important data
2. Stop the node
3. Check configuration files
4. Restart the node
5. Verify sync status

***

For more help, please refer to the official documentation or contact the Cysic community.
