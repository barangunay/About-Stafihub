# About-Stafihub
Stafihub.eth and StafihubValidator:Baranc
Hello. Today I will show you how to create a stafihub validator.
what is stafihub?
StaFiHub is a parallel chain developed by StaFi based on the Cosmos SDK. It has the follow.It will be an application chain serving the Cosmos Eco Staking Derivatives.The IBC cross-chain protocol will be supported. The token assets of the Cosmos ecosystem can be cross-chained to StaFiHub through the IBC protocol, and the rToken assets on the StaFiHub can all circulate in the Cosmos ecosystem through the IBC protocol.Cosmos ECO tokens can directly mint out StaFiHub-based staking derivatives using the Keplr wallet and the corresponding PoS Token.The StaFi team will build a cross-chain bridge between StaFi Chain and StaFiHub to support the exchange of rTokens between the two chains.


# System Requirements: 
```
8GB RAM
200 GB SSD
4 vCPU
```
```
sudo su
```

# go to root directory:
```
cd /root
```

# we are doing a system update:
```
#sudo apt update && sudo apt upgrade -y
```

# we are installing the library:
```
#sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils -y < "/dev/null"
```

# we are installing go:
```
cd $HOME
wget -O go1.18.2.linux-amd64.tar.gz https://go.dev/dl/go1.18.2.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.2.linux-amd64.tar.gz && rm go1.18.2.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bashrc
echo 'export GOPATH=$HOME/go' >> $HOME/.bashrc
echo 'export GO111MODULE=on' >> $HOME/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bashrc && . $HOME/.bashrc
go version
```

# We clone Stafi's github file
```
git clone --branch public-testnet-v3 https://github.com/stafihub/stafihub
```

# setup:
```
cd $HOME/stafihub && make install
```
# 
```
stafihubd init NodeName --chain-id stafihub-public-testnet-3
```

# Downloading Genesis file:
```
wget -O $HOME/.stafihub/config/genesis.json "https://raw.githubusercontent.com/stafihub/network/main/testnets/stafihub-public-testnet-3/genesis.json"
```

# We make the necessary configurations for Node:
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01ufis\"/" $HOME/.stafihub/config/app.toml
sed -i '/\[grpc\]/{:a;n;/enabled/s/false/true/;Ta};/\[api\]/{:a;n;/enable/s/false/true/;Ta;}' $HOME/.stafihub/config/app.toml
peers="5755e89abcb14b6b622fc22210b9644cddd14a0b@139.59.146.152:26656,724430a2cf42b94f5da6b24d4741c7418fefa24e@194.60.201.153:26656,fcb0d867153a719492a4a92f7b32533aa95fcc8d@147.135.254.37:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.stafihub/config/config.toml
```

# We create a service file and start our node
```
echo "[Unit]
Description=StaFiHub Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which stafihubd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/stafihubd.service
sudo mv $HOME/stafihubd.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable stafihubd
sudo systemctl restart stafihubd
```

# To create a wallet. Write your own wallet name in the walletName field:
```
stafihubd keys add walletName
```

# If you have joined before:
```
stafihubd keys add walletName --recover
```

# For Faucet after you get the wallet done: https://discord.gg/tz6USZWX

# Match command, when you enter this command, it should write false at the bottom, for example:
```
stafihubd status 2>&1 | jq .SyncInfo
```
 
```
stafihubd tx staking create-validator \
--moniker="NodeName" \
--amount=48885000ufis \
--gas auto \
--fees=5000ufis \
--pubkey=$(stafihubd tendermint show-validator) \
--chain-id=stafihub-public-testnet-3 \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--min-self-delegation=1 \
--from=WalletName \
--yes
```
