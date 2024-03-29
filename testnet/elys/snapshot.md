# Snapshot

![](https://services.kjnodes.com/assets/images/logos/elys.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at **08:30 UTC**

| Pruning  | Indexer | Version Tag |
| -------- | ------- | ----------- |
| 100/0/19 | null    | v0.29.24    |

| Block   | Age      | Download                                                                                                                                                                                              |
| ------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 6479996 | 18 hours | [snapshot (13.95 GB)](https://snapshots.kjnodes.com/elys-testnet/snapshot\_latest.tar.lz4) with sha256sum [_`2e47b5`_](https://snapshots.kjnodes.com/elys-testnet/snapshot\_latest.tar.lz4.sha256sum) |

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop elys.service
cp $HOME/.elys/data/priv_validator_state.json $HOME/.elys/priv_validator_state.json.backup
rm -rf $HOME/.elys/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
curl -L https://snapshots.kjnodes.com/elys-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.elys
mv $HOME/.elys/priv_validator_state.json.backup $HOME/.elys/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start elys.service && sudo journalctl -u elys.service -f --no-hostname -o cat
```



ITROCKET

### Snapshot <a href="#snap" id="snap"></a>

height: 6493887 | 38m ago | size: 3.5GB | pruning: custom: 100/0/10 | indexer: null

```bash
sudo systemctl stop elysd

cp $HOME/.elys/data/priv_validator_state.json $HOME/.elys/priv_validator_state.json.backup

rm -rf $HOME/.elys/data 
curl https://testnet-files.itrocket.net/elys/snap_elys.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.elys

mv $HOME/.elys/priv_validator_state.json.backup $HOME/.elys/data/priv_validator_state.json

sudo systemctl restart elysd && sudo journalctl -u elysd -f
```
