# Installation

### Getting Involved

Becoming a part of Neutaro is easy. You can participate by holding tokens and delegating them, running a node, or becoming a validator.

### Delegating Tokens

Delegating your tokens to a validator not only increases their staked tokens and voting power but also earns you a share of their rewards based on your delegated amount. For instance, if you want to stake 100 NTMPI tokens, you’d use the command: “1,000,000 uneutaro is 1 NTMPI.” So, if you delegate 100,000,000 uneutaro, you’re staking 100 NTMPI tokens. Use the following example command to delegate:

```bash
Neutaro tx staking delegate ValidatorAddress 100000000uneutaro --from YOURWALLET --chain-id Neutaro-1
```

**Now, here is a humble request: If you feel inclined to support me, you can delegate to my instance from** [**this link**](https://nms1.neutaro.tech/Neutaro/staking/neutarovaloper1hux6dz87n740hkc5tjm4fsxg9tyzpcqlcka03h)**. Your support is entirely voluntary, and any contributions will be greatly appreciated. I’m not a content creator making money; rather, I’m dedicating time and effort to support the project and the community.**

### Setting Up Your Node

Running a node involves executing the blockchain’s binary code. Follow these steps to set up a Validator that operates as a service on Linux.

### Preparation

We recommend using Ubuntu 22.04.03 with at least 4 cores, 8GB RAM, and 100GB of free storage. While the storage requirements may increase over time, the suggested pruning and the current state of the chain are manageable and should suffice for the next few months. However, it’s essential to understand that running a validator comes with significant responsibility, especially if you’re staking or if others delegate to your validator. Therefore, carefully consider how you plan to host your node.

Before embarking on the setup process, make sure your system is up to date. Execute the command below. All remaining commands will be run using root. For the tech-savvy individuals, I acknowledge that this might not be the best practice. However, it’s designed to assist those with limited understanding, like me when initially setting up a validator. This guide aims to support community members who may need a little extra help.

```
sudo apt update && sudo apt upgrade -y && sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

### Installing Go v1.20.4

This step is essential for setting up the Go programming environment, which is required for building and running Neutaro’s software components. Go is a widely used programming language, and specific versions may be recommended or required for compatibility with the Neutaro platform.

```
ver="1.20.4"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Make sure version 1.20.4 is installed

`go version`

### Installing the Neutaro Binary

Installing the Neutaro Binary This process is crucial for obtaining the Neutaro software and preparing it for execution. Once the build process is complete, you’ll have a locally built Neutaro binary that can be configured and run on your system

`cd $HOME`\
`git clone https://github.com/Neutaro/Neutaro`\
`cd Neutaro/cmd/Neutaro/`\
`go build`

### Installing cosmovisor

Installing cosmovisor involves setting up the cosmovisor tool, which is a process manager for Cosmos SDK-based chains, including Neutaro This process sets up the necessary directory structure and configurations for cosmovisor, allowing it to manage the Neutaro binary efficiently. The symbolic links and directory structure are essential for cosmovisor to handle upgrades and manage different versions of the Neutaro binary.

```
mkdir -p $HOME/.Neutaro/cosmovisor/genesis/bin
mv $HOME/Neutaro/cmd/Neutaro/Neutaro $HOME/.Neutaro/cosmovisor/genesis/bin/
sudo ln -s $HOME/.Neutaro/cosmovisor/genesis $HOME/.Neutaro/cosmovisor/current
sudo ln -s $HOME/.Neutaro/cosmovisor/current/bin/Neutaro /usr/local/bin/Neutaro
cd  $HOME/Neutaro/
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
```

### Now we configure the node.

Make sure to substitute ‘YOURMONIKER’ with your actual moniker for inspiration. Check out your validator profile here: [https://nms1.neutaro.tech/Neutaro/staking](https://nms1.neutaro.tech/Neutaro/staking). This is your validator – something catchy, perhaps?

`Neutaro init $TEST –chain-id Neutaro-1`\
`Neutaro config chain-id Neutaro-1`\
`Neutaro config keyring-backend os`\
`curl http://154.26.153.186/genesis.json >$HOME/.Neutaro/config/genesis.json`

### Editing the app.toml file

**Please note, for this tutorial, I have kept the ‘Editing the app.toml’ file with all default settings, but I’ve provided the options here:**

You can edit the file $HOME/.Neutaro/config/app.toml to change the minimum-gas-prices ( Currently most use 0 ) and the pruning.\
minimum-gas-prices = “0uneutaro”

For pruning you could add whatever you like. These options mainly decide how much storage the Node will use. An example would be using\
pruning=”custom”\
pruning-keep-recent=”100″\
pruning-interval=”19″

### Configuring the config.toml

In the configuration step for the config.toml file, you need to edit the “seeds” parameter to specify the seed nodes for your Neutaro node. Seed nodes help in the initial discovery of the network and assist your node in connecting to other nodes.

The correct format for the “seeds” parameter is a comma-separated list of seed node addresses. In the example you provided, the seed node address is “0e24a596dc34e7063ec2938baf05d09b374709e6@109.199.106.233:26656”. If you want to add more seed nodes, you can separate them with commas.

Here’s the corrected instruction for configuring the config.toml file:

```
nano $HOME/.Neutaro/config/config.toml
```

1. Locate the line that starts with seeds = .
2. Edit the value inside the double-quotes to include the correct seed node address:
3. CTRL + 0 to write file
4. Enter to save same file name
5. CTRL + X to exit

### Downloading the snapshot

Downloading the snapshot depends on your internet speed, which will determine how long this process takes. I’m on a 600 Mbps connection with an NVMe drive, and it typically takes around 15 minutes. The snapshot file is approximately 10 GB in size, but it’s downloaded faster than syncing the blockchain. I’ll pause the video during this part of the process.

```
cd $HOME/.Neutaro/
mv data data-old
wget http://109.199.106.233/snapshotNeutaro.tar.lz4
lz4 -d snapshotNeutaro.tar.lz4
tar -xf snapshotNeutaro.tar
```

### Create Neutaro service.

**Change the user=$USER to the acutal user you going to use, in my case root**.

```
sudo tee /etc/systemd/system/Neutaro.service > /dev/null << EOF [Unit] Description=Neutaro Node Service After=network-online.target [Service] User=root ExecStart=$(which cosmovisor) run start Restart=on-failure RestartSec=10 LimitNOFILE=65535 Environment="DAEMON_HOME=$HOME/.Neutaro" Environment="DAEMON_NAME=Neutaro" Environment="UNSAFE_SKIP_BACKUP=true" [Install] WantedBy=multi-user.target EOF
```

### Enabling the Service

```
sudo systemctl daemon-reload
```

sudo systemctl enable Neutaro

### Starting the Service/ the Node

```
sudo systemctl restart Neutaro
```

### To view the service use

```
sudo journalctl -fu Neutaro -o cat
```

**use ctrl + c to exit the log**

### Check the sync status

`Neutaro status 2>&1 | jq .SyncInfo`&#x20;

### **Proceed once it’s synced**

you will be asked for your memonic on this step. You can also remove the –recover flag and create a new wallet and send funds to this new wallet from your main wallet. You can now delete the files using the below commands ro remove the old snapshots.

Once the node is running you can delete unnecessary files using

`rm -r snapshotNeutaro.tarr`

`m -r snapshotNeutaro.tar.lz4`

`rm -r data-old`

### Sending the becomming a validator transaction

once you have a funded wallet on the node send this, **but make sure to check all the parameters to see if they are fine for you!**

`Neutaro tx staking create-validator –amount=1000000uneutaro –pubkey=$(Neutaro tendermint show-validator) –moniker=$MONIKER –chain-id=Neutaro-1 –from WALLET –keyring-backend os –commission-rate=”0.10″ –commission-max-rate=”0.20″ –commission-max-change-rate=”0.01″ –min-self-delegation=”1000000″ –gas=”auto” –gas-prices=”0.0025uneutaro” –gas-adjustment=”1.5″`
