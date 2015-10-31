**WARNING:** This is a draft document dated 30 October. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

**SPECIAL WARNING:** This should not be implemented in a production environment until subjected to security reviews.

**NOTE:** This is a purely implementation document. For design discussion, see our [design document](/design_philosophy.md). For technical discussion, see our [whitepaper](/whitepaper.md). For security discussion, stay tuned for our security excerpt.

# Object container (MEOC)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .meoc

**Terminology:**

+ Author: the entity-agent creating the MEOC
+ Payload: all information to be protected by the container
+ Static MUID: the immutable, unique identifier for this particular object. It is always the (address algorithm + file hash) combination for that object.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 45 4F 43      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 65B            | Author MUID         | Bytes               |
| 73             | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 74             | 8B             | Payload length, *N* | Unsigned 64-bit int |
| 82             | *N*            | Private payload     | Encrypted blob      |
| 82 + *N*       | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 83 + *N*       | 64B            | File hash           | Bytes               |
| 147 + *N*      | 512B           | Author signature    | Bytes               |

### 1. Magic number

ASCII "MEOC" (4D 45 4F 43)

### 2. Version number

The version number is an incrementing unsigned integer. It is not intended for human use; particular version numbers will be tagged with semantic version numbers.

**The current MEOC version is 14.**

### 3. Author MUID

The full MUID of the MEOC object containing the author's identity (her public keys).

### 4. Cipher suite

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

### 5. Payload length

A 64-bit unsigned integer representation *N* of the length (in bytes) of the binary blob associated with the payload. If the cipher suite prepends a nonce to the ciphertext, this length will include the nonce.

### 6. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. The file terminates immediately at the end of the payload.

### 7. Address algorithm

Muse may eventually need multiple hash algorithms. They are represented as an 8-bit integer immediately following the magic number. The protocol specification defines which algorithm corresponds to which integer. The 1-byte address algorithm field is prepended to the file hash to form the MUID.

Address hash algorithms, by integer representation:

1. **0x1:** SHA-512.

### 8. File hash

The hash digest of the file, using the algorithm described by the Address Algorithm field. For all Muse objects, input to the hash function is the concatenation of all previous fields, in this case:

1. Magic number
2. Version
3. Author MUID
4. Cipher suite
5. Payload length
6. Encrypted payload
7. Address algorithm

The file hash is appended to the 1-byte address algorithm field to construct a static MUID.

### 9. Author signature

This field is always an asymmetric cryptographic signature of the file hash field. The signature algorithm is defined by the cipher suite field.

# Static binding (MOBS)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mobs

**Terminology:**

