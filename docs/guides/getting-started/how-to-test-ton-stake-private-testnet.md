---
id: private-testnet-staking
title: Staking Test in Priavte Testnet
sidebar_label: Private Testnet Staking test
---

## Stake TON in Private Network

In this document, We will cover to staking TON token on private testnet which is two operator exists.

Almost of this document for How to using `stakin` and `manage=staking` command in `plasma-evm`. this command is quite useful for developer and Operator.

> For common user, Recommand to use [dashboard](https://dashboard.faraday.tokamak.network).

Setup Testing Environments with [onther-tech/go-ethereum](https://github.com/onther-tech/go-ethereum) as `rootchain` and  [onther-tech/plasma-evm](https://github.com/onther-tech/plasma-evm) as `plasma-evm`.

## Setup TON stake contracts

Let assume that one manager and two operators for staking in TON staking private testnet.

The manager deploy and manage contracts for TON staking.

The operators stakes and un-stakes TON token, also they are oprating their own plasma chain.

### Source code download for Plasma-evm

Deploy stake and other contracts to a running rootchain.

If there is no active rootchain, please follow [Setup Rootchain in Private Testnet](https://docs.tokamak.network/docs/en/guides/getting-started/how-to-open-private-testnet-manually).

First of all, Download `plasma-evm` repository with following below commands.

```bash
$ git clone https://github.com/onther-tech/plasma-evm
$ cd plasma-evm
plasma-evm $
```

For more details about setup `plasma-evm`, you can see [Setup Childchain in Private Testnet](https://docs.tokamak.network/docs/en/guides/getting-started/how-to-open-private-testnet-manually).

### Generate accounts for Manager and Operators

Create accounts for test.

```bash
plasma-evm $ build/bin/geth account importKey b71c71a67e1177ad4e901695e1b4b9ee17ae16c6668d313eac2f96dbcda3f291 \
            --datadir ./.pls.staking/manager
```

Press enter twice, to insert empty string password for convinient test.

```bash
INFO [03-24|15:35:42.112] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-24|15:35:42.276] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:
Address: {71562b71999873db5b286df957af199ec94617f7}
```

As following command, create accounts for two operators.

```bash
plasma-evm $ build/bin/geth account importKey bfaa65473b85b3c33b2f5ddb511f0f4ef8459213ada2920765aaac25b4fe38c5 \
            --datadir ./.pls.staking/operator1
```

```bash
plasma-evm $ build/bin/geth account importKey 067394195895a82e685b000e592f771f7899d77e87cc8c79110e53a2f0b0b8fc \
            --datadir ./.pls.staking/operator2
```

As following below command, create empty password file as `pwd.pass` for all accounts we created.

```bash
plasma-evm $ echo "" > pwd.pass
```

### Deploy TON Stake manager contract

`deployManagers` sub-command is in `manage-staking`, It is for deploying or management about contracts of TON staking by manager, not operator.

`deployManagers` command required two input arguments, `withdrawalDelay` and `seigPerBlock`, to run.

The description of the input parameters is as follows.

`withdrawalDelay` : Unit is the number of blocks in rootchain. Send `requestWithdrawal` transaction in order to convert staked WTON to un-staked WTON then it can be processed after this number of blocks in rootchain. For example, Let assume that this parameter set as `10`. If the request withdrawal tx processed in 100 block in rootchain, It can be valid `processRequest` transaction for this request after 110 block in rootchain.

`seigPerBlock` : The amount of maximum seigniorage of TON per block. This parameter is effect to total inflation of TON token.

```bash
plasma-evm $ make geth && build/bin/geth manage-staking deployManagers 10 1.5 \
            --datadir ./.pls.staking/manager \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x71562b71999873DB5b286dF957af199Ec94617F7 \
            --password pwd.pass \
            --rootchain.sender 0x71562b71999873DB5b286dF957af199Ec94617F7
```

As following above command, All contracts for TON stake will be deployed.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Unlocking developer account              address=0x0000000000000000000000000000000000000000
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x71562b71999873DB5b286dF957af199Ec94617F7
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/manager/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] 1. deploy TON contract
INFO [01-01|00:00:00.000] TON deployed                             addr=0x3A220f351252089D385b29beca14e27F204c296A tx=0f9edc…149995
INFO [01-01|00:00:00.000] 2. deploy WTON contract
INFO [01-01|00:00:00.000] WTON deployed                            addr=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf tx=b951fc…ab742c
INFO [01-01|00:00:00.000] 3. deploy RootChainRegistry
INFO [01-01|00:00:00.000] RootChainRegistry deployed               addr=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 tx=1455b1…8db599
INFO [01-01|00:00:00.000] 4. deploy DepositManager
INFO [01-01|00:00:00.000] DepositManager deployed                  addr=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d tx=34f04d…713bee
INFO [01-01|00:00:00.000] 5. deploy SeigManager
INFO [01-01|00:00:00.000] SeigManager deployed                     addr=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC tx=fd415d…bbcb15
INFO [01-01|00:00:00.000] 6. add WTON minter role to SeigManager
INFO [01-01|00:00:00.000] Set WTON minter to SeigManager           tx=4084ca…ec3718
INFO [01-01|00:00:00.000] 7. add TON minter role to WTON
INFO [01-01|00:00:00.000] Set TON minter to WTON                   tx=628885…e96b75
INFO [01-01|00:00:00.000] 8. Setting SeigManager address to target contracts targets="[DepositManager WTON]"
INFO [01-01|00:00:00.000] Set SeigManager to target cotnract       target=DepositManager tx=9435d0…c15c3a
INFO [01-01|00:00:00.000] Set SeigManager to target cotnract       target=WTON           tx=4f26fc…ffacb4
INFO [01-01|00:00:00.000] Staking manager contract deployed        TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
```

### Deploy PowerTON contract

Deploy `PowerTON` contract with the following command.

An input argument of `deployPowerTON` sub-command is powerton round time. Set it 60 seconds for testing.

The powerton round time refers to a cycle in which un-issued seigniorage distributed as powerton rule. For example, If it set `60s`, an operator who receives un-issued seigniorage of TON is selected every 60 seconds.

More information about `PowerTon` in [Here]().

```bash
plasma-evm $ build/bin/geth manage-staking deployPowerTON 60s \
            --datadir ./.pls.staking/manager \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x71562b71999873DB5b286dF957af199Ec94617F7 \
            --password pwd.pass \
            --rootchain.sender 0x71562b71999873DB5b286dF957af199Ec94617F7
```

### Information of deployed contracts

The information of deployed contracts with sub-command `deployManager` of `manage-staking` command is saved in `.pls.staking/manager` as rawdb.

As following command, Extract all information about deployed stake contract in rootchain then save in `manager.json` file.

```bash
 plasam-evm $ build/bin/geth manage-staking getManagers manager.json --datadir ./.pls.staking/manager

 INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/manager/geth/stakingdata ca
che=16.00MiB handles=16
INFO [01-01|00:00:00.000] Exporting manager contracts              path=manager.json
INFO [01-01|00:00:00.000] Exported manager contracts               path=manager.json
{
  "TON": "0x3a220f351252089d385b29beca14e27f204c296a",
  "WTON": "0xdb7d6ab1f17c6b31909ae466702703daef9269cf",
  "DepositManager": "0x880ec53af800b5cd051531672ef4fc4de233bd5d",
  "RootChainRegistry": "0x537e697c7ab75a26f9ecf0ce810e3154dfcaaf44",
  "SeigManager": "0x3dc2cd8f2e345951508427872d8ac9f635fbe0ec",
  "PowerTON": "0xbcdfc870ea0c6463c6ebb2b2217a4b32b93bcfb7"
}
```

Total 6 contract addresses, include `PowerTON` contract address, are stored in `manager.json`.

## Setup TON Stake

### Activate PowerTON

Use `startPowerTON`, a sub-command of `manage-staking`, to activate `PowerTON` which has the rule of un-issued seigniorage of TON.

As following command, Send transaction for activating `PowerTON`.

```bash
plasma-evm $ build/bin/geth manage-staking startPowerTON \
            --datadir ./.pls.staking/manager \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x71562b71999873DB5b286dF957af199Ec94617F7 \
            --password pwd.pass \
            --rootchain.sender 0x71562b71999873DB5b286dF957af199Ec94617F7

INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Unlocking developer account              address=0x0000000000000000000000000000000000000000
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x71562b71999873DB5b286dF957af199Ec94617F7
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/manager/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] PowerTON started                         PowerTON=0xBcDfc870Ea0C6463C6EBb2B2217a4b32B93BCFB7
```

### Mint Test TON

For this testing, have to mint test TON to each Operator who attend `stake` If `DepositManager` contract deployed as follow [Deploy TON Stake manager contract](#deploy-ton-stake-manager-contract).

In this private testnet, we assumed that two operators are exist. Operator1 and Operator2 use following accounts.

- Operator1 : `0x3cd9f729c8d882b851f8c70fb36d22b391a288cd`
- Operator2 : `0x57ab89f4eabdffce316809d790d5c93a49908510`

As following command, Mint 10,000 TON to each Operator.

```bash
plasma-evm $ build/bin/geth manage-staking mintTON 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd 10000.0 \
            --datadir ./.pls.staking/manager \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x71562b71999873DB5b286dF957af199Ec94617F7 \
            --password pwd.pass \
            --rootchain.sender 0x71562b71999873DB5b286dF957af199Ec94617F7
```

```bash
plasma-evm $ build/bin/geth manage-staking mintTON 0x57ab89f4eabdffce316809d790d5c93a49908510 10000.0 \
            --datadir ./.pls.staking/manager \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x71562b71999873DB5b286dF957af199Ec94617F7 \
            --password pwd.pass \
            --rootchain.sender 0x71562b71999873DB5b286dF957af199Ec94617F7
```

## Operator stake TON

Operator1 and Operator2 have 10,000 TON in the private testnet.

After setup operator plasma chain, Operator must register an address of rootchain to stake manager contract.

### Setup operator1 plasma chain and set stake contract address

Use `deploy` command to deploy rootchain contracts for running operator1 plasma chain.

```bash
plasma-evm $ build/bin/geth deploy ./.pls.staking/operator1/operator1_genesis.json 1021 true 2 \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --password pwd.pass \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

As following command, Initialize the plasma chain with `operator1_genesis.json` file including an address of `rootchain` contract deployed by Operator1.

```bash
plasma-evm $ build/bin/geth init ./.pls.staking/operator1/operator1_genesis.json  \
            --datadir ./.pls.staking/operator1  \
            --rootchain.url ws://127.0.0.1:8546
```

Using `setManagers` sub-command of `manage-staking`, Set the stake contract addresses for running Operator1's plasma chain.

```bash
plasma-evm $ build/bin/geth manage-staking setManagers manager.json  \
            --datadir ./.pls.staking/operator1
NFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Set address                              name=TON addr=0x3A220f351252089D385b29beca14e27F204c296A
INFO [01-01|00:00:00.000] Set address                              name=WTON addr=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf
INFO [01-01|00:00:00.000] Set address                              name=DepositManager addr=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d
INFO [01-01|00:00:00.000] Set address                              name=RootChainRegistry addr=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44
INFO [01-01|00:00:00.000] Set address                              name=SeigManager       addr=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] Set address                              name=PowerTON          addr=0xBcDfc870Ea0C6463C6EBb2B2217a4b32B93BCFB7
```

Check the information of stake contract addresses with `getManagers` sub-command of `manage-staking` in Operator1 chaindata.

```bash
plasma-evm $ build/bin/geth manage-staking getManagers --datadir ./.pls.staking/operator1
```

### Register operator1 rootchain contract and Check TON balance

Make to receive stake seigniorage of TON with register an address of rootchain which setup by Operator1 to the stake manager contract.

```bash
plasma-evm $ build/bin/geth manage-staking register \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --password pwd.pass \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

If sucessfully registered the rootchain address, output as follows.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] Registered SeigManager to RootChain      registry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 seigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC tx=b546d3…fe55ed
INFO [01-01|00:00:00.000] Registered RootChain to SeigManager      registry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 seigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC tx=6904c9…bc07a5
```

As following command, Check test TON balance of Operator1.

```bash
plasma-evm $ build/bin/geth staking balances 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

If minted test TON and registered the address of rootchain in manager contract are sucessful, The balance of Operator1's TON as follows.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] cfg.Node.DataDir                         v=.pls.staking/operator1/geth/genesis.json
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] TON Balance                              amount="10000.0 TON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] WON Balance                              amount="0 WTON"      depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Deposit                                  amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Pending withdrawal requests              num=0
INFO [01-01|00:00:00.000] Pending withdrawal WTON                  amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Total Stake                              amount="0 WTON"
INFO [01-01|00:00:00.000] Total Stake of Root Chain                amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9
INFO [01-01|00:00:00.000] Uncomitted Stake                         amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Comitted Stake                           amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
```

### Operator1 stake TON

To stake test `TON`, have to convert it to `WTON`. then can be staked in `depositManager` contract.

Operator can stake only `WTON` to `depositManager` for operating plasma chain.

As following command, Convert 1,000 TON into WTON.

> Applying 1e9(1,000,000,000 wei) unit only when the decimal point is used as the input argument of `swapFromTON` sub-command.

```bash
plasma-evm $ build/bin/geth staking swapFromTON 1000.0 \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --password pwd.pass \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd

INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
WARN [01-01|00:00:00.000] Allowances is inefficient                current=0 target=1000.0 diff=1000.0
WARN [01-01|00:00:00.000] Approve to deposit TON                   amount=1000.0
WARN [01-01|00:00:00.000] Approved to deposit TON                  amount=1000.0 tx=5d9880…76506a
INFO [01-01|00:00:00.000] Swap from TON to WTON                    amount="1000.0 TON" from=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD tx=4d15eb…904dd6
```

Stake 500 WTON of 1,000 WTON converted with using `stake` sub-command of `staking`.

```bash
plasma-evm $ build/bin/geth staking stakeWTON 500.0 \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --password pwd.pass \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

Or, you can do the above two steps at once with using `stakeTON` sub-command.

```bash
plasma-evm $ build/bin/geth staking stakeTON 500.0 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

### Setup operator2 plasma chain and set stake contract address

Use `deploy` command to deploy rootchain contracts for running operator2 plasma chain.

```bash
plasma-evm $ build/bin/geth deploy ./.pls.staking/operator2/operator2_genesis.json 1021 true 2 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

As following command, Initialize the plasma chain with `operator2_genesis.json` file including an address of rootchain contract deployed by Operator2.

```bash
plasma-evm $ build/bin/geth init ./.pls.staking/operator2/operator2_genesis.json  \
            --datadir ./.pls.staking/operator2  \
            --rootchain.url ws://127.0.0.1:8546
```

Using `setManagers` sub-command of `manage-staking`, Set the stake contract addresses for running Operator2's plasma chain.

```bash
plasma-evm $ build/bin/geth manage-staking setManagers manager.json  \
            --datadir ./.pls.staking/operator2
```

Check the information of stake contract addresses with `getManagers` sub-command of `manage-staking` in Operator2 chaindata.

```bash
plasma-evm $ build/bin/geth manage-staking getManagers --datadir ./.pls.staking/operator2
```

### Register operator2 rootchain contract and Check TON balance

Make to receive stake seigniorage of TON with register an address of rootchain which setup by Operator2 to the stake manager contract.

```bash
plasma-evm $ build/bin/geth manage-staking register \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

If sucessfully registered the rootchain address, output as follows.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/home/ubuntu/plasma-evm/.pls.staking/operator2/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] Registered SeigManager to RootChain      registry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 seigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC tx=a63891…0a9d9a
INFO [01-01|00:00:00.000] Registered RootChain to SeigManager      registry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 seigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC tx=f7017e…d8fa00
```

As following command, Check test TON balance of Operator2.

```bash
plasma-evm $ build/bin/geth staking balances 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

If minted test TON and registered the address of rootchain in manager contract are sucessful, The balance of Operator2's TON as follows.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] cfg.Node.DataDir                         v=.pls.staking/operator1/geth/genesis.json
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] TON Balance                              amount="10000.0 TON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] WON Balance                              amount="0 WTON"      depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Deposit                                  amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Pending withdrawal requests              num=0
INFO [01-01|00:00:00.000] Pending withdrawal WTON                  amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Total Stake                              amount="500.0 WTON"
INFO [01-01|00:00:00.000] Total Stake of Root Chain                amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9
INFO [01-01|00:00:00.000] Uncomitted Stake                         amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Comitted Stake                           amount="0 WTON"      rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
```

### Operator2 stake TON

To stake test `TON`, have to convert it to `WTON`. then can be staked in `depositManager` contract.

Actually, Operator can stake only `WTON` to `depositManager` for operating plasma chain.

As following command, Convert 1,000 TON into WTON.

> Applying 1e9(1,000,000,000 wei) unit only when the decimal point is used as the input argument of `swapFromTON` sub-command.

```bash
plasma-evm $ build/bin/geth staking swapFromTON 1000.0 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

