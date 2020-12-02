## The Open Ledger

Nowadays you don't need to use a cumbersome bitcoin-cli to move around the ledger. The infrastructure is very much built out and there are a plethora of easy websites that can let you view ledger transactions. In fact, most vendors that do take bitcoin or another crypto with a public blockchain ledger will usually give you the transaction ID as a receipt.

Before getting to comfortable with python, I recommend spending a some time exploring the blockchain with the bitcoin-cli program for sake of remaining versatile. Below are some sample outputs and inputs of basic usage:

A list of original API commands can be found here. Moving around the blockchain is pretty straight forward. Remember that each function call from bitcoin-cli can appended with the 'help' argument for further usage instruction.

_Note: If you do not want to run a full node but would like to creep around the blockchain, check out the good work done by the folks at Chain Query._

Moving aroun the blockchain is pretty simple and Chain Query's template allows you to do it as if you were running your own node. Their webpage also displays a copy of the API call with explanations of what each of the JSON items is. Try going through the following commands and see if you get the same output:

1. getblockchaininfo
    1. Notice the displayed content. What can you learn about the status of the blockchain?
2. getblockhash
    1. Use block number 320654. This will return a hash.
3. getblock
    1. Use the hash of 320654.

You should see the following:
```
{
    "result": {
        "hash": "00000000000000000bb166ed4b41c86e7fd309563fee60cc4511739df35027ed",
        "confirmations": 175260,
        "strippedsize": 273541,
        "size": 273541,
        "weight": 1094164,
        "height": 320654,
        "version": 2,
        "versionHex": "00000002",
        "merkleroot": "242e37766f1b74cc7b791e0a71aeac8d8b10a5f8f59ced21835ab30aff59f6d1",
        "tx": [
            "6bfc994646886058033b66121a19d9d6cddad32d3c52762302e3f09e384e8dce",
            "3e974fd112592b11a9699e579680d812f13b0203f4f17fb5731dd75473b9eec6",
        /***** LOTS OF MORE TRANSACTIONS *****/
            "3ac43b2fb5c31bde565a7a3ef1b53173246fb650d968da822d8d3f5d45907fee",
            "3acb456c858e2ae3dfcaae2b047e06a7f7d56c0e590656dad2aff6b231b5e235"
        ],
        "time": 1410696920,
        "mediantime": 1410694603,
        "nonce": 2957710210,
        "bits": "1824dbe9",
        "difficulty": 29829733124.04042,
        "chainwork": "00000000000000000000000000000000000000000001951817617647bc0ec800",
        "previousblockhash": "000000000000000004fe950dc8a0a5224e40c414bca4f3c00351e135a2b5c647",
        "nextblockhash": "00000000000000000a7e7b783eb7745a0b7bcc8ccaa8024ff34a677bb1ae8db0"
    },
    "error": null,
    "id": null
}
```
The large data block in the middle are all the transactions (tx) that were included and verified in this block. We can see that as of this writing, this block has been confirmed 175260 times. We can explore individual transactions by using the getrawtransaction command to get the transaction hash. Then we can use getrawtransaction's output as input for the decoderawtransaction, which will give us all the information of that transaction. Using the first transaction in this block, (6bfc994...) as an argument for getrawtransaction, and the returned hash for decoderawtransaction, you should get:

#### Miner reward:
```
{
    "result": {
        "txid": "6bfc994646886058033b66121a19d9d6cddad32d3c52762302e3f09e384e8dce",
        "hash": "6bfc994646886058033b66121a19d9d6cddad32d3c52762302e3f09e384e8dce",
        "version": 1,
        "size": 157,
        "vsize": 157,
        "locktime": 0,
        "vin": [
            {
                "coinbase": "038ee404062f503253482f04da8615540827d5df4173c700002e522cfabe6d6d4f795fa04a50546a2cef73a1b478fcc8807fe10efee6947c46bc5632ee37095f0400000000000000",
                "sequence": 0
            }
        ],
        "vout": [
            {
                "value": 25.03987421,
                "n": 0,
                "scriptPubKey": {
                    "asm": "OP_DUP OP_HASH160 80ad90d403581fa3bf46086a91b2d9d4125db6c1 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex": "76a91480ad90d403581fa3bf46086a91b2d9d4125db6c188ac",
                    "reqSigs": 1,
                    "type": "pubkeyhash",
                    "addresses": [
                        "1CjPR7Z5ZSyWk6WtXvSFgkptmpoi4UM9BC"
                    ]
                }
            }
        ]
    },
    "error": null,
    "id": null
}
```
This seems like a lot to look at, but take it slow and read the explanation output from Chain Query's website if you must. The above JSON shows no BTC value input under "vin" and only one output under "vout" (value in and value out, respectively). The first transaction in a block is the miner reward for verifying the block. This is the heart of "bitcoin mining". The miner who first verified the transactions in the previous block is rewarded with, at this time, ~25 bitcoins.

