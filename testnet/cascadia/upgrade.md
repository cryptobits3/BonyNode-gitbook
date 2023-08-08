# Upgrade

Upgrade height: 1774000. Please don\`t upgrade before the specified height.

### Manual upgrade <a href="#manual" id="manual"></a>

```bash
cd $HOME
wget -O cascadiad https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.4/cascadiad
chmod +x $HOME/cascadiad
sudo mv $HOME/cascadiad $(which cascadiad)
sudo systemctl restart cascadiad && sudo journalctl -u cascadiad -f
```

### Auto upgrade <a href="#auto" id="auto"></a>

Don't kill the session with CTRL+C before update is completed, if you want to disconnect the session use CTRL+B D

```bash
cd $HOME && \
wget -O cascadiad https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.4/cascadiad && \
chmod +x $HOME/cascadiad && \
old_bin_path=$(which cascadiad) && \
home_path=$HOME && \
rpc_port=$(grep -m 1 -oP '^laddr = "\K[^"]+' "$HOME/.cascadiad/config/config.toml" | cut -d ':' -f 3) && \
tmux new -s cascadia-upgrade "sudo bash -c 'curl -s https://raw.githubusercontent.com/itrocket-team/testnet_guides/main/utils/autoupgrade/upgrade.sh | bash -s -- -u \"1774000\" -b cascadiad -n \"$HOME/cascadiad\" -o \"$old_bin_path\" -h \"$home_path\" -p cascadia -r \"$rpc_port\"'"
```
