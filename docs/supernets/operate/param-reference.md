---
id: supernets-param-reference
title: Configuration Parameter Reference
sidebar_label: Configuration Parameter Reference
description: "Manpage for configurable parameters."
keywords:
  - docs
  - polygon
  - Supernets
  - flags
  - parameters
---

The Configuration Flags Reference provides a comprehensive list of flags and their descriptions for configuring the genesis configuration of a Supernet. Use this reference as a guide for setting up and customizing your Supernet.

| Flag Name & Type                   | Description                                                                                                 | Default Value                    | Mandatory Flag | Mutually Exclusive With | Example                                                                                          | Configurable During Runtime | Configurable on Restart |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------- | -------------------------------- | -------------- | --------------------- | ------------------------------------------------------------------------------------------------------ | ------------------- | ----------------------- |
| `--block-gas-limit` uint             | The maximum amount of gas used by all transactions in a block                                                | 5242880                          | NO             |                       | Command: genesis Flag: `--block-gas-limit "10000000"`                                                 | NO                 |                         |
| `--block-time` duration              | The predefined period which determines block creation frequency                                              | 2s                               | NO             |                       | Command: genesis Flag: `--block-time "10s"`                                                          | NO                 |                         |
| `--block-time-drift` uint            | Configuration for block time drift value (in seconds). Defines the time slot in which a new block can be created | 10                               | NO             |                       | Command: genesis Flag: `--block-time-drift "20"`                                                     | NO                 |                         |
| `--bootnode` stringArray             | MultiAddr URL for p2p discovery bootstrap. This flag can be used multiple times.                            |                                  | NO             |                       | Command: genesis Flag: `--bootnode "/ip4/127.0.0.1/tcp/30301/p2p/16Uiu2HAmBW3zAvTEHGj5DDygJ5AzuvaRdY5wtSLNmkvXfaQensBu"` | NO                 |                         |
| `--bridge-allow-list-admin` stringArray | List of addresses to use as admin accounts in the bridge allow list.                                          | []string{}                       | NO             |                       | Command: genesis Flag: `--bridge-allow-list-admin "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"`             | NO                 |                         |
| `--bridge-allow-list-enabled` stringArray | List of addresses to enable by default in the bridge allow list.                                              | []string{}                       | NO             |                       | Command: genesis Flag: `--bridge-allow-list-enabled "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"`           | NO                 |                         |
| `--bridge-block-list-admin` stringArray | List of addresses to use as admin accounts in the bridge block list                                           | []string{}                       | NO             |                       | Command: genesis Flag: `--bridge-block-list-admin "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"`              | NO                 |                         |
| `--bridge-block-list-enabled` stringArray | List of addresses to enable by default in the bridge block list.                                             | []string{}                       | NO             |                       | Command: genesis Flag: `--bridge-block-list-enabled "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"`            | NO                 |                         |
| `--burn-contract stringArray`        | The burn contract blocks and addresses (format: [block]:[address])                                           | []string{}                       | NO             |                       | Command: genesis Flag: `--burn-contract "0:0x0000000000000000000000000000000000000000"`                       | NO                 |                         |
| `--consensus` string                 | The consensus protocol to be used                                                                           | "polybft"                        | NO             |                       | Command: genesis Flag: `--consensus polybft`                                                          | NO                 |                         |
| `--contract-deployer-allow-list-admin` stringArray | List of addresses to use as admin accounts in the contract deployer allow list                            | []string{}                       | NO             |                       | Command: genesis Flag: `--contract-deployer-allow-list-admin "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"` | NO                 |                         |
| `--contract-deployer-allow-list-enabled` stringArray | List of addresses to enable by default in the contract deployer allow list                            | []string{}                       | NO             |                       | Command: genesis Flag: `--contract-deployer-allow-list-enabled "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"` | NO                 |                         |
| `--contract-deployer-block-list-admin` stringArray | List of addresses to use as admin accounts in the contract deployer block list                              | []string{}                       | NO             |                       | Command: genesis Flag: `--contract-deployer-block-list-admin "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"`  | NO                 |                         |
| `--contract-deployer-block-list-enabled` stringArray | List of addresses to enable by default in the contract deployer block list                              | []string{}                       | NO             |                       | Command: genesis Flag: `--contract-deployer-block-list-enabled "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"`| NO                 |                         |
| `--dir` string                      | Represents the file path for the genesis data                                                                | "./genesis.json"                 | NO             |                       | Command: genesis Flag: `--dir "/data/genesis.json"`                                                   | NO                 |                         |
| `--epoch-reward` uint                | Reward size for block sealing                                                                               | 1                                | NO             |                       | Command: genesis Flag: `--epoch-reward "10"`                                                          | NO                 |                         |
| `--epoch-size` uint                  | The epoch size for the chain                                                                                | 100000                           | NO             |                       | Command: genesis Flag: `--epoch-size "10"`                                                            | NO                 |                         |
| `--name` string                      | The name for the chain                                                                                       | "polygon-edge"                   | NO             |                       | Command: genesis Flag: `--name "test-chain"`                                                           | NO                 |                         |
| `--native-token-config` string        | Native token configuration                                                                                  | Polygon:MATIC:18:false:0x0       | NO             |                       | Command: genesis Flag: `--native-token-config MyToken:MTK:18:true:0x85da99c8a7c2c95964c8efd687e95e632fc533d6` | NO                 |                         |
| `--premine` stringArray               | The premined accounts and balances                                                                          | []string{}                       | NO             |                       | Command: genesis Flag: `--premine 0x85da99c8a7c2c95964c8efd687e95e632fc533d6:1000000000000000000000`               | NO                 |                         |
| `--reward-token-code` string          | Hex encoded reward token byte code that will be deployed                                                     | ""                               | NO             |                       | Command: genesis Flag: `--reward-token-code <deploy_byte_code>`                                         | NO                 |                         |
| `--reward-wallet` string              | Configuration of reward wallet                                                                               | NA                               | YES            |                       | Command: genesis Flag: `--reward-wallet 0x0101010101010101010101010101010101010101:1000000000000000000000000000`  | NO                 |                         |
| `--sprint-size` uint                  | The number of blocks included into a sprint                                                                  | 5                                | NO             |                       | Command: genesis Flag: `--sprint-size "2"`                                                            | NO                 |                         |
| `--stake` stringArray                 | Validators staked amount                                                                                     | stake: []string{}                | NO             |                       | Command: genesis Flag: `--stake "1000000000000000000000000000"`                                          | NO                 |                         |
| `--transactions-allow-list-admin` stringArray | List of addresses to use as admin accounts in the transactions allow list                                | []string{}                       | NO             |                       | Command: genesis Flag: `--transactions-allow-list-admin "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"`        | NO                 |                         |
| `--transactions-allow-list-enabled` stringArray | List of addresses to enable by default in the transactions allow list                                | []string{}                       | NO             |                       | Command: genesis Flag: `--transactions-allow-list-enabled "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"`      | NO                 |                         |
| `--transactions-block-list-admin` stringArray | List of addresses to use as admin accounts in the transactions block list                                | []string{}                       | NO             |                       | Command: genesis Flag: `--transactions-block-list-admin "0xbB39871E4e399b22428FdfA9E4e4Ca67842EA8Cd"`        | NO                 |                         |
| `--transactions-block-list-enabled` stringArray | List of addresses to enable by default in the transactions block list                                | []string{}                       | NO             |                       | Command: genesis Flag: `--transactions-block-list-enabled "0x2f82ad5785F6f3Fd242e7EC7a03c2cDfBA6cC6D1"`      | NO                 |                         |
| `--trieroot` string                  | Trie root from the corresponding triedb                                                                      | ""                               | NO             |                       | Command: genesis Flag: `--trieroot "0xf5ef1a28c82226effb90f4465180ec3469226747818579673f4be929f1cd8663"`    | NO                 |                         |
| `--validators` stringArray            | Validators defined by the user                                                                               | []string{}                       | NO             |                       | Command: [<ins>here</ins>](/docs/supernets/operate/deploy/genesis.md) | NO                  |                         |
| `--validators-path` string           | Root path containing polybft validators' secrets                                                             | "./"                            | NO             |                       | Command: genesis Flag: `--validators-path "/data/validators"`                                            | NO                 |                         |
| `--validators-secret` stringArray     | Validators secrets                                                                                           | []string{}                       | NO             |                       | Command: genesis Flag: `--validators-secret "0x0101010101010101010101010101010101010101010101010101010101010101"` | NO                 |                         |
| `--artifacts-path string`           | The path to the contract artifacts JSON.                                                                     |                                  | YES            |                       | Command: genesis predeploy Flag: --artifacts-path "artifacts.json"                                    | NO                  |                         |
| `--chain string`                    | The genesis file to update                                                                                   | "./genesis.json"                 | NO             |                       | Command: genesis predeploy Flag: --chain "/data/genesis.json"                                         | NO                  |                         |
| `--constructor-args stringArray`     | The constructor arguments, if any                                                                             |                                  | NO             |                       | Command: genesis predeploy Flag: --constructor-args ""                                                 | NO                  |                         |
| `--predeploy-address string`        | The address to predeploy to. Must be >= 0x0000000000000000000000000000000000001100                       | "0x0000000000000000000000000000000000001100" | YES            |                       | Command: genesis predeploy Flag: --predeploy-address "0x0000000000000000000000000000000000001111"        | NO                  |