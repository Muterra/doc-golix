# Muse cryptographic overview

A whole-system private container solution is required for storing static, message-based, transmissible data at rest. The data repository is a single, flat, content-addressed directory -- conceptually speaking, a hash table. For the sake of cryptographic understanding, files are never deleted. Duplicate file insertions are therefore only meaningful in the context of transmission timing attacks, which, though important, are outside the scope of this discussion.

This system should be fully self-hosting, and therefore requires an authenticatable (though not necessarily legally verifiable) digital identity system. Such authentication should be non-repudiable; however, identities should be considered potentially disposable, and a mechanism to deniably prove identity transitions is desirable. To facilitate system usability, these digital identities should themselves be stored on the system, and therefore within encrypted containers.

Finally, a key sharing mechanism must be established for access sharing between identities. It should have the capability, but not requirement, to initiate persistent keysharing sessions between identities. It should maintain forward security in the event of an identity compromise, though the circumstances of the identity compromise may imply a simultaneous compromise of message keys (if, for example, both the identity private keys and the individual message keys were externally encrypted and stored with the same master key).

Neither OTR, PGP, nor TLS fulfill these requirements, so we propose a new cryptosystem that does.

# Container definitions

The following four encrypted primitives are proposed:

1. Symmetrically-encrypted, authenticated container for arbitrary objects
2. Asymmetrically-encrypted, authenticated container for key exchange handshakes
3. Asymmetrically-encrypted, authenticated container for handshake ACKs
4. Asymmetrically-encrypted, authenticated container for handshake NAKs

Additionally, the following three network support primitives are proposed:

1. Unencrypted, authenticated static address binding
2. Unencrypted, authenticated dynamic address binding
3. Unencrypted, authenticated address binding removal

# Common header

All seven primitives share a common header, in the following order:

| Field name        | Field quick reference                                                                      |
| ----------        | ---------------------                                                                      |
| Magic number      | Classifies the object as one of the above seven primitives.                                |
| Version           | Primitive-specific specification version number.                                           |
| Address algorithm | Describes which hash algorithm should be used for content-based addressing.                |
| File hash         | The hash digest of the file.                                                               |
| Cipher suite      | Describes which of the available cipher suites should be used for cryptographic operations |
| Addressee muid    | A unique identifier for the identity of the primitive's addressee                          |
| Author signature  | Authenticates the primitive's authorship (not necessarily its addressee)                   |

### Magic number

Primitive-specific and used to unequivocally identify the primitive type. Could currently be inferred from the file itself (no two primitives are functionally interchangeable). Included in the file hash.

### Version

Primitive-specific, ciphersuite-independent semantic version number used to support future protocol revisions. Included in the file hash.

### Address algorithm

Integer-based specification of the hash algorithm used for primitive addressing. This is redundantly declared (the cipher suite declaration supersedes this field and includes specification of the hash algorithm), but any inconsistencies between file hash and cipher suite shall result in a rejected primitive. It is included to benefit network efficiency. It is excluded from the file hash.

Currently-supported address algorithms, by integer representation:

1. **0x1:** SHA-512.

### File hash

The hash digest of the file. Used for both addressing (address algorithm concatenated with the file hash results in the Muse unique identifier, or muid), and for signature-based authentication. Is constructed from the following elements in the following order, with no padding or delimiters:

1. Magic number
2. Version
3. Addressee muid
4. File remainder (all bytes following the author signature)

### Cipher suite

Integer-based specification of the file's cipher suite. Supersedes and redundantly specifies the address algorithm. Disagreement between the two fields will be implemented with full file rejection.

Currently-supported cipher suites, by integer representation:


#### **0x1:** SHA512/AES256/RSA4096.

