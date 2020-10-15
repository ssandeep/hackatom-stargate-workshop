# "Build Your Own Module - Stargate Edition"

This repository contains support material for the online workshop "Build Your Own Module - Stargate Edition", held during the [HackAtom V](https://five.hackatom.org/) hackathon.

The accompanying slides can be found [here](TODO). If you didn't attend the workshop, you can read the slides, and follow the tutorial steps in this repository along them.

The workshop is divided into 3 steps:

- Step 1: Setup a blockchain boilerplate.
- Step 2: Create your own `x/blog` module.
- Step 3: Run the node, and interact with your module.

> If you don't want to follow the tutorial, you can run the node directly by cloning this repository, `cd` into the `step2/` directory, and running all the commands in Step 3.

### Requirements

- [`golang`](https://golang.org/doc/install) >1.15.0 installed

```bash
git clone https://github.com/tendermint/starport && cd starport && make
```

## Step 1: Setup the blockchain boilerplate.

This first step is to get the boilerplate of creating a blockchain sorted out.

```bash
# Fetch this repository.
git clone https://github.com/amaurymartiny/hackatom-stargate-workshop
cd hackatom-stargate-workshop/step1

# Build the binary inside a `build/` directory.
mkdir build
go build -o ./build ./...

# You should see a `step1d` binary in the `build/` folder.
ls ./build
```

> Note: For the sake of this tutorial, the app in the `step1/` folder has been renamed ~`blog`~ -> `step1`

## Step 2: Create your own `x/blog` module.

During this step, we are going to alter the `x/blog` module to add our own logic.

```bash
# Add a new "post" type to your x/blog module, with fields "title" and "body"
# Add a new "comment" type to your x/blog module, with fields "postID" and "body"

# Generate the go files for these proto definitions.
./scripts/ protocgen

# Add some basic validation of the Msgs, inside `x/blog/types/MsgCreate{Post,Comment}`.
```

At the end of this step, you should have a folder content similar to the `step2/` folder.

> Note: For the sake of this tutorial, the app in the `step2/` folder has been renamed ~`blog`~ -> `step2`

## Step 3: Run the chain, and interact with your module.

There is no code change during this step, we will run the node we just created, and interact with it.

```bash
# Build the app.
go build -o ./build ./...

# Initialize configuration files and genesis file
# moniker is the name of your node
step2d init amaury --chain-id blogchain

# Copy the `Address` output here and save it for later use
# [optional] add "--ledger" at the end to use a Ledger Nano S
step2d keys add amaury --keyring-backend test

# Copy the `Address` output here and save it for later use
step2d keys add alice  --keyring-backend test

# Add both accounts, with coins to the genesis file
step2d add-genesis-account $(step2d keys show amaury -a --keyring-backend test) 1000token,100000000stake
step2d add-genesis-account $(step2d keys show alice -a --keyring-backend test) 1000token,100000000stake

# Generate the gentx
step2d gentx amaury --keyring-backend test --chain-id blogchain

# After you have generated a genesis transaction, you will have to input the genTx into the genesis file, so that your blog chain is aware of the validators. To do so, run:
step2d collect-gentxs

# Make sure your genesis file is correct.
step2d validate-genesis

# You can now start nsd by calling nsd start. You will see logs begin streaming that represent blocks being produced, this will take a couple of seconds.
step2d start
```
