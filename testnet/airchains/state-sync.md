# State sync

![](https://raw.githubusercontent.com/kj89/cosmos-images/main/logos/babylon.png)

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop babylond
cp $HOME/.babylond/data/priv_validator_state.json $HOME/.babylond/priv_validator_state.json.backup
babylond tendermint unsafe-reset-all --keep-addr-book --home $HOME/.babylond
```

#### Get and configure the state sync information <a href="#get-and-configure-the-state-sync-information" id="get-and-configure-the-state-sync-information"></a>

```
STATE_SYNC_RPC=https://babylon-testnet.rpc.kjnodes.com:443
STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@babylon-testnet.rpc.kjnodes.com:16456
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.babylond/config/config.toml

mv $HOME/.babylond/priv_validator_state.json.backup $HOME/.babylond/data/priv_validator_state.json
```

#### Download latest wasm <a href="#download-latest-wasm" id="download-latest-wasm"></a>

Currently state sync does not support copy of the `wasm` folder. Therefore, you will have to download it manually.

```
curl -L https://snapshots.kjnodes.com/babylon-testnet/wasm_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.babylond
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start babylond && sudo journalctl -u babylond -f --no-hostname -o cat
```
