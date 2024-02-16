# State sync

### State Sync <a href="#sync" id="sync"></a>

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop lavad

cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup
lavad tendermint unsafe-reset-all --home $HOME/.lava

peers="3693ea5a8a9c0590440a7d6c9a98a022ce3b2455@lava-testnet-peer.itrocket.net:20656,fe1998168f5336811a79fbcaf2d5d5a69f2f9f63@65.108.81.145:26656,40046fe63bdaa9efde27707b0d3de0bf84fedf80@86.111.48.158:26656,c19965fe8a1ea3391d61d09cf589bca0781d29fd@162.19.217.52:26656,aa5ada2c19585b0d288a4d0069922f0ac5d848e2@154.53.55.21:656,7e0c614bad2d0232b9153de29c193a2700ff5408@65.109.92.241:197,51aeaa2c757989f720c904023c2dbedfc720f75e@23.88.5.169:27656,6d4e96c5e653f0ed7f905b1d53ef8d373af55ecf@116.202.217.20:60656,706fc0f682c33ab8deb0aa84c797dc2d1d0119b4@109.123.241.222:26656,20c13bd0d972acba5588493fb528b558a0317013@38.242.133.203:26656,999b919696e7ab0d959b1c23e6b25c1e521dc657@154.53.54.11:656"  
SNAP_RPC="https://lava-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.lava/config/config.toml

mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json

sudo systemctl restart lavad && sudo journalctl -u lavad -f
```
