**WARNING:** This is a draft document dated 29 October. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

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

The file hash is used only for debinding.

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

The file hash is used only for debinding.

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

The file hash is used only for debinding.

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

An application-specific status/error code. Entirely optional, potentially very helpful.




























+ Need to define command flow.
+ Need to define required persistence provider commands
+ Need to define required components for identity containers
+ Need to define deniable aliasing
+ Need to add nonce generation
+ Need to review whitepaper for anything I missed


--------------------
# Everything below this line is (probably) wrong










## Service layer

During handshake negotiation, Muse APIs create two unidirectional pipes. These two pipes may then be used to create an arbitrary number of secondary pipes between the two parties.

**Generic API handshake:**

1. Client creates 'hello' eico for upload pipe
2. Client creates dynamic binding for 'hello' eico as upstream API pipe
3. Client create EIAR for dynamic pipe
4. Client pushes dynamic pipe, EICO over transport *as a single message*
5. Client pushes EIAR as *separate* message (for security reasons)
6. Server creates 'hello' eico for download pipe
7. Server creates dynamic binding for 'hello' eico as downstream API pipe
8. Server creates EIAR for dynamic pipe
9. Server pushes dynamic pipe, EICO over transport *as a single message*
10. Server pushes EIAR as *separate* message (for security reasons)

Note that in most cases, it's a good idea for the client to **immediately** update the upstream pipe with an ACK to signify successful completion of 10. However, this is *not* a requirement and will vary from API to API.

**Handshake excerpt where something goes wrong:**

5. Client pushes EIAR
6. Server pushes EINK

**Updating dynamic API pipe:**

1. Client creates new EICO frame
2. Client updates dynamic binding
3. Client pushes both over transport as single message
4. Server responds with ACK

**Closing the API session:**

1. Client pushes debind for upstream API pipe
2. Server acknowledges with debind request for downstream API pipe
3. Client responds with ACK 

### Storage providers

As the mediator between the transport layer and the Muse service layer, storage providers can expose both a physical and EIC API. The physical API requires a single bidirectional pipe and is a simple, unprotected pub/sub bytestream.

**Prerequisites:**

1. Establish transport mechanism. Should be single-purpose (ex: specific TCP/IP socket, single URL for http support, etc) and support messages (or otherwise-delimited transmissions)

**Storage providers are unique on a Muse network in that they *must* at some level operate outside of an EIC pipe.** However, for increased security, the storage provider API may be nested within an EIC pipe. This allows onion routing protocols to exist natively (and coexist with traditional point-to-point routing as well).


Required commands & messages:

+ Publish
+ Get
+ Subscribe
+ Unsub
+ Ack
+ Nak
+ List subscriptions
+ List bindings for MUID

Subscribe behavior: let's say I subscribe to MUID123. The subscriber should expect the following behavior:

1. SP will send client the MUID of any available resource with MUID123 as the public recipient;
2. SP will send client the MUID of any available resource with MUID123 as the public author;
3. SP will send client MUID123 of any modified dynamic binding address MUID123;
4. Except if MUID123 was received from client, in which case, send nothing.

These three cases should be differentiated in their return by a 1-byte ASCII control string prefix.





**Notes:**

+ (At some point there will need to be an SP discovery protocol)
+ (At some point there will need to be a definition of the transport interface -- aka what criteria a transport mechanism must satisfy to support the muse protocol)
+ SP "peering" agreements are handled independently of the protocol.
+ This works on an unsecured bytestream, including those with relays.

**EIC API:**

Note that the EIC API uses eic-formatted debind, bind, and EIAR requests so that a storage provider can broadcast the message to a mesh storage system.



Storage providers are a bidirectional API. Both endpoints are treated by the other as a storage provider.

USE WEBSOCKETS WHEN USING THE INTERNET AS A TRANSPORT MECHANISM.

Only APIs that require "on-the-wire" (as opposed to purely EIC-based) commands.

Everything should be signed, even query/head/get requests, to mitigate (d)dos attacks. If you're looking for blazing performance, use a lower-level pipe. Service providers may very well provide this, using high-level muse for session handshakes. That would also allow specific low-level holes to be punched in firewalls at runtime for high-performance throughput; meanwhile, all high-level muse traffic would be verified by the network. Firewalls vs performance -- best of both worlds.

Put is implicit. If an object arrives, it's a put request. Put should (?) be complemented by an ack/nak?

Get 

Ack/Nak is important. Query is unnecessary; simply "get" or "head", then if NAK, it's unavailable. Head is useful for 'lightweight' clients, as well as (potentially) some buffering situations, plus as a shortform for query. Use a single ack/nak though; if more detail is desired it can be defined by an individual storage provider and transmitted within the api pipe.

Put, Get, Head, Ack, Nak?

Get, head, ack, nak: can (and should be!) all be contained within the API pipe. The question is, what do you need to be able to perform the initial handshake?