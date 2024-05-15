# Useful commands

### Service operations ⚙️ <a href="#service-operations" id="service-operations"></a>

Check logs

```bash
sudo journalctl -u junctiond -f
```

Start service

```bash
sudo systemctl start junctiond
```

Stop service

```bash
sudo systemctl stop junctiond
```

Restart service

```bash
sudo systemctl restart junctiond
```

Check service status

```bash
sudo systemctl status junctiond
```

Reload services

```bash
sudo systemctl daemon-reload
```

Enable Service

```bash
sudo systemctl enable junctiond
```

Disable Service

```bash
sudo systemctl disable junctiond
```

Node info

```bash
junctiond status 2>&1 | jq
```

Your node peer

```bash
echo $(junctiond tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.junction/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Key management <a href="#key-management" id="key-management"></a>

Add New Wallet

```bash
junctiond keys add $WALLET
```

Restore executing wallet

```bash
junctiond keys add $WALLET --recover
```

List All Wallets

```bash
junctiond keys list
```

Delete wallet

```bash
junctiond keys delete $WALLET
```

Check Balance

```bash
junctiond q bank balances $WALLET_ADDRESS 
```

Export Key (save to wallet.backup)

```bash
junctiond keys export $WALLET
```

View EVM Prived Key

```bash
junctiond keys unsafe-export-eth-key $WALLET
```

Import Key (restore from wallet.backup)

```bash
junctiond keys import $WALLET wallet.backup
```

### Tokens <a href="#tokens" id="tokens"></a>

To valoper addressTo wallet addressAmount, amf

Withdraw all rewards

```bash
junctiond tx distribution withdraw-all-rewards --from $WALLET --chain-id junction --fees 200amf 
```

Withdraw rewards and commission from your validator

```bash
junctiond tx distribution withdraw-rewards $VALOPER_ADDRESS --from $WALLET --commission --chain-id junction --fees 200amf -y 
```

Check your balance

```bash
junctiond query bank balances $WALLET_ADDRESS
```

Delegate to Yourself

```bash
junctiond tx staking delegate $(junctiond keys show $WALLET --bech val -a) 1000000amf --from $WALLET --chain-id junction --fees 200amf -y 
```

Delegate

```bash
junctiond tx staking delegate <TO_VALOPER_ADDRESS> 1000000amf --from $WALLET --chain-id junction --fees 200amf -y 	
```

Redelegate Stake to Another Validator

```bash
junctiond tx staking redelegate $VALOPER_ADDRESS <TO_VALOPER_ADDRESS> 1000000amf --from $WALLET --chain-id junction --fees 200amf -y 
```

Unbond

```bash
junctiond tx staking unbond $(junctiond keys show $WALLET --bech val -a) 1000000amf --from $WALLET --chain-id junction --fees 200amf -y 
```

Transfer Funds

```bash
junctiond tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 1000000amf --fees 200amf -y 
```

### Validator operations <a href="#validator-operations" id="validator-operations"></a>

MonikerIdentityDetailsAmount, amfCommission rateCommission max rateCommission max change rate

Create New Validator

```bash
junctiond tx staking create-validator \
--amount 1000000amf \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(junctiond tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id junction \
--fees 200amf \
-y 
```

Edit Existing Validator

```bash
junctiond tx staking edit-validator \
--commission-rate 0.1 \
--new-moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--from $WALLET \
--chain-id junction \
--fees 200amf \
-y 
```

Validator info

```bash
junctiond status 2>&1 | jq
```

Validator Details

```bash
junctiond q staking validator $(junctiond keys show $WALLET --bech val -a) 
```

Jailing info

```bash
junctiond q slashing signing-info $(junctiond tendermint show-validator) 
```

Slashing parameters

```bash
junctiond q slashing params 
```

Unjail validator

```bash
junctiond tx slashing unjail --from $WALLET --chain-id junction --fees 200amf -y 
```

Active Validators List

```bash
junctiond q staking validators -oj --limit=2000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " 	 " + .description.moniker' | sort -gr | nl 
```

Check Validator key

```bash
[[ $(junctiond q staking validator $VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(junctiond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "Your key status is ok" || echo -e "Your key status is error"
```

Signing info

```bash
junctiond q slashing signing-info $(junctiond tendermint show-validator) 
```

### Governance <a href="#governance" id="governance"></a>

TitleDescriptionDeposit, amf

Create New Text Proposal

```bash
junctiond  tx gov submit-proposal \
--title "" \
--description "" \
--deposit 1000000amf \
--type Text \
--from $WALLET \
--fees 200amf \
-y 
```

Proposals List

```bash
junctiond query gov proposals 
```

Proposal IDProposal optionYesNoNo with vetoAbstain

View proposal

```bash
junctiond query gov proposal 1 
```

Vote

```bash
junctiond tx gov vote 1 yes --from $WALLET --chain-id junction  --fees 200amf -y 
```
