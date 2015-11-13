**WARNING:** This is a draft document dated 2 November. It is under review and is definitely **not finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

**SPECIAL WARNING:** This should not be implemented in a production environment until subjected to security reviews.

**NOTE:** This is a purely implementation document. For design discussion, see our [design document](/design_philosophy.md). For technical discussion, see our [whitepaper](/whitepaper.md). For security discussion, stay tuned for a detailed threat model and state-based analysis.

# Object container (MEOC)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .meoc

MEOCs must be stored by a persistence provider if, and only if, they are referenced in a MOBS or MOBD stored at the same persistence provider.

**Terminology:**

+ Author: the entity-agent creating the MEOC
+ Payload: all information to be protected by the container
+ Static MUID: the immutable, unique identifier for this particular object. It is always the (address algorithm + file hash) combination for that object.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 45 4F 43      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 9              | 65B            | Author MUID         | Bytes               |
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

### 3. Cipher suite

Muse plans to support multiple cipher suites. They are represented as an 8-bit integer.

See "Cipher suites and address algorithms" below for details.

### 4. Author MUID

The full MUID of the MEOC object containing the author's identity (her public keys).

### 5. Payload length

A 64-bit unsigned integer representation *N* of the length (in bytes) of the binary blob associated with the payload. If the cipher suite prepends a nonce to the ciphertext, this length will include the nonce.

### 6. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. The file terminates immediately at the end of the payload.

### 7. Address algorithm

Muse may eventually need multiple hash algorithms. They are represented as an 8-bit integer immediately following the magic number. The protocol specification defines which algorithm corresponds to which integer. The 1-byte address algorithm field is prepended to the file hash to form the MUID.

See "Cipher suites and address algorithms" below for details.

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

MOBS must be stored by a persistence provider until, and only until, they are cleared by a debinding.

**Terminology:**

