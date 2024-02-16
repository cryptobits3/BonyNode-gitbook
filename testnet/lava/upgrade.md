# Upgrade

### Manual upgrade <a href="#manual" id="manual"></a>

```bash
cd $HOME
wget -O lavad https://github.com/lavanet/lava/releases/download/v0.35.0/lavad-v0.35.0-linux-amd64
chmod +x $HOME/lavad
sudo mv $HOME/lavad $(which lavad)
sudo systemctl restart lavad && sudo journalctl -u lavad -f
```

### Auto upgrade <a href="#auto" id="auto"></a>

Don't kill the session with CTRL+C before update is completed, if you want to disconnect the session use CTRL+B D

```bash
cd $HOME && \
wget -O lavad https://github.com/lavanet/lava/releases/download/v0.35.0/lavad-v0.35.0-linux-amd64 && \
chmod +x $HOME/lavad && \
old_bin_path=$(which lavad) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.lava/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s lava-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/itrocket-team/testnet_guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"845700\" -b lavad -n \"$HOME/lavad\" -o \"$old_bin_path\" -h \"$home_path\" -p \"https://lava-testnet-api.itrocket.net/cosmos/gov/v1/proposals/147\" -r \"$rpc_port\"'"
```
