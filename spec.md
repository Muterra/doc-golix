**WARNING:** This is a draft document dated 30 October. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

**SPECIAL WARNING:** This should not be implemented in a production environment until subjected to security reviews.

**NOTE:** This is a purely implementation document. For design discussion, see our [design document](/design_philosophy.md). For technical discussion, see our [whitepaper](/whitepaper.md). For security discussion, stay tuned for our security excerpt.

# Object container (MEOC)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .meoc

Note: optimized for faster reading than writing

**Format:**

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 45 4F 43      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 9              | 64B            | File hash           | Bytes               |
| 73             | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 74             | 65B            | Author MUID         | Bytes               |
| 139            | 512B           | Author signature    | Bytes               |
| 651            | 8B             | Payload length, *N* | Unsigned 64-bit int |
| 659            | *N*            | Private payload     | Encrypted blob      |

## 1. Magic number

ASCII "MEOC" (4D 45 4F 43)

## 2. Version number

The version number is an incrementing unsigned integer. It is not intended for human use; particular version numbers will be tagged with semantic version numbers.

**The current MEOC version is 12.**

## 3. Address algorithm

Muse may eventually need multiple hash algorithms. They are represented as an 8-bit integer immediately following the magic number. The protocol specification defines which algorithm corresponds to which integer. The 1-byte address algorithm field is prepended to the file hash to form the MUID.

Address hash algorithms, by integer representation:

1. **0x1:** SHA-512.

## 4. File hash

The hash digest of the file, using the algorithm described by the Address Algorithm field. Input to the hash function is the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Author MUID
6. Payload length
7. Encrypted payload

The file has is appended to the 1-byte address algorithm field to construct a MUID.

## 5. Cipher suite

Muse plans to support multiple cipher suites. They are represented as an 8-bit integer.

Cipher suites, by representation:

