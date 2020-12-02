## Script Engine

Bitcoin's script execution engine is stateless and non-turing complete for security reasons. Instructions execute from left to right as they are pushed onto the stack. Script instructions can be found here. A simple example:

`5 10 OP_ADD 2 OP_SUB 13 OP_EQUAL`
 
1. 5 is pushed onto the stack
2. 10 is pushed onto the stack
3. OP_ADD pops the top two items off the stack (10, 5) and pushes their sum onto the stack
    1. 15 is now on top of the stack

4. 2 is pushed onto the stack
5. OP_SUB pops the top two items off the stack (2, 15) and pushes the result of 15-2 onto the stack
    1. 13 is now on top of the stack
6. 13 is pushed on top of the stack
7. OP_EQUAL pops the top two items off the stack (13, 13) and checks if they are equal. If so, returns true

Bitcoin can be unlocked and spent be those who are able to solve the conditions set by the locking script. Below is a real example of a locking script from block 400,000, transaction ID 14dc8c49b8ed1ace997867f43bbd2937899b718f1e10e3bfeeb34306bac94767.
```
        "vout": [
                  {
                      "value": 0.01799640,
                      "n": 0,
                      "scriptPubKey": {
                          "asm": "OP_DUP OP_HASH160 10dc384bb8975eff3c7e6ac1baa8a4d3527e72d5 OP_EQUALVERIFY OP_CHECKSIG",
                          "hex": "76a91410dc384bb8975eff3c7e6ac1baa8a4d3527e72d588ac",
                          "reqSigs": 1,
                          "type": "pubkeyhash",
                          "addresses": [
                              "12Y9dDZWpHx5cod9QRNJwBdXxuQuGpMGRT"
                          ]
                      }

                  },
```      

The locking script stack execution instructions are in the asm field of scriptPubKey. This field contains all the data needed to set the conditions for the receiver at address 12Y9dD/ to spend this 0.0179964 bitcoin. To do this, the receiver will construct and concatenate an unlocking script to the above locking script.
```
             Unlocking Script                                       Locking Script
      ----------------------------------      ----------------------------------------------------------------
      <12Y9dD/ signature> <129YdD PubKey>  +  <OP_DUP> <OP_HASH160> <PubKeyHash> <OP_EQUALVERIFY> <OP_CHECKSIG>
```      

Once this transaction has been verified and broadcast throughout the network, 12Y9dD/ can then "spend" it by satisfying the conditions set by the sender. Following the same method for script stack execution above, we have:

1. 12Y9dD/'s digital signature is pushed onto the stack
2. 129YdD/'s public key is pushed onto the stack
3. `OP_DUP` duplicated the top item on the stack
4. `OP_HASH160` pops the top item of the stack and hashes it with `RIPEMD160(SHA256(PubKey))` and pushes that value onto the stack. Stack now looks like this:
```
              |         |
              |PubKHash |
              |PubKey   |
              |Signature|
```
5. The public key hash is pushed onto the stack. In this case we see it is `10dc384bb8975eff3c7e6ac1baa8a4d3527e72d5`
6. `OP_EQUALVERIFY` pops the top two items off of the stack and compares them then runs `OP_VERIFY`.
   1. `OP_VERIFY` marks transaction as invalid if `OP_EQUALVERIFY` doesn't return a true value. If the hash calculated from the
7. `OP_CHECKSIG` pops the top two items off the stack (PubKey and Signature), and validates that the signature and public key match and pushes TRUE onto the stack if they do.

OP codes can be found at the bitcoin wiki.

It is important to note that the above example is not a segwit example. Segwit operates more efficiently despite Roger Ver's disdain for the project. An example of segwit stack execution is coming shortly.
