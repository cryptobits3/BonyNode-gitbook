# Snapshot

#### gRPC: <a href="#grpc" id="grpc"></a>

```bash
side-testnet-grpc.itrocket.net:45090
```

#### peers: <a href="#peer" id="peer"></a>

```bash
bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656
```

#### seeds: <a href="#seed" id="seed"></a>

```bash
9c14080752bdfa33f4624f83cd155e2d3976e303@side-testnet-seed.itrocket.net:45656
```

#### live peers: <a href="#live-peers" id="live-peers"></a>

active peers: 11 (upd. every 10 sec)

```bash
PEERS="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,9f718219b4987c9625b7e656f231095cefba29e8@95.214.54.146:26858,51d27832df0ebda927247d11eec133323c8f4347@88.198.7.204:36656,d4f63fdebcb3a3bd458885b3e15dbec595f281a7@161.97.118.43:17656,bba0fc728d37aabf7e2c25ed1c097398158161f1@89.117.53.20:26656,85a16af0aa674b9d1c17c3f2f3a83f28f468174d@167.235.242.236:26656,e6575e39599afba59bbe3422284b22edfb1adafb@23.88.5.169:24656,be9c91db3d56bc0306374145c61819849e700e63@37.27.5.156:26656,4e0fb7292b57e78a73d31a19a609ba6a0e74118f@161.97.170.6:26656,a70265a28a06e5a7d525920514ae17406dbeffd0@104.236.66.76:26656,6def6906f05a0d10a671d3cd2005529d320bb3c7@152.228.208.164:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.side/config/config.toml
```

#### addrbook: <a href="#addrbook" id="addrbook"></a>

updates every hour

```bash
wget -O $HOME/.side/config/addrbook.json https://testnet-files.itrocket.net/side/addrbook.json
```

### Snapshot <a href="#snap" id="snap"></a>

height: 247866 | 46m ago | size: 2.8GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup

rm -rf $HOME/.side/data $HOME/.side/wasmPath
curl https://testnet-files.itrocket.net/side/snap_side.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

sudo systemctl restart sided && sudo journalctl -u sided -f
```
