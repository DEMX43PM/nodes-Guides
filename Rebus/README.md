# Guide Rebus 
![reb (1)](https://user-images.githubusercontent.com/44331529/182308882-ede61e24-d692-43b9-b49f-ddb283d0711e.png)
![reb (2)](https://user-images.githubusercontent.com/44331529/182308884-d53eae18-c1d0-4a5a-8d75-8518eb30eb73.png)


[Website](https://www.rebuschain.com/)
=
[EXPLORER 1](https://mainnet.manticore.team/rebus/staking) \
[EXPLORER 2](https://rebus.explorers.guru/validators)
=
- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Mainnet   |   8| 16GB | 160GB    |

# 1) Auto_install script
```bash
wget -O rebuss https://raw.githubusercontent.com/obajay/nodes-Guides/main/Rebus/rebuss && chmod +x rebuss && ./rebuss
```
# 2) Manual installation

### Preparing the server

    sudo apt update && sudo apt upgrade -y && \
    sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y

## GO 18.3 (one command)
    ver="1.18.3" && \
    cd $HOME && \
    wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
    sudo rm -rf /usr/local/go && \
    sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
    rm "go$ver.linux-amd64.tar.gz" && \
    echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
    source $HOME/.bash_profile && \
    go version

# Binary   19.10.22
```bash
cd $HOME
git clone https://github.com/rebuschain/rebus.core.git 
cd rebus.core
git checkout v0.2.3
make install

```
`rebusd version --long | head`
+ version: 0.2.3
+ commit: ee105dac0b3f481600ba30fb75f6628f9309e91e

## Initialisation
```bash
rebusd init STAVRguide --chain-id reb_1111-1
```
## Add wallet
```console
rebusd keys add <walletName>
rebusd keys add <walletName> --recover
```
# Genesis
```bash
curl https://raw.githubusercontent.com/rebuschain/rebus.mainnet/master/reb_1111-1/genesis.zip > ~/.rebusd/config/genesis.zip
cd $HOME/.rebusd/config/ 
unzip genesis.zip && rm -rf genesis.zip
```

`sha256sum $HOME/.rebusd/config/genesis.json`
- 10cc853d7ccc8ebc67155ee4ffc1bb32caac3f05873df79e866524898b3f20eb

### Pruning (optional)
    pruning="custom" && \
    pruning_keep_recent="100" && \
    pruning_keep_every="0" && \
    pruning_interval="10" && \
    sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.rebusd/config/app.toml && \
    sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.rebusd/config/app.toml && \
    sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.rebusd/config/app.toml && \
    sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.rebusd/config/app.toml

### Indexer (optional)
    indexer="null" && \
    sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.rebusd/config/config.toml

### Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```console
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0arebus\"/" ~/.rebusd/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.rebusd/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.rebusd/config/config.toml

peers="9be95ccf0086b96b707115b7707ea73420b11b6b@159.223.152.53:26656,3a3e7123b9ae814b8d8517b6635d21b9ae45bf25@195.3.222.148:26656,d28516746773bfaeca4efa5537c0bf5990b8828e@65.21.229.33:27656,7197d316935ca7b7ac36da7d4a3a6df16cd286a7@93.170.72.118:26656,87102b5dd22c1d17f97197c078f23726ae3c6214@tinyo.fi:26656,b9b70240be5b970a4939ddd5cc9a45ff4be6c292@198.244.167.164:26656,9289288c444b781a18f13c69da42b99424442cbb@65.108.44.149:21656,4ef77b2a17e71d2535b3c8ec11830708fc299705@209.222.98.90:26656,c301abd7abe536d7791eb139599a68ecaab4ffbc@65.108.6.121:26656,dda7abe32cc84a722cf6b1d2ee3b61ebe7ad71df@135.181.212.183:21656,07b84cf4b47a2e5ad251267716fe05bcf30330cd@65.21.170.3:29656,b4941d0929595b9f83d190559e1d7126fec91cb0@172.96.161.94:26656,ce8b36056ace01414fa7a92e43eaa5bfd8705dd4@138.201.141.76:26656,89ded0a3987d22e46b756fead439e2a4d25f23cb@185.144.99.30:26656,62859ddc0485dbb37b7442135b8d468c4b2222b3@65.108.132.239:36656,eeca453e3a1cf670c78e2255b8f0bd5a9443c30b@65.108.225.71:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.rebusd/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.rebusd/config/config.toml

sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 100/g' $HOME/.rebusd/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 100/g' $HOME/.rebusd/config/config.toml
```

## Download addrbook
```console
wget -O $HOME/.rebusd/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Rebus/addrbook.json"
```
# StateSync
```bash
sudo systemctl stop rebusd
peers="9fe60255ffc5176f419d9913ca032d4b5dc413b1@141.95.124.151:20106"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.rebusd/config/config.toml
SNAP_RPC="http://141.95.124.151:20107"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.rebusd/config/config.toml
wget -O $HOME/.rebusd/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Rebus/addrbook.json"
rebusd tendermint unsafe-reset-all --home ~/.rebusd --keep-addr-book
sudo systemctl restart rebusd && journalctl -u rebusd -f -o cat
```

# SnapShot 16.09.22 (0.3 GB) block height --> 118813
```bash
# install the node as standard, but do not launch. Then we delete the .data directory and create an empty directory
sudo systemctl stop rebusd
rm -rf $HOME/.rebusd/data/
mkdir $HOME/.rebusd/data/

# download archive
cd $HOME
wget http://51.195.189.48:7011/rebusdata.tar.gz

# unpack the archive
tar -C $HOME/ -zxvf rebusdata.tar.gz --strip-components 1

# after unpacking, run the node
# don't forget to delete the archive to save space
cd $HOME
rm rebusdata.tar.gz
# start the node
sudo systemctl restart rebusd && journalctl -u rebusd -f -o cat
```

# Create a service file
```console
sudo tee /etc/systemd/system/rebusd.service > /dev/null <<EOF
[Unit]
Description=rebus
After=network-online.target

[Service]
User=$USER
ExecStart=$(which rebusd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

# Start node (one command)
```console
sudo systemctl daemon-reload && sudo systemctl enable rebusd
sudo systemctl restart rebusd && sudo journalctl -u rebusd -f -o cat
```

## Create validator
```
rebusd tx staking create-validator \
--amount 1000000arebus \
--from <walletname> \
--commission-max-change-rate "0.10" \
--commission-max-rate "0.20" \
--commission-rate "0.10" \
--min-self-delegation "1" \
--identity="" \
--details="" \
--website="" \
--pubkey $(rebusd tendermint show-validator) \
--moniker STAVRguide \
--chain-id reb_1111-1 \
--gas 300000 \
-y
```

### Delete node (one command)
```
sudo systemctl stop rebusd && \
sudo systemctl disable rebusd && \
rm /etc/systemd/system/rebusd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .rebusd && \
rm -rf rebus.core && \
rm -rf $(which rebusd)
```
