# State sync

### RPC, API, gRPC <a href="#rpc" id="rpc"></a>

RPC:[https://crossfi-testnet-rpc.itrocket.net](https://crossfi-testnet-rpc.itrocket.net/)\
API:[https://crossfi-testnet-api.itrocket.net](https://crossfi-testnet-api.itrocket.net/)J\
SON-RPC:[https://crossfi-testnet-jsonrpc.itrocket.net](https://crossfi-testnet-jsonrpc.itrocket.net/)

#### gRPC: <a href="#grpc" id="grpc"></a>

```bash
crossfi-testnet-grpc.itrocket.net:36090
```

#### peers: <a href="#peer" id="peer"></a>

```bash
66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656
```

#### seeds: <a href="#seed" id="seed"></a>

```bash
dd83e3c7c4e783f8a46dbb010ec8853135d29df0@crossfi-testnet-seed.itrocket.net:36656
```

#### live peers: <a href="#live-peers" id="live-peers"></a>

active peers: 3 (upd. every 10 sec)

```bash
PEERS="66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656,b88d969ba0e158da1b4066f5c17af9da68c52c7a@65.109.53.24:44656,5ebd3b1590d7383c0bb6696ad364934d7f1c984e@160.202.128.199:56156"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.mineplex-chain/config/config.toml
```

#### addrbook: <a href="#addrbook" id="addrbook"></a>

updates every hour

```bash
wget -O $HOME/.mineplex-chain/config/addrbook.json https://testnet-files.itrocket.net/crossfi/addrbook.json
```



### State Sync <a href="#sync" id="sync"></a>

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop crossfid

cp $HOME/.mineplex-chain/data/priv_validator_state.json $HOME/.mineplex-chain/priv_validator_state.json.backup
crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain

peers="66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656,b88d969ba0e158da1b4066f5c17af9da68c52c7a@65.109.53.24:44656,5ebd3b1590d7383c0bb6696ad364934d7f1c984e@160.202.128.199:56156"  
SNAP_RPC="https://crossfi-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.mineplex-chain/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.mineplex-chain/config/config.toml

mv $HOME/.mineplex-chain/priv_validator_state.json.backup $HOME/.mineplex-chain/data/priv_validator_state.json

sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```

### Wasm <a href="#wasm" id="wasm"></a>

Sorry, this project does not support WebAssembly.