+ Binder: the entity-agent creating the binding (not necessarily the same as the bound MEOC's author)
+ Target MUID: the static MUID for the bound MEOC

Note that the static MUID applies to this particular binding, not to the target.

## Format

| Decimal Offset | Decimal Length   | Name                | Format              |
| ------         | ---------        | ------------------- | -----------         |
| 0              | 4B               | Magic number        | 0x 4D 4F 42 53      |
| 4              | 4B               | Version number      | Unsigned 32-bit int |
| 8              | 65B              | Binder MUID         | Bytes               |
| 73             | 1B               | Cipher suite        | Unsigned 8-bit int  |
| 74             | 65B              | Target MUID         | Bytes               |
| 139            | 1B               | Override flag, *R*  | 0x0 or 0x1          |
| 140            | *LR* = 65B * *R* | Override MUID       | Bytes               |
| 140 + *LR*     | 1B               | Address algorithm   | Unsigned 8-bit int  |
| 141 + *LR*     | 64B              | File hash           | Bytes               |
| 205 + *LR*     | 512B             | Binder signature    | Bytes               |

### 1. Magic number

ASCII "MOBS" (4D 4F 42 53)

### 2. Version

See above for description. **Currently 5.**

### 3. Binder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the agent placing the binding.

### 4. Cipher suite

See above for description.

### 5. Target MUID

The MUID of the MEOC resource to bind to.

### 6. Override flag

If flagged (0x1), this MOBS must include the static MUID of a debind request to override. If unflagged (0x0), it must proceed directly to the address algorithm.

Included to ease MOBS processing.

### 7. Override MUID

Must be included if (and only if) the override flag is set.

Indicates that this MOBS is overriding a debind request. Required for protection against replay attacks. When used, this field is the static MUID of the debind request to override.

### 3. Address algorithm

See above for description.

### 4. File hash

The file hash is used only for the signature. It is **not** used for addressing the static binding. See above for further description.

### 7. Binder signature

See Author Signature above.

# Dynamic binding (MOBD)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mobd

**Terminology:**

+ Dynamic MUID, dynamic address: The secondary address for this binding. Static for the lifetime of the binding.
+ Frame: Any particular version of the binding with this dynamic MUID.

Note that the static MUID applies to *this particular frame*.

## Format

| Decimal Offset    | Decimal Length           | Name                | Format              |
| ------            | ---------                | ------------------- | -----------         |
| 0                 | 4B                       | Magic number        | 0x 4D 4F 42 44      |
| 4                 | 4B                       | Version number      | Unsigned 32-bit int |
| 8                 | 65B                      | Binder MUID         | Bytes               |
| 73                | 1B                       | Cipher suite        | Unsigned 8-bit int  |
| 74                | 1B                       | Frame count *F*     | Unsigned 8-bit int  |
| 75                | *LR* = 65B * min(*F*, 1) | Historical frames   | Bytes               |
| 75 + *LR*         | 2B                       | MUID count *N*      | Unsigned 16-bit int |
| 77 + *LR*         | *LN* = 65B * *N*         | Bound MUIDs         | Bytes               |
| 77 + *LR* + *LN*  | 1B                       | Address algorithm   | Unsigned 8-bit int  |
| 78 + *LR* + *LN*  | 64B                      | Dynamic hash        | Bytes               |
| 142 + *LR* + *LN* | 64B                      | File hash           | Bytes               |
| 206 + *LR* + *LN* | 512B                     | Binder signature    | Bytes               |

### 1. Magic number

ASCII "MOBD" (4D 4F 42 44)

### 2. Version

See above for description. **Currently 10.**

### 3. Binder MUID 

See above for description.

### 4. Cipher suite

See above for description.

### 5. Frame count

The length of the MUID history included in the binding. If zero, this is the first frame.

### 6. Historical frames

A record of the recent history of the dynamic binding's static MUIDs, sorted in ascending order of recency (ie, oldest first). Without this field, dynamic bindings are susceptible to replay attacks. All MOBD records must include at least one historical MUID. There is a balance here between network state management (more history is better) and size optimization (less history is better).

If (and only if) the frame count is zero will this indicate a new dynamic binding. In this case, the historical frames field must be a single unique nonce. This makes it possible to have multiple dynamic bindings with the same (binding agent + original bound MUIDs) seed. This nonce must never be repeated for the same seed.

We recommend using a sufficiently strong random number generator to produce a 32-bit nonce, padding the result out with zeros to achieve 65B-length. The availability of the resultant dynamic address should then be verified against the binder's preferred persistence providers. The weaker the random number generator, the more important the verification.

### 7. MUID count

An integer count of the bound MUIDs.

### 8. Bound MUIDs

An ordered list of the MUIDs for each frame. If the dynamic binding is being used as a buffered object, the most recent frame should be first.

### 9. Address algorithm

See above for description.

### 10. Dynamic hash

The MUID hash for the secondary address for the dynamic object. Remains static for the life of the binding. Is used to address the binding.

Like a file hash, generated from the concatenation of the entire preceding file of the original frame in the binding using the hash algorithm described in the Address Algorithm field.

### 11. File hash

See above for description.

### 12. Binder signature

See above for description.

## Conditions for acceptance

Persistence providers must accept updated bindings if and only if:

1. binder is identical across all frames
2. 
+ 
+ persistence provider's current frame's static MUID is contained within the historical frames
Persistence providers must only accept such new dynamic bindings if they have no existing frames for that dynamic address.

# Debind record (MDXX)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mdxx

**Terminology:**

+ Debinder: The entity-agent removing a binding.

Note that the static MUID applies to *this particular debinding*.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 44 58 58      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 65B            | Debinder MUID       | Bytes               |
| 73             | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 74             | 65B            | Target MUID         | Bytes               |
| 139            | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 140            | 64B            | File hash           | Bytes               |
| 204            | 512B           | Debinder signature  | Bytes               |

### 1. Magic number

ASCII "MDXX" (4D 44 58 58)

### 2. Version

See above for description. **Currently 5.**

### 3. Debinder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the debinding agent.

### 4. Cipher suite

See above for description.

### 5. Target MUID

The static MUID of the resource to debind.

### 6. Address algorithm

See above for description.

### 7. File hash

See above for description.

### 8. Debinder signature

See Binder Signature above.

## Conditions for acceptance

Debind requesters must remove:

1. Static and dynamic bindings, only at the request of the binder
2. Pipe requesters, pipe ACKs, and pipe NAKs, only at the request of their recipient

# Muse encrypted pipe request (MEPR)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mepr

**Terminology:**

+ Pipe: a bidirectional channel between (usually two) agents
+ Half-pipe: a unidirectional channel, from one agent to another.
+ Recipient: the entity-agent against whose public key the MEPR was encrypted. If Alice is the author and she wants to open a half-pipe to Bob, then Bob is the recipient.
+ Target: the address for the half-pipe (usually dynamic)

Note that the author is the entity-agent requesting the pipe.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 45 50 52      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 65B            | Recipient MUID             | Bytes               |
| 139            | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 140            | 512B           | Private inner container    | Bytes               |
| 652            | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 653            | 64B            | File hash                  | Bytes               |
| 717            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                 | Format      |
| ------ | --------- | -------------------  | ----------- |
| 0      | 65B       | Author MUID          | Bytes       |
| 65     | 65B       | Target MUID          | Bytes       |
| 130    | 32B       | Target symmetric key | Bytes       |

### 1. Magic number

ASCII "MEPR" (4D 45 50 52)

### 2. Version

See above for description. **Currently 10.**

### 3. Recipient MUID 

The MUID of the recipient.

### 4. Cipher suite

See above for description.

### 5. Private inner container

Encrypted asymmetrically against the public key for the recipient. Once decrypted, the plaintext contents include:

1. Author MUID
2. Target MUID
3. Target symmetric key

### 6. Address algorithm

See above for description.

### 7. File hash

See above for description.

### 8. Author symmetric signature

The symmetric signature / MAC of the author. The key for the signature is derived by:

1. DHE shared secret between author and recipient
2. Expand shared secret into key material using the file hash as salt. (Ex: cipher suite 0x1 would use HKDF+SHA512)

The material for input into the signature algorithm is the entire preceding file (including the file hash).

# Muse pipe acknowledgement (MPAK)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mpak

Pipe ACKs should only be used during pipe negotiation. They most commonly indicate a successful closure or initiation of a half-pipe. They must only be used to indicate problems incurred during pipe management, not, for example, acknowledging messages within the pipe itself.

**Terminology:**

+ Requested MUID: the MUID used in the original pipe request.

Note that here, the author is the entity-agent acknowledging the pipe -- ie, the recipient of the pipe request. Likewise, the MPAK recipient is the author of the original pipe request.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 41 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 65B            | Recipient MUID             | Bytes               |
| 139            | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 140            | 512B           | Private inner container    | Bytes               |
| 652            | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 653            | 64B            | File hash                  | Bytes               |
| 717            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 0      | 65B       | Author MUID            | Bytes       |
| 65     | 65B       | Requested MUID         | Bytes       |
| 130    | 32B       | Status code (optional) | Bytes       |

### 1. Magic number

ASCII "MPAK" (4D 50 41 4B)

### 2. Version

See above for description. **Currently 5.**

### 3. Recipient MUID 

The MUID of the recipient.

### 4. Cipher suite

See above for description.

### 5. Private inner container

Encrypted asymmetrically against the public key for the recipient. Once decrypted, the plaintext contents include:

1. Author MUID
2. Requested MUID
3. An application-specific status/exit code. Entirely optional, potentially very helpful.

### 6. Address algorithm

See above for description.

### 7. File hash

See above for description.

### 8. Author symmetric signature

See above for description.

# Muse pipe non-acknowledgement (NPNK)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mpnk

Pipe non-acknowledgements (NAKs) should only be used during pipe negotiation. They most commonly indicate a rejection of the pipe request, but may also indicate an error in opening the pipe (wrong key, etc). They must only be used to indicate problems incurred during pipe management, not, for example, malformed messages within the pipe itself.

**Terminology:**

No new.

Note that here, the author is the entity-agent non-acknowledging the pipe -- ie, the recipient of the pipe request. Likewise, the MPAK recipient is the author of the original pipe request.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 4E 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 65B            | Recipient MUID             | Bytes               |
| 139            | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 140            | 512B           | Private inner container    | Bytes               |
| 652            | 1B             | Address algorithm          | Unsigned 8-bit int  |
| 653            | 64B            | File hash                  | Bytes               |
| 717            | 64B            | Author symmetric signature | Bytes               |

**Asymmetrically encrypted inner container format:**

| Offset | Length    | Name                   | Format      |
| ------ | --------- | -------------------    | ----------- |
| 0      | 65B       | Author MUID            | Bytes       |
| 65     | 65B       | Requested MUID         | Bytes       |
| 130    | 32B       | Status code (optional) | Bytes       |

### 1. Magic number

ASCII "MPNK" (4D 50 4E 4B)

### 2. Version

See above for description. **Currently 5.**

### 3. Recipient MUID 

The MUID of the recipient.

### 4. Cipher suite

See above for description.

### 5. Private inner container

Encrypted asymmetrically against the public key for the recipient. Once decrypted, the plaintext contents include:

1. Author MUID
2. Requested MUID
3. An application-specific status/error code. Entirely optional, potentially very helpful.

### 6. Address algorithm

See above for description.

### 7. File hash

See above for description.

### 8. Author symmetric signature

See above for description.

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

+ Need to define specific sequence for accepting or rejecting all objects
+ Need to define required components for identity containers
+ Need to define deniable aliasing
+ Need to add nonce generation
+ Need to review whitepaper for anything I missed