+ Binder: the entity-agent creating the binding (not necessarily the same as the bound MEOC's author)
+ Target MUID: the static MUID for the bound MEOC

Note that the static MUID applies to this particular binding, not to the target.

## Format

| Decimal Offset | Decimal Length | Name                | Format              |
| ------         | ---------      | ------------------- | -----------         |
| 0              | 4B             | Magic number        | 0x 4D 4F 42 53      |
| 4              | 4B             | Version number      | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite        | Unsigned 8-bit int  |
| 9              | 65B            | Binder MUID         | Bytes               |
| 74             | 65B            | Target MUID         | Bytes               |
| 139            | 1B             | Address algorithm   | Unsigned 8-bit int  |
| 140            | 64B            | File hash           | Bytes               |
| 204            | 512B           | Binder signature    | Bytes               |

### 1. Magic number

ASCII "MOBS" (4D 4F 42 53)

### 2. Version

See above for description. **Currently 6.**

### 3. Cipher suite

See above for description.

### 4. Binder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the agent placing the binding.

### 5. Target MUID

The MUID of the MEOC resource to bind to.

### 6. Address algorithm

See above for description.

### 7. File hash

The file hash is used only for the signature. It is **not** used for addressing the static binding. See above for further description.

### 8. Binder signature

See Author Signature above.

# Dynamic binding (MOBD)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mobd

MOBDs must be stored until, and only until, they are cleared by a debinding, or successfully updated by their binder.

**Terminology:**

+ Dynamic MUID, dynamic address: The secondary address for this binding. Static for the lifetime of the binding.
+ Frame: Any particular version of the binding with this dynamic MUID.

Note that the static MUID applies to *this particular frame*.

## Format

| Decimal Offset    | Decimal Length           | Name                | Format              |
| ------            | ---------                | ------------------- | -----------         |
| 0                 | 4B                       | Magic number        | 0x 4D 4F 42 44      |
| 4                 | 4B                       | Version number      | Unsigned 32-bit int |
| 8                 | 1B                       | Cipher suite        | Unsigned 8-bit int  |
| 9                 | 65B                      | Binder MUID         | Bytes               |
| 74                | 1B                       | Frame count *F*     | Unsigned 8-bit int  |
| 75                | *LR* = 65B * min(*F*, 1) | Historical frames   | Bytes               |
| 75 + *LR*         | 2B                       | MUID count *N*      | Unsigned 16-bit int |
| 77 + *LR*         | *LN* = 65B * *N*         | Target MUIDs        | Bytes               |
| 77 + *LR* + *LN*  | 1B                       | Address algorithm   | Unsigned 8-bit int  |
| 78 + *LR* + *LN*  | 64B                      | Dynamic hash        | Bytes               |
| 142 + *LR* + *LN* | 64B                      | File hash           | Bytes               |
| 206 + *LR* + *LN* | 512B                     | Binder signature    | Bytes               |

### 1. Magic number

ASCII "MOBD" (4D 4F 42 44)

### 2. Version

See above for description. **Currently 11.**

### 3. Cipher suite

See above for description.

### 4. Binder MUID 

See above for description.

### 5. Frame count

The length of the MUID history included in the binding. If zero, this is the first frame.

### 6. Historical frames

A record of the recent history of the dynamic binding's static MUIDs, sorted in ascending order of recency (ie, oldest first). Without this field, dynamic bindings are susceptible to replay attacks. All MOBD records must include at least one historical MUID. There is a balance here between network state management (more history is better) and size optimization (less history is better).

If (and only if) the frame count is zero will this indicate a new dynamic binding. In this case, the historical frames field must be a single unique nonce. This makes it possible to have multiple dynamic bindings with the same (binding agent + original bound MUIDs) seed. This nonce must never be repeated for the same seed.

We recommend using a sufficiently strong random number generator to produce a 32-bit nonce, padding the result out with zeros to achieve 65B-length. The availability of the resultant dynamic address should then be verified against the binder's preferred persistence providers. The weaker the random number generator, the more important the verification.

### 7. MUID count

An integer count of the bound MUIDs.

### 8. Target MUIDs

An ordered list of the MUIDs for each frame. If the dynamic binding is being used as a buffered object, sort in ascending order of recency (ie, oldest first).

### 9. Address algorithm

See above for description.

### 10. Dynamic hash

The MUID hash for the secondary address for the dynamic object. Remains static for the life of the binding. Is used to address the binding.

Like a file hash, generated from the concatenation of the entire preceding file of the original frame in the binding using the hash algorithm described in the Address Algorithm field.

### 11. File hash

See above for description.

### 12. Binder signature

See above for description.

# Debind record (MDXX)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mdxx

MDXXs must be stored until, and only until, they are cleared by a subsequent chained debinding. Note that, due to the risk of a replay attack, debindings should only be chained when an entity-agent is ready to rebind. Furthermore, since multiple rebindings with partial chains can potentially create conflicting network states, best practices dictate that the full binding/debinding/rebinding/etc chain **should always** be stated explicitly.

**Terminology:**

+ Debinder: The entity-agent removing a binding.

Note that the static MUID applies to *this particular debinding*.

## Format

| Decimal Offset | Decimal Length   | Name                    | Format              |
| ------         | ---------        | -------------------     | -----------         |
| 0              | 4B               | Magic number            | 0x 4D 44 58 58      |
| 4              | 4B               | Version number          | Unsigned 32-bit int |
| 8              | 1B               | Cipher suite            | Unsigned 8-bit int  |
| 9              | 65B              | Debinder MUID           | Bytes               |
| 74             | 1B               | Target chain length *C* | Unsigned 8-bit int  |
| 75             | *LT* = 65B * *C* | Target MUID chain       | Bytes               |
| 75 + *LT*      | 1B               | Address algorithm       | Unsigned 8-bit int  |
| 76 + *LT*      | 64B              | File hash               | Bytes               |
| 140 + *LT*     | 512B             | Debinder signature      | Bytes               |

### 1. Magic number

ASCII "MDXX" (4D 44 58 58)

### 2. Version

See above for description. **Currently 7.**

### 3. Cipher suite

See above for description.

### 4. Debinder MUID 

The full MUID (hash algorithm byte + file hash) of the MEOC object containing the identity (the public keys) of the debinding agent.

### 6. Target chain length

The number of records covered by the debinding. See below.

### 7. Target MUID chain

Only one object can be debound, but static objects may be bound, debound, rebound, and so on. In this case, the chain will always reference:

1. **Static** MUID for MEOC or MOBD being debound
2. Static MUID for initial debinding
3. Static MUID for MEOC for MOBD being re-debound
4. Static MUID for second debinding

And so forth. At a minimum, each rebind cycle must include the most recent binding and the most recent debinding, but it need not include the entire chain. This chaining is required for protection against replay attacks.

### 8. Address algorithm

See above for description.

### 9. File hash

See above for description.

### 10. Debinder signature

See Binder Signature above.

# Muse encrypted pipe request (MEPR)

+ Preferred stored file extension: no extension
+ Secondary stored file extension: .mepr

MEPRs must be stored until, and only until, they are cleared by a debinding from their recipient.

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
| 8              | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 9              | 65B            | Recipient MUID             | Bytes               |
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

See above for description. **Currently 11.**

### 3. Cipher suite

See above for description.

### 4. Recipient MUID 

The MUID of the recipient.

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

MPAKs must be stored until, and only until, they are cleared by a debinding from their recipient.

Pipe ACKs should only be used during pipe negotiation. They most commonly indicate a successful closure or initiation of a half-pipe. They must only be used to indicate problems incurred during pipe management, not, for example, acknowledging messages within the pipe itself.

**Terminology:**

+ Requested MUID: the MUID used in the original pipe request.

Note that here, the author is the entity-agent acknowledging the pipe -- ie, the recipient of the pipe request. Likewise, the MPAK recipient is the author of the original pipe request.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 41 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 9              | 65B            | Recipient MUID             | Bytes               |
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

See above for description. **Currently 6.**

### 3. Cipher suite

See above for description.

### 4. Recipient MUID 

The MUID of the recipient.

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

MPNKs must be stored until, and only until, they are cleared by a debinding from their recipient.

Pipe non-acknowledgements (NAKs) should only be used during pipe negotiation. They most commonly indicate a rejection of the pipe request, but may also indicate an error in opening the pipe (wrong key, etc). They must only be used to indicate problems incurred during pipe management, not, for example, malformed messages within the pipe itself.

**Terminology:**

No new.

Note that here, the author is the entity-agent non-acknowledging the pipe -- ie, the recipient of the pipe request. Likewise, the MPAK recipient is the author of the original pipe request.

## Format

| Decimal Offset | Decimal Length | Name                       | Format              |
| ------         | ---------      | -------------------        | -----------         |
| 0              | 4B             | Magic number               | 0x 4D 50 4E 4B      |
| 4              | 4B             | Version number             | Unsigned 32-bit int |
| 8              | 1B             | Cipher suite               | Unsigned 8-bit int  |
| 9              | 65B            | Recipient MUID             | Bytes               |
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

See above for description. **Currently 6.**

### 3. Cipher suite

See above for description.

### 4. Recipient MUID 

The MUID of the recipient.

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

# Object verification

The following section outlines the order of operations while verifying objects, and the conditions under which Muse objects are considered valid. Items with strict execution order are numbered.

Note that in all cases, if a Muse object is successfully received, verified, and stored, a persistence provider **must** issue an ACK, even if the persistence provider already had a copy of the object.

## MEOC order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly-formatted file
5. (The following steps may be done in parallel)
    + Author retrieval
        1. Verify author exists at persistence provider
        2. Retrieve author's identity file
        3. Verify author's identity file includes public signature key for cipher suite
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
6. Verify signature

## MOBS order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly-formatted file
5. (The following steps may be done in parallel)
    + MOBS status check
        1. Verify no unchained debinding exists from the binder for the target address. If one exists, check fails; issue NAK
        2. If chained debinding exists from the binder for the target address, verify that chain permits rebinding. If not, check fails; issue NAK
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
        3. Verify binder's identity file includes public signature key for cipher suite
    + Digest remaining file
        1. Verify target MUID exists at persistence provider
        2. Verify address algorithm
        3. Verify file hash
6. Verify signature

## MOBD order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly-formatted file
5. (The following steps may be done in parallel)
    + MOBD consistency check
        1. Check if dynamic address already exists at persistence provider. If so:
            1. If frame count is zero, check fails. Issue NAK.
            2. If binder MUID differs from existing dynamic address binding, check fails. Issue NAK.
            3. If preexisting binding's static MUID is not contained within historical frames, check fails. Issue NAK.
        2. If dynamic address does not exist at persistence provider,
            1. Verify no unchained debinding exists from the binder for the target address. If one exists, check fails; issue NAK
            2. If chained debinding exists from the binder for the target address, verify that chain permits rebinding. If not, check fails; issue NAK
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
        3. Verify binder's identity file includes public signature key for cipher suite
    + Digest remaining file
        1. Verify at least one target MUID exists at persistence provider
        2. Verify address algorithm
        3. Verify dynamic hash
        4. Verify file hash
6. Verify signature

## MDXX order of verification

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly formatted file
5. (The following steps may be done in parallel)
    + MDXX status check
        1. If static MUID for the in-verification MDXX already exists at persistence provider, check passes; continue (new state identical to old state if rest of verification passes)
        2. If static MUID for the in-verification MDXX exists within an existing debinding chain, check fails; issue NAK
        3. If the in-verification MDXX has a chain length greater than one, and the persistence provider does not have at least one target MUID in storage, check fails; issue NAK
    + MDXX reference check
        1. Verify at least one target MUID exists at persistence provider
        2. Verify binder MUID matches author MUID (for MOBS, MOBD, MDXX) or recipient MUID (for MEPR, MPAK, MPNK)
    + Binder retrieval
        1. Verify binder exists at persistence provider
        2. Retrieve binder's identity file
        3. Verify binder's identity file includes public signature key for cipher suite
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
6. Verify signature

Note that, since persistence providers are required to keep only their most recent MOBD frame, to successfully debind a MOBD requires the MDXX to reference the most recent available frame.

## MEPR order of verification

**Public verification:**

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly formatted file
5. (The following steps may be done in parallel)
    + Recipient retrieval
        1. Verify recipient exists at persistence provider
        2. Retrieve recipient's identity file
        3. Verify recipient's identity file includes public encryption key and Diffie-Hellman public key for cipher suite
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
6. Verify signature

**Private verification:**

1. Public verification (see above)
2. Decrypt private inner container
3. Author verification
    1. Verify author exists
    2. Retrieve author's identity file
    3. Verify author's identity file includes Diffie-Hellman public key for cipher suite
4. Perform symmetric signature key agreement procedure (see "Author symmetric signature" above)
5. Verify author symmetric signature

## MPAK order of verification

**Public verification:**

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly formatted file
5. (The following steps may be done in parallel)
    + Recipient retrieval
        1. Verify recipient exists at persistence provider
        2. Retrieve recipient's identity file
        3. Verify recipient's identity file includes public encryption key and Diffie-Hellman public key for cipher suite
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
6. Verify signature

**Private verification:**

1. Public verification (see above)
2. Decrypt private inner container
3. Author verification
    1. Verify author exists
    2. Retrieve author's identity file
    3. Verify author's identity file includes Diffie-Hellman public key for cipher suite
4. Perform symmetric signature key agreement procedure (see "Author symmetric signature" above)
5. Verify author symmetric signature

## MPNK order of verification

**Public verification:**

1. Verify magic number
2. Verify version
3. Verify cipher suite
4. Verify length is correct for properly formatted file
5. (The following steps may be done in parallel)
    + Recipient retrieval
        1. Verify recipient exists at persistence provider
        2. Retrieve recipient's identity file
        3. Verify recipient's identity file includes public encryption key and Diffie-Hellman public key for cipher suite
    + Digest remaining file
        1. Verify address algorithm
        2. Verify file hash
6. Verify signature

**Private verification:**

1. Public verification (see above)
2. Decrypt private inner container
3. Author verification
    1. Verify author exists
    2. Retrieve author's identity file
    3. Verify author's identity file includes Diffie-Hellman public key for cipher suite
4. Perform symmetric signature key agreement procedure (see "Author symmetric signature" above)
5. Verify author symmetric signature

# Cipher suites and address algorithms

**Note that this is not a substitute for a proper threat model or security analysis.** Those documents have been separated from the protocol specification for readability and digestibility purposes.

## Cipher suites

By Muse integer representation:

+ **0x0:** None. Reserved for testing and development purposes.
+ **0x1:** SHA512/AES256/RSA4096.  
  **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537.  
  **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537.  
  **Symmetric encryption:** AES-256 in **SIV mode (formally AEAD_AES_SIV_CMAC_512)**  
  **Symmetric shared secrets:** 2048-bit Diffie-Hellman [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.  
  **Key agreement from shared secret:** HKDF+SHA512. Salt is application-specific.  
  **Symmetric signatures / MAC:** HMAC+SHA512
+ **0x2:** SHA512/AES256/RSA4096.  
  **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537.  
  **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537.  
  **Symmetric encryption:** AES-256 in **CTR mode with nonce distributed privately with the key**  
  **Symmetric shared secrets:** 2048-bit Diffie-Hellman [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.  
  **Key agreement from shared secret:** HKDF+SHA512. Salt is application-specific.  
  **Symmetric signatures / MAC:** HMAC+SHA512

**Quick note:** Why not elliptic curves first? Basically, development resource allocation. There was a reasonably large amount of thought put into this decision, and I stand by it. We need a prototype that works and is suitably secure for alpha testing. ECC is a very high priority for the production standard. If you want to discuss this further, please [get in contact directly](mailto:badg@muterra.io).

**Quick note 2:** Why not an AE/AEAD block mode? The added complexity doesn't make sense in this application. Dynamic bindings don't use symmetric encryption, and everything else is non-malleable. There's already a hash, and a signature, on top of the existing non-malleability, for MEOC records. CTR is very simple, which makes it much harder to screw up.

**Quick note 3:** Non-deterministic nonce generation will be implemented at a later date, in a different cipher suite, for performance-sensitive applications. It may also use ChaCha20. This is on the horizon, but it's a bit of a ways out. For now, the best route for performant streams is to use Muse to negotiate secret keys for lower-level transport sockets (etc) directly between hardware.

### CTR vs SIV

Relevant details:

1. key/access control is separate from content containers
2. all resources are content-addressed by their hash digests
3. the protocol is transport agnostic, so traffic analysis, etc are deliberately out-of-scope

Because everything is hash addressed, and content is retained asynchronously (potentially indefinitely; note that key persistence is handled separately), we're very concerned about data deduplication. "Ideally" (ie, ignoring security, which we're obviously not doing), to minimize network clutter, every (plaintext + key) combination would result in identical ciphertext.

Of course, in practice, we need to be extremely concerned about reusing a (key + nonce) combination. Replay attacks are meaningless to this specific protocol[1] -- so the thought is, we can generate the nonce from a manipulation of the hash of the plaintext. This way an identical (key + nonce) combination will by definition result in an identical file.

[1] Without going into a ton of detail, replays for the symmetric containers (MEOCs) are indistinguishable to re-uploading the file. Because retention is handled by the bindings, which have replay protection, a replay of the container without the binding would be automatically garbage-collected by the persistence provider. If the persistence provider already has the MEOC, a second upload is meaningless. This ignores traffic analysis and the like, which again, is out-of-scope for the protocol.

**SIV mode**:

Note: in SIV mode, the MAC tag must be prepended to the ciphertext in the MEOC payload.

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

Note: in SIV mode, the nonce should not be prepended to the ciphertext in the MEOC payload. It should be considered a functional component of the key, and must be distributed therewith. It may be random, but must never be reused for the same key. New key generation should be accompanied by new nonce generation to emphasize their equivalence and discourage accidental content duplication.

Advantages:

+ Fast, and may be totally precomputed
+ Compromise between deduplication and performance
+ Cryptographic simplicity
+ Library availability

Disadvantages:

+ Relies on the strength of the random number generator for birthday resistance
+ **Note that this places restrictions on keys, as the new nonce must be distributed in a new key exchange for new content.** This makes it unsuitable for API pipes.

## Address algorithms

By Muse integer representation:

+ **0x0:** None. Reserved for testing and development purposes.
+ **0x1:** SHA-512.

**Not-so-quick note:** Why not SHA-256? Two primary reasons. The first is to reduce the possibility of an accidental (or malicious) birthday attack against the hash itself. That does sound a little ridiculous, to be fair, given the sense of scale:

+ In 2013 the internet [reached approximately 4E21 bytes](https://en.wikipedia.org/wiki/Zettabyte)
+ Muse headers consume ~1kB per file. Taking this as a minimum filesize yields 4e18 possible files in 2013
+ The internet is expanding rapidly, and will [probably double in size within the next 5 years](http://www.forbes.com/sites/gilpress/2014/08/22/internet-of-things-by-the-numbers-market-estimates-and-forecasts/). 
+ Taking 2020 with 1E19 bytes as a baseline, and a 5-year doubling period (approximately 13.9% yearly growth), and extrapolating to a 2040 design life (making Muse approximately as old as the internet is today), yields 1.6E20 files
+ Combine all of that and the formula for [birthday attack probability](https://en.wikipedia.org/wiki/Birthday_attack), and you get p≈1-10^-10^-38 for 256 bits, which is just vanishingly small.

However, this kind of analysis is grossly misleading. It is wholly predicated on the strength of the hash function, and by this kind of analysis, MD5 still looks acceptable (please do *not* mistake that as an argument for MD5). Point is: giving ourselves some headroom for such a potentially large application seems prudent.

Secondly, and much less importantly, we are considering defining 0x2 as a concatenation of SHA256 and SHA3-256 (operating independently over the same input bytes; this is a whole separate discussion). This allows us some version-independent wiggle room, though we could also just reserve an extra 256 bits in the standard to accomplish the same end result.