# Installation

Manual Installation&#x20;

```bash
# install dependencies, if needed
sudo apt update
sudo apt install -y curl git jq lz4 build-essential unzip

bash <(curl -s "https://raw.githubusercontent.com/nodejumper-org/cosmos-scripts/master/utils/go_install.sh")
source .bash_profile
```

NODE\_MONIKER=You Moniker

```bash
#!/bin/bash

NODE_MONIKER="Cascadia"

curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.4/cascadiad -o cascadiad
chmod +x cascadiad
sudo mv cascadiad /usr/local/bin
cascadiad version # ac03925

cascadiad config keyring-backend test
cascadiad config chain-id cascadia_6102-1
cascadiad init "$NODE_MONIKER" --chain-id cascadia_6102-1

curl -L https://github.com/CascadiaFoundation/chain-configuration/raw/master/testnet/genesis.json.gz -o ~/.cascadiad/config/genesis.zip
gunzip -c ~/.cascadiad/config/genesis.zip > ~/.cascadiad/config/genesis.json
rm -rf ~/.cascadiad/config/genesis.zip

curl -s https://snapshots1-testnet.nodejumper.io/cascadia-testnet/addrbook.json > $HOME/.cascadiad/config/addrbook.json

SEEDS="42c4a78f39935df1c20b51c4b0d0a21db8f01c88@cascadia-testnet-seed.itrocket.net:40656"
PEERS=""
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.cascadiad/config/config.toml

sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.cascadiad/config/app.toml
sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.cascadiad/config/app.toml
sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.cascadiad/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.cascadiad/config/app.toml

sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001aCC"|g' $HOME/.cascadiad/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.cascadiad/config/config.toml

sudo tee /etc/systemd/system/cascadiad.service > /dev/null << EOF
[Unit]
Description=Cascadia Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which cascadiad) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF

cascadiad tendermint unsafe-reset-all --home $HOME/.cascadiad --keep-addr-book

SNAP_NAME=$(curl -s https://snapshots1-testnet.nodejumper.io/cascadia-testnet/info.json | jq -r .fileName)
curl "https://snapshots1-testnet.nodejumper.io/cascadia-testnet/${SNAP_NAME}" | lz4 -dc - | tar -xf - -C "$HOME/.cascadiad"

sudo systemctl daemon-reload
sudo systemctl enable cascadiad
sudo systemctl start cascadiad

sudo journalctl -u cascadiad -f --no-hostname -o cat
```

Copy

Automatic Installation&#x20;

pruning: **custom/100/0/10**; indexer: **kv**

```bash
source <(curl -s https://raw.githubusercontent.com/nodejumper-org/cosmos-scripts/master/cascadia/cascadia_6102-1/install.sh)
```

Create Validator&#x20;

```bash
# create wallet
cascadiad keys add wallet

## console output:
#- name: wallet
#  type: local
#  address: cascadia104g6jcsg6fmmvn2a2t0nqg4ex68xgqdt2mpqne
#  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"Auq9WzVEs5pCoZgr2WctjI7fU+lJCH0I3r6GC1oa0tc0"}'
#  mnemonic: ""

#!!! SAVE SEED PHRASE
kite upset hip dirt pet winter thunder slice parent flag sand express suffer chest custom pencil mother bargain remember patient other curve cancel sweet

#!!! SAVE PRIVATE VALIDATOR KEY
cat $HOME/.cascadiad/config/priv_validator_key.json

# wait util the node is synced, should return FALSE
cascadiad status 2>&1 | jq .SyncInfo.catching_up

# get EVM address of your wallet
cascadiad address-converter $(cascadiad keys show wallet -a)

# go to https://www.cascadia.foundation/faucet and faucet some tokens to the EVM wallet

# verify the balance
cascadiad q bank balances $(cascadiad keys show wallet -a)

## console output:
#  balances:
#  - amount: "2000000000000000000"
#    denom: aCC

# create validator
cascadiad tx staking create-validator \
--amount=1000000000000000000aCC \
--pubkey=$(cascadiad tendermint show-validator) \
--moniker="$NODE_MONIKER" \
--chain-id=cascadia_6102-1 \
--commission-rate=0.1 \
--commission-max-rate=0.2 \
--commission-max-change-rate=0.05 \
--min-self-delegation=1 \
--gas-prices=7aCC \
--gas-adjustment=1.5 \
--gas=auto \
--from=wallet \
-y

# make sure you see the validator details
cascadiad q staking validator $(cascadiad keys show wallet --bech val -a)
```
