# State sync

### RPC, API, gRPC <a href="#rpc" id="rpc"></a>

Public RPC:[https://cascadia-testnet-rpc.itrocket.net:443](https://cascadia-testnet-rpc.itrocket.net/)\
Public API:[https://cascadia-testnet-api.itrocket.net:443](https://cascadia-testnet-api.itrocket.net/)

#### gRPC: <a href="#grpc" id="grpc"></a>

```bash
cascadia-testnet-grpc.itrocket.net:40090
```

#### peers: <a href="#peer" id="peer"></a>

```bash
c6e3921222655345d8296353994e917f13a1b4a1@cascadia-testnet-peer.itrocket.net:40656
```

#### seeds: <a href="#seed" id="seed"></a>

```bash
42c4a78f39935df1c20b51c4b0d0a21db8f01c88@cascadia-testnet-seed.itrocket.net:40656
```

#### live peers: <a href="#live-peers" id="live-peers"></a>

active peers: 31 (upd. every 10 sec)

```bash
PEERS="c6e3921222655345d8296353994e917f13a1b4a1@cascadia-testnet-peer.itrocket.net:40656,da8204dcdc45ac1e1dee9c3dc6f33f29cc1539a7@176.57.184.215:28656,b19ac2a44da617ee60920fe9cecd2fee1595f0cf@46.4.101.89:26680,1ecb3286ed1e3ef898459f55948234014b3be6f7@46.4.68.113:21456,1d61222b7b8e180aacebfd57fbd2d8ab95ebdc4c@65.109.93.152:35656,3ae7f63ee1cf8ea10a4596c4d5d46bb2074500fb@65.109.99.123:26656,46b554c4cddbde4adb7b10e7b6c66e46845ffc33@135.181.116.109:22796,5126c2904cf4d9ed9b2c6cd203fccbe3983229da@23.88.5.169:22656,8c37e44a6c8c2c73287cd35a43ed532844b29d29@65.108.229.93:33656,a0d606bdfe2b4e0bf462f27d03de899d3a37fc12@185.216.176.209:36656,f78611ffa950efd9ddb4ed8f7bd8327c289ba377@65.109.108.150:46656,e85f72848ba9586c6704445d1118fb35e2ca5804@65.109.84.33:38656,16b622800dedb46f764eb052bd9596b762a05bfd@49.12.84.248:15656,80dc7406f7ec05f26cd4e529208fc911f75923b0@65.21.235.172:39656,e25bf22448e62faca2359985303ec4557f662444@95.217.11.20:26656,a66c5923f0d75f6ca62b357dc53c2d23b4f7da52@89.163.221.50:26656,3c0a861f998447d8b3ab83324187692fc86b92eb@213.239.215.165:55656,f9123cc641d69c4674d59f122640ef7d6f209e88@95.214.55.138:10656,11f2e6b2ce7845d738262d160be5a1249b0e1ae9@31.220.77.238:26656,ed0bf599702c33b800d2bc16f7968c03a678406d@65.109.50.234:26656,040d0b6ffefba3283b5763e26c352c7b1b232c1f@65.109.90.171:34656,adfcea6d0e2bb22f6b8de6c74221f8a16fac9ccc@37.120.172.227:46656,30408b285f4989484dff0a273d5221c583e5eff3@164.92.82.243:26656,d8da38b74746b2ad4308e4d35e25564ff8d954f5@192.145.37.163:26656,315825d37462b716b57fbe6d2af1c57239b9910d@65.109.66.126:15556,6a25da4432fc66f52c6edfd26b4fd6f327b28714@62.171.182.164:26656,e2c90e8b67e19ceee4b177ea7909dbee24de7c7a@162.55.175.11:12656,8274413c90f8f34ccdd35c0f3a59d546d8abbcbb@155.133.22.40:26656,d8083cfabcdcd3028c32bff0cd2170483058d828@149.102.152.136:26656,84e7d5644be79bfab25855aaa321cc35a811e222@162.55.85.246:26656,a2b41b91da1a6bb58a2dbf94daa8df088e591791@46.4.226.215:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.cascadiad/config/config.toml
```

#### addrbook: <a href="#addrbook" id="addrbook"></a>

updates every hour

```bash
wget -O $HOME/.cascadiad/config/addrbook.json https://testnet-files.itrocket.net/cascadia/addrbook.json
```



### State Sync <a href="#sync" id="sync"></a>

If you don't want to wait for a long synchronization you can use:

```bash
sudo systemctl stop cascadiad

cp $HOME/.cascadiad/data/priv_validator_state.json $HOME/.cascadiad/priv_validator_state.json.backup
cascadiad tendermint unsafe-reset-all --home $HOME/.cascadiad

peers="c6e3921222655345d8296353994e917f13a1b4a1@cascadia-testnet-peer.itrocket.net:40656,da8204dcdc45ac1e1dee9c3dc6f33f29cc1539a7@176.57.184.215:28656,b19ac2a44da617ee60920fe9cecd2fee1595f0cf@46.4.101.89:26680,1ecb3286ed1e3ef898459f55948234014b3be6f7@46.4.68.113:21456,1d61222b7b8e180aacebfd57fbd2d8ab95ebdc4c@65.109.93.152:35656,3ae7f63ee1cf8ea10a4596c4d5d46bb2074500fb@65.109.99.123:26656,46b554c4cddbde4adb7b10e7b6c66e46845ffc33@135.181.116.109:22796,5126c2904cf4d9ed9b2c6cd203fccbe3983229da@23.88.5.169:22656,8c37e44a6c8c2c73287cd35a43ed532844b29d29@65.108.229.93:33656,a0d606bdfe2b4e0bf462f27d03de899d3a37fc12@185.216.176.209:36656,f78611ffa950efd9ddb4ed8f7bd8327c289ba377@65.109.108.150:46656,e85f72848ba9586c6704445d1118fb35e2ca5804@65.109.84.33:38656,16b622800dedb46f764eb052bd9596b762a05bfd@49.12.84.248:15656,80dc7406f7ec05f26cd4e529208fc911f75923b0@65.21.235.172:39656,e25bf22448e62faca2359985303ec4557f662444@95.217.11.20:26656,a66c5923f0d75f6ca62b357dc53c2d23b4f7da52@89.163.221.50:26656,3c0a861f998447d8b3ab83324187692fc86b92eb@213.239.215.165:55656,f9123cc641d69c4674d59f122640ef7d6f209e88@95.214.55.138:10656,11f2e6b2ce7845d738262d160be5a1249b0e1ae9@31.220.77.238:26656,ed0bf599702c33b800d2bc16f7968c03a678406d@65.109.50.234:26656,040d0b6ffefba3283b5763e26c352c7b1b232c1f@65.109.90.171:34656,adfcea6d0e2bb22f6b8de6c74221f8a16fac9ccc@37.120.172.227:46656,30408b285f4989484dff0a273d5221c583e5eff3@164.92.82.243:26656,d8da38b74746b2ad4308e4d35e25564ff8d954f5@192.145.37.163:26656,315825d37462b716b57fbe6d2af1c57239b9910d@65.109.66.126:15556,6a25da4432fc66f52c6edfd26b4fd6f327b28714@62.171.182.164:26656,e2c90e8b67e19ceee4b177ea7909dbee24de7c7a@162.55.175.11:12656,8274413c90f8f34ccdd35c0f3a59d546d8abbcbb@155.133.22.40:26656,d8083cfabcdcd3028c32bff0cd2170483058d828@149.102.152.136:26656,84e7d5644be79bfab25855aaa321cc35a811e222@162.55.85.246:26656,a2b41b91da1a6bb58a2dbf94daa8df088e591791@46.4.226.215:26656"  
SNAP_RPC="https://cascadia-testnet-rpc.itrocket.net:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.cascadiad/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.cascadiad/config/config.toml

mv $HOME/.cascadiad/priv_validator_state.json.backup $HOME/.cascadiad/data/priv_validator_state.json

sudo systemctl restart cascadiad && sudo journalctl -u cascadiad -f
```

### Wasm <a href="#wasm" id="wasm"></a>

Sorry, this project does not support WebAssembly.
