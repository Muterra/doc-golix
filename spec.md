**WARNING:** This is a draft document dated 29 October. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

**SPECIAL WARNING:** This should not be implemented in a production environment until subjected to security reviews.

**NOTE:** This is a purely implementation document. For design discussion, see our [design document](/design_philosophy.md). For technical discussion, see our [whitepaper](/whitepaper.md). For security discussion, stay tuned for our security excerpt.

# MEOC records

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

Muse plans to support multiple cipher suites. They are represented as an 8-bit integer. ECC is the next support candidate.

Cipher suites, by representation:

1. **0x1:** SHA512/AES256/RSA4096. **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537. **Symmetric encryption:** AES-256 in CTR mode with nonce prepended to payload ciphertext. Nonce generation is described below. **Diffie-Hellman deniable exchange:** 2048-bit [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.

## 6. Author MUID

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the author's identity (her public keys).

## 7. Author signature

This field is always an asymmetric cryptographic signature of the file hash field. The signature algorithm is defined by the cipher suite field.

## 8. Payload length

A 64-bit unsigned integer representation *N* of the length (in bytes) of the binary blob associated with the payload. If the cipher suite prepends a nonce to the ciphertext, this length will include the nonce.

## 9. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. The file terminates immediately at the end of the payload.

## Static bindings

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

## Dynamic bindings

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

We recommend using a sufficiently strong random number generator to produce the nonce. The availability of the resultant dynamic address should then be verified against the binder's preferred persistence providers. The weaker the random number generator, the more important the availability verification.

## 9. Dynamic frame count

An integer count of the bound MUIDs. The length of the remaining record can be calculated by the frame count * 65 bytes.

## 10. Bound MUIDs

An ordered list of the MUIDs for each frame. If the dynamic binding is being used as a buffered object, the most recent frame should be first.








--------------------------

# Everything below this line is incorrect.








## API requesters

API requesters (EIARs, which I pronounce "ears") serve as signaling mechanism for an API handshake. They are encrypted asymmetrically against the public key of the recipient. Remember that all EICOs are single author, (potentially) multiple consumer -- in other words, unidirectional. Any bidirectional API therefore requires both a client/consumer pipe and a server/producer pipe, even just to issue an ACK. An eiar is generated by the API client/consumer, signaling both the MUID and access key for the consumer's "pipe" to the API. In response, the API producer generates a response eiar, signaling the MUID and key to its response pipe.

APIs themselves may be self-describing: the pipe may describe its intended purpose internally, but such a description cannot be contained within the eiar. 

An eiar is removed by the *recipient*, through a debind request against the file hash of the eiar.

### Format

**Public header:**

| Offset | Length    | Name                | Format              | Hex off |
| ------ | --------- | ------------------- | -----------         | ---     |
| 0      | 4B        | Magic number        | x65x69x61x72        | 0       |
| 4      | 4B        | Version             | Unsigned 32-bit int | x       |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x       |
| 10     | 64B       | File hash           | Bytes               | x       |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x       |
| 76     | 66B       | Recipient MUID      | Bytes               | x       |

Total length 654B | x

**Inner container:**

| Offset | Length    | Name                 | Format      |
| ------ | --------- | -------------------  | ----------- |
| 0      | 66B       | Author               | Bytes       |
| 130    | 66B       | Target MUID          | Bytes       |
| 196    | 32B       | Target symmetric key | Bytes       |

Total size: 228B (encrypted size 512B) | x

**Public footer:**

| Offset | Length    | Name                | Format      | Hex off |
| ------ | --------- | ------------------- | ----------- | ---     |
| 142    | 64B       | Author HMAC         | Bytes       | x       |

#### 1. Magic number

API requester magic number: ASCII "eiar" (65 69 61 72)

#### 2. Cipher suite

See above.

#### 3. Author signature

The author's signature. Unlike other EIC objects, EIAR signatures are constructed from the bitwise XOR of the file hash and inner hash. This is to preserve the security of the author's identity to an outside observer with a dictionary of public keys.

#### 4. File hash

The hash of this API request, starting immediately after the hash (ie starting with byte 584, as with EICOs).

#### 5. EIAR version

**Currently 0.0.8.** 

#### 6. Recipient MUID

The MUID for the recipient.

#### 7. Inner hash

Digest of all remaining bytes in the inner container. It is included as an integrity assurance and to ease signature verification.

#### 8. Author

The MUID of the identity creating the request.

#### 9. Target MUID

The MUID of the dynamic binding to use for the API pipe. By keeping this encrypted, there is no public link between the author and the recipient.

#### 10. Target symmetric key

The encryption key for the API pipe.

### Known vulnerabilities

EIAR files have one design tradeoff that justifies explicit discussion. To prevent an observer from inferring a connection between the API consumer and the API provider, the signature can only be verified **after** decrypting the request. Though this *does* prevent a malicious party from successfully initiating an API pipe on someone else's behalf, because the signature verification requires the recipient's private key, it cannot be performed by the network. This opens a potential DoS attack vulnerability: an API provider could be overwhelmed by spoofed EIARs with invalid signatures from invalid MUIDs. Such an attack would compromise the API provider's ability to initiate new API pipes, but presumably leave its ability to handle existing APIs intact.

However, EIARs have no hard response requirement, so two attack mitigation strategies are immediately apparent:

1. For in-progress attack mitigation from EIAR "cold calls", trusted third-party verification of request origin using the EIAR file hash. The requestor registers the request with the third party, and the API provider verifies the author's signature using the identity acquired from the third party. Exposes the API consumer/provider relationship to the third party.
2. For attack prevention, adding an extra API channel to existing API consumers, allowing them to initiate EIARs on others' behalf. This is a decentralized version of (1) above that requires prior planning.

## API handshake NAKs

API NAKs are a single-purpose denial of an API request. They should **not** be used for API problems; they should only be used during initial API handshake negotiation. They acknowledge (not necessarily successful) receipt of the API request, combined with either unsuccessful operation or outright refusal of the request.

Most internal NAK information is optional, to allow a single NAK to be built, cached, and reused in the case of DoS attack mitigation.

API NAKs are removed by the *recipient*, through a debind request against the file hash of the NAK.

### Format

**Public header:**

| Offset | Length    | Name                | Format              | Hex off |
| ------ | --------- | ------------------- | -----------         | ---     |
| 0      | 4B        | Magic number        | x65x69x4Ex4B        | 0       |
| 4      | 4B        | Version             | Unsigned 32-bit int | x       |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x       |
| 10     | 64B       | File hash           | Bytes               | x       |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x       |
| 76     | 66B       | Recipient MUID      | Bytes               | x       |

Total length 654B | x

**Inner container:**

| Offset | Length    | Name                       | Format              |
| ------ | --------- | -------------------        | -----------         |
| 0      | 66B       | Author                     | Bytes               |
| x      | 66B       | Requesting MUID (optional) | Bytes               |
| x      | 32B       | Error code (optional)      | Unsigned 32-bit int |

Total size: xB (encrypted size 512B) | x

**Public footer:**

| Offset | Length    | Name                | Format      | Hex off |
| ------ | --------- | ------------------- | ----------- | ---     |
| 142    | 64B       | Author HMAC         | Bytes       | x       |

#### 1. Magic number

API NAK magic number: ASCII "eiNK" (65 69 4E 4B)

#### 2. Cipher suite

See above.

#### 3. Author signature

The author's signature. Unlike other EIC objects, EINO signatures are constructed from the bitwise XOR of the file hash and inner nonce. This is to preserve the security of the author's identity to an outside observer with a dictionary of public keys.

#### 4. File hash

The hash of this binding request, starting immediately after the hash (ie starting with byte 584, as with EICOs).

#### 5. EINK version

**Currently 0.0.3.** 

#### 6. Recipient MUID

The MUID for the recipient (the identity requesting the API that is currently being denied).

#### 7. Inner nonce

Random noise. This preserves verifiability of the signature, while still protecting the author from a public key dictionary attack, even if all optional arguments are omitted.

#### 8. Author

The entity denying the EIAR.

#### 9. Requesting MUID

The file hash of the EIAR request that this NAK is denying. May be omitted to reuse the built NAK for subsequent requests from the same recipient. Best practice is to send at least one NAK including this field, falling back on a cached, generic NAK for subsequent denied requests from the same source in times of heavy traffic.

#### 10. Error code

An API-specific error code. Should be defined by the API itself, and entirely optional. If one is available, best practices should follow similar fallback-to-generic behavior as the requesting EIAR file hash.

## API disconnect ACKs

API ACKs are a single-purpose API "farewell". They should **not** be used for ACKs within an actual Muse API; they should only be used during final API disconnection, after all parties have cleaned up their API pipes. They acknowledge successful closure of the API.

Most internal ACK information is optional, to allow a single NAK to be built, cached, and reused in the case of DoS attack mitigation.

API ACKs are removed by the *recipient*, through a debind request against the file hash of the ACK.

### Format

**Public header:**

| Offset | Length    | Name                | Format              | Hex off |
| ------ | --------- | ------------------- | -----------         | ---     |
| 0      | 4B        | Magic number        | x65x69x41x4B        | 0       |
| 4      | 4B        | Version             | Unsigned 32-bit int | x       |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x       |
| 10     | 64B       | File hash           | Bytes               | x       |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x       |
| 76     | 66B       | Recipient MUID      | Bytes               | x       |
| 142    | 512B      | Author signature    | Bytes               | x       |

Total length 654B | 28C

**Inner container:**

| Offset | Length    | Name                    | Format              |
| ------ | --------- | -------------------     | -----------         |
| 32     | 66B       | Author                  | Bytes               |
| x      | 66B       | Closing MUID (optional) | Bytes               |
| x      | 32B       | Exit code (optional)    | Unsigned 32-bit int |

**Public footer:**

| Offset | Length    | Name                | Format      | Hex off |
| ------ | --------- | ------------------- | ----------- | ---     |
| 142    | 64B       | Author HMAC         | Bytes       | x       |

Total size: xB (encrypted size 512B) | x

#### 1. Magic number

API requestor magic number: ASCII "eiAK" (65 69 41 4B)

#### 2. Cipher suite

See above.

#### 3. Author signature

The author's signature. Unlike other EIC objects, EINO signatures are constructed from the bitwise XOR of the file hash and inner nonce. This is to preserve the security of the author's identity to an outside observer with a dictionary of public keys.

#### 4. File hash

The hash of this binding request, starting immediately after the hash (ie starting with byte 584, as with EICOs).

#### 5. EIAK version

**Currently 0.0.3.** 

#### 6. Recipient MUID

The MUID for the recipient (the identity requesting the API that is currently being denied).

#### 7. Inner nonce

Random noise. This preserves verifiability of the signature, while still protecting the author from a public key dictionary attack, even if all optional arguments are omitted.

#### 8. Author

The entity denying the EIAR.

#### 9. Closing MUID

The MUID of the dynamic API pipe that this ACK is recognizing the closure of. May be omitted to reuse the built ACK for subsequent requests from the same recipient. EIAKs should be very rare compared to other EIC traffic: the represent both successful creation and removal of an API. As such, best practice is to always include this field. However, a cached, generic ACK may be used as a fallback for subsequent denied requests from the same source in times of heavy traffic.

#### 10. Exit code

An API-specific exit code. Should be defined by the API itself, and entirely optional. If one is available, best practices should follow similar fallback-to-generic behavior as the closing MUID.

## Debind requestors

Debind requestors remove

1. Static bindings,
2. Dynamic bindings, or
3. API requestors.

They will only be accepted from the MUID that created the binding (or the recipient of the API requestor).

### Format

| Offset | Length    | Name                | Format              | Hexoff |
| ------ | --------- | ------------------- | -----------         | ---    |
| 0      | 4B        | Magic number        | x65x69x58x58        | 0      |
| 4      | 4B        | Version             | Unsigned 32-bit int | x      |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x      |
| 10     | 64B       | File hash           | Bytes               | x      |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x      |
| 76     | 66B       | Debinder MUID       | Bytes               | x      |
| 142    | 512B      | Debinder signature  | Bytes               | x      |
| x      | 66B       | Target              | Bytes               | x      |
| x      | 32B       | Nonce               | Bytes               | x      |

#### 1. Magic number

Debind requestor magic number: ASCII "eiXX" (65 69 58 58)

#### 2. Cipher suite

See above.

#### 3. Debinder signature

#### 4. File hash

#### 5. eiXX version

**Currently 0.0.3.** 

#### 6. Debinder MUID

#### 7. Target

#### 8. Nonce 

Random noise. Prevents spoofing a static binding into a static debind request.

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