Stake 500 WTON of 1,000 WTON converted with using `stake` sub-command of `staking`.

```bash
plasma-evm $ build/bin/geth staking stakeWTON 500.0 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```

Or, you can do the above two steps at once with using `stakeTON` sub-command.

```bash
plasma-evm $ build/bin/geth staking stakeTON 500.0 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510
```


## Check TON commit rewards and withdrawal

Operator client is going to submit an Tx commit to rootchain when Operator proceeds mining child chain block.

At this time, Seigniorage rewards of TON will be calculated as follow how much `WTON` staked by each operator in the seigniorage manager contract.

### Run Operator1 chain

As following command, Run Opreator1 node in private network.

```bash
plasma-evm $ build/bin/geth \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --password pwd.pass \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --operator 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

Connect a console of Operator1's node from new terminal with following command.

```bash
plasma-evm $ build/bin/geth attach --datadir ./.pls.staking/operator1
```

Run command with `geth attach` then connect javascript console of geth.

console에 `eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[0], value: 0})` 입력하여 임의의 Tx를 생성하여 블록을 진행시킨다.
To proceed operator1's child chain,  Send dummy tx insert with `eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[0], value: 0})` command into console.

```javascript
> web3.eth.accounts
["0x3cd9f729c8d882b851f8c70fb36d22b391a288cd"]
> eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[0], value: 0})
"0x0a65e80eb105c448ffa1ca50430dc1d3f4b0da14ad1d4793a43ed36b6df0959c"
> eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[0], value: 0})
"0x81130ae471f536c04cc6b9901962dd5a15bb72f3924422ea051a3b0494c0fade"
```

Send dummy transaction, with 0 ETH to itself, More than twice.

> For sending commit Tx in rootchain, Have to mine blocks more than `Epoch` number which used in deploying rootchain contract by Operator.

The Epoch Number is `2` used in this example.

Close console connection with sending `exit` command at console.

### Check seigniorage

Operator2 stake rewards has increased as `Uncommited` status because only operator1 chain proceed at [Run Operator1 chain](#run-operator1-chain).

In new terminal, Check Operator2 rewards of staked TON with using `staking balances`, like as following.

```bash
plasma-evm $ build/bin/geth staking balances 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --datadir ./.pls.staking/operator2 \
            --rootchain.url ws://127.0.0.1:8546 \
            --unlock 0x57ab89f4eabdffce316809d790d5c93a49908510 \
            --password pwd.pass \
            --rootchain.sender 0x57ab89f4eabdffce316809d790d5c93a49908510

INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] cfg.Node.DataDir                         v=.pls.staking/operator2/geth/genesis.json
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator2/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] TON Balance                              amount="9000.0 TON" depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] WON Balance                              amount="500.0 WTON" depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Deposit                                  amount="500.0 WTON" rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Pending withdrawal requests              num=0
INFO [01-01|00:00:00.000] Pending withdrawal WTON                  amount="0 WTON"     rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Total Stake                              amount="1100.0 WTON"
INFO [01-01|00:00:00.000] Total Stake of Root Chain                amount="1100.0 WTON"  rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080
INFO [01-01|00:00:00.000] Uncomitted Stake                         amount="100.0 WTON"    rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
INFO [01-01|00:00:00.000] Comitted Stake                           amount="500.0 WTON"  rootchain=0x8Bb208b42B2d1dA1606B3E06ad6648514b6aE080 depositor=0x57ab89f4eAbDfFCe316809D790D5c93a49908510
```

The above result is an example(modified). Actual seigniorage WTON number will be float, the numbers with under decimal point, because it calculated by timestamp of block in rootchain.

### Withdrawal rewards

Both Operator1 and Operator2 are received rewards of seigniorage of staked TON due to Operator1 tx commit.

The rewards is additionally issued in `WTON` for each operator account.

In this part, we are going to withdraw `WTON` including the seigniorage of staked in Operator1.

For withdraw 510 WTON, use `requestWithdrawal` sub-command of `staking` for request withdrwaing as following.

```bash
plasma-evm $ build/bin/geth staking requestWithdrawal 510.0 \
              --datadir ./.pls.staking/operator1 \
              --rootchain.url ws://127.0.0.1:8546 \
              --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
              --password pwd.pass \
              --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

