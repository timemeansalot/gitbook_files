# How to Run a Validator node

**During Testnet Phase III, validators will be admitted in a permissioned way.**

This guide will help you set up and run a Cysic Network mainnet validator node, including system requirements, installation configuration, node startup, private key generation, staking operations, and node monitoring.

<mark style="color:$warning;">If you need to run a validator node for a testnet network, please globally replace "mainnet" in the document with "testnet", and change all chain-ids from "cysicmint\_4399-1" to "cysicmint\_4398-1".</mark>

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
mkdir -p /data/mainent && cd /data
# Download validator repo
git clone https://github.com/cysic-labs/validator
# Copy mainent folder to data
cp -rf validator/mainent/* /data/mainent
# Change directory to /data/mainent
cd /data/mainent
```



### Install Docker

```bash
sudo apt remove $(dpkg --get-selections docker.io docker compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl status docker
```

### Start Node with Docker Compose

#### 1. Pull the latest docker image

```yaml
docker compose pull
```

#### 2. Start Node

\*\*\* Before starting the node, you need to send the node's  Elastic IP address to the Cysic team for whitelist configuration \*\*\*

```bash
# Start validator node
docker compose up -d node

# View node logs
docker compose logs -f node
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

#### <sub><mark style="color:red;">\*\*Important\*\* write this mnemonic phrase in a safe place.<mark style="color:red;"></sub> <sub><mark style="color:red;">It is the only way to recover your account if you ever forget your password.<mark style="color:red;"></sub>

```bash
# Enter running container
docker exec -it node bash

# Generate new validator key
./cysicmintd keys add validator-xxx --home ./cysicmint --keyring-backend test
rocket athlete cage target walnut behave slow short fire fiscal neither apology reason boy stem lawsuit bird vessel arm among sugar jealous clerk exclude

# Verify generated node ID and validator public key
./cysicmintd tendermint show-node-id --home ./cysicmint --keyring-backend test
./cysicmintd tendermint show-validator --home ./cysicmint --keyring-backend test
```

#### 2. Backup Private Key

#### 2. Backup Private Key

<sup><mark style="color:red;">**\*\*Important\*\* Please make sure to securely back up the exported validator private key. Safeguarding this information is crucial, as it is the only way to restore access and manage the account. Do not share this key with untrusted individuals.**<mark style="color:red;"></sup>

```bash
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

<mark style="color:red;">\*\*\* Please contact the Cysic team to obtain mainnet staking tokens \*\*\*</mark>

#### 2. Create Validator

Use the `stake-as-validator` command to create a validator and self-stake:

```bash
# Create validator (example parameters)
./cysicmintd tx staking create-validator \
    --amount=100000000000000000000CGT \
    --moniker="james" \
    --details="james's validator"
    --chain-id cysicmint_4399-1 \
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
    --chain-id cysicmint_4399-1 \
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
    --chain-id cysicmint_4399-1

# Withdraw rewards
./cysicmintd tx distribution withdraw-all-rewards \
    $(./cysicmintd keys show validator-xxx --keyring-backend test --bech=val -a) \
    --from validator \
    --keyring-backend test \
    --chain-id cysicmint_4399-1
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
