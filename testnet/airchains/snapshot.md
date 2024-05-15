# Snapshot

![](https://raw.githubusercontent.com/kj89/cosmos-images/main/logos/babylon.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at **11:30 UTC**

| Pruning  | Indexer | Version Tag |
| -------- | ------- | ----------- |
| 100/0/19 | null    | v0.7.2      |

| Block  | Age     | Download                                                                                     |
| ------ | ------- | -------------------------------------------------------------------------------------------- |
| 314990 | 7 hours | [snapshot (0.85 GB)](https://snapshots.kjnodes.com/babylon-testnet/snapshot\_latest.tar.lz4) |

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop babylond
cp $HOME/.babylond/data/priv_validator_state.json $HOME/.babylond/priv_validator_state.json.backup
rm -rf $HOME/.babylond/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.kjnodes.com/babylon-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.babylond
mv $HOME/.babylond/priv_validator_state.json.backup $HOME/.babylond/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start babylond && sudo journalctl -u babylond -f --no-hostname -o cat
```