1. **0x1:** SHA512/AES256/RSA4096.  
   **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537.  
   **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537.  
   **Symmetric encryption:** AES-256 in CTR mode with nonce prepended to payload ciphertext. Nonce generation is described below.  
   **Symmetric shared secrets:** 2048-bit Diffie-Hellman [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.  
   **Key agreement from shared secret:** HKDF+SHA512. Salt is application-specific.  
   **Symmetric signatures / MAC:** HMAC+SHA512

**Quick note:** Why not elliptic curves first? Basically, development resource allocation. There was a reasonably large amount of thought put into this decision, and I stand by it. We need a prototype that works and is suitably secure for alpha testing. ECC is a very high priority for the production standard, as is a serious consideration of moving the address algorithm to a concatenation of SHA256 and SHA3 for the same input bytes. If you want to discuss this further, please [get in contact directly](mailto:badg@muterra.io).

**Quick note 2:** Why not an AE/AEAD block mode? The added complexity doesn't make sense in this application. Dynamic bindings don't use symmetric encryption, and everything else is non-malleable. There's already a hash, and a signature, on top of the existing non-malleability, for MEOC records. CTR is very simple, which makes it much harder to screw up.

## 6. Author MUID

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the author's identity (her public keys).

## 7. Author signature

This field is always an asymmetric cryptographic signature of the file hash field. The signature algorithm is defined by the cipher suite field.

## 8. Payload length

A 64-bit unsigned integer representation *N* of the length (in bytes) of the binary blob associated with the payload. If the cipher suite prepends a nonce to the ciphertext, this length will include the nonce.

## 9. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. The file terminates immediately at the end of the payload.

# Static binding (MOBS)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mobs

**Format:**

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 4F 42 53      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 9              | 64B            | File hash           | Bytes               |
| 73             | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 74             | 65B            | Binder MUID         | Bytes               |
| 139            | 512B           | Binder signature    | Bytes               |
| 651            | 65B            | Target MUID         | Bytes               |

## 1. Magic number

ASCII "MOBS" (4D 4F 42 53)

## 2. Version

See above for description. **Currently 4.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Binder MUID
6. Target MUID

The file hash is used only for the signature. It is **not** used for addressing the static binding. See above for further description.

## 5. Cipher suite

See above for description.

## 6. Binder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the agent placing the binding.

## 7. Binder signature

See Author Signature above.

## 8. Target MUID

The MUID of the MEOC resource to bind to.

# Dynamic binding (MOBD)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mobd

**Format:**

| Decimal Offset | Decimal Length | Name                    | Format              |
| ------         | ---------      | -------------------     | -----------         |
| 0              | 4B             | Magic number            | 0x 4D 4F 42 44      |
| 4              | 4B             | Version number          | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm       | Unsigned 8-bit int  |
| 9              | 64B            | File hash               | Bytes               |
| 73             | 1B             | Cipher suite            | Unsigned 8-bit int  |
| 74             | 65B            | Binder MUID             | Bytes               |
| 139            | 512B           | Binder signature        | Bytes               |
| 651            | 64B            | Dynamic hash            | Bytes               |
| 715            | 32B            | Nonce                   | Bytes               |
| 747            | 4BB            | Dynamic frame count *N* | Unsigned 32-bit int |
| 751            | 65B * *N*      | Bound MUIDs             | Bytes               |

## 1. Magic number

ASCII "MOBD" (4D 4F 42 44)

## 2. Version

See above for description. **Currently 8.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Binder MUID
6. Dynamic hash
7. Nonce
8. Dynamic frame count
9. Bound MUIDs

The file hash is used only for the signature. It is **not** used for addressing the dynamic binding. See above for further description.

## 5. Cipher suite

See above for description.

## 6. Binder MUID 

See above for description.

## 7. Binder signature

See above for description.

## 7. Dynamic hash

The MUID hash for the secondary address for the dynamic object. 

Generated from the concatenated:

1. Nonce
2. Binder MUID
3. Dynamic frame count
4. Bound MUIDs 

of the first frame in the binding, using the hash algorithm described in the Address Algorithm field. Remains static for the life of the binding. Is used to address the binding.

## 8. Nonce

This field makes it possible to have multiple dynamic bindings with the same (binder + original frames) seed. It should never be repeated for the same binder + original frames combination. It is static across the lifetime of the dynamic binding.

We recommend using a sufficiently strong random number generator to produce the nonce. The availability of the resultant dynamic address should then be verified against the binder's preferred persistence providers. The weaker the random number generator, the more important the verification.

## 9. Dynamic frame count

An integer count of the bound MUIDs. The length of the remaining record can be calculated by the frame count * 65 bytes.

## 10. Bound MUIDs

An ordered list of the MUIDs for each frame. If the dynamic binding is being used as a buffered object, the most recent frame should be first.

# Debind record (MDXX)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mdxx

**Format:**

| Decimal Offset | Decimal Length | Name                    | Format              |
| ------         | ---------      | -------------------     | -----------         |
| 0              | 4B             | Magic number            | 0x 4D 44 58 58      |
| 4              | 4B             | Version number          | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm       | Unsigned 8-bit int  |
| 9              | 64B            | File hash               | Bytes               |
| 73             | 1B             | Cipher suite            | Unsigned 8-bit int  |
| 74             | 65B            | Debinder MUID           | Bytes               |
| 139            | 512B           | Debinder signature      | Bytes               |
| 651            | 65B            | Target MUID             | Bytes               |

Debind requesters must remove:

1. Static and dynamic bindings, only at the request of the binder
2. Pipe requesters, pipe ACKs, and pipe NAKs, only at the request of their recipient

## 1. Magic number

ASCII "MDXX" (4D 44 58 58)

## 2. Version

See above for description. **Currently 4.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Debinder MUID
6. Target MUID

The file hash is used only for the signature. It is **not** used for addressing the debind statement. See above for further description.

## 5. Cipher suite

See above for description.

## 6. Debinder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the debinding agent.

## 7. Binder signature

See Author Signature above.

## 8. Target MUID

The MUID of the MEOC resource to debind. If multiple authors have bound to a particular MUID, the debind must only remove the binding associated with the agent MUID specified in the debind statement. 

For a static binding, this is the same as the MEOC's original MUID. For a dynamic binding, this is the dynamic address. For a pipe requester, pipe ACK, or pipe NAK, this is the address algorithm + file hash.

# Muse encrypted pipe request (MEPR)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mepr

**Public header format:**

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 45 50 52      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 9              | 64B            | File hash                  | Bytes               |
| 73             | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 74             | 65B            | Recipient MUID             | Bytes               |
| 139            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                 | Format      |
| ------ | --------- | -------------------  | ----------- |
| 0      | 65B       | Author MUID          | Bytes       |
| 65     | 65B       | Target MUID          | Bytes       |
| 130    | 32B       | Target symmetric key | Bytes       |

## 1. Magic number

ASCII "MEPR" (4D 45 50 52)

## 2. Version

See above for description. **Currently 9.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Recipient MUID
6. Encrypted payload

The file hash is used only for debinding and persistence retrieval.

## 5. Cipher suite

See above for description.

## 6. Recipient MUID 

The MUID of the agent against whose public key the MEPR was encrypted. If Alice is the author and she wants to open a pipe with Bob, this is Bob's MUID.

## 7. Author symmetric signature

The symmetric signature / MAC of the author. The key for the signature is derived by:

1. DHE shared secret between author and recipient
2. Expand shared secret into key material using the file hash as salt. (Ex: cipher suite 0x1 would use HKDF+SHA512)

The material for input into the signature algorithm is then constructed as:

1. Magic number
2. Version
3. Address algorithm
4. File hash
5. Cipher suite
6. Recipient MUID
7. Encrypted payload

## 8. Author MUID (private)

The MUID of the agent requesting the pipe.

## 9. Target MUID (private)

The MUID of the dynamic binding (or MEOC, but this use is not recommended) for the author's half-pipe.

## 10. Target symmetric key (private)

The encryption key for the author's half-pipe.

# Muse pipe acknowledgement (MPAK)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mpak

**Public header format:**

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 41 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 9              | 64B            | File hash                  | Bytes               |
| 73             | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 74             | 65B            | Recipient MUID             | Bytes               |
| 139            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 0      | 65B       | Author MUID            | Bytes       |
| 65     | 65B       | Requested MUID         | Bytes       |
| 130    | 32B       | Status code (optional) | Bytes       |

Pipe ACKs should only be used during pipe negotiation. They most commonly indicate a successful closure or initiation of a half-pipe. They must only be used to indicate problems incurred during pipe management, not, for example, acknowledging messages within the pipe itself.

## 1. Magic number

ASCII "MPAK" (4D 50 41 4B)

## 2. Version

See above for description. **Currently 4.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Recipient MUID
6. Encrypted payload

The file hash is used only for debinding and persistence retrieval.

## 5. Cipher suite

See above for description.

## 6. Recipient MUID 

The MUID of the agent against whose public key the MEPR was encrypted. This is the agent that requested the pipe.

## 7. Author symmetric signature

The symmetric signature / MAC of the author. The key for the signature is derived by:

1. DHE shared secret between author and recipient
2. Expand shared secret into key material using the file hash as salt. (Ex: cipher suite 0x1 would use HKDF+SHA512)

The material for input into the signature algorithm is then constructed as:

1. Magic number
2. Version
3. Address algorithm
4. File hash
5. Cipher suite
6. Recipient MUID
7. Encrypted payload

## 8. Author MUID (private)

The MUID of the agent non-acknowledging the pipe.

## 9. Requested MUID (private)

The MUID of the dynamic binding (or MEOC, but this use is not recommended) that was requested.

## 10. Status code (private, optional)

An application-specific status/exit code. Entirely optional, potentially very helpful.

# Muse pipe non-acknowledgement (NPNK)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mpnk

**Public header format:**

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 4E 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 9              | 64B            | File hash                  | Bytes               |
| 73             | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 74             | 65B            | Recipient MUID             | Bytes               |
| 139            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 0      | 65B       | Author MUID            | Bytes       |
| 65     | 65B       | Requested MUID         | Bytes       |
| 130    | 32B       | Status code (optional) | Bytes       |

Pipe non-acknowledgements (NAKs) should only be used during pipe negotiation. They most commonly indicate a rejection of the pipe request, but may also indicate an error in opening the pipe (wrong key, etc). They must only be used to indicate problems incurred during pipe management, not, for example, malformed messages within the pipe itself.

## 1. Magic number

ASCII "MPNK" (4D 50 4E 4B)

## 2. Version

See above for description. **Currently 4.**

## 3. Address algorithm

See above for description.

## 4. File hash

Constructed from the concatenation of:

1. Magic number
2. Version
3. Address algorithm
4. Cipher suite
5. Recipient MUID
6. Encrypted payload

The file hash is used only for debinding and persistence retrieval.

## 5. Cipher suite

See above for description.

## 6. Recipient MUID 

The MUID of the agent against whose public key the MEPR was encrypted. This is the agent that requested the pipe.

## 7. Author symmetric signature

The symmetric signature / MAC of the author. The key for the signature is derived by:

1. DHE shared secret between author and recipient
2. Expand shared secret into key material using the file hash as salt. (Ex: cipher suite 0x1 and address algorithm 0x1 would use HKDF+SHA512 on the SHA512 file hash)

The material for input into the signature algorithm is then constructed as:

1. Magic number
2. Version
3. Address algorithm
4. File hash
5. Cipher suite
6. Recipient MUID
7. Encrypted payload

## 8. Author MUID (private)

The MUID of the agent non-acknowledging the pipe.

## 9. Requested MUID (private)

The MUID of the dynamic binding (or MEOC, but this use is not recommended) that was requested.

## 10. Status code (private, optional)

An application-specific status/error code. Entirely optional, potentially very helpful.

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

```A``` sends bare request, or request with a MUID to indicate an update.

```B``` responds with:

+ ACK if reachable and ready
+ NAK if reachable and unready

This command is also used by ```A``` to initiate a connection with ```B```.

## Publish

```A``` sends ```B``` a Muse network object.

```A``` sends MEOC, MOBS, MOBD, MDXX, MEPR, MPAK, or MPNK

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

## Get

```A``` requests an object from ```B```.

```A``` sends MUID

```B``` responds with:

+ The object if successful
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new get command.

## Subscribe

```A``` requests to be subscribed to an address at ```B```.

```A``` sends MUID

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful 

If ```B``` receives:

+ An updated copy of a dynamic binding with the indicated dynamic MUID
+ An asymmetric request with the indicated MUID as recipient

then ```B``` starts to ping ```A``` with the MUID only (not the actual object). If unsuccessful, ```B``` *may* repeat ping until successful.

## Unsubscribe

```A``` requests to be unsubscribed from an object at ```B```.

```A``` sends MUID

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

## List subscribed addresses

```A``` requests a list of subscribed MUIDs from ```B```.

```A``` sends bare command

```B``` responds with:

+ List of MUIDs if successful
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new list command.

## List binders

```A``` requests a list of agents who have bound to a specified MUID ```B```.

```A``` sends MUID

```B``` responds with:

+ List if successful
+ NAK if unsuccessful

There is no return response from ```A```. Retries are initiated with a new list command.

## Disconnect

```A``` disconnects from ```B```, terminating all subscriptions and requests.

```A``` sends bare request

```B``` responds with:

+ ACK if successful
+ NAK if unsuccessful

```B``` should not require this from ```A```; persistence providers should also include a timeout (or other connection termination) method. This command is included to allow ```B``` to log off a particular device securely.







--------------------

--------------------

--------------------

+ Need to define required components for identity containers
+ Need to define deniable aliasing
+ Need to add nonce generation
+ Need to review whitepaper for anything I missed