**WARNING:** This is a draft document dated 28 February 2016. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining the Hypergolix [mailing list](https://www.hypergolix.com/#page-top).

**SPECIAL WARNING:** This protocol should not be used for private data in a production environment until subjected to a third-party audit. Until that time, consider all Golix information to be fully public.

**NOTE:** This is a purely implementation document. For design discussion, see our [design document](/design_philosophy.md). For technical discussion, see our [whitepaper](/whitepaper.md). For security discussion, see [here](security-whitepaper.md).

# Cipher suites and address algorithms

**Note that this is not a substitute for a proper threat model or security analysis.** Those documents have been separated from the protocol specification for readability and digestibility purposes.

## Cipher suites

By Golix integer representation:

+ **0x0:** None. Reserved for testing and development purposes.
+ **0x1:** SHA512/AES256/RSA4096.  
  **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. Salt length 64 bytes.  
  **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537.  
  **Symmetric encryption:** AES-256 in **CTR mode with nonce distributed privately with the key**  
  **Symmetric shared secrets:** Elliptic curve Diffie-Hellman using Curve25519.  
  **Key agreement from shared secret:** HKDF+SHA512. Salt with bitwise XOR of the the address components of the two parties' GHIDs.  
  **Symmetric signatures / MAC:** HMAC+SHA512
+ **0x2:** SHA512/AES256/RSA4096.  
  **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. Salt length 64 bytes.  
  **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537.  
  **Symmetric encryption:** AES-256 in **SIV mode (formally AEAD_AES_SIV_CMAC_512)**  
  **Symmetric shared secrets:** Elliptic curve Diffie-Hellman using Curve25519.  
  **Key agreement from shared secret:** HKDF+SHA512. Salt with bitwise XOR of the the address components of the two parties' GHIDs.  
  **Symmetric signatures / MAC:** HMAC+SHA512

**WARNING:** Ciphersuite **0x2** is likely to be removed and replaced in the near future. Implementations should not expect its definition to stay stable, and should instead rely upon **0x1** until future notice.

**Quick note:** Why not elliptic curves first? Basically, development resource allocation. There was a reasonably large amount of thought put into this decision, and I stand by it. We need a prototype that works and is suitably secure for alpha testing. ECC is a very high priority for the production standard. If you want to discuss this further, please [get in contact directly](mailto:badg@muterra.io). We'd like to experiment some with the **0x1** ciphersuite, and make sure integrated packaging of nonce+key works how we'd like it to, and from there we will likely replace **0x2** with curve25519/ed25519/ecies.

**Quick note 2:** Why not an AE/AEAD block mode? The added complexity doesn't make sense in this application. Dynamic bindings don't use symmetric encryption, and everything else is non-malleable. There's already a hash, and a signature, on top of the existing non-malleability, for GEOC records. CTR is very simple, which makes it much harder to screw up.

**Quick note 3:** Investigating alternative stream ciphers is on our long-term horizon, as are highly-performant (symmetric only) one-to-one pipes. For now, the best route for performant streams is to use Golix to negotiate secret keys for lower-level transport sockets (etc) directly between hardware.

### CTR vs SIV

Relevant details:

1. key/access control is separate from content containers
2. all resources are content-addressed by their hash digests
3. the protocol is transport agnostic, so traffic analysis, etc are deliberately out-of-scope

Because everything is hash addressed, and content is retained asynchronously (potentially indefinitely; note that key persistence is handled separately), we're very concerned about data deduplication. "Ideally" (ie, ignoring security, which we're obviously not doing), to minimize network clutter, every (plaintext + key) combination would result in identical ciphertext.

Of course, in practice, we need to be extremely concerned about reusing a (key + nonce) combination. Replay attacks are meaningless to this specific protocol[1] -- so the thought is, we can generate the nonce from a manipulation of the hash of the plaintext. This way an identical (key + nonce) combination will by definition result in an identical file.

[1] Without going into a ton of detail, replays for the symmetric containers (GEOCs) are indistinguishable to re-uploading the file. Because retention is handled by the bindings, which have replay protection, a replay of the container without the binding would be automatically garbage-collected by the persistence provider. If the persistence provider already has the GEOC, a second upload is meaningless. This ignores traffic analysis and the like, which again, is out-of-scope for the protocol.

**SIV mode**:

Note: in SIV mode, the MAC tag must be prepended to the ciphertext in the GEOC payload.

Advantages:

