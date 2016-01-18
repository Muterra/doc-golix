# Current revision: 18 Jan 2016

+ Clarified spec re: salt length in signature (should match hash length = 64 bytes)
+ Added some shameless [IndieGoGo campaign](https://www.indiegogo.com/projects/ethyr-modern-encrypted-email) plugs

# Previous revisions

## 11 Dec 2015, #9a6e2ff

+ Changed all DH from 2048-bit/Group #14 to ECDH/Curve25519
+ Changed external links to reflect muterra.io and ethyr.net reorganization

## 13 Nov 2015, #10d6a60

+ Removed deterministic nonce generation in spec
+ Changed cipher suite 0x1 to SIV mode with MAC prepended to ciphertext in spec
+ Added cipher suite 0x2 as CTR mode with nonce accompanying key distribution in spec
+ Added important questions to top of readme

## 10 Nov 2015, #83eb7e5

Initial draft release.