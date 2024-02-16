# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

height: 854136 | 3h ago | size: 1.1GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop lavad

cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup

rm -rf $HOME/.lava/data $HOME/.lava/wasmPath
curl https://testnet-files.itrocket.net/lava/snap_lava.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.lava

mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json

sudo systemctl restart lavad && sudo journalctl -u lavad -f
```

### &#x20;<a href="#sync" id="sync"></a>