#### "Normal" Transaction ouput (From block 495914):
```
{
    "result": {
        "txid": "f0ca474f7cb0667b6b3eb885e9f46f2616ed2eae6d81786f7bf8052ad2db10a1",
        "hash": "f0ca474f7cb0667b6b3eb885e9f46f2616ed2eae6d81786f7bf8052ad2db10a1",
        "version": 1,
        "size": 225,
        "vsize": 225,
        "locktime": 0,
        "vin": [
            {
                "txid": "8e60564d3c02ab9536e6c2ead088724a4530042e5d525fc93437ff9319bc16d9",
                "vout": 1,
                "scriptSig": {
                    "asm": "30440220046c461c16fed3a8bef66e6ce8d38e816ac4c1007d6e5c85800e124821785f2f02202b9c1688b0e583c73f5b80e5c356266a980757ea35001112b0fb0681d148c0f8[ALL] 02227cedfab55d1b7642d47a5ac92638ed8822a23c3ddadf88defea45a37f5935e",
                    "hex": "4730440220046c461c16fed3a8bef66e6ce8d38e816ac4c1007d6e5c85800e124821785f2f02202b9c1688b0e583c73f5b80e5c356266a980757ea35001112b0fb0681d148c0f8012102227cedfab55d1b7642d47a5ac92638ed8822a23c3ddadf88defea45a37f5935e"
                },
                "sequence": 4294967295
            }
        ],
        "vout": [
            {
                "value": 0.15160000,
                &quoquot;n": 0,
                "scriptPubKey": {
                    "asm": "OP_DUP OP_HASH160 8728bb23b570e07c9c2333a3e0fdd36b2989250c OP_EQUALVERIFY OP_CHECKSIG",
                    "hex": "76a9148728bb23b570e07c9c2333a3e0fdd36b2989250c88ac",
                    "reqSigs": 1,
                    "type": "pubkeyhash",
                    "addresses": [
                        "1DKf1wNughSBmMV2q6bgzcMSKVCfTR6HN2"
                    ]
                }
            },
            {
                "value": 20.55025980,
                "n": 1,
                "scriptPubKey": {
                    "asm": "OP_DUP OP_HASH160 b08f46e4d21cd0547a8a1e2e43e5440284f710a4 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex": "76a914b08f46e4d21cd0547a8a1e2e43e5440284f710a488ac",
                    "reqSigs": 1,
                    "type": "pubkeyhash",
                    "addresses": [
                        "1H6ZZpRmMnrw8ytepV3BYwMjYYnEkWDqVP"
                    ]
                }
            }
        ]
    },
    "error": null,
    "id": null
}
```
This JSON shows a more casual transaction. Notice there are two different output values under "vout"; ("n": 0 and "n": 1). This is because everytime you send bitcoin to an address, you are actually sending all the value, and are then sent back the remaining balance. This type of transaction is akin to giving a 10 dollar bill for an item that costs 2 dollars are receiving 8 dollars in return instead of dividing up your bitcoin to find out the exact change. Bitcoin is, by nature, indivisible.

#### Now check this shit out...