If Operator1 has 510 WTON or more, the withdraw request is successfully processed.

```bash
INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] Withdrawal requested                     rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 amount="510.0 WTON" tx=570061…
b07f4d
```

And re-check Operator1 balance, you can see the amount of 510 WTON in a line start with `Pending withdrawal ..`.

```bash
plasma-evm $ build/bin/geth staking balances 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd

INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] cfg.Node.DataDir                         v=.pls.staking/operator1/geth/genesis.json
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] TON Balance                              amount="9000.0 TON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] WON Balance                              amount="500.0 WTON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Deposit                                  amount="500.0 WTON" rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Pending withdrawal requests              num=1
INFO [01-01|00:00:00.000] Pending withdrawal WTON                  amount="510.0 WTON" rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Uncomitted Stake                         amount="0 WTON"                                rootchain=0x17FB80e2E16b02faC9369334
24305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Comitted Stake                           amount="10 WTON"                                rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
```

To finalize withdrawal request, use `processWithdrawal` sub-command as follow.

```bash
plasma-evm $ build/bin/geth staking processWithdrawal \
              --datadir ./.pls.staking/operator1 \
              --rootchain.url ws://127.0.0.1:8546 \
              --unlock 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
              --password pwd.pass \
              --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd
```

If `processWithDrawal` tx is successfully processed, then you can check 1,010 WTON in `WTON Balance` in result of using `balances`.

