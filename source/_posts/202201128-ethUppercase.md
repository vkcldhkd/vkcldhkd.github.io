---
title: Is Ethereum wallet address case sensitive?
date: 2022-11-28 14:02:41
tags: BlockChain
---

Ethereum wallet addresses are in hex [0-9A-F]*. While the address itself is case-insensitive (A is the same as a to the network), the case sensitivity is used as a (optional) checksum. It was built as an after-thought to an addressing scheme that lacked basic checksum validation.
https://github.com/ethereum/EIPs/issues/55#issuecomment-187159063

The checksum works like so:
lowercase address and remove 0x prefix
sha3 hash result from #1
change nth letter of address according to the nth letter of the hash:
0,1,2,3,4,5,6,7 → Lowercase
8, 9, a, b, c, d, e, f → Uppercase
So, you sha3 hash the address, and look at each Nth character of the sha result. If it’s 7 or below, the Nth character in the address is lowercase. If it is 8 or above, that character is uppercase.
(Brought from an old Ethereum forum whose link is now broken.)