* **Address algorithm:** 0x1 = SHA-512.
* **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. 
* **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537. 
* **Symmetric encryption:** AES-256 in CTR mode, with nonce prepended to payload ciphertext, with deterministic nonce generation (as described below).
* **Diffie-Hellman deniable exchange:** 2048-bit [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.

#### Deterministic nonce generation

CTR block mode requires counter generation to produce a unique seed for each block to avoid a repeating pad. This incrementing seed is typically implemented as a nonce, which is then concatenated with the actual counter. This nonce is often constructed from randomness, but it need not be: it must simply not be reused for any given key (or counter state must be preserved between messages, which is generally impractical).

There are three core arguments for a non-random nonce. First, as the ciphertext will affect the content-derived address, it is desirable (though not required) that the same plaintext + key combination should always result in the same ciphertext. Second, forcing a deterministic nonce may actually reduce the risk of catastrophic nonce + key combination reuse in implementation code written by cryptographically inexperienced developers, by ensuring that any given nonce + key combination can only result from identical plaintext (and therefore identical ciphertext). Third, because of the overall system structure, recreation of an identical file (from the same plaintext + key combination) should affect the system's security footprint primarily through slightly increased exposure to external timing attacks.

One weakness of this system is that a common message used by multiple authors could potentially subject that message to statistical analysis if multiple authors use the same key. As such, this reasoning is based upon the assumption that keys should be sufficiently long and sufficiently random to prevent accidental key collisions. Furthermore, because the nonce must exist prior to encryption, if it is to be deterministically generated from the plaintext, it should be constructed in such a way that it cannot leak information about the plaintext, even if the underlying content digest generation is found to be insecure.

The proposed method of deterministic nonce generation is to hash the plaintext, then encrypt a single block of that hash with the base key, using the result as the nonce for CTR mode (and reusing the base key to generate the keystream). In addition to the above, this has the desirable properties of:

* Collision resistance
* Pseudo-randomized plaintext for the block cipher, minimizing information leak from the nonce even if the counter is implemented incorrectly

### Addressee muid

The unique identifier for the primitive's addressee. For symmetric files, this is the primitive's author. For asymmetric files, this is the primitive's recipient (against whose public key the asymmetric file is encrypted). This muid can be used to look up the identity file for the addressee. It is the single piece of public metadata in primitives, and included in the file hash. As such, a malicious modification by an attacker (or even the original author) must result in a different file.

### Author signature

Provides the authentication assurance for the file by the author. For symmetric files, it is simply the signature of the file hash (*not the muid*). For asymmetric files, to protect the relationship between author and recipient against a dictionary attack, it is the signature of the file hash XORd with an asymmetrically-encrypted "inner salt".












# System concerns

## Balancing non-repudiation and deniability

In a publishable (ie potentially one-to-many) format, both non-repudiation and deniability can be desirable qualities. The Muse protocol system supports both, through the creation of ephemeral identities. Commmunications authored by any given identity are non-repudiable, but temporary identities may be created ad-hoc. Here, a DH shared secret can be used to deniably prove original identity to the conversation partner. For added privacy, this exchange can be done with a third identity (which is then immediately disposed).








------------

Particular concerns:

Signature key vs public encryption key: should the signature key for API generation (which is being encrypted asymmetrically against someone else's public key) be the public encryption key (ie, API generation uses all a single set of keys, and then all symmetric messages are signed with the other), or would it be better this way? The former is a bit more complex but I think is a better division of security concerns (the ability to generate a new API channel is roughly equivalent to an ephemeral key exchange, which means the signature on that exchange is more important than that on symmetric object files).

Deterministic nonce generation. Good idea or bad idea?

Any way to improve forgeability of temporary identities?



Symmetric is potentially one-to-many, BUT handshake generation is always one-to-one. Could use a DH -> KDF -> MAC signature there, but it would come at the cost of protocol complexity. However, it WOULD eliminate the awkward step of xoring the inner hash and nonce with the rest of it. YES, DO THIS. IT ALSO PROVIDES DOS MITIGATION. THIS IS A VERY GOOD IDEA.