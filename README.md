# dCKB contracts

This repository is a fork of [NexisDao original repository](https://github.com/NexisDao/NexisDao-core) without all the information non relevant to dCKB. Follows a bit of documentation on the scripts uses.

## [Type Lock](contracts/type_lock/src/entry.rs)

- `Type lock` is a lock script and it's deployed with this code hash: [0x8baa01f58baab0cb58fc319136ea4f6866ed59c323fa94bf7d6b72bea21c74de](https://explorer.nervos.org/script/0x8baa01f58baab0cb58fc319136ea4f6866ed59c323fa94bf7d6b72bea21c74de/data).
- This is the locks that locks the `dCKB` `NervosDAO` deposits.
- In a `dCKB` `NervosDAO` deposit transaction:

 1. The `NervosDAO` deposit type script is the always in the first cell and `Type lock` is its lock script.
 2. Its `args` is always constant: `0x010044adfd493be5af2f53688e814c52595f8675097251d3843ef41ecfcab0000c`
 3. With this argument it just requires `Dao info` to be included in the transaction.

## [Dao Info](contracts/dao_info/src/entry.rs)

- `Dao info` is a type script and it's deployed with this code hash: [0x6fb198a4ef2cc0fa63c2ef7596c169452323d8ce678bdb3f75c77dc1eac2f47f](https://explorer.nervos.org/script/0x6fb198a4ef2cc0fa63c2ef7596c169452323d8ce678bdb3f75c77dc1eac2f47f/data).
- It contains the logic regulating interactions between `dCKB` and `NervosDAO`.
- Usually it's the second cell, right after the `NervosDAO` deposit cell, which always comes first.
- It's a receipt of the deposit, its data is the deposit amount.
- Its arguments contains the type of `DepCells` containing the data about other cells, this avoids circular dependencies.
- `Dao info` unlocks by checking that its `tx hash` is the same of the deposit.
- The lock of this cell is the lock that ultimately locks the deposit:

 1. In the first revision of dCKB this lock was always a [`pwlock-k1-acpl`](<https://explorer.nervos.org/transaction/0xdd14d054d526d3659f8ee8f2defbaa06d1467ed252e8a6505faa8bf6f40e4c0e>), same as `dCKB` `UDT` cell.
 2. In the [second revision of dCKB](https://medium.com/@NexisDAO/nexis-has-already-upgraded-dckb-ccd25d6d9b0f) this lock changed to a [closed source script](<https://explorer.nervos.org/script/0xe21a856d64d311b2df0a4ecb7dcc66ebebccf5bb623a3031d26bb2455a30a72e/data>), this lock can be unlocked if the user `pwlock-k1-acpl` is used as input and output lock.

## [Udt Owner](contracts/udt_owner/src/entry.rs)

- `Udt owner` is a lock script and it's deployed with this code hash: [0x29a81473e24924e394a9148ab357c2492fedf65241848b7a87539a7db9c3d43f](https://explorer.nervos.org/script/0x29a81473e24924e394a9148ab357c2492fedf65241848b7a87539a7db9c3d43f/data).
- As the name suggest it is `dCKB`'s `SUDT owner cell`.
- Usually comes as [last cell](<https://explorer.nervos.org/transaction/0x2bbbcd82bbd53d2cf511844ded0818966258f1d4945ef0396ff5fb01f079fcd2>).
- The design is a public owner cell: one owner cell in input one in output.
- Its `args` is always constant: `0xe3e93d10fd0bf4bcf8da9dec59a51f083521b3e11a10077614b3b53b933792d60f000000`
- With this argument it just requires `Dao info` to be included in the transaction.
