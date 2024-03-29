# Shamir Secret Sharing Backup Wallet SLIP-39 Recovery

Trezor created the fantastic Shamir Backup for backing up your wallet.
As of 2019-27-19 it is only implemented on Trezor T and you can only recover it on Trezor T. No other wallets have it implemented yet, so I was looking for an additional way to recover it.
Following this tutorial you can generate your addresses, public and private keys as well as your xprv and xpub for importing it in other wallets.

## DISCLAMER
THIS IS JUST A PROOF OF CONCEPT! DON'T USE IT WITH YOUR REAL SEED! 
USE IT WITH A DEMO WALLET!
IF YOU REALLY NEED TO USE IT WITH YOUR SEED - USE IT ON AN OFFLINE COMPUTER! CLONE iancoleman.io/bip39 AND https://github.com/trezor/python-shamir-mnemonic/ AND install nodejs and the bip32 library FIRST, THEN DISCONNECT FROM THE INTERNET AND OVERWRITE YOUR HARDDISK AFTERWARDS

## Wallet Recovery 
1. Generate a new wallet on Trezor T and choose Shamir Backup
2. Use the python reference implementation python-shamir-mnemonic https://github.com/trezor/python-shamir-mnemonic/ to recover your wallet and generate the master secret
3. Use the bip32 javascript library to generate the BIP32 Root Key
    * Install nodejs https://nodejs.org
    * Install BIP32 library https://github.com/bitcoinjs/bip32
        * `npm install bip32`
    * Open a nodejs interactive shell (open a terminal and type `node`)
    * type the following commands (replace 77d... with your master secret):
        * `const bip32 = require('bip32');`
        * `bip32.fromSeed(Buffer.from("77d6dc4c820ca1c8d402cad5467630f9", 'hex')).toBase58();`
        This will print out the BIP32 Root Key
4.  Use https://iancoleman.io/bip39/ for wallet recovery.
    * Copy the Bip32 Root Key into the BIP32 Root Key field to recover your wallet
    * Choose BIP44 or BIP49 Derivation path to generate the addresses and keys to your shamir backup

## Example
1. Shamir Secret Backup generated on Trezor T
    * Share 1: 
        * `airline provide academic acid charity trouble boring cradle empty deliver boundary entrance pleasure anxiety taught damage window soldier cradle evaluate`
    * Share 2:
        * `airline provide academic agency armed salon ceiling admit rapids divorce hairy tolerate advance costume forecast shrimp golden short entrance public`
2. Generate Addresses on Trezor T for comparison:
    ```
    BTC Legacy account #1 BIP32 Path: m/44'/0'/0'/0/0: 1HubpNopePj8Mt5s7cDi3ZuDqGCEw6etMX
    BTC Legacy account #1 BIP32 Path: m/44'/0'/0'/0/1: 12NP6LWt9YQdiEZVdwgG9BJupxPLU7PYkG
    BTC Account #1 BIP32 Path: m/49'/0'/0'/0/0: 39ygxy6bZVBLWJpCYoeoX3BY4NTbjme2CF
    BTC Account #1 BIP32 Path: m/49'/0'/0'/0/1: 38twR7oqfXyVzsxkkqo5gY2cQc6Yox1Fa2
    ```
3. Run python reference implementation
    ```
    python-shamir-mnemonic$ shamir recover
    Enter a recovery share: airline provide academic acid charity trouble boring cradle empty deliver boundary entrance pleasure anxiety taught damage window soldier cradle evaluate

    ⛬ 1 of 2 shares needed from group airline provide academic
    Enter a recovery share: airline provide academic agency armed salon ceiling admit rapids divorce hairy tolerate advance costume forecast shrimp golden short entrance public

    ✓ 2 of 2 shares needed from group airline provide academic
    SUCCESS!
    Your master secret is: 77d6dc4c820ca1c8d402cad5467630f9

    ```
    If you have set a passphrase: run `shamir recover -p` instead. Then you will be asked for the passphrase.
4. Run in a terminal
    ```
    node
    Welcome to Node.js v12.18.3.
    Type ".help" for more information.
    > const bip32 = require('bip32');
    undefined
    > bip32.fromSeed(Buffer.from("77d6dc4c820ca1c8d402cad5467630f9", 'hex')).toBase58();
    'xprv9s21ZrQH143K3Rf94sUXonk96w9sNriPbZNAUm4kaWHzV37yNb116WR5oZpD1rMTV8sdYgGFr1WV3atCVHAVeHkCVEh2FQ2zxkHJJ7ixCYE'
    ```

5. Open https://iancoleman.io/bip39/ and copy the this key without exclamation marks into the BIP32 Root Key Field. Generate and compare the addresses. Here are the results for the same BIP44 and BIP49 paths
    ```
    m/44'/0'/0'/0/0 	1HubpNopePj8Mt5s7cDi3ZuDqGCEw6etMX 	028583a7f48f6079cad2caea1a7fdc4a9d396b964f572bf93e48056dc8d24d1a00 	KzEs8ysV8EYFHy3VoNNKyG6GBs28HVKsN9f9HCnFQpRZSZvNC17o
    m/44'/0'/0'/0/1 	12NP6LWt9YQdiEZVdwgG9BJupxPLU7PYkG 	02315c9571681e24779b97c4e2ccb0350130c0e400570242e7fb1b8e4a78daa5a4 	Kwrnzb5u4p9FjySc9HPpszYV5h4fcXSRQUMo7zqc8w5UCmiVdaF6
    m/49'/0'/0'/0/0 	39ygxy6bZVBLWJpCYoeoX3BY4NTbjme2CF 	038912d3350c11c6dbd0d1e49f5ad9177eff9ae18e21869dcadea32dcb8c4aa09e 	L2XcrqPBRW4iFoHHfLEFAezey9moYPCVn1jk6Nf8neEMPT95ttGz
    m/49'/0'/0'/0/1 	38twR7oqfXyVzsxkkqo5gY2cQc6Yox1Fa2 	03d2acdc93ff34b602b2e184deed886a7c4c1b11d85f09755f34accc42972bb1fb 	L5LJggJFFXcg7i4bVcXQWNJqmdJ3cDfRn8BL4kqhXdniCidurNLJ
    ```

