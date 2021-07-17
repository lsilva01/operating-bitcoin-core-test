# Managing Wallets

## 1. Backing Up and Resoring Wallet

### 1.1 Creating Wallet

Since version 0.21, Bitcoin Core no longer has a default wallet.
Wallets can be created with the RPC command `createwallet` and more information about this command can be found running `bitcoin-cli help createwallet`.

The following command, for example, creates a descriptor wallet:

>$ bitcoin-cli -named createwallet wallet_name="wallet01" descriptors=true

If the node is running on testnet network, `-testnet` parameter should be added. Or `-signet` if signet.

>$ bitcoin-cli -testnet -named createwallet wallet_name="wallet01" descriptors=true

The `descriptors` parameter can be omitted if the intention is to create a legacy wallet.

For now, the default type is the legacy wallet, but that should change in the near future.

By default, wallets will be create in the `~/.bitcoin/wallets/wallet_name` folder. If running on testnet, it will be created in `~/.bitcoin/testnet3/wallets/wallet_name`.

### 1.2 Encryping Wallet

The `wallet.dat` file is not encrypted by default and is therefore vulnerable if an attacker gains access to the device where the wallet or backups are stored.

The wallet must be encrypted with the following command:

>$ bitcoin-cli -rpcwallet="wallet-01" encryptwallet "passphrase"

The `encryptwallet` command is used only when the wallet is not encrypted yet. Otherwise, the `walletpassphrasechange` command should be used.

>$ bitcoin-cli -rpcwallet="wallet-01" walletpassphrasechange "oldpassphrase" "newpassphrase"

The `-rpcwallet` is the name of the wallet that will be copied.

The term "encrypt the wallet" used here is not very accurate. This command only encrypts the private key, all other wallet information, as transactions are still visible.

Note that if the passphrase is lost, the coins in the wallet will also be lost forever.

### 1.3 Listing Wallets

To open or backup a wallet, the user needs to know the its name. The command `listwallets`.

>$ bitcoin-cli listwallets 

### 1.4 Backing Up Wallet

Wallets can be safely copied for another destination. This backup file should be stored off-line. It may be a USB drive, another computer or external hard drive.

If the wallet and backup are lost for any reason, the bitcoins related to this wallet key will become permanently inaccessible.

The command is `bitcoin-cli backupwallet "destination"`. The destination parameter must include the name of the file. Otherwise, the command will return an error message like "Error: Wallet backup failed!" if the wallet is of descriptor type. If it is of legacy type, it will be copied with the name `wallet.dat`.

>$ bitcoin-cli -rpcwallet="wallet-01" backupwallet /home/node01/Backups/backup01.dat

### 1.5 Backup Frequency

The Bitcoin Core wallet was originally a collection of unrelated private keys with their associated addresses. If a non-HD wallet generated a key/address, gave that address out and then restored a backup from before that key’s generation, then any funds sent to that address would be lost definitively.

However, since [v0.13](https://github.com/bitcoin/bitcoin/blob/master/doc/release-notes/release-notes-0.13.0.md), with the introduction of HD wallets, the keypool essentially became an address look-ahead pool. Restoring old backups can no longer definitively lose funds as long as the addresses used were from the wallet’s HD seed (since all private keys can be rederived from the seed).

So, theoretically, a single backup is enough. But it is recommended to make regular backups (1 time a day or a week) or when there is a relevant amount of new transactions in the wallet.

### 1.6 Restoring Wallet From a Backup

To restore a wallet, an empty wallet must be created. And then, the backup file rewrites the `wallet.dat` of this new wallet.

The user must unload the wallet and load it again after replacing the file.

>$ bitcoin-cli createwallet "restored_wallet"

>$ bitcoin-cli unloadwallet "restored_wallet"

>$ cp ~/Backups/backup01.dat ~/.bitcoin/wallets/restored_wallet/wallet.dat

>$ bitcoin-cli loadwallet "restored_wallet"

After these steps, the wallet balance can be checked.

>$ bitcoin-cli -rpcwallet="restored_wallet" getbalance

### 1.7 `-rescan` and `-reindex`

The `-rescan` argument rescans the blockchain for missing wallet transactions on startup.

>$ bitcoind -rescan

This is only necessary it there are missing transactions after restoring the wallet.

A pruned node, however, is incompatible with the `-rescan` option, since it does not have the data to check for relevant transactions.

In that case, it is necessary restart the node with the `-reindex` command line option to start over with the initial sync. The wallet will scan for relevant transactions during the synchronization and rediscover the funds and transaction history of the wallet.

>$ bitcoind -reindex