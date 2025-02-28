![Coyote bot logo](https://pbs.twimg.com/profile_images/1437957467225268226/a_qfpwtb_400x400.jpg "Logo Coyote bot logo")
# BSC Node Installer Tutorial

#### created by cedrik31tlse#8672 (Discord) / cedrik31tlse (Telegram)

Tutorial to create a node on Binance Smart Chain network (BSC) to use with Coyote Chainsniper Linux Edition.

Link to official server of Coyote bots : https://discord.gg/fP3Z4tY92j

This tutorial helps to create a node on BSC network from a fresh install of Ubuntu 20.04.

0. HETZNER put disk in RAID 0 

```
- Run your server in Linux Rescue mode
  - Go in your server panel on hetzner website
  - Go in Rescue tab. Activate it and save the new password
  - Go in Reset tab and do a Automatic reset
  - Run in SSH with the new root password
  
  Type :
  installimage
  
  Choose Ubuntu 2004 minimal
  then you will be in setup file

  Then change SWRAIDLEVEL to 0
  
  Then press F2 to save
  Then press F10 2 times to exit.
  
  At the end of the reformat, enter "reboot". 
  The server will reboot and you can start at step 1.
```

1. Update all packages

```
apt update && apt upgrade -y
```

2. Create geth user and download geth BSC

```
useradd -m geth
cd /home/geth
wget https://github.com/binance-chain/bsc/releases/download/v1.1.7/geth_linux
chmod +x geth_linux
```

3. Edit start.sh command line

```
nano start.sh
```

4. Paste content in the empty file

```
./geth_linux --config ./config.toml --datadir ./mainnet --diffsync --cache 48000 --rpc.allow-unprotected-txs --txlookuplimit 0 --ws --ws.addr 0.0.0.0 --ws.origins '*' --ws.api eth,net,web3,txpool,debug
```

5. Make start.sh file executable

```
chmod +x start.sh
```

6. Install required packages

```
apt install unzip aria2 -y
```

7. Download all data

```
wget https://github.com/binance-chain/bsc/releases/download/v1.1.7/mainnet.zip
unzip mainnet.zip
mv mainnet/genesis.json genesis.json
mv mainnet/config.toml config.toml
```

8. Change URLs by the last pruned snapshots available you can find here : https://github.com/bnb-chain/bsc-snapshots

```
aria2c -o geth.tar.lz4 -x 4 -s 12 "https://tf-dex-prod-public-snapshot-site1.s3-accelerate.amazonaws.com/geth-20220214.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=2tfJQYSr7v4rXur%2BMyg77pV%2F1pg%3D&Expires=1647467962" "https://tf-dex-prod-public-snapshot.s3-accelerate.amazonaws.com/geth-20220214.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=e04Fa%2BEfGooSyvs%2FsBcqNW7hdPo%3D&Expires=1647467962" "https://tf-dex-prod-public-snapshot-site3.s3-accelerate.amazonaws.com/geth-20220214.tar.lz4?AWSAccessKeyId=AKIAYINE6SBQPUZDDRRO&Signature=ef1JljCd7t6jNh9lEV7%2FoowtUT4%3D&Expires=1647467963"
```

9. Extract snapshot

```
tar -I lz4 -xvf geth.tar.lz4
```

10. Move to datadir folder

```
mv server/data-seed/* mainnet/
```

11. Create service to start the node

```
nano /lib/systemd/system/geth.service
```

12. Copy paste this content

```
[Unit]
Description=BSC Full Node
[Service]
User=geth
Type=simple
WorkingDirectory=/home/geth
ExecStart=/bin/bash /home/geth/start.sh
Restart=on-failure
RestartSec=5
[Install]
WantedBy=default.target
```

13. Chown all files to geth user

```
chown -R geth.geth /home/geth/*
```

14. Start node

```
systemctl enable geth
systemctl start geth
```

15. Configure Chainsniper config.json file to use the node (if Chainsniper is installed on same server) :

```
Http node  : http://127.0.0.1:8545
WS node : ws://127.0.0.1:8546
```
