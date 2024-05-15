# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

height: 106644 | 2h ago | size: 1.2GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop junctiond

cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup

rm -rf $HOME/.junction/data $HOME/.junction/wasmPath
curl https://testnet-files.itrocket.net/airchains/snap_airchains.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction

mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json

sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```

### &#x20;<a href="#sync" id="sync"></a>
