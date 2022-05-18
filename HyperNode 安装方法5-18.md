This page contains steps and technical advice to help you set up the HyperNode

## HyperNode hardware requirement

The following are the minimum configuration requirements for running nodes
	64-bit Linux distribution
	CPU with 6 cores
	16GB RAM
	Connection to Internet
	Public IP
	2TB*2 SSD


## HyperNode installation
### Install software
Download the latest version of HyperNode:

https://github.com/BlockPILabs/testnets/tree/main/HyperNode

```
# Download the source files and configuration file
cd ~
wget https://raw.githubusercontent.com/BlockPILabs/testnets/main/HyperNode/HyperNode20220516.tar.gz
tar -zxf HyperNode20220516.tar.gz
mv HyperNode20220516 HyperNode
mv ./HyperNode/HyperNode /usr/local/bin/
chmod +x /usr/local/bin/HyperNode
```


### Initialization
Go to the HyperNode directory 
```
cd ~/HyperNode
```

Create a local account
Initialization will create a Public Key in the directory where the current system resides, 
which is used for statistics encryption
It will generate a directory named 'keystore' and a private key in this directory. 
Then the address is generated and exported.
```
HyperNode init
[root.go:97] [INFO] [05-12|02:18:57.924] node init                                module=app
Your new account is locked with a password. Please give a password. Do not forget this password.
Password: 
Repeat password: 
//Input twice，and the keystore file is in ./keystore directory
```

Create passwd.txt file and set {passwd} as the password input above
```
echo '{passwd}' >> ~/HyperNode/passwd.txt
```

### Configure config.yml
The configurations below need to be set up for individuals. Others stay default.
```
# provider: set provider id, input erc20 address. You can use this account to log in to the browser to view node status and revenue.
provider: "0x***********"

# Chain configurations are for the target blockchain
chain:
  name: "klaytn"
  network: "8217"

# Node Node configuration is where to set up the back-end of the target blockchain network (for both rpc and archive node).
Node:
  addr: "http://127.0.0.1:8551"

# beacon beacon. Connect to the nearest beacon node to get the gateways list information and improve HyperNode performance. 
# Overwrite {beacon_ip}
beacon:
  [ "{beacon_ip}:9090" ]
```

There are several choices for {beacon_ip}. Choose one with lowest ping latency.
```
141.95.205.50
15.235.145.122
15.235.11.42
```


## Run HyperNode
### ### Run HyperNode with CLI
Parameters:

--datadir dir  # The main directory of the HyperNode

--keystore_path keystore # The path of the keystore files

--password_path ./passwd.txt # The passwd.txt file created previously


Startup command format:

```
cd ~/HyperNode
HyperNode --datadir ~/HyperNode --keystore_path ~/HyperNode/keystore/key --password_path passwd.txt
```

Command demo:

```
cd ~/HyperNode
HyperNode --datadir ~/HyperNode --keystore_path ~/HyperNode/keystore/UTC--2022-03-14T10-28-00.437250400Z--2fe38d190b8813155db481d1a42d0d328114b37d --password_path ~/HyperNode/passwd.txt
```

### Run HyperNode from systemd file
systemd file demo

You can either input the auto-generated keyname after '--keystore_path keystore/' in the demo or rename the key file `UTC--2022-03-****` to `key` in the keystore directory

`vim /etc/systemd/system/HyperNode.service`

```
[Unit]
Description=daemon
After=network-online.target

[Service]
User=root
WorkingDirectory=/root/HyperNode
ExecStart=/usr/local/bin/HyperNode --datadir /root/HyperNode --keystore_path keystore/key --password_path passwd.txt
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

Set the server to run HyperNode at startup 

`systemctl enable HyperNode`

Start the program

`systemctl start HyperNode`


Stop the program

`systemctl stop HyperNode`


Check status of the program

`systemctl status HyperNode`



### Read log

Read the program log

`tail log/HyperNode.log`

Through journalctl

`journalctl -f -u HyperNode`