```bash
plasma-evm $ build/bin/geth staking balances 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd \
            --datadir ./.pls.staking/operator1 \
            --rootchain.url ws://127.0.0.1:8546 \
            --rootchain.sender 0x3cd9f729c8d882b851f8c70fb36d22b391a288cd

INFO [01-01|00:00:00.000] Maximum peer count                       ETH=50 LES=0 total=50
INFO [01-01|00:00:00.000] Operator account is unlocked             address=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Set options for submitting a block       mingaspirce=1000000000 maxgasprice=100000000000 resubmit=0s
INFO [01-01|00:00:00.000] cfg.Node.DataDir                         v=.pls.staking/operator1/geth/genesis.json
INFO [01-01|00:00:00.000] Allocated cache and file handles         database=/Users/jinhwan/gitrepo/plasma-evm/.pls.staking/operator1/geth/stakingdata cache=16.00MiB handles=16
INFO [01-01|00:00:00.000] Using manager contracts                  TON=0x3A220f351252089D385b29beca14e27F204c296A WTON=0xdB7d6AB1f17c6b31909aE466702703dAEf9269Cf DepositManager=0x880EC53Af800b5Cd051531672EF4fc4De233bD5d RootChainRegistry=0x537e697c7AB75A26f9ECF0Ce810e3154dFcaaf44 SeigManager=0x3Dc2cd8F2E345951508427872d8ac9f635fBe0EC
INFO [01-01|00:00:00.000] TON Balance                              amount="9000.0 TON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] WON Balance                              amount="1010.0 WTON" depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Deposit                                  amount="0 WTON" rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Pending withdrawal requests              num=0
INFO [01-01|00:00:00.000] Pending withdrawal WTON                  amount="0 WTON" rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Uncomitted Stake                         amount="0 WTON"                                rootchain=0x17FB80e2E16b02faC9369334
24305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
INFO [01-01|00:00:00.000] Comitted Stake                           amount="0 WTON"                                rootchain=0x17FB80e2E16b02faC936933424305d4F29F9d5D9 depositor=0x3cD9F729C8D882B851F8C70FB36d22B391A288CD
```