+ Network simplicity and deduplication
+ Does not rely upon strength of random number generation
+ [Well-documented](http://web.cs.ucdavis.edu/~rogaway/papers/siv.pdf); also, [good security properties](https://www.iacr.org/archive/eurocrypt2006/40040377/40040377.pdf)
+ Places no restrictions on keys (ie, compatible with reuse, ratcheting, new keys, etc)

Disadvantages:

+ Slow, and requires 2 passes
+ Relatively new, bringing the potential for buggy implementation code
+ Less availability in off-the-shelf crypto libraries
+ Doubles key size, unless using a secondary KDF

**CTR mode**:

Note: in SIV mode, the nonce should not be prepended to the ciphertext in the GEOC payload. It should be considered a functional component of the key, and must be distributed therewith. It may be random, but must never be reused for the same key. New key generation should be accompanied by new nonce generation to emphasize their equivalence and discourage accidental content duplication. Note that the nonce size must match the block size for the underlying symmetric cipher.

Advantages:

+ Fast, and may be totally precomputed
+ Compromise between deduplication and performance
+ Cryptographic simplicity
+ Library availability

Disadvantages:

+ Relies on the strength of the random number generator for birthday resistance
+ **Note that this places restrictions on keys, as the new nonce must be distributed in a new key exchange for new content.** This makes it unsuitable for API pipes.

## Address algorithms

By Golix integer representation:

+ **0x0:** None. Reserved for testing and development purposes., and bootstrapping identity containers.
+ **0x1:** SHA-512.

**Not-so-quick note:** Why not SHA-256? Two primary reasons. The first is to reduce the possibility of an accidental (or malicious) birthday attack against the hash itself. That does sound a little ridiculous, to be fair, given the sense of scale:

+ In 2013 the internet [reached approximately 4E21 bytes](https://en.wikipedia.org/wiki/Zettabyte)
+ Golix headers consume ~1kB per file. Taking this as a minimum filesize yields 4e18 possible files in 2013
+ The internet is expanding rapidly, and will [probably double in size within the next 5 years](http://www.forbes.com/sites/gilpress/2014/08/22/internet-of-things-by-the-numbers-market-estimates-and-forecasts/). 
+ Taking 2020 with 1E19 bytes as a baseline, and a 5-year doubling period (approximately 13.9% yearly growth), and extrapolating to a 2040 design life (making Golix approximately as old as the internet is today), yields 1.6E20 files
+ Combine all of that and the formula for [birthday attack probability](https://en.wikipedia.org/wiki/Birthday_attack), and you get p≈1-10^-10^-38 for 256 bits, which is just vanishingly small.

However, this kind of analysis is grossly misleading. It is wholly predicated on the strength of the hash function, and by this kind of analysis, MD5 still looks acceptable (please do *not* mistake that as an argument for MD5). Point is: giving ourselves some headroom for such a potentially large application seems prudent.

Secondly, and much less importantly, we are considering defining 0x2 as a concatenation of SHA256 and SHA3-256 (operating independently over the same input bytes; this is a whole separate discussion). This allows us some version-independent wiggle room, though we could also just reserve an extra 256 bits in the standard to accomplish the same end result.

# Identity containers (GIDC)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .gidc

Identities are contained within specially-formatted container objects. Unlike other Golix objects, identity containers do not require a binding to persist. A persistence provider should only remove an identity container under extreme circumstances; they should be considered, generally speaking, to be indefinitely persistent. However, they may be bound by dynamic bindings, and that dynamic address may be used as a proxy for the author's GHID.

Because they may have different versions, identity containers are not necessarily deterministically reproducible; rebuilding them from the base public keys without knowing what identity container version was originally used will generally result in a different author GHID. Furthermore, each individual identity container can only support a single ciphersuite.

An author using multiple author GHIDs for the same public keys may have catastrophic consequences for that author, but will leave the network unaffected. Given the difficulty of identity recreation, Golix best practices are to **always** wrap identity containers within a dynamic binding as a proxy, *even if there is only a single GIDC associated with that entity.*

The file hash of the GIDC container (or, again, its dynamic binding proxy) is the Author/Recipient GHID for all other objects.

## Format

| Decimal Offset | Decimal Length | Name                    | Format              |
| ------         | ---------      | -------------------     | -----------         |
| 0              | 4B             | Magic number            | 0x 47 49 44 43      |
| 4              | 4B             | Version number          | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite            | Unsigned 8-bit int  |
| 9              | ...            | Signature key material  | (See below)         |
| ...            | ...            | Encryption key material | (See below)         |
| ...            | ...            | Exchange key material   | (See below)         |
| ...            | 1B             | Address algorithm       | Unsigned 8-bit int  |
| ...            | 64B            | File hash               | Bytes               |

**Why not ASN.1 / DER / PEM encoding?** In a nutshell, this decision is made out of a strong aversion to upstream dependencies. To elaborate: typical DER encodings for ASN.1 key material don't support multiple key serializations. Because identity files are a collection of three public keys, this means they cannot be immediately used as-is without additional code. That leaves three options: 

1. using DER for the entire identity container, introducing an upstream dependency or downstream adaptation
2. using DER for the key material, and embedding those three blobs within another encoding
3. avoiding DER / ASN.1 entirely.

Given the very rigid Golix requirements for identity files, and the inherent simplicity of the encoding (public keys are simply large integers), it makes sense for us to define a basic, explicit, versioned container for the public keys. Furthermore, until the Golix protocol reaches maturity, we would like to strongly disincentivize reuse of Golix private keys with other cryptographic systems. In other words, explicitly breaking compatibility with existing key storage standards helps insulate Golix users from protocol and implementation vulnerabilities.

### 1. Magic number

ASCII "```GIDC```" (0x 47 49 44 43)

### 2. Version number

The version number is an incrementing unsigned integer. It is not intended for human use; particular version numbers will be tagged with semantic version numbers.

**The current GIDC version is 2.**

### 3. Cipher suite

Golix supports multiple cipher suites. They are represented as an 8-bit integer. See "Cipher suites and address algorithms" above for details. For GIDC objects, this represents the ciphersuite supported by the identity. This determines the lengths of the key material fields. Currently, identities can only support a single ciphersuite, even if those ciphersuites use the same public keys. This may change in the future.

### 4-6. Key material

Key material formatting, including length, is determined by the declared ciphersuite. Each key is used as follows:

+ **Signature key material:** the key material for object signature verification.
+ **Encryption key material:** the key material for asymmetric encryption, as used in the GARQ object.
+ **Exchange key material:** the key material for symmetric identity exchange, as used for GARQ "signatures" (key agreement for HMAC) and deniable identity aliasing.

#### Ciphersuite 1

| Offset | Length    | Name                               | Format      |
| ------ | --------- | -------------------                | ----------- |
| 9      | 512       | Signature key material (RSA-4096)  | Bytes       |
| 521    | 512       | Encryption key material (RSA-4096) | Bytes       |
| 1033   | 32        | Exchange key material (Curve25519) | Bytes       |

**Notes:**

1. RSA key material (the signature and encryption key) consists only of the RSA public modulus. The public exponent is defined within the ciphersuite, and excluded from the identity file. It must be encoded as a big-endian 4096-bit integer.
2. Curve25519 key material (the exchange key) consists of the uncompressed, unpadded concatenation of the public elliptic curve point *Q* = (*x*, *y*). Both *x* and *y* must be encoded as big-endian 128-bit integers.

#### Ciphersuite 2

**Note:** ciphersuite 2 (differs from CS1 only by using SIV mode on AES) may soon be removed from the Golix standard.

| Offset | Length    | Name                               | Format      |
| ------ | --------- | -------------------                | ----------- |
| 9      | 512       | Signature key material (RSA-4096)  | Bytes       |
| 521    | 512       | Encryption key material (RSA-4096) | Bytes       |
| 1033   | 32        | Exchange key material (Curve25519) | Bytes       |

**Notes:**

1. RSA key material (the signature and encryption key) consists only of the RSA public modulus. The public exponent is defined within the ciphersuite, and excluded from the identity file. It must be encoded as a big-endian 4096-bit integer.
2. Curve25519 key material (the exchange key) consists of the uncompressed, unpadded concatenation of the public elliptic curve point *Q* = (*x*, *y*). Both *x* and *y* must be encoded as big-endian 128-bit integers.

### 7. Address algorithm

Golix may eventually need multiple hash algorithms. They are represented as an 8-bit integer immediately following the magic number. The protocol specification defines which algorithm corresponds to which integer. The 1-byte address algorithm field is prepended to the file hash to form the GHID.

See "Cipher suites and address algorithms" above for details.

### 8. File hash

The hash digest of the file, using the algorithm described by the Address Algorithm field. For **all** Golix objects, input to the hash function is the concatenation of all previous fields. For example, in the case of a GIDC, the file hash is the concatenation of:

1. Magic number
2. Version
3. Cipher suite
4. Signature key material
5. Encryption key material
6. Exchange key material
7. Address algorithm

The file hash is appended to the 1-byte address algorithm field to construct a static GHID.

# Object container (GEOC)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .geoc

GEOCs must be stored by a persistence provider if, and only if, they are referenced in a GOBS or GOBD stored at the same persistence provider.

**Terminology:**

+ Author: the entity-agent creating the GEOC
+ Payload: all information to be protected by the container
+ Static GHID: the immutable, unique identifier for this particular object. It is always the (address algorithm + file hash) combination for that object.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 47 45 4F 43      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 9              | 65B            | Author GHID         | Bytes               |
| 74             | 8B             | Payload length, *N* | Unsigned 64-bit int |
| 82             | *N*            | Private payload     | Encrypted blob      |
| 82 + *N*       | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 83 + *N*       | 64B            | File hash           | Bytes               |
| 147 + *N*      | 512B           | Author signature    | Bytes               |

### 1. Magic number

ASCII "GEOC" (47 45 4F 43)

### 2. Version number

The version number is an incrementing unsigned integer. It is not intended for human use; particular version numbers will be tagged with semantic version numbers.

**The current GEOC version is 14.**

### 3. Cipher suite

Golix supports multiple cipher suites. They are represented as an 8-bit integer.

See "Cipher suites and address algorithms" above for details.

### 4. Author GHID

The full GHID of the GEOC object containing the author's identity (her public keys).

### 5. Payload length

A 64-bit unsigned integer representation *N* of the length (in bytes) of the binary blob associated with the payload. If the cipher suite prepends a nonce to the ciphertext, this length will include the nonce.

### 6. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. The file terminates immediately at the end of the payload.

### 7. Address algorithm

See above for description.

### 8. File hash

See above for description.

### 9. Author signature

This field is always an asymmetric cryptographic signature of the file hash field. The signature algorithm is defined by the cipher suite field.

# Static binding (GOBS)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .gobs

GOBS must be stored by a persistence provider until, and only until, they are cleared by a debinding.

**Terminology:**

+ Binder: the entity-agent creating the binding (not necessarily the same as the bound GEOC's author)
+ Target GHID: the static GHID for the bound GEOC

Note that the static GHID applies to this particular binding, not to the target.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 47 4F 42 53      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 9              | 65B            | Binder GHID         | Bytes               |
| 74             | 65B            | Target GHID         | Bytes               |
| 139            | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 140            | 64B            | File hash           | Bytes               |
| 204            | 512B           | Binder signature    | Bytes               |

### 1. Magic number

ASCII "GOBS" (47 4F 42 53)

### 2. Version

See above for description. **Currently 6.**

### 3. Cipher suite

See above for description.

### 4. Binder GHID 

The full GHID (hash algorithm byte + file hash) of the GEOC object containing the identity (the public keys) of the agent placing the binding.

### 5. Target GHID

The GHID of the resource to bind to. Valid choices are:

1. Static GEOC address
2. Dynamic GOBD address

Binding to the address prevents its removal by the persistence provider.

### 6. Address algorithm

See above for description.

### 7. File hash

The file hash is used only for the signature. See above for further description.

### 8. Binder signature

See Author Signature above.

# Dynamic binding (GOBD)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .gobd

GOBDs must be stored until, and only until, they are cleared by a debinding, or successfully updated by their binder. Like GEOC objects, external bindings may prevent debinding. In this case, the persistence provider must retain the author's debind object as it otherwise would, even if the GOBD object persists.

**Terminology:**

+ Dynamic GHID, dynamic address: The secondary address for this binding. Static for the lifetime of the binding.
+ Frame: Any particular version of the binding with this dynamic GHID.

Note that the static GHID applies to *this particular frame*.

## Format

| Decimal Offset | Decimal Length | Name                      | Format              |
| ------         | ---------      | -------------------       | -----------         |
| 0              | 4B             | Magic number              | 0x 47 4F 42 44      |
| 4              | 4B             | Version number            | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite              | Unsigned 8-bit int  |
| 9              | 65B            | Binder GHID               | Bytes               |
| 74             | 8B             | Monotonic frame counter   | Unsigned 64-bit int |
| 82             | 2B             | Target vector length *LR* | Unsigned 16-bit int |
| 84             | *LR*           | Target vector             | Bytes               |
| 84 + *LR*      | 1B             | Dynamic address algorithm | Unsigned 8-bit int  |
| 85 + *LR*      | 64B            | Dynamic hash              | Bytes               |
| 149 + *LR*     | 1B             | File address algorithm    | Unsigned 8-bit int  |
| 150 + *LR*     | 64B            | File hash                 | Bytes               |
| 214 + *LR*     | 512B           | Binder signature          | Bytes               |

### 1. Magic number

ASCII "GOBD" (47 4F 42 44)

### 2. Version

See above for description. **Currently 16.**

### 3. Cipher suite

See above for description.

### 4. Binder GHID 

See above for description.

### 5. Monotonic frame counter

A monotonically increasing counter indicating frame ordering. The counter prevents replay attacks and state synchronization discrepancies. Persistence providers must reject any attempted frame with a counter lower than the existing frame at the persister. The counter is zero-indexed, *ie* the first frame uploaded has a counter of zero.

### 6. Target vector length

The length, in bytes, of the GHID target vector included in the binding.

### 7. Target vector

A record of the recent history of the dynamic binding's target GHIDs, sorted from most recent to oldest. The first GHID in the target vector is the current target of the binding.

The GHID of the object representing the current state of the binding. The only valid targets are:

+ The static GHID of a GEOC object
+ The dynamic GHID of a GOBD object
+ The static GHID of a GIDC object

Circular references between dynamic bindings are not allowed. Persistence providers must detect any attempts to create these references, and must always reject the latest of the dynamic bindings (the one that "closes" the circular reference). If multiple closing bindings are submitted within the same individual transport request, the persistence provider should reject all of them. Persistence providers may enforce a maximum depth for chained dynamic bindings.

Note that it is impossible to have two *different* dynamic bindings with identical initial target lists. It is therefore also impossible to directly preallocate multiple dynamic "branches". Applications seeking such functionality have at least two options:

1. Create a new dynamic binding at the branch point, updating downstream references to point to the new binding where appropriate. 
2. Immediately create two dynamic bindings, with one targeting the other.
3. Create a disposable zeroth (initial) frame, constructing a target vector of a single random GHID, effectively using that GHID as a nonce. Then, update the binding with the desired (duplicate) target.

### 8. Dynamic address algorithm

See above for description. Applies to the dynamic hash.

### 9. Dynamic hash

The GHID hash for the secondary address for the dynamic object. Remains static for the life of the binding. Is used to address the binding.

Like a file hash, generated from the concatenation of the entire preceding file of the original frame in the binding using the hash algorithm described in the Address Algorithm field.

### 10. File address algorithm

See above for description. Applies to the file hash.

### 11. File hash

See above for description.

### 12. Binder signature

See above for description.

# Debind record (GDXX)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .gdxx

Note that, without chaining, binding and debinding are subject to replay attacks. Therefore, GDXXs must be stored until, and only until, they are cleared by subsequent debinding. For example (ignoring garbage collection):

1. Create object #Obj1  
   Bind #BBB1, references (and holds) #Obj1
2. Debind #XXX1, references (and clears) #BBB1
3. Debind #XXX2, references (and clears) #XXX1  
   Bind #BBB1, references (and holds) #Obj1
4. Debind #XXX3, references (and clears) #XXX2  
   Debind #XXX1, references (and clears) #BBB1
5. Debind #XXX4, references (and clears) #XXX3  
   Debind #XXX2, references (and clears) #XXX1  
   Bind #BBB1, references (and holds) #Obj1

In each case, the *most recent* set of bindings and debindings must be retained in full by the persistence provider. In other words, ignoring the initial object, the persistence provider state at the end of each exchange above would be:

1. Binding #BBB1  
   **Result: hold #Obj1**
2. Debinding #XXX1 (prevents replay of #BBB1)  
   **Result: release #Obj1**
3. Debinding #XXX2 (prevents replay of #XXX1)  
   Binding #BBB1  
   **Result: hold #Obj1**
4. Debinding #XXX3 (prevents replay of #XXX2)  
   Debinding #XXX1 (prevents replay of #BBB1)  
   **Result: release #Obj1**
5. Debinding #XXX4 (prevents replay of #XXX3)  
   Debinding #XXX2  (prevents replay of #XXX1)  
   Binding #BBB1  
   **Result: hold #Obj1**

**Terminology:**

+ Debinder: The entity-agent removing a binding.

Note that the static GHID applies to *this particular debinding*.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 47 44 58 58      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 9              | 65B            | Debinder GHID       | Bytes               |
| 74             | 65B            | Target GHID         | Bytes               |
| 139            | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 140            | 64B            | File hash           | Bytes               |
| 204            | 512B           | Debinder signature  | Bytes               |

### 1. Magic number

ASCII "GDXX" (47 44 58 58)

### 2. Version

See above for description. **Currently 9.**

### 3. Cipher suite

See above for description.

### 4. Debinder GHID 

The full GHID (hash algorithm byte + file hash) of the GEOC object containing the identity (the public keys) of the debinding agent.

### 7. Target GHID

Only one object can be debound per debinding. The target GHID is the address algorithm + file hash of the particular binding or debinding to remove. The **only** valid debind targets are:

1. Static bindings (reference the static binding's GHID, not the object's GHID)
2. Dynamic binding frames (reference the dynamic GHID, not the individual frame's GHID)
3. Debindings (reference the debinding's GHID)
4. Asymmetric requests (reference the GARQ's GHID)

### 8. Address algorithm

See above for description.

### 9. File hash

See above for description.

### 10. Debinder signature

See Binder Signature above.

# Golix encrypted asymmetric request (GARQ)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .ghid
+ Tertiary stored file extension: .garq

GARQs must be stored until, and only until, they are cleared by a debinding from their recipient.

**Terminology:**

+ Pipe: a bidirectional channel between (usually two) agents
+ Half-pipe: a unidirectional channel, from one agent to another.
+ Recipient: the entity-agent against whose public key the GARQ was encrypted. If Alice is the author and she wants to open a half-pipe to Bob, then Bob is the recipient.
+ Target: the address for the half-pipe (usually dynamic)

Note that the author is the entity-agent requesting the pipe.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 47 41 52 51      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 9              | 65B            | Recipient GHID             | Bytes               |
| 140            | 512B           | Private inner container    | Bytes               |
| 652            | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 653            | 64B            | File hash                  | Bytes               |
| 717            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                 | Format              |
| ------ | --------- | -------------------  | -----------         |
| 0      | 65B       | Author GHID          | Bytes               |
| 65     | 2B        | Payload identifier   | Bytes               |
| 67     | 2B        | Payload length, *LA* | Unsigned 16-bit int |
| 69     | *LA*      | Payload              | Bytes               |

### 1. Magic number

ASCII "GARQ" (47 41 52 51)

### 2. Version

See above for description. **Currently 12.**

### 3. Cipher suite

See above for description.

### 4. Recipient GHID 

The GHID of the recipient.

### 5. Private inner container

Encrypted asymmetrically against the public key for the recipient. Once decrypted, the plaintext contents are defined as:

1. The GHID of the GARQ author.
2. A 2-byte identifier for the payload contents
3. The length, in bytes, of the payload
4. The payload

See "Asymmetric request types" below.

**Note that certain cryptosystems (ex: RSA) may impose limits on the size of the payload.** Asymmetric requests are not intended to be used as communication channels, but rather as handshakes in their establishment.

+ RSA-OAEP [can encrypt](https://www.ietf.org/rfc/rfc2437.txt) at most ``` modulus_size - 2 - 2 * hash_size ``` bytes *total*. With the internal header overhead also accounted for, ciphersuites 1 and 2 can therefore handle a maximum payload of ```512 - 2 - 2 * 64 - 69 == 313``` bytes.

### 6. Address algorithm

See above for description.

### 7. File hash

See above for description.

### 8. Author symmetric signature

The symmetric signature / MAC of the author. The key for the signature is derived by:

1. (EC)DH shared secret between author and recipient
2. Expand shared secret into key material using the file hash as salt. (Ex: cipher suite 0x1 would use HKDF+SHA512)

The material for input into the signature algorithm is the entire preceding file (including the file hash).

## Asymmetric request types

Though the outer container of asymmetric requests is always identical, the internals may differ. The Golix standard currently defines four different objects for use in asymmetric requests:

### Pipe handshake (HS)

Pipe requests are used (primarily) to establish a dynamically-bound GEOC pipe between two Golix identities. They are really only intended to be used once between any particular entity-entity pairing; best practice is to immediately construct a *symmetric* share between the two entities, and use that to bootstrap any additional pipes.

Pipe requests are identified by the ASCII string "HS" ( 0x 48 53 ).

| Offset | Length    | Name                | Format      |
| ------ | --------- | ------------------- | ----------- |
| 69     | 65B       | Target GHID         | Bytes       |
| 134    | 1B        | Secret length, *LK* | Bytes       |
| 135    | *LK*      | Target secret       | Bytes       |

### Pipe acknowledgement (AK)

Pipe ACKs should only be used during pipe negotiation. They most commonly indicate a successful closure or initiation of a half-pipe. They must only be used to indicate problems incurred during pipe management, not, for example, acknowledging messages within the pipe itself.

Pipe acknowledgements are identified by the ASCII string "AK" ( 0x 41 4B ).

**Terminology:**

+ Requested GHID: the GHID used in the original pipe request.

Note that here, the author is the entity-agent acknowledging the pipe, not the entity that made the request. Also note that the requested GHID references the request's GHID, **not** the request's target's GHID.

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 69     | 65B       | Requested GHID         | Bytes       |
| 134    | 32B       | Status code (optional) | Bytes       |

### Pipe non-acknowledgement (NK)

Pipe non-acknowledgements (NAKs) should only be used during pipe negotiation. They most commonly indicate a rejection of the pipe request, but may also indicate an error in opening the pipe (wrong key, etc). They must only be used to indicate problems incurred during pipe management, not, for example, malformed messages within the pipe itself.

Pipe acknowledgements are identified by the ASCII string "NK" ( 0x 4E 4B ).

Note that here, the author is the entity-agent non-acknowledging the pipe, not the entity that made the request. Also note that the requested GHID references the request's GHID, **not** the request's target's GHID.

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 69     | 65B       | Requested GHID         | Bytes       |
| 134    | 32B       | Status code (optional) | Bytes       |

### Arbitrary content (0x0000)

Arbitrary asymmetric content is primarily reserved for Golix spec development purposes. It is, once again, explicitly **not** intended for general-purpose use. Application-level communication should **always** be conducted within symmetric pipes.

Pipe acknowledgements are identified by the bytestring ```0x0000```.

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 69     | *LA*      | Payload                | Bytes       |

# Ancillary binary formats

Two additional binary objects are considered network-critical and included in the Golix standard to facilitate network operations and interoperability. They are:

+ A specific serialization for secret sharing (keys, nonces, etc)
+ A specific serialization for identities (asymmetric signing, asymmetric encryption, and ECDH exchange public keys)

## Secret sharing

Secrets are generally embedded as the target secret in a GARQ pipe request. They are defined in the Golix spec to ensure implementation interoperability.

Format:

| Offset | Length    | Name                | Format              |
| ------ | --------- | ------------------- | -----------         |
| 0      | 2B        | Identifier          | ASCII "```SH```"    |
| 2      | 2B        | Version             | Unsigned 16-bit int |
| 4      | 1B        | Cipher suite        | Unsigned 8-bit int  |
| 5      | ...       | Key                 | Bytes               |
| ...    | ...       | Seed                | Bytes               |

1. **Identifier:** constant to denote a serialized secret. ASCII "```SH```" (0x 53 48)
2. **Version:** the version for the secret serialization. Current is 2.
3. **Cipher suite:** the integer representation of the cipher suite used by the corresponding GEOC object. This determines the key and seed lengths.
4. **Key:** the key material. Length is defined on a per-ciphersuite basis.
5. **Seed:** the nonce or initialization vector, if any is required by the ciphersuite. Length is defined on a per-ciphersuite basis.

### Ciphersuite 1

| Offset | Length    | Name                | Format      |
| ------ | --------- | ------------------- | ----------- |
| 5      | 32        | Key                 | Bytes       |
| 37     | 16        | Seed                | Bytes       |

### Ciphersuite 2

**Note:** ciphersuite 2 (differs from CS1 only by using SIV mode on AES) may soon be removed from the Golix standard.

| Offset | Length    | Name                | Format      |
| ------ | --------- | ------------------- | ----------- |
| 5      | 64        | Key                 | Bytes       |
| XX     | 0         | Seed                | Unused      |

# Persistence provider commands

All of the following commands must be supported by all persistence providers. Additional application-specific commands may be added, though this is not necessarily recommended. Overlay standards define their format for different transport mechanisms. All commands are bidirectional. Nomenclature used here is party ```A``` initiates (in a unidirectional architecture, the client), ```B``` responds (unidirectional server).

## ACK

```A``` acknowledges a transmission from ```B```, indicating successful completion of request. May be accompanied by a status code.

```A``` sends bare statement

```B``` does not respond

## NAK

```A``` acknowledges a transmission from ```B```, indicating unsuccessful completion of request. May be accompanied by a status code.

```A``` sends bare statement

```B``` does not respond

## Ping

```A``` queries ```B``` to determine reachability.

```A``` sends bare request

```B``` responds with:

+ ACK if reachable and ready
+ NAK if reachable and unready

This command can also used by ```A``` to initiate a connection with ```B```.

## Publish

```A``` sends ```B``` a Golix network object.

```A``` sends GIDC, GEOC, GOBS, GOBD, GDXX, GARQ

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

## Get

```A``` requests an object from ```B```.

```A``` sends GHID

```B``` responds with:

+ The object if successful
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new get command. Persistence providers should always be capable of graceful handling of requests for missing objects.

## Subscribe

```A``` requests to be subscribed to an address at ```B```.

```A``` sends GHID

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful 

If ```B``` receives:

+ An updated copy of a dynamic binding with the indicated dynamic GHID
+ An asymmetric request with the indicated GHID as recipient

then ```B``` will publish only that specific object (GOBD or GARQ) to ```A```. If unsuccessful, ```B``` *may* repeat until successful.

## Unsubscribe

```A``` requests to be unsubscribed from an object at ```B```.

```A``` sends GHID

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

## List subscribed addresses

```A``` requests a list of subscribed GHIDs from ```B```.

```A``` sends bare command

```B``` responds with:

+ List of GHIDs if successful
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new list command.

## List bindings

```A``` requests a list of bindings targeting a specified GHID ```B```.

```A``` sends GHID

```B``` responds with:

+ List if successful (may be empty)
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new list command.

If the specified GHID exists at ```B```, then ```B``` must return successfully, even if there are no bindings for that GHID, or if the target is an invalid target for bindings. The list returned must be ordered as follows:

1. Static bindings must be first (leftmost)
2. Dynamic bindings follow (rightmost)
3. For any of the above two sets, bindings placed by the author must come first (leftmost)
4. No other order is defined.

## Query debinding

```A``` requests to know if a specified binding GHID ```B``` has been targeted for debinding.

```A``` sends GHID

```B``` responds with:

+ GHID of debinding if successful and binding exists
+ Null object if successful and binding does not exist
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new list command.

## Disconnect

```A``` disconnects from ```B```, terminating all subscriptions and requests.

```A``` sends bare request

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

```B``` should not require this from ```A```; persistence providers should also include a timeout (or other connection termination) method. This command is included to allow ```B``` to log off a particular device securely.

# Object verification

The following section outlines the order of operations while verifying objects, and the conditions under which Golix objects are considered valid. Items with strict execution order are numbered.

Note that in all cases, if a Golix object is successfully received, verified, and stored, a persistence provider **must** issue an ACK, even if the persistence provider already had a copy of the object.

## GIDC order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + Verify valid signature public key
    + Verify valid encryption public key
    + Verify valid exchange public key
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
5. Verify EOF reached

## GEOC order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + Author retrieval
        1. Verify author exists at persistence provider
        2. Retrieve author's identity file
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
5. Verify signature
6. Verify EOF reached

## GOBS order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + GOBS status check: verify no debinding exists for the binding's resultant ```GHID```
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
5. Verify signature
6. Verify EOF reached

## GOBD order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + GOBD status check: verify no debinding exists for the binding's dynamic ```GHID```
    + GOBD consistency check: 
        + if dynamic address **does not exist** at persistence provider...
            1. Verify binding has no history (*ie*, it's new)
            2. Verify dynamic address algorithm
            3. Verify dynamic hash
        + else...
            1. Verify existing binder matches uploaded (new) binder
            2. Verify existing frame ```GHID``` is included in list of historical ```GHID```s for the uploaded (new) binding
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
    + Digest remaining file
        1. Verify frame address algorithm
        2. Verify frame hash
5. Verify signature
6. Verify EOF reached

## GDXX order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + GDXX status check: verify no debinding exists for the debinding's resultant ```GHID```
    + GDXX reference check
        1. Verify target GHID exists at persistence provider
        2. Verify debinder GHID matches binder GHID (for GOBS, GOBD, GDXX) or recipient GHID (for GARQ)
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
5. Verify signature
6. Verify EOF reached

## GARQ order of verification

**Public (server/persister) verification:**

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. (The following steps may be done in parallel)
    + Verify recipient exists at persistence provider
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
5. Verify EOF reached

**Privileged (client/recipient) verification:**

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Digest remaining file
    1. Verify address algorithm
    2. Verify file hash
5. Verify EOF reached
6. Decrypt payload
7. Author verification
    1. Verify author exists
    2. Retrieve author's identity file
8. Perform symmetric signature key agreement procedure (see "Author symmetric signature" above)
9. Verify author symmetric signature