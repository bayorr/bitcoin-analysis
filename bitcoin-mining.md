## Bitcoin Mining

Most of the internet deduces cryptocurrency mining to the reader by saying that the network generated bitcoin by "solving complex math problems", and leaves it at that. In reality, there is a bit more (or less) going on, which I will explain here with a real blockchain example.

To understand mining, one must first understand the process of encryption and hash algorithms. In a sentence, bitcoin mining is accomplished by constructing a block of transactions and then creating a hash output that meets a certain requirement using the SHA256 hash algorithm. The steps to mine bitcoin are basically the following:

1. Construct a block of transactions from the mempool
2. Create a block header
3. Find a hash that meets the target/difficulty
4. When found, inform the network
5. repeat

Using the same block from our example in Creeping Around the Blockchain, we can see all the inputs and rules that were used to create this block, and proof of the computational work (hence, proof-of-work) that was required to generate the blockhash.
```
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
```
A block head is simply a hex-encoded string that contains the following data:

1. 4 bytes: Bitcoin-core version (versionHex)
2. 32 bytes: Previous block header hash (previous blockhash)
3. 32 bytes: A merkle tree hash of all the transactions (merkle root)
4. 4 bytes: Creation time of block in seconds from Unix Epoch (time)
5. 4 bytes: Difficulty target for block (bits)
6. 4 bytes: Value used to meet hash requirement (nonce)

These six inputs are hex encoded and put into little endian format. So we are left with:

1. 0x02000000
2. 0x47c6b5a235e15103c0f3a4bc14c4404e22a5a0c80d95fe040000000000000000 (prev)
3. 0xd1f659ff0ab35a8321ed9cf5f8a5108b8dacae710a1e797bcc741b6f76372e24 (merkle)
4. 1410696920 ---hex/little endian---> 0xd8861545 ‭(time)
5. 0xe9db2418
6. 2957710210 ---hex/little endian---> ‭0x82134bb0 (nonce)

Put together we can construct: 
`0x0200000047c6b5a235e15103c0f3a4bc14c4404e22a5a0c80d95fe040000000000000000d1f659ff0ab35a8321ed9cf5f8a5108b8dacae710a1e797bcc741b6f76372e24d8861554e9db241882134bb0`

We then run this header through the sha256 algorithm twice to find the block header hash. If it meets the target (being a low enough value) it will be broadcast to the network and accepted by other nodes.
```python
from hashlib import sha256
from binascii import unhexlify
header_320654 = unhexlify("0200000047c6b5a235e15103c0f3a4bc14c4404e22a5a0c80d95fe040000000000000000d1f659ff0ab35a8321ed9cf5f8a5108b8dacae710a1e797bcc741b6f76372e24d8861554e9db241882134bb0")
sha256(sha256(header_320654).digest()).hexdigest()
```
This will yield:`ed2750f39d731145cc60ee3f5609d37f6ec8414bed66b10b0000000000000000`
          
In reverse byte order we get the value: `00000000000000000bb166ed4b41c86e7fd309563fee60cc4511739df35027ed`.
If we want to get really specific, the blockchain is actually a chain of blockheaders that are assigned to blocks of transaction data. The networks nodes ability to quickly retrieve transaction data from the block header makes this possible.

### Block Header Input Details:

1. version/version hex: Pretty self explanatory. In older blocks you will see this in little-endian format (least significant bytes first). This is used to track software upgrades.
2. time:For a quick conversion of epoch time to gregorian. The gregorian time of the above example is Sunday September 14, 2014 12:15:20 PM (GMT). The Unix Epoch Time is the number of seconds that have elapsed since 1 january, 1970 (midnight UTC).
3. previousblockhash:In order to make this a chain, the latest block must be connected. This is done by using the previous block hash as an input.
4. merkleroot:A merkle tree/root is a type of data structure commonly used in computer science. In the case of the blockchain, it works by hashing all transactions in a "bottom and up" approach and then pairing them, and then repeating this process until we are left with one hash. A merkle tree data structure is more easily imagined by a tree root system, with the root branches being data points and the tree stump being the final merkle root hash as seen in the image below.
    see https://ezpsychedelic.gitbooks.io/the-n00b-nerd/content/bitcoins-and-shitcoins/mining.html
    
Imagine the bottom layer (blocks A through H) as individual transactions. A hash for each transaction is generated and then these are paired together and the hash of the pair is generated. These hashes then each contain two transactions. These are then paired and the processes is repeated until we are left with one hash which was derived from all the transactions in the block. Each hash is generated with the SHA256 algorithm into a 32-byte hash.
Obviously for this to work, we need an even number of transactions. In the event that there is an uneven number of transactions, the last transaction is duplicated as seen above (G2).
This is an efficient way to group transaction data together since a node only needs to produce log2(n) 32-byte hashes to prove that a transaction is included in a block.
1. difficulty:The difficulty requirement is encoded in hexadecimal notation. The first byte is the exponent value (exponent) and the remaining bytes are the coefficient value (hex_coefficient). The difficulty can be derived from the following formula:
   1.difficulty = hex_coefficient * 2**(8 * (exponent - 3))
So for this block, the difficulty target can be found by replacing the values with their respective hexadecimal representations:
   2.difficulty = 0x24dbe9 * 2**(8 * (0x18 - 3))
The first byte in 'bits' tells us that an accepted hash should be less than 24 bytes long because 0x18 = 24 in decimal. Given that the header hash is 32 bytes long, this means that an acceptable hash must have at least 8 bytes of zeros in the front. You will commonly here and read on the internet that mining bitcoin is akin to solving complex mathematical problems. This is not technically correct. It is more akin to sampling through a lot of lottery tickets before finding a winning hash.
2. nonce:This is an integral part of the proof-of-work concept. The best way to think of the nonce is the number of attempts the miner had before finding an appropriate hash that meets the difficulty target. In the above example, the block hash generated was: `00000000000000000bb166ed4b41c86e7fd309563fee60cc4511739df35027ed`

The nonce value tells us that the miner who mined this block cycled through 2,957,710,210 different salt values, changing the salt increment value with each iteration of a loop before finding a hash that met the target. Without doing the math, we can approximate that at the time this block was mined the target must have been around the value of `00000000000000000c0000000000000000000000000000000000000000000000`, meaning that a hash with a lower value had to be found.

Hopefully you are able to put this all together to understand what mining bitcoing really means. If you don't understand how hashing works in general, there are multiple 3-5 minute youtube videos that explain the concept. You should now clearly see that bitcoins are not generated by simply "solving complex math problems". They are made by verifying recent transactions using unique cryptographic features. There is a difficulty set on the network that changes every 2016 blocks (~2 weeks) to accomodate increasing or decreasing network power. Just as it is more difficult to strike gold the more miners there are in an area, the more difficult it is to find bitcoin the more computing power there is looking for it. Bitcoin mining is then, well, more akin to actual resource mining than it is to solving math.
