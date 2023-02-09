# Teritori_contributions

### web3validator provides much more than security! We are actively participating in the development of the Network and Community by providing informational, technical and humanitarian support!


* [Install node](https://github.com/web3validator/Teritori_contributions#install-binary)
<!-- * [Create RPC]() -->
* [State sync](https://github.com/web3validator/Teritori_contributions#state-sync)


## Install binary

You can check new version here [Teritori releases tab](https://github.com/TERITORI/teritori-chain/releases)
```
cd $HOME
git clone https://github.com/TERITORI/teritori-chain.git -b v1.3.0
cd $HOME/teritori-chain
make install

```
### Genesis
```
wget https://teritori.web3validator.info/genesis_teritori.json -o $HOME/.teritorid/config/genesis.json

```
### Addrbook
```
wget https://teritori.web3validator.info/addrbook_teritori.json -o $HOME/.teritorid/config/addrbook.json

```
### You can download  `teritorid.service`
```
wget https://teritori.web3validator.info/teritorid.service -P /etc/systemd/system/

```
If you want to quickly catch up with the network, use this [State Sync](https://github.com/MaxMavaIll/Guide_Web_Teritori#state-sync)
```
sudo systemctl start teritorid && sudo journalctl -u teritorid -f --no-hostname -o cat
```



## State sync

<details >
  <summary><b>Pruning</b></summary>
  
  ```
  pruning = "default"
  
  pruning-keep-recent = "0"
  pruning-keep-every = "0"
  pruning-interval = "0"
  ```
  
</details>

## Public RPC 
  ```
  "https://teritori.web3validator.info:28157"
 
  ```
  
  ### We get the height and txhash -> `config.toml`
  ```
  SNAP_RPC="https://teritori.web3validator.info:28157"

  LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
  BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
  TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
  echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

  sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
  s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
  s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
  s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
  s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.teritorid/config/config.toml
  sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.teritorid/config/config.toml

  ```
  ### Reset the database 
  ```
  sudo systemctl stop teritorid && 
  teritorid tendermint unsafe-reset-all --home $HOME/.teritorid --keep-addr-book
  
  ```
  ## Restart node and check the logs
  ```
  sudo systemctl start teritorid && sudo journalctl -u teritorid -f --no-hostname -o cat
  ```


