![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/efd19714-2463-4cbb-8a3c-0e469e92d673)

## Project Overview:
[0G Labs](https://0g.ai/) is a modular artificial intelligence blockchain with a scalable programmable data availability (DA) layer adapted for artificial intelligence dapps. Its modular technology enables seamless communication between chains, ensuring security, eliminating fragmentation, and maximizing connectivity. The project appeared on the radar until about 2 weeks ago after a $35M investment from Tier-1 funds, and today 0G are launching a paid testnet. At the time of writing this guide there are about ~300 nodes installed, so I recommend you don't hesitate and start installing. Node will be paid, the moderators of the project actively write about it in Discord. Before installing the node you need to pass the quests on Galxe. Let's take a detailed look at the investment, as well as the requirements for the server:

**Invested:$35,000,000 (Hack VC, Delphi Digital, Animoca Brands, OKX and others)**

**Server Specifications(recommended):**

**CPU: 4 cores;**

**RAM: 8 GB;**

**Storage: 500 GB SSD NVMe;**

**OS: Ubuntu 20.04**

### Official project resources:

- **Website:** [go to](https://0g.ai/)
- **Discord:** [go](https://discord.com/invite/0glabs)
- **Twitter:** [go](https://twitter.com/0G_labs)
- **Telegram:** [go to](https://t.me/web3_0glabs) 
- **Explorer with nodes:** [go](https://dashboard.nodebrand.xyz/0g-chain)
- **Official Node Installation Guide:** [go to](https://github.com/trusted-point/0g-tools)

## Instructions for installing the node:

**Install node using Bash script:**

**1. Update the server packages:**

```
sudo apt update && sudo apt upgrade -y
```

**2. Install additional software:** 

```
sudo apt install curl git git wget hget htop tmux build-essential jq make lz4 gcc unzip -y
```

**3. Install the script from Nodes Guru:** 

```
wget -q -O 0g.sh https://api.nodes.guru/0g.sh && sudo chmod +x 0g.sh && ./0g.sh && source $HOME/.bash_profile
```

**The script will ask you to enter the name of your validator. After entering the name, it will start installing the necessary software for the node to work. If you see the same as on the screenshot below, it means that your node has been successfully installed and you can proceed to the next step:**

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/831ba79a-b5e4-44e6-9abd-84e0517c8182)

**4. Install Snapshot. Execute the commands one by one:**

```
curl -Ls https://snapshots.liveraven.net/snapshots/testnet/zero-gravity/addrbook.json > $HOME/.0gchain/config/addrbook.json
```

```
PEERS=$(curl -s --max-time 3 --retry 2 --retry-connrefused "https://snapshots.liveraven.net/snapshots/testnet/zero-gravity/peers.txt")
if [ -z "$PEERS" ]; then
    echo "No peers were retrieved from the URL."
else
    echo -e "\nPEERS: "$PEERS""
    sed -i "s/^persistent_peers *=.*/persistent_peers = "$PEERS"/" "$HOME/.0gchain/config/config.toml".
    echo -e "\nConfiguration file updated successfully.\n"
fi
```

```
sudo systemctl stop 0g
```

```
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
```

```
rm -rf $HOME/.0gchain/data
```

```
curl -L http://snapshots.liveraven.net/snapshots/testnet/zero-gravity/zgtendermint_16600-1_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.0gchain
```

```
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
```

```
sudo systemctl restart 0g
```

```
0gchaind status | jq .sync_info
```

**The node must have a status of "catching_up: false" to proceed to the next step. If the node has the status **"catching_up: true "** - WAIT FOR THE NODE TO SYNCHRONIZE AND THE STATUS CHANGES TO "false".**

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/e2c48e67-3eb7-4db9-852c-d4172f66dda1)

**5. Create a wallet. To do this, execute the command:**

```
0gchaind keys add wallet --eth
```

**The server will ask for a password that will protect the wallet.**

**Don't forget to save the seed phrase to a safe place**

**You can import your past wallet if you put noda before the update:**

```
0gchaind keys add wallet --recover --eth
```

**Enter your wallet password**

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/963439e5-e129-428b-82f8-2ca083d225c9)

**To get a wallet address that starts with 0x, you can first run the command below to get the private key of your key. Then import the returned private key into the Metamask wallet to get the wallet address.**

```
0gchaind keys unsafe-export-eth-key wallet
```

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/f4d89462-d725-4d7f-894f-eec0dbf4ea2b)


**Go to [faucet](https://faucet.0g.ai/), request tokens. After checking the wallet balance:**

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/2147977d-5b95-49be-84a6-8498dc857f92)

```
0gchaind q bank balances $(0gchaind keys show wallet -a)
```

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/eb209ee1-1b6c-42a5-be30-29ab3270e00d)

## Crane gives you 10000000000ua0gi. In order to create a node you need 20000000000ua0gi. To make a node active you need at least 10000000ua0gi (10 times more)!

** Let's check the node synchronization status again. Let's make sure that "catching_up : false":**

```
0gchaind status | jq .sync_info
```

**6. Let's create a validator. Execute the command:**

```
0gchaind tx staking create-validator \.
--amount=20000000000ua0gi \
--pubkey=$(0gchaind tendermint show-validator) \.
--moniker="$VALIDATOR" \
--chain-id=zgtendermint_16600-1 \
--commission-rate=0.10.
--commission-max-rate=0.20.
--commission-max-change-rate=0.01 \
--min-self-delegation=1.
--from=wallet.
--gas-prices=0.0025ua0gi.
--gas-adjustment=1.5.
--gas=300,000
-y
```

**7. Copy valoperaddress:**

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/97041f5b-4754-4299-8097-498c58a1f62f)

**And delegate tokens to yourself using the command:**

```
0gchaind tx staking delegate <validator address> --from wallet <amount>ua0gi --gas=auto --gas-adjustment=1.4 -y

> Replace <validator address> with the address of the validator to which you want to delegate tokens;
> Replace <amount> with the number of tokens you want to delegate.
```

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/8c80511d-7880-4b6b-988b-ac104c963704)

**8. Let's check the logs:**

```
sudo journalctl -u 0g -f
```

![image](https://github.com/Mozgiii9/0GLabsSetupTheNode/assets/74683169/16bd60f4-f70f-4c1a-8d2d-5f4d6b3ffb05)

**Delete the node:**

```
sudo systemctl stop 0g
```

```
sudo systemctl disable 0g
```

```
sudo rm -rf $(which 0gchaind) $HOME/.0gchain
```
