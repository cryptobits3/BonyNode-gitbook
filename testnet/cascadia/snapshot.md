# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

height: 2155022 | 1h ago | size: 3.4GB | pruning: custom: 100/0/10 | indexer:

```bash
sudo systemctl stop crossfid

cp $HOME/.mineplex-chain/data/priv_validator_state.json $HOME/.mineplex-chain/priv_validator_state.json.backup

rm -rf $HOME/.mineplex-chain/data $HOME/.mineplex-chain/wasmPath
curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain

mv $HOME/.mineplex-chain/priv_validator_state.json.backup $HOME/.mineplex-chain/data/priv_validator_state.json

sudo systemctl restart crossfid && sudo journalctl -u crossfid -f
```

