# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u cascadiad -f
```

Start service

```bash
sudo systemctl start cascadiad
```

Stop service

```bash
sudo systemctl stop cascadiad
```

Restart service

```bash
sudo systemctl restart cascadiad
```

Check service status

```bash
sudo systemctl status cascadiad
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable cascadiad
```

Disable Service

```bash
sudo systemctl disable cascadiad
```

Sync info

```bash
cascadiad status 2>&1 | jq .SyncInfo
```

Node info

```bash
cascadiad status 2>&1 | jq .NodeInfo
```

Your node peer

```bash
echo $(cascadiad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.cascadiad/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
cascadiad keys add $WALLET
```

Restore executing wallet

```bash
cascadiad keys add $WALLET --recover
```

List All Wallets

```bash
cascadiad keys list
```

Delete wallet

```bash
cascadiad keys delete $WALLET
```

Check Balance

```bash
cascadiad q bank balances $(cascadiad keys show $WALLET -a)
```

Export Key (save to wallet.backup)

```bash
cascadiad keys export $WALLET
```

Import Key (restore from wallet.backup)

```bash
cascadiad keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, aCC

Withdraw all rewards

```bash
cascadiad tx distribution withdraw-all-rewards --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC
```

Withdraw rewards and commission from your validator

```bash
cascadiad tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Check your balance

```bash
cascadiad query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
cascadiad tx staking delegate $(cascadiad keys show $WALLET --bech val -a) 1000000aCC --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Delegate

```bash
cascadiad tx staking delegate <TO_VALOPER_ADDRESS> 1000000aCC --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Redelegate Stake to Another Validator

```bash
cascadiad tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000aCC --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Unbond

```bash
cascadiad tx staking unbond $(cascadiad keys show $WALLET --bech val -a) 1000000aCC --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Transfer Funds

```bash
cascadiad tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000aCC --gas-prices 7aCC -y
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

MonikerIdentityDetailsAmount, aCCCommission rateCommission max rateCommission max change rate

Create New Validator

```bash
cascadiad tx staking create-validator \
--amount 1000000aCC \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(cascadiad tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id cascadia_6102-1 \
--gas-prices 7aCC \
-y
```

Edit Existing Validator

```bash
cascadiad tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id cascadia_6102-1 \
--gas-prices 7aCC \
-y
```

Validator info

```bash
cascadiad status 2>&1 | jq .ValidatorInfo
```

Validator Details

```bash
cascadiad q staking validator $(cascadiad keys show $WALLET --bech val -a)
```

Jailing info

```bash
cascadiad q slashing signing-info $(cascadiad tendermint show-validator)
```

Unjail validator

```bash
cascadiad tx slashing unjail --broadcast-mode block --from $WALLET --chain-id cascadia_6102-1 --gas-prices 7aCC -y
```

Active Validators List

```bash
cascadiad q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl
```

Check Validator key

```bash
[[ $(cascadiad q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(cascadiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
cascadiad q slashing signing-info $(cascadiad tendermint show-validator)
```

### Governance <a href="#governance" id="governance"></a>

TitleDescriptionDeposit, aCC

Create New Text Proposal

```bash
cascadiad  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000aCC \
--type Text \
--from $WALLET \
--gas-prices 7aCC \
-y 
```

Proposals List

```bash
cascadiad query gov proposals
```

Proposal ID Proposal option Yes/No with veto/Abstain

View proposal

```bash
cascadiad query gov proposal 1
```

Vote

```bash
cascadiad tx gov vote 1 yes --from $WALLET --chain-id cascadia_6102-1  --gas-prices 7aCC -y
```