This is the public and decentralized ledger that is all the hype surrounding crypto currency. We can work our way backwards through the blockchain and trace bitcoin expenditure (technically, the change of private keys and conditions that can further set future conditions for the defined bitcoin, but that is for a different article). The above transaction uses several inputs in "vin". The first being the transaction ID of the previous transaction of this bitcoin ( "vin": [ {"txid": 8e60564d3c0...} ] ). We can use this transaction ID as an argument for getrawtransaction and then the subsequent hash for decoding with decoderawtransaction, we can trace this bitcoin on the public ledger. Below is the JSON output:
```
{
    "result": {
        "txid": "8e60564d3c02ab9536e6c2ead088724a4530042e5d525fc93437ff9319bc16d9",
        "hash": "8e60564d3c02ab9536e6c2ead088724a4530042e5d525fc93437ff9319bc16d9",
        "version": 1,
        "size": 225,
        "vsize": 225,
        "locktime": 0,
        "vin": [
            {
                "txid": "a2a28d83ed903f12dcc87318bba3f3c56d78c8c5690dfa1419540ead055c7123",
                "vout": 1,
                "scriptSig": {
                    "asm": "304402200a068e6e9f6ba55819a55ab9590a6191db0698515a199516a66e23e055d7e1cd02207170f9a205d96a50e73052fc9ced10b624041c4f345ee86bca477e8e2fe155d8[ALL] 02227cedfab55d1b7642d47a5ac92638ed8822a23c3ddadf88defea45a37f5935e",
                    "hex": "47304402200a068e6e9f6ba55819a55ab9590a6191db0698515a199516a66e23e055d7e1cd02207170f9a205d96a50e73052fc9ced10b624041c4f345ee86bca477e8e2fe155d8012102227cedfab55d1b7642d47a5ac92638ed8822a23c3ddadf88defea45a37f5935e"
                },
                "sequence": 4294967295
            }
        ],
        "vout": [
            {
                "value": 0.03750000,
                "n": 0,
                "scriptPubKey": {
                    "asm": "OP_DUP OP_HASH160 ce4160167c1bb5c0d9f2fee21ddff63cd06a2c33 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex": "76a914ce4160167c1bb5c0d9f2fee21ddff63cd06a2c3388ac",
                    "reqSigs": 1,
                    "type": "pubkeyhash",
                    "addresses": [
                        "1KoaWsnJqCMmTkLQ1SYQ2Y7K8HYVxWpY1f"
                    ]
                }
            },
            {
                "value": 20.70335980,
                "n": 1,
                "scriptPubKey": {
                    "asm": "OP_DUP OP_HASH160 b08f46e4d21cd0547a8a1e2e43e5440284f710a4 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex": "76a914b08f46e4d21cd0547a8a1e2e43e5440284f710a488ac",
                    "reqSigs": 1,
                    "type": "pubkeyhash",
                    "addresses": [
                        "1H6ZZpRmMnrw8ytepV3BYwMjYYnEkWDqVP"
                    ]
                }
            }
        ],
        "hex": "010000000123715c05ad0e541914fa0d69c5c8786dc5f3a3bb1873c8dc123f90ed838da2a2010000006a47304402200a068e6e9f6ba55819a55ab9590a6191db0698515a199516a66e23e055d7e1cd02207170f9a205d96a50e73052fc9ced10b624041c4f345ee86bca477e8e2fe155d8012102227cedfab55d1b7642d47a5ac92638ed8822a23c3ddadf88defea45a37f5935effffffff0270383900000000001976a914ce4160167c1bb5c0d9f2fee21ddff63cd06a2c3388acecd1667b000000001976a914b08f46e4d21cd0547a8a1e2e43e5440284f710a488ac00000000",
        "blockhash": "0000000000000000008bcaea5ed4fce8226e0b9fed50529968c6fe158e58f0bf",
        "confirmations": 13,
        "time": 1511538990,
        "blocktime": 1511538990
    },
    "error": null,
    "id": null
}
```
From the ledger, we see that the owner of this bitcoin (1H6ZZpRmMnrw8ytepV3BYwMjYYnEkWDqVP) had 20.7408598 bitcoin (vout "n":0 + "n":1). He then sent 0.0375 BTC to 1KoaWsnJqCMmTkLQ1SYQ2Y7K8HYVxWpY1f and recieved 20.7033598 back (vout "n":1). This transaction was then used as the input for transaction ("f0ca474f7cb0...") and shows that he then sent 0.15160 BTC to address _1DKf1wNughSBmMV2q6bgzcMSKVCfTR6HN2 _and received 20.5502598 BTC in return.
