**DANGER ZONE:** This is a draft document **in-process** and under review and is absolutely **not even close to finalized!** If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

# Abstract

... Concise abstracts about complex topics are really hard to write; it's going to be a hot minute. Expect this sooner or (probably) **later**.

"Preservation of agent state"

Text-based links should be represented as muid://base64URLsafeencodedMUID

# Table of contents

*Apologies for any broken links while we're still writing...* If you find one, feel free to [submit an issue](https://github.com/Muterra/muse-doc/issues/new) to let us know.

+ [Philosophical and technical motivations](#Philosophical-and-technical-motivations)
    + [The future requires agency](#The-future-requires-agency)
        + [Agency requires privacy](#Agency-requires-privacy)
        + [Privacy requires security](#Privacy-requires-security)
        + [Privacy breaks existing architecture](#Privacy-breaks-existing-architecture)
    + [Agency implies sharing](#Agency-requires-sharing)
    + [Sharing requires identity](#Sharing-requires-identity)
+ [Technical requirements](#Technical-requirements)
    + [Separation of concerns](#Separation-of-concerns)
    + [Static content](#Static-content)
    + [Mutable context](#Mutable-context)
    + [Universal identities](#Universal-identities)
+ [Technical implementation](#Technical-implementation)
    + [EIC filetype](#EIC-filetype)
    + [Service layer](#Service-layer)
        + [Storage providers](#Storage-providers)
        + [Access providers](#Access-providers)
        + [Identity providers](#Identity-providers)
    + [Application layer](#Application-layer)
        + [API definitions](#API-definitions)
        + [Best practices](#Best-practices)
+ [Applications and implications](#Applications-and-implications)
    + [Scalable network deployment](#Scalable-network-deployment)
    + [Decentralized systems](#Decentralized-systems)
    + [Egalitarianism](#Egalitarianism)
    + [Digital statehood](#Digital-statehood)

# Philosophical and technical motivations

## The future requires agency

Murphy's law: anything that can happen, will. 3+ billion people on the internet and growing. Need to be able to make our own decisions about data; you can't trust a system with billions of people. One-in-a-million odds are happening every day.

internet footprints are essentially unavoidable. No other medium has so profoundly capitalized on collective learning. This connectivity comes at a tremendous cost: when you upload data, you forfeit power over it. Individual websites *may* provide you some measure of external privacy, but this protection seldom applies to the service itself. In an age when the internet is such a core part of everyday life, for many people, non-participation is simply not an option, and this relationship between site and user is untenable at best, non-consensual at worst.

Digital disempowerment is not just a personal concern. The absence of data agency irreparably fractures development: it forces every site and every service to make assumptions about desired behavior of their entire userbase, which simply isn't possible.

In the physical world, personal agency is the defining characteristic of animate existence. Without it, digital interactions will be unavoidably and inescapably reluctant. In this way, historic answers to consumer concerns like privacy have been predominantly secondary considerations and localized solutions.
 
### Agency requires privacy

Much of the benefit to agent-oriented architecture is contained in the ability to treat the internet as a global asynchronous communications system. Agents directly address other agents, and the muse-implementing network is treated as a single coherent information repository. As such, it replaces the website as a medium for data storage, which, by extension, eliminates the notion of site-specific privacy. This arrangement **requires** *all* confidential data to be private end-to-end, or it would be immediately exposed to every other agent. We are then left with a choice: have no explicit security requirement, allowing individual applications to develop their own information controls; or, make Muse private by default, thereby implementing a new standard for universal data privacy. The proliferation of both generic security solutions like SSL, and of the astoundingly important privacy questions now regularly raised, strongly indicates that the latter option is the clear choice.

Having spelled out a privacy requirement, we need to define its scope. Since we're discussing information that already exists, and information isn't known to spontaneously appear, we can assume it has an author. We see privacy as an absence of information, and therefore to be "private by default", information must be known to *only and exactly* its author at the time it is created. This is intuitive in the physical world, and it's our firm belief that this paradigm should also apply to digital data. With current technology, we can then say that a functional definition of "private by default" requires data to be encrypted from pre-upload to post-download. This is known as [end-to-end encryption](https://en.wikipedia.org/wiki/End-to-end_encryption).

### Privacy requires security

### Privacy breaks existing architecture

obscures the public information we currently use to make the web work

How to find information? Re: humans, and re: computers

How to establish relationships between information?

## Agency implies sharing

100% private information isn't particularly useful. The web derives most of its utility from the *collective* knowledge that is stored there; without the ability to share information, the internet would be little different from an exceptionally large paper notebook. Sharing is clearly a practical necessity, but for the "private by default" requirement to have any meaning, we have to set some ground rules:

1. **Sharing requires agency.** An agent must be capable of independent action. Agency implies the ability to intentionally create, share, and receive information and cannot be directly restricted by other agents. Agents can be influenced but not controlled. Agents may be non-human, but all agents are equal of opportunity.
2. **Sharing requires consent.** An agent must make a deliberate choice to share information. Consent is informed: an agent must know exactly and exhaustively what is being shared and recipients must be explicitly known by the agent before consent. Informed consent requires limited consent; past sharing does not imply future sharing. To be voluntary, consent must also be deniable: the agent *must* see non-sharing as a viable option. [Vendor lock-in](https://en.wikipedia.org/wiki/Vendor_lock-in) is non-consensual, and manifests itself in a myriad of ways.
3. **Sharing requires receivability.** A tree falling in an empty forest definitely makes a sound, but nobody cares unless someone hears it. Though the act of sharing itself does not require consent from the recipient, receipt is wholly voluntary: the recipient has every right to wholly ignore a share. Receipt is also stateful: a recipient must know who shared the information with them.
4. **Sharing requires an author.** An agent must claim provable authorship of information before sharing it, though authorship need not be meaningful, and an author may be anonymously, pseudonymously, or explicitly named. Authorship is not ownership, and information, once created, cannot be directly controlled. All authorship is equivalent; no author can be inherently valued over another. Sharing is an egalitarian exchange.

From this point we can start to see some rhyme to the reason. Between the requirement to be private by default and the philosophical limitations we're placing on sharing, we can start to spell out some technical requirements of such a system:

1. Data must be encrypted end-to-end, and sharing should not be connectible through metadata.
2. Data must have exactly one agent-author, and the author must digitally sign the data.
3. Data must be explicitly shared by one agent to another agent through the recipient's public key.
4. Data must have a common, open, definite interface.
5. Agents are distinguished only by the existence of a public/private keypair.

That's a good start, but there are also a few inherent caveats to sharing that should be made explicit:

1. **The past is immutable.** You cannot change history. We consider digital data to be an extension of physical memory; once you've shared something, there's no going back. It has entered the functional memory of the recipient.
2. **The future is indeterminate.** You cannot predict the future. The past isn't always relevant; the past may be immutable but our understanding of it isn't. Life is inherently dynamic.
3. **The ephemerality of the present is unenforceable.** Absolute control over persistence, whether digital *or* physical, is impossible. Data, once created, cannot be reliably destroyed. Nor, in many cases, should it be.

This introduces a bit of a paradox for shared digital data. On the one hand, data is definitely and exactly created; the digital medium has a "perfect" memory, so long as that memory exists -- and remember, it cannot be forcibly expired -- so long as that memory exists, it is exact. That implies a unique and static address system 

4. Data must be uniquely and statically addressed. This is required for static consent, irrevocable receipt, and static receipt.

From these core requirements, we can then assert a few more:

1. Data must be self-sufficient; every piece of data must be capable of standalone "operation" on the network, because any other arrangement would require third-party knowledge of encrypted data.
2. Private keys must be know only to the agent associated with its public key, and any entity operating with that private key must be considered one and the same as the agent. Otherwise, that "agent" doesn't really have agency.
3. Since data must be self-sufficient and metadata cannot be publicly exposed, for receipt to be stateful, the sharing agent must be identified within the share container.
4. Addresses are static but the world is not; some mechanism to support data recontextualization, modification, etc is necessary.
5. For logistical reasons, true dynamic communication must be supported. This is subtly different than sharing, but conversion from dynamic to static must be trivial.

## Sharing requires identity

"You do not have a right to retroactive anonymity! You cannot disown your statements. They are a part of your past and will remain that way. You can, however, expound upon them, give them context, or revise them. Nothing is immutable except the past." That said, that's only because you can't change the past. You're more than welcome to preemptive anonymity.

# Technical requirements

## Separation of concerns

Make development work better!

## Static content

Everything is static and the past is immutable. 

Ephemerality is unenforceable.

## Mutable context

*Content* on the eicnet is static, but *concepts* are not. The fundamental idea behind heritage in the eicnet is to support *conceptual addressing*. Heritage provides a way for a newer eics object to reference or update an older eics object while preserving both the history and accessibility of the original object. In short, heritage affects the concept without affecting the content.

Inheritance is a familiar concept, and we'll use it to explain the mechanics of eicnet heritage. It is a one-way transaction: though both the parent and child remain addressable via their respective muids, the child inherits attributes from the parent while the parent remains unchanged. There are two steps to the process when referencing the child. First, the consumer (not the author) adds all of the hash-addressed binary blobs from the parent to her content map for the child. Then, the manifest mapping of the child is chained on top of the parent. At this point, the eics is "resolved".

Anteheritance is similar to inheritance, but reverses the relationship between parent and child. Referencing the parent yields a modification, while the child remains unchanged by the parent. Note that because the static parent was written before the static child, the parent contains no explicit link to the child. Therefore the eics consumer *must already be aware of the child* to apply the anteheritance.

Note that heritage is completely unrestricted in terms of authorship and key reuse. In other words, a child eics may be authored by a different muid than a parent, and the child may or may not reuse the parent key (though they should never reuse a nonce!). When reusing keys, new eicas may or may not be generated, and lacking an explicit access file, consumers should always try the parent key. But, just because someone has anteherited or inherited an eics, does **not** mean that everyone will agree to that heritage. It is up to the consumer to allow or disallow a child to anteherit, and accessing a parent does not automatically imply that the consumer will likewise access an inheriting child.




You can't use the dynamic address strategy (static address for dynamic content based on original address) in place of anteheritance, because other people anteheriting (which should be a normal thing) needs to be behind the container wall, or metadata is publicly available.


## Universal identities

Messages themselves are authenticated, but identities are not. So, for deniable secure communications, just create a fresh identity (register a new public key). All messages from that new key will be provably owned, but the person or thing behind the fresh identity itself will not be connectible with the identity.

This presents a vulnerability in that the fresh identity, by virtue of that relationship, cannot assure its conversation partner of its real identity. That functionality, however, *can* be accomplished by deniably linking the fresh identity with the old, permanent identity through a DHE. More concretely: Sam wants to leak something to Chris; Sam's usual public identity is S1 and creates a disposable identity S2. She then starts talking to Chris' normal public identity from S2 and, from within that context, shares a DHE with Chris **based on S1**. Since Chris knows he didn't create the DHE, this proves to Chris that S2 has access to S1's private key, but to an outside observer Chris could just as simply have created S2 and conducted the DHE with his own private key. The trick is that it has to be forgeable by either party, thereby making it "my word against theirs".

# Encrypted information container network

EIC files: "Encrypted Information Container". Wholly big endian.

Preferred file extensions are .eic, or, if specificity is desired, .eics for static files, .eicd for dynamic files, and .eica for access files.

Every .eic file has an muid, or muse UID, that serves as a unique identifier for the content. Regardless of stored location (hard drive, URL, p2p network...), an .eic file will be capable of unambiguous referencing by its muid. It is deterministically created from the content (aka EIC is content-addressed) from a SHA-512 hash ("file hash", see below). The muid is therefore 64B long.

Additionally, dynamic EIC files support a secondary (static) address, based upon their original content (in a forward-verifiable manner). It is also a SHA512 hash (see below).

## Pending changes

+ Will consider changing several length fields to ZBG-style ```[1-byte length size *n*][*n*-byte length]```
+ Change EICa signature to file hash XOR author XOR target?

## EIC Objects

EIC object files are the fundamental basis of a Muse network. Their goal is to

1. be easily, unambiguously referenceable,
2. keep data private until shared, and
3. have a verifiable origin.

Specifically, they are a 

1. content-addressed
2. encrypted container
3. with a public-record author.

and more specifically, they are

1. referenced via "muse unique identifier" or muid;
2. symmetrically encrypted, with keys exchanged through author-to-author APIs; and
3. include their author's muid within their unencrypted header.

On a technical level, they are built from two parts:

1. Public header
2. Private payload

and offer the following security assurances:

1. Confidentiality,
2. Integrity,
3. Authenticity of authorship

**However**, it is important to note that authors can be anything or anyone -- a server, a person, a sensor, any data producer/consumer -- and that the identity associated with that author's muid may be named, pseudonymous, or anonymous. As such, though the authorship is authenticated, it may not be meaningful; its physical origin is repudiable. It is also possible to deniably authenticate a disposable identity against an existing author, but that is handled and discussed in the service layer.

### Format

Note: optimized for faster reading than writing

**Public header:**

| Offset | Length    | Name                | Format              | Hexoff |
| ------ | --------- | ------------------- | -----------         | ---    |
| 0      | 4B        | Magic number        | x65x69x63x6F        | 0      |
| 4      | 4B        | Version             | Unsigned 32-bit int | x      |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x      |
| 10     | 64B       | File hash           | Bytes               | x      |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x      |
| 76     | 66B       | Author muid         | Bytes               | x      |
| 142    | 512B      | Author signature    | Bytes               | x      |
| 654    | 8B        | Payload length, *N* | Unsigned 64-bit int | x      |
| 662    | *N*       | Private payload     | Encrypted blob      | x      |

Total public length 662B | 294



Adding an hmac would prove that the author had access to the symmetric encryption key *before* consumer decryption.

1. corollary: how would a consumer decrypt the resource without the author sharing the key?
2. allow the network to police naive, unprivileged attempts to steal authorship (except how would the attacker distribute the key?)

it cannot:

1. offer any assurance of a relationship between plaintext and author (beyond first-come-first-serve)






#### 1. Magic number

EIC object magic number: ASCII "eico" (65 69 63 6F).

#### 2. Version numbers

The version number is a semantic version number (ex. 1.2.3, where 1 is the major, 2 is the minor, 3 is the patch) encoded as unsigned integers as follows:

| Name  | Bit length | Offset |
| ---   | ---        | ---    |
| Maj   | 8          | 0      |
| Min   | 8          | 8      |
| Patch | 16         | 16     |

Therefore, the maximum version is 255.255.65535. Version needs to be after the file hash so that the author signature verifies the correct version is applied when opening.

**The current EICO version is 0.0.11.**

#### 3. Address algo

EIC will eventually need multiple hash algorithms. They are represented as a 16-bit integer immediately following the magic number.

Address hash algorithms, by integer representation:

1. **0x1:** SHA-512.

#### 4. File hash

The hash digest of the file. It is constructed from the concatenation of magic number, version bytes, author/recipient muid, and all bytes following the signature. In other words, it is (Magic = [0, 3] + version = [4, 7] + author/recipient = [76, 141] + rest = [654, EOF]). This, together with the integer representation of the algorithm used (see "Address algo" above), is used as the unique content identifier (the muid) for each file, which is therefore (for hash algo #1) 66 bytes long, and can be directly read from any EIC file as bytes [8, 73]. 

Note that the file hash excludes the address algorithm, cipher suite, signature, and of course the file hash itself. Omitting or modifying any of these fields is synonymous with denial of access. Magic number is included to minimize exposure to filetype spoofing between EIC filetypes (though it should be noted that in its current version, no eic file can be properly parsed with a mismatched version). The version number is included to decrease exposure to version spoofing and increase flexibility of future versions. Author/recipient muid *must* be included to prevent spoofing the author + signature combination -- by including the muid in the file hash, though a different identity could claim authorship, it would create a second muid for the contested author. The remainder of the file must also be included (for hopefully obvious reasons).

#### 5. Cipher suite

EIC will eventually offer support for multiple cipher suites. They are represented as a 16-bit integer immediately following the address hash algorithm declaration.

Cipher suites, by integer representation:

1. **0x1:** SHA512/AES256/RSA4096. **Address algorithm:** 0x1 = SHA-512. **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537. **Symmetric encryption:** AES-256 in CTR mode with nonce prepended to payload ciphertext. Nonce generation is described below. **Diffie-Hellman deniable exchange:** 2048-bit [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.

Note on the redundancy of the address algorithm: in a slightly more ideal world, it might be possible to separate out the function of content addressing from the question of the underlying cipher suite. However, since the file hash is used for signature generation, it is an inherent component of the security for the file. Therefore, though it is redundant and perhaps not an ideal division of concerns, we are including it redundantly within the cipher suite.

#### 6. Author signature

Provides assurances of integrity, authenticity, and non-repudiation from the author of the file. An asymmetric cryptographic signature of the file hash / muid.

#### 7. Author muid

EIC objects make the backbone of the eicnet. Any persistent network objects are built from them. "Identities" -- at a minimum, the public half of a public/private keypair -- are no exception. As such, instead of encasing the public key in each file (or some other nonsense), every file makes reference to the muid of its author. For access files, they also target a specific identity.

Within the EICs corresponding to that author, the public key should be defined under the dictionary key "pubkey". This will probably change in the process of dealing with multiple cipher suites.

Note that the author muid must be used in the file hash to prevent a potential storage provider attacker from altering the author field. With the author not included in the file hash, this would not result in a different muid; with it contained, an attacker is prevented from retroactively claiming authorship over *existing* resources.

#### 8. Payload length

A 64-bit integer representation *N* of the length of the binary blob associated with the payload. If the cipher suite prepends the nonce to the ciphertext, this length will include the nonce. The total length of the file should therefore be (660 + *N*) bytes.

#### 9. Private payload

The payload follows the public header immediately, with no padding. It is encrypted according to the cipher suite definition, as described in the public header. It may therefore begin with a nonce. The file terminates with the end of the payload, with no end flags.

## Static bindings

Static bindings allow storage providers to effectively answer the question "when am I allowed to delete this resource?" They are a public record of data retention -- a formal declaration of information use -- and prevent compliant storage providers from garbage collecting their referenced EIC objects. They directly and publicly reference the bound EICO muid and contain a public record of the author that created the binding. They do not create a secondary address for content.

There is no concept of individual copies of information with Muse; identical content encrypted identically will always be referenced identically, regardless of storage location. Therefore sharing content does not create copies; if Alice authors content and shares it with Bob, he has no inherent way of preserving access to that information. Static bindings create exactly such a mechanism: Bob may now place a hold on the data. The caveat is that, as public information, Alice will know that Bob is the person preventing erasure.

### Format

| Offset | Length    | Name                | Format              | Hexoff |
| ------ | --------- | ------------------- | -----------         | ---    |
| 0      | 4B        | Magic number        | x65x69x73x62        | 0      |
| 4      | 4B        | Version             | Unsigned 32-bit int | x      |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x      |
| 10     | 64B       | File hash           | Bytes               | x      |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x      |
| 76     | 66B       | Binder muid         | Bytes               | x      |
| 142    | 512B      | Binder signature    | Bytes               | x      |
| 654    | 66B       | Target muid         | Bytes               | x      |

Total length 720B | x

#### 1. Magic number

Static binding magic number: ASCII "eisb" (65 69 73 62)

#### 2. Cipher suite

See above (identical to EICO and necessary for signature generation / verification)

#### 3. Binder signature

The signature of the request's file hash by the binder. Prevents (in the above example) a third party, Eve, from binding to the EICO while pretending to be Bob. Also prevents a third party from constructing a re-binding of the author's original binding after the author debinds.

#### 4. File hash

As usual, the hash of bytes 584 onwards.

#### 5. eisb version

**Currently 0.0.3.**

#### 6. Binder muid 

The muid of the identity requesting the binding.

#### 7. Target muid

The muid of the resource to bind to.

## Dynamic bindings

Dynamic bindings, like static bindings, formally declare data use and block data removal. However, unlike static bindings, they allocate a secondary dynamic content address to existing static muids, and may be updated by the binding party. In other words, they bind a static muid to potentially dynamic content.

Note that dynamic bindings *must* be implemented in this abstraction layer to allow for state-preserving APIs (including, for example, sessions) to exist. They are the most primitive form of reconciling immutable content (like a single temperature reading) with mutable concepts (what's the temperature).

### Format

| Offset | Length    | Name                    | Format              | Hexoff |
| ------ | --------- | -------------------     | -----------         | ---    |
| 0      | 4B        | Magic number            | x65x69x64x62        | 0      |
| 4      | 4B        | Version                 | Unsigned 32-bit int | x      |
| 8      | 2B        | Address algo            | Unsigned 16-bit int | x      |
| 10     | 64B       | File hash               | Bytes               | x      |
| 74     | 2B        | Cipher suite            | Unsigned 16-bit int | x      |
| 76     | 66B       | Binder muid             | Bytes               | x      |
| 142    | 512B      | Binder signature        | Bytes               | x      |
| 654    | 64B       | Dynamic hash            | Bytes               | x      |
| 718    | 32        | Nonce                   | Bytes               | x      |
| 750    | 4B        | Dynamic frame count *n* | Unsigned 32-bit int | x      |
| 754    | 66B * *n* | Bound muids             | Bytes               | x      |

Minimum length (header + 1 muid) xB | x

#### 1. Magic number

Dynamic binding magic number: ASCII "eidb" (65 69 64 62)

#### 2. Cipher suite

See above.

#### 3. Binder signature

The signature of the muid requesting the dynamic binding. This is the signature of the local file hash, **not** the dynamic muid (or any individual bound muid).

#### 4. File hash

The hash of this binding request, starting immediately after the hash (ie starting with byte 584, as with EICOs).

#### 5. EIDB version

**Currently 0.0.7.**

#### 6. Binder muid

The muid for the entity creating the dynamic binding.

#### 7. Dynamic hash

The muid hash for the secondary address for the dynamic object. This is generated from the hash of the concatenated nonce, binder muid, buffer frame count, and bound muids of the first frame in the binding. That is, the binder takes bytes 652 and onwards of the binding, hashes the result, and is left with the dynamic muid. From that point forward, the dynamic muid does not change, regardless of updates to the binding.

#### 8. Nonce

32 bits of random, non-reusable noise, used to generate and verify the dynamic muid. The nonce requirement is created to allow the same author to create two divergent dynamic bindings from the same start point. It is included in the header to allow future verification, guarding against spoofing dynamic muids.

#### 9. Dynamic frame count

The count of the bound muids. Buffered objects may be natively created by binding to more than one EICO. The byte length of the remaining binding (the bound muids) can be calculated by the frame count * 64 (the length, in bytes, of a single muid).

#### 10. Bound muids

An ordered list of the muids for each frame. This should be used as a FIFO queue, ie, the leftmost (first) muid is the most recent frame.

## API requesters

API requesters (EIARs, which I pronounce "ears") serve as signaling mechanism for an API handshake. They are encrypted asymmetrically against the public key of the recipient. Remember that all EICOs are single author, (potentially) multiple consumer -- in other words, unidirectional. Any bidirectional API therefore requires both a client/consumer pipe and a server/producer pipe, even just to issue an ACK. An eiar is generated by the API client/consumer, signaling both the muid and access key for the consumer's "pipe" to the API. In response, the API producer generates a response eiar, signaling the muid and key to its response pipe.

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
| 76     | 66B       | Recipient muid      | Bytes               | x       |

Total length 654B | x

**Inner container:**

| Offset | Length    | Name                 | Format      |
| ------ | --------- | -------------------  | ----------- |
| 0      | 32B       | Inner nonce          | Bytes       |
| 64     | 66B       | Author               | Bytes       |
| 130    | 66B       | Target muid          | Bytes       |
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

#### 6. Recipient muid

The muid for the recipient.

#### 7. Inner hash

Digest of all remaining bytes in the inner container. It is included as an integrity assurance and to ease signature verification.

#### 8. Author

The muid of the identity creating the request.

#### 9. Target muid

The muid of the dynamic binding to use for the API pipe. By keeping this encrypted, there is no public link between the author and the recipient.

#### 10. Target symmetric key

The encryption key for the API pipe.

### Known vulnerabilities

EIAR files have one design tradeoff that justifies explicit discussion. To prevent an observer from inferring a connection between the API consumer and the API provider, the signature can only be verified **after** decrypting the request. Though this *does* prevent a malicious party from successfully initiating an API pipe on someone else's behalf, because the signature verification requires the recipient's private key, it cannot be performed by the network. This opens a potential DoS attack vulnerability: an API provider could be overwhelmed by spoofed EIARs with invalid signatures from invalid muids. Such an attack would compromise the API provider's ability to initiate new API pipes, but presumably leave its ability to handle existing APIs intact.

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
| 76     | 66B       | Recipient muid      | Bytes               | x       |
| 142    | 512B      | Author signature    | Bytes               | x       |

Total length 654B | x

**Inner container:**

| Offset | Length    | Name                       | Format              |
| ------ | --------- | -------------------        | -----------         |
| 0      | 32B       | Inner nonce                | Bytes               |
| 32     | 66B       | Author                     | Bytes               |
| x      | 66B       | Requesting muid (optional) | Bytes               |
| x      | 32B       | Error code (optional)      | Unsigned 32-bit int |

Total size: xB (encrypted size 512B) | x

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

#### 6. Recipient muid

The muid for the recipient (the identity requesting the API that is currently being denied).

#### 7. Inner nonce

Random noise. This preserves verifiability of the signature, while still protecting the author from a public key dictionary attack, even if all optional arguments are omitted.

#### 8. Author

The entity denying the EIAR.

#### 9. Requesting muid

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
| 76     | 66B       | Recipient muid      | Bytes               | x       |
| 142    | 512B      | Author signature    | Bytes               | x       |

Total length 654B | 28C

**Inner container:**

| Offset | Length    | Name                    | Format              |
| ------ | --------- | -------------------     | -----------         |
| 0      | 32B       | Inner nonce             | Bytes               |
| 32     | 66B       | Author                  | Bytes               |
| x      | 66B       | Closing muid (optional) | Bytes               |
| x      | 32B       | Exit code (optional)    | Unsigned 32-bit int |

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

#### 6. Recipient muid

The muid for the recipient (the identity requesting the API that is currently being denied).

#### 7. Inner nonce

Random noise. This preserves verifiability of the signature, while still protecting the author from a public key dictionary attack, even if all optional arguments are omitted.

#### 8. Author

The entity denying the EIAR.

#### 9. Closing muid

The muid of the dynamic API pipe that this ACK is recognizing the closure of. May be omitted to reuse the built ACK for subsequent requests from the same recipient. EIAKs should be very rare compared to other EIC traffic: the represent both successful creation and removal of an API. As such, best practice is to always include this field. However, a cached, generic ACK may be used as a fallback for subsequent denied requests from the same source in times of heavy traffic.

#### 10. Exit code

An API-specific exit code. Should be defined by the API itself, and entirely optional. If one is available, best practices should follow similar fallback-to-generic behavior as the closing muid.

## Debind requestors

Debind requestors remove

1. Static bindings,
2. Dynamic bindings, or
3. API requestors.

They will only be accepted from the muid that created the binding (or the recipient of the API requestor).

### Format

| Offset | Length    | Name                | Format              | Hexoff |
| ------ | --------- | ------------------- | -----------         | ---    |
| 0      | 4B        | Magic number        | x65x69x58x58        | 0      |
| 4      | 4B        | Version             | Unsigned 32-bit int | x      |
| 8      | 2B        | Address algo        | Unsigned 16-bit int | x      |
| 10     | 64B       | File hash           | Bytes               | x      |
| 74     | 2B        | Cipher suite        | Unsigned 16-bit int | x      |
| 76     | 66B       | Debinder muid       | Bytes               | x      |
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

#### 6. Debinder muid

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
+ List bindings for muid

Subscribe behavior: let's say I subscribe to MUID123. The subscriber should expect the following behavior:

1. SP will send client the muid of any available resource with MUID123 as the public recipient;
2. SP will send client the muid of any available resource with MUID123 as the public author;
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

### Access providers

### Identity providers

## Application layer

### API definitions

### Best practices

# Applications and implications

## Scalable network deployment

Treat networks of any size as the same kind of entity. Every network just supplies whichever service layer components it wants.

## Decentralized systems

+ Distributed consensus storing (use something like maidsafe as the storage provider for the whole internet). Use proof of storage as a cryptocurrency?
+ Distributed consensus computing (add another primitive to the service layer: like maidsafe, except instead of distributed storage, it's distributed computing -- but offers similar guarantees). Could use this for network architecture, servers, etc. Use proof of execution as a cryptocurrency?
+ Proof of storage + proof of execution = proof of contract fulfillment.
+ Secure nested routing (like TOR) on an underlying P2P storage provider is very easy

## Egalitarianism

This is a vital step towards *self-enforcing* net neutrality. The storage provider API creates a strong division of concerns between network and application operations.

## Digital statehood

Meaningful social contracts with the services we use.

Could be cool.

































# Scratchbook

**"Test post, please ignore"**



Compared to the contemporary status quo, decentralization of network infrastructure (physical or virtual) constititutes a beneficial divestiture of institutional power. It creates room for smaller players to participate in network operations, and as such, reduces the likelihood of network abuse. But **for some individuals and organizations, total decentralization is rarely (if ever) a practical option.** As such, truly effective networks are of hybrid design: capable of decentralization, but never contingent upon it. Ignoring the complicated subject of spam management, email is by far the most successful example of this approach, and Muse seeks to emulate its approach.



Design decision: (slightly redundantly) separate the address hash definition from the cipher suite in the header. Decreases computational cost, eases pain of maintaining multiple hash algos, and decreases size of muid links, which must include the hash algo.





Dynamic EIC files are a specialized symmetrically-encrypted container file. They are intended to retain static content-addressability with truly mutable content. They are **not** meant to be a mutable form of an EICs file, and EICd files support *only a single binary blob*. There are some tradeoffs involved with these files, and generally speaking they are not intended to be widely shared, but instead be reserved for the few situations in which maintaining a full set of anteherited static files would be impractical, impossible, or expensive. There is also additional overhead associated with the initial creation of dynamic files, as they involve more hash operations. However, they need not resolve inheritance/anteheritance chains, so bandwidth requirements may be lower.

Keep in mind that even here, the ephemerality of these files is still unenforceable. Dynamic files are ones in which the storage layer is *permitted*, but not *required* to remove previous versions. Similarly, they are *expected*, but might not *guarantee*, that the buffer *request* is fulfilled.  Neither removal nor complete buffering are enforceable by storage layer consumers -- though that does not prevent consumers from changing storage providers. Presumably you should not be charged in the event that data persists beyond its requested lifetime.

It is also important that, from square one, dynamic files be declared as such, so that if they're shared, users would be aware that the contents of the file may be especially short-lived.

Specific EICd goals:

1. Can be searched for by header hash (returns a list of all muids associated with that header hash)
    + Problem: header must be deterministically unique for each file
    + Solution: use a zeroth hash in the header
    + Problem: build order -- zeroth hash cannot be a file hash, as the file is unbuilt when it is calculated
    + Solution: use a zeroth payload hash
2. Supports buffered frames
    + Problem: content addressing for specific frames
    + Solution: requestable through muid as always
    + Problem: knowing how many buffer frames to maintain
    + Solution: buffer length flag
3. Buffered frames have a determinate order
    + Problem: zero-knowledge, self-contained sequence
    + Solution: hash chain (include the previous muid in the header)
    + Problem: muid must always be verifiably correct for the content, and replacing the zeroth hash with a hash chain prevents this
    + Solution: hash chain must be separate field
4. Termination
    + Problem: efficient 'delete' notification
    + Solution: transmit header with no payload and 0 buffer request



# Potential future improvements

1. Dedicated standard for key exchange API: avoid RSA signature overhead once API channel has been created.
2. ECC





## Caveats:

+ Ephemerality is unenforceable

---------------

This is a *digital* information network. Until humans are capable of directly processing digital data, human-addressability is outside the scope of the framework. Human meaning should be layered on top of machine meaning; anything else is an improper forced abstraction.

---------------

Sharing privacy is a different scope; in general, we have to rely upon social norms to control that.

-----------

Decouple http://en.wikipedia.org/wiki/Zooko%27s_triangle

-----------

Provide the absolute lowest level of functionality necessary to implement such a system, even the most common higher level functions are outside the scope of the file format.

-----------

Absolute minimum level of intrusion; absolute minimum level of overhead.

----------

"Agent state consistency"


## A next-generation internet built around secure, private, scalable interactions between equal agents

Today's web is public and centralized by default. DNS, HTTP, SMTP, and the rest of the internet's application layer negotiate conversations between *machines*, lacking any inherent awareness of the "agent" responsible for receiving and acting upon the transferred information. This network-oriented architecture, in contrast to an agent-oriented architecture, is one of the major limitations of the contemporary internet. 

Network-oriented architecture is characterized by static machine addressing, regardless of agent identity: IP addresses talking to each other on behalf of unknown parties. This failure to separate inter-agent communication from network operations hinders modularization and distribution of both network and services. Changing network configurations can break existing data references, decreasing the dependability of "persistent" data. And because it is blind to the intended recipient of network traffic, network-oriented architecture makes robust end-to-end security exceptionally difficult, posing a significant burden to meaningful digital privacy.

Agent-oriented architecture, however, is characterized by static agent identity, regardless of machine addressing: direct conversation between individual parties, on any client machine or network topology. It requires a new abstraction layer to mediate between the network-oriented transport layer and the redefined, agent-oriented application layer. This new "service layer" is, both by design and necessity, "private by default": encrypted at rest, with information unshared upon creation. This service layer provides data persistence, agent identification, and information sharing to the network as a whole at the protocol level.

This new internet protocol stack negotiates network-location-based data streams into action-based endpoints more effectively than the existing world wide web, making it far more suitable for the ever-growing world of internet-connected physical devices. 

-------

Because its primary goal is to transform the loosely-connected tangle of internet services into a single amalgamated source of information, we've termed this new configuration the mesh-made-muse.


------------

Crucial information semantics -- like authorship or creation date -- exist only through third parties.

and information is inherently semantic. 

--------------