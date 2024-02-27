# Install

## Namada-testnet

**Chain ID: shielded-expedition.88f17d1d14 | Latest Version Tag: v0.31.6 | Custom Port: 124**

***

#### Install dependencies

#### Update system and install build tools

```bash
sudo apt -q update
sudo apt -qy install make git-core libssl-dev pkg-config libclang-12-dev libudev-dev build-essential protobuf-compiler
sudo apt -qy upgrade
```

#### Download CometBFT

```bash
mkdir -p $HOME/.local/bin
curl -sL https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz | tar -C $HOME/.local/bin -xzf- cometbft
```

#### Install Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
echo "export PATH=$HOME/.local/bin:$PATH" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

#### Download and build binaries

```bash
# Clone project repository
cd $HOME
rm -rf shielded-expedition.88f17d1d14
git clone -b v0.31.6 https://github.com/anoma/namada.git shielded-expedition.88f17d1d14
cd shielded-expedition.88f17d1d14
make build-release
for BIN in namada namadac namadan namadar namadaw; do install -m 0755 target/release/$BIN $HOME/.local/bin/$BIN; done
```

#### Create SystemD service unit

```bash
sudo tee /etc/systemd/system/namada.service > /dev/null << EOF
[Unit]
Description=Namada node
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/.local/bin/namada node ledger run
Restart=always
RestartSec=10
LimitNOFILE=65535
Environment="CMT_LOG_LEVEL=p2p:none,pex:error"
Environment="NAMADA_CMT_STDOUT=true"
Environment="NAMADA_LOG=info"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.local/bin"

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable namada
```

#### Initialize the node

```bash
export PATH=$HOME/.local/bin:$PATH
namada client utils join-network --chain-id shielded-expedition.88f17d1d14 # --genesis-validator bengt
```

#### Set custom ports

**You can change value CUSTOM\_PORT=124 to any other ports**

```bash
CUSTOM_PORT=124
```

```bash
sed -i \
-e "s|^proxy_app = \"tcp://127.0.0.1:26658\"|proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"|" \
-e "s|^laddr = \"tcp://127.0.0.1:26657\"|laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"|" \
-e "s|^laddr = \"tcp://0.0.0.0:26656\"|laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"|" \
-e "s|^prometheus_listen_addr = \":26660\"|prometheus_listen_addr = \":${CUSTOM_PORT}66\"|" \
$HOME/.local/share/namada/shielded-expedition.88f17d1d14/config.toml
```

#### Start service and check the logs

```bash
sudo systemctl start namada && sudo journalctl -u namada -f --no-hostname -o cat
```
