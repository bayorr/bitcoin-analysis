
## Alternative Transactions

Interesting and useful transaction types exist within the bitcoin protocol but are often unused. Indeed, bitcoin's script allows one to set certain types of conditions for payments. A few interesting ones are explained below.

#### Multisignature
   1. Requires n number of signatures to spend so long as n < 15
        Ideally used for businesses/board controlled funds
        Process is simplified with P2SH

#### P2SH - Pay-to-script-hash
   1. Designed to make complex payments such as multisignature payments as simple as regular transactions
   2. Complex locking script is replaced with a its hash
   3. To spend it further, user must create a script that will match the hashCheck Lock Time Verify
   4. Most common type of transaction script

#### CLTV - Check-lock-time-verify
#### Relative Timelocks
#### Relative Timelocks with nSequence

## Multisignature Transactions

Multisignature transactions formatted in the following format are more and more rare everyday as wallet software developers upgrade to the use of P2SH for multisignature. But in the even you see it on the blockchain, this is what a multisignature locking script looks like.

`M <PubKey 1> <PubKey 2> <Pubkey 3> N OP_CHECKMULTISIG`

Where M _is the required number required to meet the threshold of required signatures in the group, _N. Say you and your two business partners agreed on a scheme where two of the three of you had to concede to use funds. You would then substitute m _and _n appropriately.

`2 <PubKey 1> <PubKey 2> <Pubkey 3> 3 OP_CHECKMULTISIG`

You can imagine that this could get cumbersome for the network quite quickly given the byte length of public keys and signatures once the above locking script is combined with the locking script.

```
     Unlocking Script                             Locking Script
--------------------------   +   -----------------------------------------------------
<signature 1> <Signature 2>      2 <PubKey 1> <PubKey 2> <Pubkey 3> 3 OP_CHECKMULTISIG
```

Because the length of transaction scripts can get ridiculously long, these aren't used so often anymore. Fortunately a new method has been developed and will be explored below in the P2SH.

_NOTE: There is a bug in the consensus rules regarding multisig scripts. OP_CHECKMULTISIG will pop an extra value off the stack at execution. In this case it pops off M+1. The extra item is discarded and this causes no error so long as there is an item in the stack. The simple workaround is to add a zero to the beginning of the unlocking script so it has something to pop._

 `0 <signature 1> <Signature 2>      2 <PubKey 1> <PubKey 2> <Pubkey 3> 3 OP_CHECKMULTISIG`

An example of how this is done is provided by Gavin Andresen on his github. As you can see in line 14 of his code, the redeem script hex for only three public keys is quite long.

`52410491bba2510912a5bd37da1fb5b1673010e43d2c6d812c514e91bfa9f2eb129e1c183329db55bd868e209aac2fbc02cb33d98fe74bf23f0c235d6126b1d8334f864104865c40293a680cb9c020e7b1e106d8c1916d3cef99aa431a56d253e69256dac09ef122b1a986818a7cb624532f062c1d1f8722084861c5c3291ccffef4ec687441048d2455d2403e08708fc1f556002f1b6cd83f992d085097f9974ab08a28838f07896fbab08f39495e15fa6fad6edbfb1e754e35fa1c7844c41f322a1863d4621353ae`

This hexdump includes the script stack execution codes that have been presented. You find them where they should be.
```
    52: OP_2:                the number 2 is pushed onto stack
    41: OP_DATA_0x41:        uncompressed pub key (65 Bytes)
        0491BBA2510912A5:BD37DA1FB5B16730
        10E43D2C6D812C51:4E91BFA9F2EB129E
        1C183329DB55BD86:8E209AAC2FBC02CB
        33D98FE74BF23F0C:235D6126B1D8334F
        86
        MultiSig's uncompressed Public Key (X9.63 form)
        corresponding bitcoin address is:    139FpKh63Vn4Y73ijtyqq8A6XESH8brxqs
    41: OP_DATA_0x41:        uncompressed pub key (65 Bytes)
        04865C40293A680C:B9C020E7B1E106D8
        C1916D3CEF99AA43:1A56D253E69256DA
        C09EF122B1A98681:8A7CB624532F062C
        1D1F8722084861C5:C3291CCFFEF4EC68
        74
        MultiSig's uncompressed Public Key (X9.63 form)
        corresponding bitcoin address is:    1PNvbXZFysxvx3252w9JHMa7zbG95snqnm
    41: OP_DATA_0x41:        uncompressed pub key (65 Bytes)
        048D2455D2403E08:708FC1F556002F1B
        6CD83F992D085097:F9974AB08A28838F
        07896FBAB08F3949:5E15FA6FAD6EDBFB
        1E754E35FA1C7844:C41F322A1863D462
        13
        MultiSig's uncompressed Public Key (X9.63 form)
        corresponding bitcoin address is:    1jqo3ptYSnUhCJq75MMyRuwC2zNyQqRy3
    53: OP_3:                the number 3 is pushed onto stack
        ##### --> 2-of-3 Multisig
    AE: OP_CHECKMULTISIG:    terminating multisig

        corresponding bitcoin address is:    3QJmV3qfvL9SuYo34YihAf3sRCW3qSinyC
```
The above output is a breakdown of the data. A big thanks to pebwindkraft at stackexchange.com for helping me out with this!

## P2SH

Building your transactions from the command line or other script friendly environments can unlock use cases for bitcoin that you are probably unaware of. Before jumping in, there are a few things to be aware of.

   1. base58 encoding scheme was created for bitcoin. It rids the character set of ASCII characters that look similar as the number 0 and the letter O.
   2. A user's "balance" is actually a concept created by bitcoin wallet software. In fact, the wallet doesn't store bitcoin in the tradition sense. It continuously scans the blockchain for unspent transaction outputs (UTXO) with unlocking conditions that can be met by the user's private keys.
