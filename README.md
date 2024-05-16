# Initia

![image](https://github.com/cryptonaveen/initia/assets/116494356/d0bb02f5-bb0a-4dfd-9337-a55df07ce5bf)

## Project overview

#### The Initia L1 is an interoperable network that employs MoveVM, the first integration of the Move Smart Contracting Language natively compatible with Cosmos' Inter-Blockchain Communication (IBC) protocol. Furthermore, Initia's architecture enables L2s to utilize EVM, WasmVM, or MoveVM while facilitating seamless messaging and bridging between them. Initia uses a robust economic system to align incentives throughout the entire ecosystem.

## Hardware requirement

- Memory: 16+ GB RAM
- CPU: 6 cores
- Disk: 1 TB SSD

### 1. Install required packages

```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```
### 2. Install

```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### 3. Install git

```
git clone https://github.com/initia-labs/initia
```
```
cd initia
```
```
git checkout v0.2.14
```
```
make build
```
```
mkdir -p $HOME/.initia/cosmovisor/genesis/bin
```
```
mv /root/initia/build/initiad $HOME/.initia/cosmovisor/genesis/bin/
```

### 4. Link system

```
sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
```
```
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f
```

### 5. Cosmovisor download

```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

### 6. Create a service file

```
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=initia node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.initia"
Environment="DAEMON_NAME=initiad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.initia/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```

### 7. Enable Initia service

```
sudo systemctl daemon-reload
```
```
sudo systemctl enable initiad.service
```

### 8. Node settings

```
initiad config set client chain-id initiation-1
```
```
initiad config set client node tcp://localhost:15657
```
```
initiad config set client keyring-backend test
```

### 9. Initialize the node ( replace $MONIKER to your name )

```
initiad init $MONIKER --chain-id initiation-1
```
Example : initiad init Cryptonaveen --chain-id initiation-1

### 10. Download genesis.json

```
rm ~/.initia/config/genesis.json
```
```
curl -Ls https://raw.githubusercontent.com/molla202/pokemon/main/genesis.json > $HOME/.initia/config/genesis.json
```
```
curl -Ls https://raw.githubusercontent.com/Core-Node-Team/Testnet-TR/main/Initia/addrbook.json > $HOME/.initia/config/addrbook.json
```

### 11. Port settings

```
echo "export N_PORT="15"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
sed -i.bak -e "s%:1317%:${N_PORT}317%g;
s%:8080%:${N_PORT}080%g;
s%:9090%:${N_PORT}090%g;
s%:9091%:${N_PORT}091%g;
s%:8545%:${N_PORT}545%g;
s%:8546%:${N_PORT}546%g;
s%:6065%:${N_PORT}065%g" $HOME/.initia/config/app.toml
```
```
sed -i.bak -e "s%:26658%:${N_PORT}658%g;
s%:26657%:${N_PORT}657%g;
s%:6060%:${N_PORT}060%g;
s%:26656%:${N_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${N_PORT}656\"%;
s%:26660%:${N_PORT}660%g" $HOME/.initia/config/config.toml
```

### 12. Add seeds and peers

```
PEERS="0922ef833bab6ce088cd177d92eca0559662da86@65.108.68.214:48656,ad1d9fd5ae250a8bb0f17c177c175193713706f8@185.252.232.131:26656,7d097908682ef4f4e168f2136da2612ec43da27c@85.215.181.21:26656,126fc20a625be5597ad78ec83e60779a7622ad28@81.17.101.99:53456,8591f74169c48fd4f17dda241f73cdc07527a917@5.104.82.104:26656,0d6437ca9242b5878f6c784b88e918ba12f12c08@89.58.63.240:53456,a8638b4701f2d11e9269dfd4c2ed0509bd7b12d9@194.163.191.117:39656,8ca37f169ca249129ac75bed997580920073ba33@109.199.126.253:53456,cd69bcb00a6ecc1ba2b4a3465de4d4dd3e0a3db1@65.108.231.124:51656,ff49591b3a082cf5bfd1681bd5c55f7cc5219147@194.233.95.54:26656,9fc67f4588ae642413cafd1922fe96699aed5704@81.17.102.18:53456,a3660a8b7a0d88b12506787b26952930f1774fc2@65.21.69.53:48656,8865154e79da32e3fbd8df52ddbaa233e6544a81@77.237.243.146:17956,cbba1ec1e228e01b31d22864c36fb7039088a5aa@194.163.152.41:53456,a5f9ae13b0c671c2baad13715517affb1217ff92@65.108.225.207:54656,09765efb870fb69b11568d425a13a2033a56e9ec@81.17.101.96:53456,028999a1696b45863ff84df12ebf2aebc5d40c2d@37.27.48.77:26656,5a92728d87a660c85f7561238f8ebe42d3a7e2eb@89.117.72.163:10656,12cd2a5f22782094cc90470def2fc665050a551d@62.169.20.176:53456,93df4265efb2c40c748c27c58727d098052ef21c@158.220.89.67:53456,b19f671ff5e092a06699ecdb8e7e7353ccc6c2d8@142.93.107.148:17956,c4000188a0d3e41c0cad42587db198ff3f5f4231@62.171.135.103:39656,8ebd5bbde10cb276a6e8bf6da29060cacc16579e@38.242.230.124:17956,bf3be2b12aa5a6bb3cae1a79be0c074c568e0f0a@62.169.20.203:53456,439fe02b731d7f99a62e44ac5b2e1b02353ca631@38.242.251.179:39656,69fe42000271c39753de70cf049d9af45b574b79@161.97.123.232:53456,32f59b799e6e840fb47b363ba59e45c3519b3a5f@136.243.104.103:24556,c54833816c56e1fae1d7f62522c38870dc1bceaa@213.199.50.250:53456,2df6653c1b99c7b61dd4d5f24b4af73c23b15997@185.252.232.220:26656,dc5f72018dfdaa3530a2e6f8d51af086ad536000@152.53.33.225:53956,c3895951d8b22c2338fce585e619834c994537ea@82.197.67.229:53456,3fa4956d09383ff17299b7eee95c3b63537b804c@45.13.59.110:53456,249646ca0f525656c7085b36f66b2fc1a90100e2@161.97.158.236:53456,ea3ec643308afd9948a7948dccce72347676c32f@113.161.82.243:39656,7252acf51cfacc2ac722de12cf9e50d926e5401e@185.250.37.172:26656,16fc75cd95eef7e0aa48f314a524e882d38ec114@38.242.231.117:26656,fc36ef4bf9ab6650dea1b35940194c0d37d98e3e@138.201.225.169:53456,827b4fe957cdca89854d38a7bd92f21295417bcd@86.48.5.64:17956,ee704edacfdda971b502613dfa4befd041d6445d@103.144.209.104:17956,daf3c40f001d98427bf646042c22ef2da2ab6cb8@38.242.154.107:15656,e6dea6d030a556223459b170556286e8aa9b6242@37.27.28.161:17956,fe11149f0aa7b9bd2d42cedb264b74a4ffecde72@91.151.90.47:26656,aa7a30637c6695f2a4c6738e1c70883057963830@176.40.40.203:26656,01fc7f9da2a68eb8527487e3738b20da86fdb6b9@5.161.209.191:15656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml
```

### 13. set minimum gas

```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
```

### 14. Prune subgraphs

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.initia/config/app.toml
```
```
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.initia/config/app.toml
```
```
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.initia/config/app.toml
```

### 15. Add snap

```
initiad tendermint unsafe-reset-all --home $HOME/.initia
```
```
curl -o - -L http://37.120.189.81/initia_testnet/initia_snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.initia
```

### 16. Start the node

```
sudo systemctl daemon-reload
```
```
sudo systemctl restart initiad
```

### 17. Check the log

```
sudo journalctl -u initiad.service -f --no-hostname -o cat
```

### 18. Create initia wallet and import here


Initia wallet link : https://chromewebstore.google.com/detail/initia-wallet/ffbceckpkpbcmgiaehlloocglmijnpmp

- Create wallet and backup private key

```
initiad keys add wallet --recover
```

- Paste your private key and continue node setup

```
initiad keys add $WALLET NAME
```

- Use this command for create wallet directly on Node
- create new password
- Dont forget to save your recovery details

### 19. Claim Faucet

- Faucet link here : https://faucet.testnet.initia.xyz/

### 20. Create validator

```
initiad tx mstaking create-validator \
  --amount=5000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=$MONIKER \
  --chain-id=initiation-1 \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --from=$WALLET NAME \
  --identity="" \
  --website="" \
  --details="" \
  --node=http://localhost:15657 \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.15uinit \
  -y
```

Note : Change $MONIKER TO your name Change $WALLLET_NAME to your wallet name you can add identity, you can add your twitter profile link as wabsite

### 21. Edit validator ( If you want to change validator info use this command )

```
initiad tx mstaking edit-validator \
--moniker "$MONIKER" \
--from $WALLET NAME \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

### 22. Delegate tokens to your validator

```
initiad tx mstaking delegate $(initiad keys show $WALLET NAME --bech val -a)  miktar000000uinit --from wallet --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:15657 -y
```
Note : NOTE: Type the name of the wallet in the $WALLET NAME section

### 23. Unjail your node

```
initiad tx slashing unjail --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:15657 -y
```

### 24. Withdraw rewards

```
initiad tx slashing unjail --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:15657 -y
```

### 25. Vote governance

```
initiad tx gov vote 75 yes --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:15657 -y
```

✅ Your setup done, stay tuned for more update
