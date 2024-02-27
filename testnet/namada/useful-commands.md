# Useful Commands

## Useful commands

#### Add new key

```bash
namada wallet gen --alias wallet
```

#### Recover existing key

```bash
namada wallet derive --alias wallet --hd-path default
```

#### List all keys

```bash
namada wallet list
```

#### Delete key

```bash
namada wallet remove --alias wallet --do-it
```

#### Query wallet balance

```bash
namada client balance --owner wallet
```

#### Initialize a new account

```bash
namada client init-account \
  --public-keys wallet \
  --signing-keys wallet \
  --threshold 1 \
  --alias account \
  --memo "pub_key"
```

#### Create a new validator

```bash
namada client init-validator \
  --alias "YOUR_VALIDATOR_ALIAS" \
  --account-keys wallet \
  --signing-keys wallet \
  --commission-rate 0.05 \
  --max-commission-rate-change 0.01 \
  --email validator@example.com \
  --memo "pub_key"
```

#### Delegate/bond tokens

```bash
namada client bond --source wallet --validator "YOUR_VALIDATOR_ALIAS" --amount 10 --memo "memo"
```

#### Unbond tokens

```bash
namada client unbond --source wallet --validator "YOUR_VALIDATOR_ALIAS" --amount 10 --memo "memo"
```

#### Withdraw unbonded tokens

```bash
namada client withdraw --source wallet --validator "YOUR_VALIDATOR_ALIAS" --memo "memo"
```

#### Unjail validator

```bash
namada client unjail-validator --validator "YOUR_VALIDATOR_ALIAS" --memo "memo"
```

#### Validator state

```bash
namada client validator-state --validator "YOUR_VALIDATOR_ALIAS"
```

#### Get sync info

```bash
curl -s localhost:12457/status | jq .result.sync_info
```

#### Governance

```bash
namada client vote-proposal  --proposal-id 1 --vote yay --signing-keys wallet --address wallet --memo "memo"
```

#### Remove node

```bash
sudo systemctl stop namada
sudo systemctl disable namada

sudo rm /etc/systemd/system/namada.service
sudo systemctl daemon-reload

rm -f $HOME/.local/bin/namada* $HOME/.local/bin/cometbft
rm -rf $HOME/.local/share/namada
```

#### Reload service configuration

```bash
sudo systemctl daemon-reload
```

#### Enable service

```bash
sudo systemctl enable namada
```

#### Disable service

```bash
sudo systemctl disable namada
```

#### Start service

```bash
sudo systemctl start namada
```

#### Stop service

```bash
sudo systemctl stop namada
```

#### Restart service

```bash
sudo systemctl restart namada
```

#### Check service status

```bash
sudo systemctl status namada
```

#### Check service logs

```bash
sudo journalctl -u namada -f --no-hostname -o cat
```
