# Installation

### Manual Installation <a href="#installation" id="installation"></a>

[Official Documentation](https://docs.lavanet.xyz/testnet)Recommended Hardware: 4 Cores, 8GB RAM, 100GB of storage (NVME)

```bash
# install dependencies, if needed
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```



```bash
# install go, if needed
cd $HOME
VER="1.20.5"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin

# set vars
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export LAVA_CHAIN_ID="lava-testnet-2"" >> $HOME/.bash_profile
echo "export LAVA_PORT="20"" >> $HOME/.bash_profile
source $HOME/.bash_profile

# download binary
cd $HOME
wget -O lavad https://binary.lavanet.xyz/v0.35.0/linux-amd64/lavad
chmod +x $HOME/lavad
mv $HOME/lavad $HOME/go/bin/lavad

# config and init app
lavad config node tcp://localhost:${LAVA_PORT}657
lavad config keyring-backend os
lavad config chain-id lava-testnet-2
lavad init $MONIKER --chain-id $LAVA_CHAIN_ID
sed -i \
  -e 's/timeout_commit = ".*"/timeout_commit = "30s"/g' \
  -e 's/timeout_propose = ".*"/timeout_propose = "1s"/g' \
  -e 's/timeout_precommit = ".*"/timeout_precommit = "1s"/g' \
  -e 's/timeout_precommit_delta = ".*"/timeout_precommit_delta = "500ms"/g' \
  -e 's/timeout_prevote = ".*"/timeout_prevote = "1s"/g' \
  -e 's/timeout_prevote_delta = ".*"/timeout_prevote_delta = "500ms"/g' \
  -e 's/timeout_propose_delta = ".*"/timeout_propose_delta = "500ms"/g' \
  -e 's/skip_timeout_commit = ".*"/skip_timeout_commit = false/g' \
  $HOME/.lava/config/config.toml

# download genesis and addrbook
wget -O $HOME/.lava/config/genesis.json https://testnet-files.itrocket.net/lava/genesis.json
wget -O $HOME/.lava/config/addrbook.json https://testnet-files.itrocket.net/lava/addrbook.json

# set seeds and peers
SEEDS="eb7832932626c1c636d16e0beb49e0e4498fbd5e@lava-testnet-seed.itrocket.net:20656"
PEERS="3693ea5a8a9c0590440a7d6c9a98a022ce3b2455@lava-testnet-peer.itrocket.net:20656,fe1998168f5336811a79fbcaf2d5d5a69f2f9f63@65.108.81.145:26656,40046fe63bdaa9efde27707b0d3de0bf84fedf80@86.111.48.158:26656,c19965fe8a1ea3391d61d09cf589bca0781d29fd@162.19.217.52:26656,aa5ada2c19585b0d288a4d0069922f0ac5d848e2@154.53.55.21:656,7e0c614bad2d0232b9153de29c193a2700ff5408@65.109.92.241:197,51aeaa2c757989f720c904023c2dbedfc720f75e@23.88.5.169:27656,6d4e96c5e653f0ed7f905b1d53ef8d373af55ecf@116.202.217.20:60656,706fc0f682c33ab8deb0aa84c797dc2d1d0119b4@109.123.241.222:26656,20c13bd0d972acba5588493fb528b558a0317013@38.242.133.203:26656,999b919696e7ab0d959b1c23e6b25c1e521dc657@154.53.54.11:656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.lava/config/config.toml

# set custom ports in app.toml
sed -i.bak -e "s%:1317%:${LAVA_PORT}317%g;
s%:8080%:${LAVA_PORT}080%g;
s%:9090%:${LAVA_PORT}090%g;
s%:9091%:${LAVA_PORT}091%g;
s%:8545%:${LAVA_PORT}545%g;
s%:8546%:${LAVA_PORT}546%g;
s%:6065%:${LAVA_PORT}065%g" $HOME/.lava/config/app.toml

# set custom ports in config.toml file
sed -i.bak -e "s%:26658%:${LAVA_PORT}658%g;
s%:26657%:${LAVA_PORT}657%g;
s%:6060%:${LAVA_PORT}060%g;
s%:26656%:${LAVA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${LAVA_PORT}656\"%;
s%:26660%:${LAVA_PORT}660%g" $HOME/.lava/config/config.toml

# config pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.lava/config/app.toml

# set minimum gas price, enable prometheus and disable indexing
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0ulava"|g' $HOME/.lava/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.lava/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.lava/config/config.toml

# create service file
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=Lava node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.lava
ExecStart=$(which lavad) start --home $HOME/.lava
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# reset and download snapshot
lavad tendermint unsafe-reset-all --home $HOME/.lava
if curl -s --head curl https://testnet-files.itrocket.net/lava/snap_lava.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/lava/snap_lava.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lava
    else
  echo no have snap
fi

# enable and start service
sudo systemctl daemon-reload
sudo systemctl enable lavad
sudo systemctl restart lavad && sudo journalctl -u lavad -f
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

pruning: custom: 100/0/10 | indexer: null

```bash
source <(curl -s https://itrocket.net/api/testnet/lava/autoinstall/)
```

### Create wallet <a href="#create-wallet" id="create-wallet"></a>

```bash
# to create a new wallet, use the following command. don’t forget to save the mnemonic
lavad keys add $WALLET

# to restore exexuting wallet, use the following command
lavad keys add $WALLET --recover

# save wallet and validator address
WALLET_ADDRESS=$(lavad keys show $WALLET -a)
VALOPER_ADDRESS=$(lavad keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile

# check sync status, once your node is fully synced, the output from above will print "false"
lavad status 2>&1 | jq .SyncInfo

# before creating a validator, you need to fund your wallet and check balance
lavad query bank balances $WALLET_ADDRESS
```

### Create validator <a href="#create-validator" id="create-validator"></a>

MonikerIdentityDetailsAmount, ulavaCommission rateCommission max rateCommission max change rate

```bash
lavad tx staking create-validator \
--amount 1000000ulava \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(lavad tendermint show-validator) \
--moniker "test" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id lava-testnet-2 \
--fees 300ulava \
-y
```

### Monitoring <a href="#monitoring" id="monitoring"></a>

If you want to have set up a monitoring and alert system use [our cosmos nodes monitoring guide with tenderduty](https://teletype.in/@itrocket/bdJAHvC\_q8h)

### Security <a href="#security" id="security"></a>

To protect you keys please don\`t share your privkey, mnemonic and follow a basic security rules

#### Set up ssh keys for authentication <a href="#ssh" id="ssh"></a>

You can use this [guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04) to configure ssh authentication and disable password authentication on your server

#### Firewall security <a href="#firewall" id="firewall"></a>

Set the default to allow outgoing connections, deny all incoming, allow ssh and node p2p port

```bash
sudo ufw default allow outgoing 
sudo ufw default deny incoming 
sudo ufw allow ssh/tcp 
sudo ufw allow ${LAVA_PORT}656/tcp
sudo ufw enable
```

### Delete node <a href="#delete" id="delete"></a>

```bash
sudo systemctl stop lavad
sudo systemctl disable lavad
sudo rm -rf /etc/systemd/system/lavad.service
sudo rm $(which lavad)
sudo rm -rf $HOME/.lava
sed -i "/LAVA_/d" $HOME/.bash_profile
```
