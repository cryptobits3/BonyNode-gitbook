# 🚀 ARCHWAY

![](https://raw.githubusercontent.com/kj89/cosmos-images/main/logos/archway.png)

Archway is an incentivized smart contract chain for Cosmos ecosystem which gives developers a simple way to build scalable cross-chain dapps. The developers automatically receive rewards for their contributions to the network.

| Chain ID      | Latest Version Tag | Wasm |
| ------------- | ------------------ | ---- |
| constantine-3 | v1.0.0-rc.2        | ON   |

[Website](https://archway.io/) | [Discord](https://discord.gg/archwayhq) | [Twitter](https://twitter.com/archwayhq)

Subscribe to our free [🤖 Testnet Proposal Bot](https://t.me/kjnodes\_testnet\_proposal\_bot) to never miss upcoming proposals

### Chain explorer <a href="#chain-explorer" id="chain-explorer"></a>

[https://explorer.kjnodes.com/archway-testnet](https://explorer.kjnodes.com/archway-testnet)

### Public endpoints <a href="#public-endpoints" id="public-endpoints"></a>

* api: [https://archway-testnet.api.kjnodes.com](https://archway-testnet.api.kjnodes.com/)
* rpc: [https://archway-testnet.rpc.kjnodes.com](https://archway-testnet.rpc.kjnodes.com/)
* grpc: archway-testnet.grpc.kjnodes.com:15690

### Peering <a href="#peering" id="peering"></a>

**state-sync**

```
d5519e378247dfb61dfe90652d1fe3e2b3005a5b@archway-testnet.rpc.kjnodes.com:15656
```

**seed-node**

```
3f472746f46493309650e5a033076689996c8881@archway-testnet.rpc.kjnodes.com:15659
```

**addrbook**

```
curl -Ls https://snapshots.kjnodes.com/archway-testnet/addrbook.json > $HOME/.archway/config/addrbook.json
```

**live-peers** (22)

```
peers="7e9827b5154a4ec4de93e277b19f77a387661664@46.4.213.198:26656,f7a7c6bf673c201c55ecf0d249df43826293d9d4@176.9.28.41:26656,e5e71ccd387eba74fec51b211e9236fca965af40@46.4.5.45:11556,b7084c40af131f24ab7e449a9844e0f56c94fa41@51.91.30.173:4000,61f4a7303f2c0c942167cf3592e5922f1095b40d@95.217.107.249:26656,453c95abab8d3e2d6a31f9a377f9f12cbe618c55@51.159.57.101:26656,8b96338b18c1e4a76a119fe0812c131a4e2cc96a@65.109.70.45:20656,5ae7629c56c6b710d5129e06a836fcaab06d2cee@43.131.30.29:26656,db8d38295a1ac80d1d333106886984037ce05555@65.109.93.35:26656,a985ecf6c6054d29b6e088e4434831497f7cc643@121.78.209.27:13056,5069525117c370eedfca4dbdf79a2d092c3b9687@173.249.49.123:24656,2b096acb7644899710adaad8d22c8784367c76d4@169.1.16.246:2010,a8c07490f5aa649f5a17e0e7c3fcb93f144c6ed5@66.42.38.167:26656,5c2a752c9b1952dbed075c56c600c3a79b58c395@195.3.220.140:26946,e8d60ff778f3c27f54382ff22c7ac071f2a81027@35.223.36.227:26656,f0993a9eef446cbfed4ed78bcb4179143079a5f3@51.161.84.41:26356,8b4a3a6f4dd71cd1ca98b0fb9d948ab69d57826f@89.58.32.218:27700,9a5b41ac06b3c131ca6e4959a465d6bc0d103e66@88.198.52.46:11556,0cf5d2bcc49c1acddb6b7b2bc547543ec2fbe844@34.239.246.206:26656,d0a57dec1e14e60e73c9a3f89f7cf351a846bd8a@120.226.39.220:16656,9588fb1df2b32f50ca95c31dd92de0cd4724eac3@120.226.39.200:26656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:15656"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.archway/config/config.toml
```
