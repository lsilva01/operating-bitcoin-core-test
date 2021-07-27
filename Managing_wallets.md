# Managing The Wallet

## 1. Backing Up and Restoring The Wallet

### 1.1 Creating The Wallet

Since version 0.21, Bitcoin Core no longer has a default wallet.
Wallets can be created with the RPC command `createwallet`. More information about this command can be found running `bitcoin-cli help createwallet`.

The following command, for example, creates a descriptor wallet:

`$ bitcoin-cli -named createwallet wallet_name="wallet-01" descriptors=true`

The `descriptors` parameter can be omitted if the intention is to create a legacy wallet.

For now, the default type is the legacy wallet, but that should change in the near future.

Wallets are created in the `wallets` folder of the data directory. The default folder varies by operational systems, as shown below. The user can also change it using `-datadir` or `-walletdir` initialization parameters.

| Operating System | Default wallet directory                                    | 
| -----------------|:------------------------------------------------------------|
| Linux            | `home/<user>/.bitcoin/wallets`                              |
| Windows          | `C:\Users\<user>\AppData\Roaming\Bitcoin\wallets`           | 
| macOS            | `/Users/<user>/Library/Application Support/Bitcoin/wallets` | 

### 1.2 Encrypting The Wallet

The `wallet.dat` file is not encrypted by default and is, therefore, vulnerable if an attacker gains access to the device where the wallet or the backups are stored.

The wallet must be encrypted with the following command:

`$ bitcoin-cli -rpcwallet="wallet-01" encryptwallet "passphrase"`

The `encryptwallet` command is used only when the wallet has not been encrypted yet. Otherwise, the `walletpassphrasechange` command should be used.

`$ bitcoin-cli -rpcwallet="wallet-01" walletpassphrasechange "oldpassphrase" "newpassphrase"`

The argument passed to `-rpcwallet` is the name of the wallet to be encrypted.

The term "encrypt the wallet" used here is not very accurate. This command encrypts only the private key. All other wallet information, such as transactions, is still visible.

The wallet can also be encrypted in the `createwallet` command via the `passphrase` argument:

`$ bitcoin-cli -named createwallet wallet_name="wallet-01" descriptors=true passphrase="passphrase"`

Note that if the passphrase is lost, all the coins in the wallet will also be lost forever.

### 1.3 Backing Up The Wallet

Wallets can be safely copied to another destination. This backup file should be stored on offline devices only, such as on a USB drive or an external hard drive.

If the wallet and backup are lost for any reason, the bitcoins related to this wallet will become permanently inaccessible.

The `bitcoin-cli backupwallet "destination"` RPC command or `Backup Wallet` GUI menu item must be used to ensure the file is in a safe state when the copy is made. The destination parameter must include the name of the file. Otherwise, the command will return an error message like "Error: Wallet backup failed!" for descriptor wallets. If it is a legacy wallet, it will be copied and a file will be created with the default file name `wallet.dat`.

`$ bitcoin-cli -rpcwallet="wallet-01" backupwallet /home/node01/Backups/backup-01.dat`

### 1.4 Backup Frequency

The original Bitcoin Core wallet was a collection of unrelated private keys. If a non-HD wallet had received funds to an address and then restored from a backup made before the address was generated, then any funds sent to said address are lost forever because there's no deterministic mechanism to derive that address again.

However, [version 0.13](https://github.com/bitcoin/bitcoin/blob/master/doc/release-notes/release-notes-0.13.0.md) introduced a deterministic mechanism from which to derive keys; HD wallets. Users no longer lose funds when restoring old backups because all addresses are derived from the HD wallet seed.

This means that a single backup is enough to recover the coins at any time. It is still recommended to make regular backups (once a week) or after a significant amount of new transactions to keep the metadata such as labels.

Metadata cannot be retrieved from a blockchain rescan, so if the backup is too old, it will be lost forever.

Wallets created before version 0.13 are not HD and must be backed up every 100 keys used since the previous backup, or even more often to keep the metadata.

### 1.5 Restoring The Wallet From a Backup

To restore a wallet, an empty one must be created first. The backup file then rewrites the `wallet.dat` of this new wallet.

The user must unload the wallet and load it again after replacing the file.

```
$ bitcoin-cli createwallet "restored-wallet"
$ bitcoin-cli unloadwallet "restored-wallet"
$ cp ~/Backups/backup-01.dat ~/.bitcoin/wallets/restored-wallet/wallet.dat
$ bitcoin-cli loadwallet "restored-wallet"
```

After that, `getwalletinfo` can be used to check if the wallet has been fully restored.

`$ bitcoin-cli -rpcwallet="restored-wallet" getwalletinfo`