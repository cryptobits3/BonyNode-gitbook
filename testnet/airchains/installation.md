# Installation

![](https://raw.githubusercontent.com/kj89/cosmos-images/main/logos/babylon.png)

| Chain ID   | Latest Version Tag | Custom Port |
| ---------- | ------------------ | ----------- |
| bbn-test-2 | v0.7.2             | 164         |

#### Setup validator name <a href="#setup-validator-name" id="setup-validator-name"></a>

Replace **YOUR\_MONIKER\_GOES\_HERE** with your validator name

```
MONIKER="YOUR_MONIKER_GOES_HERE"
```

#### Install dependencies <a href="#install-dependencies" id="install-dependencies"></a>

**UPDATE SYSTEM AND INSTALL BUILD TOOLS**

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

**INSTALL GO**

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.20.5.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download and build binaries <a href="#download-and-build-binaries" id="download-and-build-binaries"></a>

```
# Clone project repository
cd $HOME
rm -rf babylon
git clone https://github.com/babylonchain/babylon.git
cd babylon
git checkout v0.7.2

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.babylond/cosmovisor/genesis/bin
mv build/babylond $HOME/.babylond/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.babylond/cosmovisor/genesis $HOME/.babylond/cosmovisor/current -f
sudo ln -s $HOME/.babylond/cosmovisor/current/bin/babylond /usr/local/bin/babylond -f
```

#### Install Cosmovisor and create a service <a href="#install-cosmovisor-and-create-a-service" id="install-cosmovisor-and-create-a-service"></a>

```
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0

# Create service
sudo tee /etc/systemd/system/babylond.service > /dev/null << EOF
[Unit]
Description=babylon-testnet node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.babylond"
Environment="DAEMON_NAME=babylond"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.babylond/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable babylond
```

#### Initialize the node <a href="#initialize-the-node" id="initialize-the-node"></a>

```
# Set node configuration
babylond config chain-id bbn-test-2
babylond config keyring-backend test
babylond config node tcp://localhost:16457

# Initialize the node
babylond init $MONIKER --chain-id bbn-test-2

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/babylon-testnet/genesis.json > $HOME/.babylond/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/babylon-testnet/addrbook.json > $HOME/.babylond/config/addrbook.json

# Add seeds
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@babylon-testnet.rpc.kjnodes.com:16459\"|" $HOME/.babylond/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.00001ubbn\"|" $HOME/.babylond/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.babylond/config/app.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:16458\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:16457\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:16460\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:16456\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":16466\"%" $HOME/.babylond/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:16417\"%; s%^address = \":8080\"%address = \":16480\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:16490\"%; s%^address = \"localhost:9091\"%address = \"0.0.0.0:16491\"%; s%:8545%:16445%; s%:8546%:16446%; s%:6065%:16465%" $HOME/.babylond/config/app.toml
```

#### Download latest chain snapshot <a href="#download-latest-chain-snapshot" id="download-latest-chain-snapshot"></a>

```
curl -L https://snapshots.kjnodes.com/babylon-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.babylond
[[ -f $HOME/.babylond/data/upgrade-info.json ]] && cp $HOME/.babylond/data/upgrade-info.json $HOME/.babylond/cosmovisor/genesis/upgrade-info.json
```

#### Start service and check the logs <a href="#start-service-and-check-the-logs" id="start-service-and-check-the-logs"></a>

```
sudo systemctl start babylond && sudo journalctl -u babylond -f --no-hostname -o cat
```

### Becoming a Validator <a href="#becoming-a-validator" id="becoming-a-validator"></a>

#### 1. Create a Keyring and Get Funds <a href="#1-create-a-keyring-and-get-funds" id="1-create-a-keyring-and-get-funds"></a>

Validators are required to have funds for two reasons:

* They need to provide a self delegation
* They need to pay for transaction fees for submitting BLS signature transactions

Currently, validators can only use the test keyring backend. In the future, Babylon will support other types of encrypted backends provided by the Cosmos SDK for validators.

**ADD NEW KEY**

```
babylond keys add wallet
```

**(OPTIONAL) RECOVER EXISTING KEY**

```
babylond keys add wallet --recover
```

**REQUEST FUNDS FROM THE BABYLON TESTNET FAUCET**

This can be accomplished by going to the `#faucet` channel of [official Discord server](https://discord.gg/babylonglobal) to request funds by providing the address you created before. After joining the channel, users send a request starting with `!faucet` followed by the request address. For example, `!faucet bbn1sajf5fd7tyjt0jjy6lqzahy09jl2nkcnx5qm06`

#### 2. Create a BLS key <a href="#2-create-a-bls-key" id="2-create-a-bls-key"></a>

Validators are expected to submit a BLS signature at the end of each epoch. To do that, a validator needs to have a BLS key pair to sign information with.

Using the address that you created on the previous step.

```
babylond create-bls-key $(babylond keys show wallet -a)
```

This command will create a BLS key and add it to the `$HOME/.babylond/config/priv_validator_key.json`. This is the same file that stores the private key that the validator uses to sign blocks. Please ensure that this file is secured properly.

After creating a BLS key, you need to restart your node to load this key into memory.

```
sudo systemctl restart babylond
```

#### 3. Modify the Configuration <a href="#3-modify-the-configuration" id="3-modify-the-configuration"></a>

Furthermore, you need to specify the name of the key that the validator will be using to submit BLS signature transactions under the `$HOME/.babylond/config/app.toml` file. Edit this file and set the key name to the one that holds funds on your keyring:

```
sed -i -e "s|^key-name *=.*|key-name = \"wallet\"|" $HOME/.babylond/config/app.toml
```

Finally, it is strongly recommended to modify the timeout\_commit value under `$HOME/.babylond/config/config.toml`. This value specifies how long a validator will wait before commiting a block before starting on a new height. Given that Babylon aims to have a 10 second time between blocks, set this value to:

```
sed -i -e "s|^timeout_commit *=.*|timeout_commit = \"10s\"|" $HOME/.babylond/config/config.toml
```

#### 4. Create the Validator <a href="#4-create-the-validator" id="4-create-the-validator"></a>

Contrary to a vanilla Cosmos SDK chain, a validator for Babylon is created through the `babylond tx checkpointing create-validator` command. This command expects that a BLS validator key exists under the `$HOME/.babylond/config/priv_validator_key.json`.

Babylon validators are required to submit a BLS signature transaction every epoch (with current parameters every \~30mins). Those transactions currently cost a static gas fee of `100ubbn`. Therefore, it is important that validators maintain enough unbonded funds in their keyring to pay for those transaction fees.

```
# Please make sure you have adjusted **moniker**, **identity**, **details** and **website** to match your values.

babylond tx checkpointing create-validator \
--amount 1000000ubbn \
--pubkey $(babylond tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id bbn-test-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.00001ubbn \
-y
```

Note: In order to become an active validator, you need to have more `ubbn` tokens bonded than the last validator ordered by the tokens bonded (or the validator set to not be full) as well as have at least `10000000ubbn` bonded.

Congrats! You are now a validator on the Babylon system.
