# Snapshot

### Snapshot <a href="#snap" id="snap"></a>

height: 1792987 | 2h ago | size: 1.3GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop cascadiad

cp $HOME/.cascadiad/data/priv_validator_state.json $HOME/.cascadiad/priv_validator_state.json.backup

rm -rf $HOME/.cascadiad/data 
curl https://testnet-files.itrocket.net/cascadia/snap_cascadia.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.cascadiad

mv $HOME/.cascadiad/priv_validator_state.json.backup $HOME/.cascadiad/data/priv_validator_state.json

sudo systemctl restart cascadiad && sudo journalctl -u cascadiad -f
```