## Appendix sub-commands 

Plasma-evm has a new commands `manage-staking` and  `staking` for deploying and managements of TON token staking.

### `manage-staking` sub-commands

This table is about sub-commands of `manage-staking` and its arguments.

| Sub-command    | Argument        | Unit    | Describes   |
|----------------|------------------|---------|--------|
| deployManager  | withdrawalDelay* | Int     | In order to convert a staked WTON to a un-staked WTON, have to send `requestWithdrawal` transaction. It can be processible after the number of this parameter blocks increased in rootchain.  |
|                | seigPerBlock*    | Float   | The amount of maximum seigniorage of TON per block. This parameter is effect to total inflation of TON token. |
| deployPowerTON | roundDuration*   | Int(Seconds) | Deploy `PowerTON` contract. this sub-command required `roundDuration`, unit is seconds. for example, If deployed `PowerTON` contract with `60s` as round duration.  an operator who receives un-issued seigniorage of TON is selected every 60 seconds.
| startPowerTON  |  None            | -       | Activate `PowerTON` contract which deployed with `deployPowerTON` sub-command. |
| getManagers    | <filename>*      | string       | Extract the addressses of the stake manager contracts from the db, located with `--datadir` and save the addresses as `<filename>.json`. In most cases, the path for  `--datadir` should be specified same as `deployManager` sub-command.  |
| setManagers  |  <filename>*      | string       | Read the target file (e.g `manager.json`) that contains the addresses of stake manager contracts then set addresses for running the operator's plasma chain. the path of `--datadir` should place in the operator chaindata location. |
| register    |  None            | -       | Operator have to register own rootchain contract address to seigniroage manager contract in order to receive seigniorage TON. the path of `--datadir` should place in the operator chaindata location, which was set stake manager contracts by `setManager` sub-command.  |
| mintTON  |  amount*            | Float or Int       | Generate test TON token as much as input argument.  |

