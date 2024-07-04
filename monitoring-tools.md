---
description: COSMOS node protecting and monitoring guide with tenderduty
coverY: 0
---

# 🧙‍♀️ Monitoring Tools

Update packages and Install dependencies

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc tmux pkg-config libssl-dev libleveldb-dev tar -y
```

Install go

```
ver="1.18.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```

Install tenderduty

```
cd $HOME
rm -rf tenderduty
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
cp example-config.yml config.yml
```

Open config.yml file

```
sudo nano $HOME/tenderduty/config.yml
```

For simple monitoring without notifications, just change these in the config:

* **Osmosys** to \<PROJECT\_NAME>
* **chain\_id: osmosis-1** to chain\_id: \<YOUR\_CHAIN\_ID>
* **valoper\_address: osmovaloper1xxxxxxx...** to valoper\_address: \<YOUR\_VALOPER\_ADDRESS>
* **url: tcp://localhost:26657** TO url: tcp://localhost:\<YOUR\_NODE\_RPC\_PORT>

<figure><img src="https://img2.teletype.in/files/9c/f2/9cf2a127-dbad-40b3-bf59-0a03aa2ba614.jpeg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/9c/f2/9cf2a127-dbad-40b3-bf59-0a03aa2ba614.jpeg" alt="" height="231" width="850"><figcaption></figcaption></figure>

<figure><img src="https://img3.teletype.in/files/64/a5/64a5a8b6-18d7-4929-9958-532852c226cb.jpeg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img3.teletype.in/files/64/a5/64a5a8b6-18d7-4929-9958-532852c226cb.jpeg" alt="" height="189" width="850"><figcaption></figcaption></figure>

**Optional If you want to add another node monitoring, you can dublicate this section on conf.yml file**

```
chains:

  # The user-friendly name that will be used for labels. Highly suggest wrapping in quotes.
  "Osmosis":
    # chain_id is validated for a match when connecting to an RPC endpoint, also used as a label in several places.
    chain_id: osmosis-1
    # Hooray, in v2 we derive the valcons from abci queries so you don't have to jump through hoops to figure out how
    # to convert ed25519 keys to the appropriate bech32 address
    valoper_address: osmovaloper1xxxxxxx...
    # Should the monitor revert to using public API endpoints if all supplied RCP nodes fail?
    # This isn't always reliable, not all public nodes have websocket proxying setup correctly.
    public_fallback: no

    # Controls various alert settings for each chain.
    alerts:
      # If the chain stops seeing new blocks, should an alert be sent?
      stalled_enabled: yes
      # How long a halted chain takes in minutes to generate an alarm
      stalled_minutes: 10

      # Most basic alarm, you just missed x blocks ... would you like to know?
      consecutive_enabled: yes
      # How many missed blocks should trigger a notification?
      consecutive_missed: 5
      # NOT USED: future hint for pagerduty's routing
      consecutive_priority: critical

      # For each chain there is a specific window of blocks and a percentage of missed blocks that will result in
      # a downtime jail infraction. Should an alert be sent if a certain percentage of this window is exceeded?
      percentage_enabled: no
      # What percentage should trigger the alert
      percentage_missed: 10
      # Not used yet, pagerduty routing hint
      percentage_priority: warning

      # Should an alert be sent if the validator is not in the active set ie, jailed,
      # tombstoned, unbonding?
      alert_if_inactive: yes
      # Should an alert be sent if no RPC servers are responding? (Note this alarm is instantaneous with no delay)
      alert_if_no_servers: yes

      # for this *specific* chain it's possible to override alert settings. If the api_key or webhook addresses are empty,
      # the global settings will be used. Note, enabled must be set both globally and for each chain.

      # Chain specific setting for pagerduty
      pagerduty:
        enabled: yes
        api_key: "" # uses default if blank

      # Discord settings
      discord:
        enabled: yes
        webhook: "" # uses default if blank

      # Telegram settings
      telegram:
        enabled: yes
        api_key: "" # uses default if blank
        channel: "" # uses default if blank

    # This section covers our RPC providers. No LCD (aka REST) endpoints are used, only TM's RPC endpoints
    # Multiple hosts are encouraged, and will be tried sequentially until a working endpoint is discovered.
    nodes:
      # URL for the endpoint. Must include protocol://hostname:port
      - url: tcp://localhost:26657
        # Should we send an alert if this host isn't responding?
        alert_if_down: yes
      # repeat hosts for monitoring redundancy
      - url: https://some-other-node:443
        alert_if_down: no
```

Create service file and start tenderduty

```
sudo tee /etc/systemd/system/tenderdutyd.service << EOF
[Unit]
Description=Tenderduty
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=5
TimeoutSec=180

User=$USER
WorkingDirectory=$HOME/tenderduty
ExecStart=$(which tenderduty)

# there may be a large number of network connections if a lot of chains
LimitNOFILE=infinity

# extra process isolation
NoNewPrivileges=true
ProtectSystem=strict
RestrictSUIDSGID=true
LockPersonality=true
PrivateUsers=true
PrivateDevices=true
PrivateTmp=true

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable tenderdutyd
sudo systemctl start tenderdutyd
```

Now you can see the logs

```
sudo journalctl -fu tenderdutyd
```

<figure><img src="https://img1.teletype.in/files/01/34/013486f5-e802-4164-99e4-3310d2848b5c.jpeg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img1.teletype.in/files/01/34/013486f5-e802-4164-99e4-3310d2848b5c.jpeg" alt="" height="250" width="827"><figcaption></figcaption></figure>

You can open dashboard on web browser by using tenderduty port and your server IP http://\<YOUR\_SERVER\_IP>:\<PORT>\
Default port on tenderduty is 8888

<figure><img src="https://img2.teletype.in/files/10/23/10238232-df26-4de4-a600-fd6aa6c796ce.jpeg" alt=""><figcaption></figcaption></figure>

### Turn on firewall to protect your server and open the required port

Default RPC port is 26656, prometheus - 26660 and tenderduty - 8888. if you have custom ports on your node or have another nodes in this server, check it and open custom ports!

```
sudo apt install ufw 
sudo ufw default allow outgoing 
sudo ufw default deny incoming 
sudo ufw allow ssh/tcp 
sudo ufw limit ssh/tcp 
sudo ufw allow 26656,26660,8888/tcp
sudo ufw enable
```

### Configure Telegram alerting <a href="#id-939r" id="id-939r"></a>

Open telegram and find @BotFather

1. Create telegram bot via `@BotFather`, customize it and `get bot API token` ([how\_to](https://www.siteguarding.com/en/how-to-get-telegram-bot-api-token)).
2. Create the group: `alarm` . Customize them, add the bot in your chat and `get chats IDs` ([how\_to](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id)).
3. Open config.yml file

* change **enabled: no** to enabled: yes
* **api\_key: '5555555555:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA'** to api\_key: '\<YOUR\_BOT\_API\_KEY>'
* **channel: "-666666666"** to channel: "\<YOUR\_GROUP\_CHAT\_ID>"

```
nano $HOME/tenderduty/config.yml
```

<figure><img src="https://img4.teletype.in/files/bf/35/bf352fcc-76ed-4645-a700-ce2ae63fd792.jpeg" alt="" height="145" width="850"><figcaption><p>And save this ctrl+x y enter</p></figcaption></figure>

Restart tenderduty

```
sudo systemctl restart tenderdutyd && sudo journalctl -fu tenderdutyd
```

Congratulations! You have set up a monitoring and alert system
