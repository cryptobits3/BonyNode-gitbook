---
coverY: 0
---

# 🚀 AIRCHAINS

<figure><img src="https://itrocket.net/_next/image/?url=%2F_next%2Fstatic%2Fmedia%2Fairchains.bfb162f6.jpg&#x26;w=96&#x26;q=75" alt=""><figcaption></figcaption></figure>

Website - [https://www.airchains.io/](https://www.airchains.io/)

Subscribe to our free [🤖 Testnet Proposal Bot](https://t.me/kjnodes\_testnet\_proposal\_bot) to never miss upcoming proposals

### Chain explorer <a href="#chain-explorer" id="chain-explorer"></a>

[https://explorer.bonynode.online/airchains](https://explorer.bonynode.online/airchains)

### Public endpoints <a href="#public-endpoints" id="public-endpoints"></a>

* api:  [https://airchains-testnet-api.bonynode.online/](https://airchains-testnet-api.bonynode.online/)
* rpc: [https://airchains-testnet-rpc.bonynode.online/](https://airchains-testnet-rpc.bonynode.online/)
* grpc: [https://airchains-testnet-grpc.bonynode.online:19090](https://airchains-testnet-grpc.bonynode.online:19090)

### RPC, API, gRPC <a href="#rpc" id="rpc"></a>



#### gRPC: <a href="#grpc" id="grpc"></a>

```bash
ahttps://airchains-testnet-grpc.bonynode.online:19090
```

#### peers: <a href="#peer" id="peer"></a>

```bash
47f61921b54a652ca5241e2a7fc4ed8663091e89@airchains-testnet-peer.itrocket.net:19656
```

#### seeds: <a href="#seed" id="seed"></a>

```bash
04e2fdd6ec8f23729f24245171eaceae5219aa91@airchains-testnet-seed.itrocket.net:19656
```

#### live peers: <a href="#live-peers" id="live-peers"></a>

active peers: 11 (upd. every 10 sec)

```bash
PEERS="47f61921b54a652ca5241e2a7fc4ed8663091e89@airchains-testnet-peer.itrocket.net:19656,e78a440c57576f3743e6aa9db00438462980927e@5.161.199.115:26656,747b20b00224128bb3a3022cfa557fa105a8e41d@84.247.140.127:43456,f786dcc80601ddd33ba98c609795083ba418d740@158.220.119.11:43456,0b1159b05e940a611b275fe0006070439e5b6e69@[2a03:cfc0:8000:13::b910:277f]:13756,c8f6b1a795a6d9cd2ec39faf277163a9711fc81b@38.242.194.19:43456,552d2a5c3d9889444f123d740a20237c89711109@109.199.96.143:43456,cc27f4e54a78b950adaf46e5413f92f5d53d2212@209.126.86.186:43456,f5b69a02abeb3340ccd266f049ed6aabc7c0ea88@94.72.114.150:43456,43ab9af9fabe523a0a8794ae779100c50d1383e6@5.189.142.177:17656,db38d672f66df4de01b26e1fa97e1632fbfb1bdf@173.249.57.190:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.junction/config/config.toml
```

#### addrbook: <a href="#addrbook" id="addrbook"></a>

updates every hour

```bash
wget -O $HOME/.junction/config/addrbook.json https://testnet-files.itrocket.net/airchains/addrbook.json
```
