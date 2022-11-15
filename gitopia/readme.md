<p style="font-size:14px" align="center">
<a href="https://upasian.org" target="_blank">Visit Our Website</a>
</p><hr>

<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/108946833/201699963-0398094b-330b-465b-b3ad-252ae93bc5ad.png">
</p>

# setup node gitopia for testnet — gitopia-janus-testnet-2

Official Documentation:
>- [Instruction setup validator](https://docs.gitopia.com/installation/index.html)

Explorer:
>- https://gitopia.explorers.guru/

## Hardware

|  Komponen |  Minimum requirements |
| ------------ | ------------ |
| CPU  | 4x CPUs; the faster clock speed the better |
| RAM | 8GB RAM |
| Storage  | 100GB of storage (SSD or NVME) |

## Software

|Komponen | Minimum requirements |
| ------------ | ------------ |
| OS | Ubuntu 20.04 |

## Setup fullnode Gitopia 
You can set up your fullnode noise in minutes by using the automated script below. It will ask you to enter your validator node name!
```
wget -O gitopia.sh https://raw.githubusercontent.com/Megumiiiiii/Gitopia/main/gitopia.sh && chmod +x gitopia.sh && ./gitopia.sh
```

## Post installation

When the installation is complete, please load the variables into the system
```
source $HOME/.bash_profile
```

Next you have to make sure your validator is syncing blocks. You can use command below to check synchronization status
```
gitopiad status 2>&1 | jq .SyncInfo
```

### (OPSIONAL) Sinkronisasi Status by [kjnodes](https://services.kjnodes.com/home/testnet/gitopia/state-sync)
You can declare your node sync in minutes by running below command
```
sudo systemctl stop gitopiad
cp $HOME/.gitopia/data/priv_validator_state.json $HOME/.gitopia/priv_validator_state.json.backup
gitopiad tendermint unsafe-reset-all --home $HOME/.gitopia

STATE_SYNC_RPC=https://gitopia-testnet.rpc.kjnodes.com:443
STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@gitopia-testnet.rpc.kjnodes.com:41656
LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
  $HOME/.gitopia/config/config.toml
sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
  $HOME/.gitopia/config/config.toml
mv $HOME/.gitopia/priv_validator_state.json.backup $HOME/.gitopia/data/priv_validator_state.json

sudo systemctl start gitopiad && journalctl -u gitopiad -f --no-hostname -o cat
```

### Buat dompet
To create a new wallet you can use the command below. Don't forget to save the mnemonic
```
gitopiad keys add $WALLET
```

(OPSIONAL) To recover your wallet using the seed phrase
```
gitopiad keys add $WALLET --recover
```

To get the current wallet list
```
gitopiad keys list
```
Export Privkey
```
gitopiad keys export namewallet --unarmored-hex --unsafe
```

### Simpan info dompet
Tambahkan dompet dan alamat valoper ke dalam variabel
```
GITOPIA_WALLET_ADDRESS=$(gitopiad keys show $WALLET -a)
GITOPIA_VALOPER_ADDRESS=$(gitopiad keys show $WALLET --bech val -a)
echo 'export GITOPIA_WALLET_ADDRESS='${GITOPIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export GITOPIA_VALOPER_ADDRESS='${GITOPIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
To create a validator first, you need to fund your wallet with a testnet token.
>- https://gitopia.com/home

### Create validator
Before creating a validator, make sure you have at least 1 tlore (1 tlore equals 1000000 tlore) and your nodes are synced

To check your wallet balance:
```
gitopiad query bank balances $GITOPIA_WALLET_ADDRESS
```
> If your wallet doesn't show any balance, chances are your nodes are still syncing. Please wait for it to finish for synchronization then continue

To create your validator run command below:
```
gitopiad tx staking create-validator \
  --amount 1000000utlore \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(gitopiad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $GITOPIA_CHAIN_ID
```

## Security
To protect your keys, make sure you follow basic security rules

### Siapkan kunci ssh untuk otentikasi
A good tutorial on how to set up ssh keys for authentication to your server can be found [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20 -04)

### Keamanan Firewall Dasar
Start by checking the ufw status.
```
sudo ufw status
```

Set default to allow outgoing connections, deny all incoming except ssh and 26656. Limit SSH login attempts
```
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh/tcp
sudo ufw limit ssh/tcp
sudo ufw allow ${GITOPIA_PORT}656,${GITOPIA_PORT}660/tcp
sudo ufw enable
```

## Monitoring
To monitor and get alerts about your validator's health status, you can use my guide on [Setting up monitoring and alerts for noise validators](https://github.com/nodesxploit/testnet/blob/main/nois/monitoring/README.md )

### Check your validator key
```
gitopiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### Get a list of validators
```
gitopiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Get list of currently connected peers by id
```
curl -sS http://localhost:${GITOPIA_PORT}657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

## Commands useful
### Management Service
Check log
```
journalctl -fu gitopiad -o cat
```

Start Service
```
sudo systemctl start gitopiad
```

Stop Service
```
sudo systemctl stop gitopiad
```

Restart Service
```
sudo systemctl restart gitopiad
```

### Information Node
Synchronization Node
```
gitopiad status 2>&1 | jq .SyncInfo
```

Info validator
```
gitopiad status 2>&1 | jq .ValidatorInfo
```

Node information
```
gitopiad status 2>&1 | jq .NodeInfo
```

show node id
```
gitopiad tendermint show-node-id
```

### Operation wallet
register dompet
```
gitopiad keys list
```

recover wallet
```
gitopiad keys add $WALLET --recover
```

Delete wallet
```
gitopiad keys delete $WALLET
```

Get wallet token
```
gitopiad query bank balances $GITOPIA_WALLET_ADDRESS
```

Transfer Token
```
gitopiad tx bank send $GITOPIA_WALLET_ADDRESS <TO_GITOPIA_WALLET_ADDRESS> 10000000utlore
```

### Voting
```
gitopiad tx gov vote 1 yes --from $WALLET --chain-id=$GITOPIA_CHAIN_ID
```

### Staking, Delegate, and Reward
Delegate token
```
gitopiad tx staking delegate $GITOPIA_VALOPER_ADDRESS 10000000utlore --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

Delegate back stake from validator to another validator 
```
gitopiad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000utlore --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

withdraw all rewards
```
gitopiad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

withdraw commision
```
gitopiad tx distribution withdraw-rewards $GITOPIA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$GITOPIA_CHAIN_ID
```

### validator Management
Edit validator
```
gitopiad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$GITOPIA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
gitopiad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$GITOPIA_CHAIN_ID \
  --gas=auto
```

### Delete Node
this command will delete node from your server!
```
sudo systemctl stop gitopiad
sudo systemctl disable gitopiad
sudo rm /etc/systemd/system/gitopia* -rf
sudo rm $(which gitopiad) -rf
sudo rm $HOME/.gitopia* -rf
sudo rm $HOME/gitopia -rf
sed -i '/GITOPIA_/d' ~/.bash_profile
```
