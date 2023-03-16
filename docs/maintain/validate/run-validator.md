---
id: run-validator
title: Using Packages
description: Use the package to set up your validator node on the Polygon Network.
keywords:
  - docs
  - polygon wiki
  - polygon
  - binary
  - node
  - validator
  - sentry
image: https://matic.network/banners/matic-network-16x9.png
---
import useBaseUrl from '@docusaurus/useBaseUrl';

This guide will walk you through running a Polygon validator node from packages.

For system requirements,
follow the [Validator Node System Requirements](validator-node-system-requirements.md) guide.

:::tip
Steps in this guide involve waiting for the **Heimdall** and **Bor** services to fully sync.
This process takes several days to complete. Alternatively, you can use a maintained snapshot, which will reduce the sync time to a few hours. For detailed instructions, see [<ins>Snapshot Instructions for Heimdall and Bor</ins>](/docs/operate/snapshot-instructions-heimdall-bor).

For snapshot download links, see [<ins>Polygon Chains Snapshots</ins>](https://snapshot.polygon.technology/).
:::

## Prerequisites

* Two machines — one [sentry](/maintain/glossary.md#sentry) and one [validator](/maintain/glossary.md#validator).

* Bash is installed on both the sentry and the validator machines.

* RabbitMQ installed on both the sentry and the validator machines.
  See [Downloading and Installing RabbitMQ](https://www.rabbitmq.com/download.html).

:::info

Please follow the steps on [<ins>bloXroute instructions</ins>](/maintain/validate/bloxroute.md) to connect your nodes to the bloXroute gateways.

:::

## Overview

To get to a running validator node, conduct the following in this **exact sequence of steps**:

:::caution

You will run into configuration issues if these steps are performed out of sequence.
It is important to keep in mind that a sentry node must always be set up before the validator node.

:::

1. Prepare two machines, one for the sentry node and one for the validator node.
2. Install the Heimdall and Bor binaries on the sentry and validator machines.
3. Set up the Heimdall and Bor service files on the sentry and validator machines.
4. Set up the Heimdall and Bor services on the sentry and validator machines.
5. Configure the sentry node.
6. Start the sentry node.
7. Configure the validator node.
8. Set the owner and signer keys.
9. Start the validator node.
10. Check node health with the community.

## Installing package

### Heimdall

- Install the default latest version of sentry for the Polygon Mainnet:

    ```shell
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/heimdall.sh | bash
    ```

    or install a specific version, node type (`sentry` or `validator`), and network (`mainnet` or `mumbai`). All release versions can be found on
    [Heimdall GitHub repository](https://github.com/maticnetwork/heimdall/releases).

    ```shell
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/heimdall.sh | bash -s -- <version> <network> <node_type>
    # Example:
    # curl -L https://raw.githubusercontent.com/maticnetwork/install/main/heimdall.sh | bash -s -- v0.3.0 mainnet sentry
    ```

### Bor

- Install the default latest version of sentry for Mainnet:

    ```shell
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/bor.sh | bash
    ```

    or install a specific version,  node type (`sentry` or `validator`), and network (`mainnet` or `mumbai`). All release versions could be found on
    [Bor Github repository](https://github.com/maticnetwork/bor/releases).

    ```shell
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/bor.sh | bash -s -- <version> <network> <node_type>

    # Example:
    # curl -L https://raw.githubusercontent.com/maticnetwork/install/main/bor.sh | bash -s -- v0.3.3 mainnet sentry
    ```

### Check installation

- Check Heimdall installation

    ```shell
    heimdalld version --long
    ```

- Check Bor installation

    ```shell
    bor version
    ```

:::note

Before proceeding, Bor should be installed on both the sentry and validator machines.

:::

## Configuration

In this section, we will go through steps to initialize and customize configurations nodes.

:::caution

Bor and Heimdall 0.3.0 uses standardized paths for configuration files and chain data. If you have existing
config files and chain data on your node, please skip this section and jump directly to **[Migration](#upgrade-from-02x-to-03x) section** to learn about migrating configs and data to standardized file locations.

:::

### Configure Heimdall

- Initialize Heimdall configs

```shell
# For mainnet
sudo -u heimdall heimdalld init --chain=mainnet --home /var/lib/heimdall

# For testnet
sudo -u heimdall heimdalld init --chain=mumbai --home /var/lib/heimdall
```

Open the Heimdall configuration file for editing:

```sh
vi /var/lib/heimdall/config/config.toml
```

In `config.toml`, change the following parameters:

* `moniker` — any name. Example: `moniker = "my-sentry-node"`.
* `seeds` — the seed node addresses consisting of a node ID, an IP address, and a port.

  Use the following values:

  ```toml
  seeds="f4f605d60b8ffaaf15240564e58a81103510631c@159.203.9.164:26656,4fb1bc820088764a564d4f66bba1963d47d82329@44.232.55.71:26656,2eadba4be3ce47ac8db0a3538cb923b57b41c927@35.199.4.13:26656,3b23b20017a6f348d329c102ddc0088f0a10a444@35.221.13.28:26656,25f5f65a09c56e9f1d2d90618aa70cd358aa68da@35.230.116.151:26656"
  ```

* `pex` — set the value to `true` to enable the peer exchange. Example: `pex = true`.
* `private_peer_ids` — the node ID of Heimdall set up on the validator machine.

  To get the node ID of Heimdall on the validator machine:

  1. Log in to the validator machine.
  2. Run:
     ```sh
     heimdalld tendermint show-node-id
     ```

  Example: `private_peer_ids = "0ee1de0515f577700a6a4b6ad882eff1eb15f066"`.

* `prometheus` — set the value to `true` to enable the Prometheus metrics. Example: `prometheus = true`.
* `max_open_connections` — set the value to `100`. Example: `max_open_connections = 100`.

Save the changes in `config.toml`.

### Configure Bor

In `/var/lib/bor/config.toml`, add the following:

```
[p2p]
    [p2p.discovery]
        static-nodes = ["<replace with enode://validator_machine_enodeID@validator_machine_ip:30303>"]
```

To get the node ID of Bor on the validator machine:

1. Log into the validator machine.
2. Run `bor bootnode -node-key /var/lib/bor/data/bor/nodekey -dry-run`.

Example output: `"enode://410e359736bcd3a58181cf55d54d4e0bbd6db2939c5f548426be7d18b8fd755a0ceb730fe5cf7510c6fa6f0870e388277c5f4c717af66d53c440feedffb29b4b@134.209.100.175:30303"`.

Example content of static node field in `/var/lib/bor/config.toml`:
```
[p2p]
    [p2p.discovery]
        static-nodes = ["enode://410e359736bcd3a58181cf55d54d4e0bbd6db2939c5f548426be7d18b8fd755a0ceb730fe5cf7510c6fa6f0870e388277c5f4c717af66d53c440feedffb29b4b@134.209.100.175:30303"]
```

Save the changes in `/var/lib/bor/config.toml`.

### Configuring a firewall

The sentry machine must have the following ports open to the world `0.0.0.0/0`:

* `26656`- Your Heimdall service will connect your node to other nodes Heimdall service.

* `30303`- Your Bor service will connect your node to other nodes Bor service.

* `22`- For the validator to be able to ssh from wherever they are.

## Configure service files for Bor and Heimdall

After successfully installing Bor and Heimdall through [packages](#install-with-packages-recommended), their service file could be found under `/lib/systemd/system`, and Bor's config
file could be found under `/var/lib/bor/config.toml`.
You will need to check and modify these files accordingly.

    - In the service file, set `--chain` to `mainnet` or `mumbai` accordingly

  Save the changes in `/lib/systemd/system/heimdalld.service`.

- Make sure the chain is set correctly in `/var/lib/bor/config.toml` file. Open the file with following command `sudo vi /var/lib/bor/config.toml`

    - In the config file, set `chain` to `mainnet` or `mumbai` accordingly.

    - To enable Archive mode, you can optionally enable the following flags:

      ```
      gcmode "archive"

      [jsonrpc]
        [jsonrpc.ws]
          enabled = true
          port = 8546
          corsdomain = ["*"]
      ```

  Save the changes in `/var/lib/bor/config.toml`.


## Starting the Sentry Node

You will first start the Heimdall service. Once the Heimdall service syncs, you will start the Bor service.


### Reload service files

Reloading service files to make sure all changes to service files are loaded correctly.

```sh
sudo systemctl daemon-reload
```

### Starting the Heimdall service

Start the Heimdall services:

```sh
sudo service heimdalld start
```

Check the Heimdall service logs:

```sh
journalctl -u heimdalld.service -f
```

:::note

In the logs, you may see the following errors:

* `Stopping peer for error`
* `MConnection flush failed`
* `use of closed network connection`

These logs mean that one of the nodes on the network refused a connection to your node.
Wait for your node to crawl more nodes on the network; you do not need to do anything
to address these errors.

:::

Check the Heimdalld logs:

```sh
journalctl -u heimdalld.service -f
```

Check the sync status of Heimdall:

```sh
curl localhost:26657/status
```

In the output, the `catching_up` value is:

* `true` — the Heimdall service is syncing.
* `false` — the Heimdall service is fully synced.

Wait for the Heimdall service to sync fully.

### Starting the Bor service

Once the Heimdall service syncs, start the Bor service.

Start the Bor service:

```sh
sudo service bor start
```

Check the Bor service logs:

```sh
journalctl -u bor.service -f
```

## Installing packages on the Validator Node

Follow the same [installation steps](#installing-packages) on validator node.

## Configuring the Validator Node

:::note

To complete this section, you must have an RPC endpoint of your fully synced Ethereum mainnet
node ready.

:::

:::caution

Bor and Heimdall 0.3.0 uses standardized paths for configuration files and chain data. If you have existing
config files and chain data on your node, please jump directly to **[Migration](#upgrade-from-02x-to-03x-1) section** to learn about migrating configs and data to standardized file locations.

:::

### Configure Heimdall

Log in to the remote validator machine.

Initialize heimdall configs

```shell
# For mainnet
sudo -u heimdall heimdalld init --chain=mainnet --home /var/lib/heimdall

# For testnet
sudo -u heimdall heimdalld init --chain=mumbai --home /var/lib/heimdall
```

Open the Heimdall configuration file for editing:

```sh
vi /var/lib/heimdall/config/config.toml
```

In `config.toml`, change the following:

* `moniker` — any name. Example: `moniker = "my-validator-node"`.
* `pex` — set the value to `false` to disable the peer exchange. Example: `pex = false`.
* `private_peer_ids` — comment out the value to disable it. Example: `# private_peer_ids = ""`.

  To get the node ID of Heimdall on the sentry machine:

  1. Log in to the sentry machine.
  2. Run `heimdalld tendermint show-node-id`.

Example: `persistent_peers = "sentry_machineNodeID@sentry_instance_ip:26656"`

* `prometheus` — set the value to `true` to enable the Prometheus metrics. Example: `prometheus = true`.

Save the changes in `config.toml`.

Open for editing `vi /var/lib/heimdall/config/heimdall-config.toml`.

In `heimdall-config.toml`, change the following:

* `eth_rpc_url` — an RPC endpoint for a fully synced Ethereum mainnet node or testnet node,
  i.e Infura. `eth_rpc_url =<insert Infura or any full node RPC URL to Ethereum>`

Example: `eth_rpc_url = "https://nd-123-456-789.p2pify.com/60f2a23810ba11c827d3da642802412a"`

Save the changes in `heimdall-config.toml`.


### Configuring Bor


In `/var/lib/bor/config.toml`, add the following:

```
[p2p]
    [p2p.discovery]
        static-nodes = ["<replace with enode://validator_machine_enodeID@validator_machine_ip:30303>"]
```

To get the node ID of Bor on the sentry machine:

1. Log into the sentry machine.
2. Run `bor bootnode -node-key /var/lib/bor/data/bor/nodekey -dry-run`.

Example output: `"enode://410e359736bcd3a58181cf55d54d4e0bbd6db2939c5f548426be7d18b8fd755a0ceb730fe5cf7510c6fa6f0870e388277c5f4c717af66d53c440feedffb29b4b@134.209.100.175:30303"`.

Example content of static node field in `/var/lib/bor/config.toml`:
```
[p2p]
    [p2p.discovery]
        static-nodes = ["enode://410e359736bcd3a58181cf55d54d4e0bbd6db2939c5f548426be7d18b8fd755a0ceb730fe5cf7510c6fa6f0870e388277c5f4c717af66d53c440feedffb29b4b@134.209.100.175:30303"]
```

Save the changes in `/var/lib/bor/config.toml`.

## Setting the Owner and Signer Key

On Polygon, it is recommended that you keep the owner and signer keys different.

* Signer — the address that signs the
  [checkpoint transactions](/docs/maintain/glossary.md#checkpoint-transaction). The recommendation is to keep at least 1 ETH on the signer address.
* Owner — the address that does the staking transactions. The recommendation is to keep the MATIC
  tokens on the owner address.

### Generating a Heimdall private key

You must generate a Heimdall private key only on the validator machine. Do not generate a Heimdall
private key on the sentry machine.

To generate the private key, run:

```sh
heimdallcli generate-validatorkey ETHEREUM_PRIVATE_KEY
```

where

* ETHEREUM_PRIVATE_KEY — your Ethereum wallet address.

This will generate `priv_validator_key.json`. Move the generated JSON file to the Heimdall configuration
directory:

```sh
mv ./priv_validator_key.json /var/lib/heimdall/config
```

### Generating a Bor keystore file

You must generate a Bor keystore file only on the validator machine. Do not generate a Bor keystore file
on the sentry machine.

To generate the private key, run:

```sh
heimdallcli generate-keystore ETHEREUM_PRIVATE_KEY
```

where

* ETHEREUM_PRIVATE_KEY — your Ethereum private key.

When prompted, set up a password to the keystore file.

This will generate a `UTC-<time>-<address>` keystore file.

Move the generated keystore file to the Bor configuration directory:

```sh
mv ./UTC-<time>-<address> /var/lib/bor/keystore/
```

### Add password.txt

Make sure to create a `password.txt` file then add the Bor keystore file password right in the
`/var/lib/bor/password.txt` file.

### Add your Ethereum address

Open for editing `vi /etc/matic/metadata`.

In `metadata`, add your Ethereum address. Example: `VALIDATOR_ADDRESS=0xca67a8D767e45056DC92384b488E9Af654d78DE2`.

Save the changes in `metadata`.

## Upgrade from 0.2.x to 0.3.x

Bor 0.3.0 and Heimdall 0.3.0 uses new CLIs and path standards. It is recommended to set up everything from a new machine.
However, if you still want to perform upgrade on existing node, you need to follow one-time migration steps
outlined below. If you are installing everything from a new machine, you can skip this section and continue to [Configure service files](#configure-service-files-for-bor-and-heimdall-1).

- Stop existing Heimdall and Bor services
    ```shell
    sudo service bor stop
    sudo service heimdalld stop
    sudo service heimdalld-rest-server stop
    sudo service heimdalld-bridge stop
    ```

- Create a backup folder in case something went wrong
    ```shell
    mkdir ./backup
    mkdir ./backup/bin
    mkdir -p ./backup/go/bin
    ```

- Move old binaries
    ```shell
    sudo mv /usr/bin/bor ./backup/bin
    sudo mv /usr/bin/heimdalld ./backup/bin
    sudo mv /usr/bin/heimdallcli ./backup/bin
    sudo mv /usr/bin/bridge ./backup/bin
    sudo mv $(go env GOPATH)/bin/bor ./backup/go/bin
    sudo mv $(go env GOPATH)/bin/heimdalld ./backup/go/bin
    sudo mv $(go env GOPATH)/bin/heimdallcli ./backup/go/bin
    sudo mv $(go env GOPATH)/bin/bridge ./backup/go/bin
    ```

- Move old service files
    ```shell
    sudo mv /etc/systemd/system/bor.service ./backup
    sudo mv /etc/systemd/system/heimdalld.service ./backup
    sudo mv /etc/systemd/system/heimdalld-rest-server.service ./backup
    sudo mv /etc/systemd/system/heimdalld-bridge.service ./backup
    ```

- Migrate the Heimdall and Bor directory to `/var/lib` and change ownership:
    ```shell
    sudo mv ~/.heimdalld /var/lib/heimdall
    sudo mv ~/.bor /var/lib/bor
    sudo chown -R heimdall /var/lib/heimdall
    sudo chown -R bor /var/lib/bor
    ```

    In case data copying is too slow or original data folder is mounted on a different device, you can create symlinks

    ```shell
    sudo chown -R heimdall ~/.bor
    sudo chown -R bor ~/.heimdalld
    sudo rm -rf /var/lib/heimdall
    sudo ln -nfs ~/.heimdalld /var/lib/heimdall
    sudo ln -nfs ~/.bor /var/lib/bor
    sudo chown -R heimdall /var/lib/heimdall
    sudo chown -R bor /var/lib/bor
    ```


- Copy configurations in `node/bor/start.sh` to bor configuration file `/var/lib/bor/config.toml`. Note that some
  flags are renamed in the new CLI, you can find the documentation for new CLI [here](https://github.com/maticnetwork/bor/tree/master/docs/cli) and sample configuration file in [launch repository](https://github.com/maticnetwork/launch).

  You can use [this util script](https://github.com/maticnetwork/bor/blob/develop/scripts/getconfig.sh) to convert start.sh to a config.toml file on your host. Example usage:

  ```shell
  $ git clone https://github.com/maticnetwork/bor.git
  $ cd bor/scripts
  $ BOR_DIR=/var/lib/bor ./getconfig.sh
  * Path to start.sh: /home/ubuntu/node/bor/start.sh
  * Your validator address (e.g. 0xca67a8D767e45056DC92384b488E9Af654d78DE2), or press Enter to skip if running a sentry node: 0xca67a8D767e45056DC92384b488E9Af654d78DE2
  Thank you, your inputs are:
  Path to start.sh: /home/ubuntu/node/bor/start.sh
  Address: 0xca67a8D767e45056DC92384b488E9Af654d78DE2
  Path to the config file: /home/ubuntu/node/bor/start-config.toml
  ...

  $ sudo cp /home/ubuntu/node/bor/start-config.toml /var/lib/bor/config.toml
  $ sudo chown bor /var/lib/bor/config.toml
  ```



## Configure service files for bor and heimdall

After successfully installing Bor and Heimdall through [packages](#install-with-packages-recommended), their service file could be found under `/lib/systemd/system`, and Bor's config
file could be found under `/var/lib/bor/config.toml`.
You will need to check and modify these files accordingly.

- Make sure the chain is set correctly in `/lib/systemd/system/heimdalld.service` file. Open the file with following command `sudo vi /lib/systemd/system/heimdalld.service`

    - In the service file, set `--chain` to `mainnet` or `mumbai` accordingly
    - Add `--bridge --all` to the heimdall command line for validator, example:
      ```
        ExecStart=/usr/local/bin/heimdalld start --home /var/lib/heimdall \
          --chain=mainnet \
          --bridge --all \
          --rest-server
      ```

  Save the changes in `/lib/systemd/system/heimdalld.service`.

- Make sure the chain is set correctly in `/var/lib/bor/config.toml` file. Open the file with following command `sudo vi /var/lib/bor/config.toml`

    - In the config file, set `chain` to `mainnet` or `mumbai` accordingly.

    - Enable validator flags, example:
      ```
      [miner]
        mine = true
        gaslimit = 20000000
        gasprice = "30000000000"
        etherbase = "VALIDATOR ADDRESS"

      [accounts]
        allow-insecure-unlock = true
        password = "/var/lib/bor/password.txt"
        unlock = ["VALIDATOR ADDRESS"]
      ```

  Save the changes in `/var/lib/bor/config.toml`.

## Starting the Validator Node

At this point, you must have:

* The Heimdall service on the sentry machine syncs and is running.
* The Bor service on the sentry machine running.
* The Heimdall service and the Bor service on the validator machine configured.
* Your owner and signer keys configured.

### Reload service files

Reloading service files to make sure all changes to service files are loaded correctly.

```
sudo systemctl daemon-reload
```

### Starting the Heimdall service

You will now start the Heimdall service on the validator machine. Once the Heimdall service syncs, you
will start the Bor service on the validator machine.

:::note

The Heimdall service takes several days to sync from scratch fully.

Alternatively, you can use a maintained snapshot, which will reduce the sync time to a few hours.
For detailed instructions, see [Snapshot Instructions for Heimdall and Bor](https://forum.matic.network/t/snapshot-instructions-for-heimdall-and-bor/2278).

For snapshot download links, see [Polygon Chains Snapshots](https://snapshot.polygon.technology/).

:::

Start the Heimdall services:

```sh
sudo service heimdalld start
```

Check the Heimdall service logs:

```sh
journalctl -u heimdalld.service -f
```

Check the sync status of Heimdall:

```sh
curl localhost:26657/status
```

In the output, the `catching_up` value is:

* `true` — the Heimdall service is syncing.
* `false` — the Heimdall service is synced.

Wait for the Heimdall service to fully sync.

### Starting the Bor service

Once the Heimdall service on the validator machine syncs, start the Bor service on
the validator machine.

Start the Bor service:

```sh
sudo service bor start
```

Check the Bor service logs:

```sh
journalctl -u bor.service -f
```

## Health Checks with the Community

Now that your sentry and validator nodes are in sync and running, head over to
[Discord](https://discord.gg/polygon) and ask the community to health-check your nodes.

:::note

As validators, it’s mandatory to always have a check of the signer address. If the ETH balance reaches below 0.5 ETH then it should be refilled. Avoiding this will push out nodes from submitting checkpoint transactions.

:::

## Next Steps: Staking

Now that you have your sentry and validator nodes are health-checked, proceed to
the [Staking](/maintain/validator/core-components/staking.md) guide to start backing the network.