# Tichex Blockchain

This repository contains the source code of the Tichex Blockchain

# Building the application
If you want to build the `go-tichex` application in this repo to see the functionalities, **Go 1.12.1+** is required .

Add some parameters to environment is necessary if you have never used the `go mod` before.

```bash
mkdir -p $HOME/go/bin
echo "export GOPATH=$HOME/go" >> ~/.bash_profile
echo "export GOBIN=\$GOPATH/bin" >> ~/.bash_profile
echo "export PATH=\$PATH:\$GOBIN" >> ~/.bash_profile
echo "export GO111MODULE=on" >> ~/.bash_profile
source ~/.bash_profile
```

Now, you can install and run the application.

```bash
# Install the app into your $GOBIN
make install

# Now you should be able to run the following commands:
tichexd help
tichexcli help
```

## Running the live network and using the commands

To initialize configuration and a `genesis.json` file for your application and an account for the transactions, start by running:

> _*NOTE*_: In the below commands addresses are are pulled using terminal utilities. You can also just input the raw strings saved from creating keys, shown below. The commands require [`jq`](https://stedolan.github.io/jq/download/) to be installed on your machine.

> _*NOTE*_: If you have run the tutorial before, you can start from scratch with a `tichexd unsafe-reset-all` or by deleting both of the home folders `rm -rf ~/.tichex*`

> _*NOTE*_: If you have the Cosmos app for ledger and you want to use it, when you create the key with `tichexcli keys add jack` just add `--ledger` at the end. That's all you need. When you sign, `jack` will be recognized as a Ledger key and will require a device. 

```bash
# Initialize configuration files and genesis file
tichexd init --chain-id namechain

# Copy the `Address` output here and save it for later use 
# [optional] add "--ledger" at the end to use a Ledger Nano S 
tichexcli keys add jack

# Copy the `Address` output here and save it for later use
tichexcli keys add alice

# Add both accounts, with coins to the genesis file
tichexd add-genesis-account $(tichexcli keys show jack -a) 1000teur,1000tkx
tichexd add-genesis-account $(tichexcli keys show alice -a) 1000teur,1000tkx

# Configure your CLI to eliminate need for chain-id flag
tichexcli config chain-id namechain
tichexcli config output json
tichexcli config indent true
tichexcli config trust-node true
```

You can now start `tichexd` by calling `tichexd start`. You will see logs begin streaming that represent blocks being produced, this will take a couple of seconds.

Open another terminal to run commands against the network you have just created:

```bash
# First check the accounts to ensure they have funds
tichexcli query account $(tichexcli keys show jack -a) 
tichexcli query account $(tichexcli keys show alice -a) 

# Buy your first name using your coins from the genesis file
tichexcli tx nameservice buy-name jack.id 5nametoken --from jack 

# Set the value for the name you just bought
tichexcli tx nameservice set-name jack.id 8.8.8.8 --from jack 

# Try out a resolve query against the name you registered
tichexcli query nameservice resolve jack.id
# > 8.8.8.8

# Try out a whois query against the name you just registered
tichexcli query nameservice whois jack.id
# > {"value":"8.8.8.8","owner":"cosmos1l7k5tdt2qam0zecxrx78yuw447ga54dsmtpk2s","price":[{"denom":"nametoken","amount":"5"}]}

# Alice buys name from jack
tichexcli tx nameservice buy-name jack.id 10nametoken --from alice 
```

# Transactions
You can now start the first transaction

```bash
tichexcli tx send --from=$(tichexcli keys show jack -a) $(tichexcli keys show alice -a) 10teur
```

# Query
Query an account

```bash
tichexcli query account $(tichexcli keys show jack -a)
```