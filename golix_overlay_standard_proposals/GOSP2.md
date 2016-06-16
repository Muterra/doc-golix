# WARNING: THIS DOCUMENT IS BOTH DEPRECATED AND UNDER REWRITE

# GOSP 2: Private key storage container

**Problem abstract:** In order to make digital identities (see [GOSP 1](/muse_overlay_standard_proposals/GOSP1.md)) available on multiple devices, and to enable safe Muse storage thereof, a secure private key container file is necessary.

**Goals:**

1. Once the container's GHID is known, the private keys contained within should be accessible through a single password
2. The container file must, on its own, be heavily resistant to brute-force cracking attacks
3. For usability reasons, a single container should enclose all three private keys.

**Solution TL;DR:**

1. First generate public identity container as per MOSP 1 and retain its final byte representation
2. The identity owner chooses a password for the identity. 
3. This password (utf-8) is salted with the bytes from 1.
4. A symmetric key is generated using the scrypt.hash function and the salted password from 3.
5. Pack all three private keys together, with no padding, delimiter, etc. Order them as: sign, encrypt, exchange.
6. Create the EICO using the symmetric key from 4 and payload from 5.