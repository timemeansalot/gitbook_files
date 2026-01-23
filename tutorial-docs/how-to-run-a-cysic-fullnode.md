# How to run a Cysic Fullnode

## How to Run a Cysic FullNode

This guide will help you set up and run a Cysic Network fullnode, including system requirements, installation configuration, node startup and node monitoring.

### Table of Contents

1. System Requirements
2. Download Configuration Files
3. Get Docker Image
4. Start Node with Docker Compose
5. Node Health Monitoring
6. Common Commands
7. Troubleshooting

### System Requirements

#### Minimum Hardware Requirements

* **CPU**: 4 cores (8 cores recommended)
* **Memory**: 8 GB RAM (16 GB recommended)
* **Storage**: 500 GB SSD (2TB recommended for long-term operation)
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

\
Before starting the nod&#x65;**,&#x20;**<mark style="color:$danger;">**you need to send the node's exit IP address to the Cysic team for whitelist configuration**</mark>**.**

* [https://forms.gle/JYaQB8bXNJPktRuR6](https://forms.gle/JYaQB8bXNJPktRuR6)



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

### Pull Docker Image

#### 1. Install docker

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

And then pull the docker image

```bash
docker compose pull
```

#### 2. Download snapshot data

Before starting the node, you need to update the `app.toml` configuration file:

```bash
wget https://cysic-data-mainnet.s3.ap-southeast-1.amazonaws.com/cysic_data_mainnet_20251225.tar.gz
tar -zxvf cysic_data_mainnet_20251225.tar.gz -C /data/mainnet/data
```

#### 3. Start Node

```bash
# Start validator node
docker compose up -d node

# View node logs
docker compose logs -f node
```

#### 4. Verify Node Running Status

```bash
# Check node status
curl http://localhost:26657/status

# Check node information
curl http://localhost:26657/abci_info
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
docker compose down

# Restart node
docker compose restart node

# View node logs
docker compose logs -f --tail=100 node

# Start node
docker compose up -d
```

### Troubleshooting

#### Common Issues

1. **Node Cannot Sync**
   1. Check network connection
   2. Verify configuration files
   3. Clear data and resync
2. **Container Startup Failure**
   1. Check if ports are occupied
   2. Verify configuration file paths
   3. Check container logs

#### Log Analysis

```bash
# View detailed logs
docker compose logs --tail=100 node

# Filter error logs
docker compose logs node | grep -i error

# Real-time log monitoring
docker compose logs -f node | grep -E "(error|failed|panic)"
```

#### Emergency Recovery

If the node encounters issues, follow these steps to recover:

1. Backup important data
2. Stop the node
3. Check configuration files
4. Restart the node
5. Verify sync status

***

For more help, please refer to the official documentation or contact the Cysic community.<br>