> Input argument with "*" is required.

### `staking` sub-commands

This table is about sub-commands of `staking` and its arguments.

| Sub-command    | Argument        | Unit    | Describes   |
|----------------|------------------|---------|--------|
| balances   |  address*         | address       | Output information about how much this address has `TON`, `WTON`, `staked WTON(==deposit)`, `reward WTON(==(Un)Committed)` and others. |
| swapFromTON  |  amount*            | Float or Int       | Send transaction for convert `WTON` to `TON` token. the argument is how much will be convert. Target address must be specified `--rootchain.sender` flag. |
| swapToTON  |  amount*            | Float or Int       |  Send transaction that convert `TON` to `WTON` token. the argument is how much will be convert. Target address must be specified `--rootchain.sender` flag. |
| stakeTON   |  amount*            | Float or Int  | Combined `swapFromTON` and `stakeWTON`, convert and stake at once. Send transaction that the operator's `TON` to the `stake` state with this amount. Target address must be specified `--rootchain.sender` flag. |
| stakeWTON  |  amount*            | Float or Int  | In order to receive seigniorage of TON token, Operator have to stake `WTON`. Send transaction that the operator's `WTON` to the `stake` state with this amount. Target address must be specified `--rootchain.sender` flag. |
| requestWithdrawal  |  amount*            | Float or Int       | Send transaction that convert  `WTON` state from `stake` to `un-stake`.  Target address must be specified `--rootchain.sender` flag. this un-stake request will be valid after the number of blocks, `withdrawalDelay` specified in `depositManager`, increased in rootchain.  |
| processWithdrawal  | numRequests         | Int       | Finalize un-stake requests. Without using argument, it will finalize all valid requests. |

> Input argument with "*" is required.

> Not applying 1e9 unit without decimal point in `amount`.