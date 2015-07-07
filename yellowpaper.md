**DANGER ZONE:** This document is under review and is absolutely **not considered final!**

A next-generation internet built around secure, private, scalable interactions between people and things 
=========

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

Table of contents
--------------

asdf

Inseparable privacy
------------------

Much of the benefit to agent-oriented architecture is contained in the ability to treat the internet as a global asynchronous communications system. Agents directly address other agents, and the muse itself is treated as a single coherent information repository. As such, it replaces the website as a medium for data storage, which, by extension, eliminates the notion of site-specific privacy. This arrangement **requires** *all* confidential data to be private end-to-end, or it would be immediately exposed to every other agent. We are then left with a choice: have no explicit security requirement, allowing individual applications to develop their own information controls; or, make the muse private by default, thereby implementing a new standard for universal data privacy. The proliferation of both generic security solutions like SSL, and of the astoundingly important privacy questions now regularly raised, strongly indicates that the latter option is the clear choice.

Having spelled out a privacy requirement, we need to define its scope. Since we're discussing information that already exists, and information isn't known to spontaneously appear, we can assume it has an author. We see privacy as an absence of information, and therefore to be "private by default", information must be known to *only and exactly* its author at the time it is created. This is intuitive in the physical world, and it's our firm belief that this paradigm should also apply to digital data. With current technology, we can then say that a functional definition of "private by default" requires data to be encrypted from pre-upload to post-download. This is known as [end-to-end encryption](http://en.wikipedia.org/wiki/End-to-end_encryption).

However, 100% private information isn't particularly useful. The web derives most of its utility from the *collective* knowledge that is stored there; without the ability to share information, the internet would be little different from an exceptionally large paper notebook. Sharing is clearly a practical necessity, but for the "private by default" requirement to have any meaning, we have to set some ground rules:

1. **Sharing requires agency.** An agent must be capable of independent action. Agency implies the ability to intentionally create, share, and receive information and cannot be directly restricted by other agents. Agents can be influenced but not controlled. Agents may be non-human, but all agents are equal of opportunity.
2. **Sharing requires consent.** An agent must make a deliberate choice to share information. Consent is informed: an agent must know exactly and exhaustively what is being shared and recipients must be explicitly known by the agent before consent. Informed consent requires limited consent; past sharing does not imply future sharing. To be voluntary, consent must also be deniable: the agent *must* see non-sharing as a viable option. [Vendor lock-in](en.wikipedia.org/wiki/Vendor_lock-in) is non-consensual, and manifests itself in a myriad of ways.
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


Scratchbook
===============

Caveat:
------

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


#Technical implementation

Pending changes
-----------

None at this time.

Rejected changes
-------

+ "Nonce/IV will be moved *after* the payload, and the payload length field will exclude the nonce/IV. File hash will then use the payload length to exclude the nonce/IV from the muid." This doesn't actually help any. A different nonce/IV will always, always result in a different muid, unless the hash is of pre-encrypted content, which is a security vulnerability.
+ "Heritage linearization algorithm (modified C3) will be fully defined. This is an enhancement that will not affect the binary representation of EIC objects." This will be moved to a different level of abstraction. It doesn't fit the "bare minimum intrusion" philosophy. Leave it to convention/contract, not to the base definition.

.eic filetype 
========

"Encrypted Information Container". Wholly big endian.

Preferred file extensions are .eic, or, if specificity is desired, .eics for static files, .eicd for dynamic files, and .eica for access files.

Every .eic file has an muid, or muse UID, that serves as a unique identifier for the content. Regardless of stored location (hard drive, URL, p2p network...), an .eic file will be capable of unambiguous referencing by its muid. It is deterministically created from the content (aka EIC is content-addressed) from a SHA-512 hash ("file hash", see below). The muid is therefore 64B long.

Additionally, dynamic EIC files support a secondary (static) address, based upon their original content (in a forward-verifiable manner). It is also a SHA512 hash (see below).
Common fields
===========

Magic number
----------

For the usual purpose.

Cipher suites
------------

EIC will eventually offer support for multiple cipher suites. They are represented as a 32-bit integer immediately following the magic number.

If the cipher suite uses a nonce or IV, **it will be prepended to the payload ciphertext**. As such, any implementations should be aware of the cipher suite while unpacking the payload.

Cipher suites, by integer representation:

+ **0x00000001:** SHA512/AES256/RSA4096. **Addressing:** SHA-512. **Signature:** RSASSA-PSS, MGF1+SHA512, public exponent 65537. **Asymmetric encryption:** RSAES-OAEP, MGF1+SHA512, public exponent 65537. **Symmetric encryption:** AES-256 CTR SHA512/AES256/RSA4096. **Deniable exchange:** 2048-bit [Group #14](http://www.ietf.org/rfc/rfc3526.txt) with a generator of 2.

Author signature
----------

Provides assurances of integrity, authenticity, and non-repudiation from the author of the file. It is an asymmetric cryptographic signature, generally of the file hash / muid. *For access files, this is a signature of the bitwise XOR of the file hash + inner hash*. This is to preserve the security of the author's identity to an outside observer with a dictionary of public keys.

File hash / muid
----------

The SHA-512 digest of the entire remaining .eic. This is used as the unique content identifier for each file. Note that it excludes the magic number and signature (and therefore starts at byte 580)

Version numbers
-------------

The version number is a semantic version number (ex. 1.2.3, where 1 is the major, 2 is the minor, 3 is the patch) encoded as an unsigned 32-bit integer as follows:

| Name  | Bit length | Offset |
| ---   | ---        | ---    |
| Maj   | 8          | 0      |
| Min   | 8          | 8      |
| Patch | 16         | 16     |

Therefore, the maximum version is 255.255.65535. Version needs to be after the file hash so that the author signature verifies the correct version is applied when opening.

Recipients and authors
---------

EICs files make the backbone of the eicnet. Any persistent network objects are built from them. "Identities" -- at a minimum, the public half of a public/private keypair -- are no exception. As such, instead of encasing the public key in each file (or some other nonsense), every file makes reference to the muid of its author. For access files, they also target a specific identity.

Within the EICs corresponding to that author, the public key should be defined under the dictionary key "pubkey". This will probably change in the process of dealing with multiple cipher suites.

Access files
==========

Access files exist solely for access control to static and dynamic files. They may (or may not, we'll see) support deletion for forward security. They are encrypted asymmetrically against the public key of the target.

Outer header fields:
-------------------

| Offset | Length    | Name                  | Format              | Hex off |
| ------ | --------- | -------------------   | -----------         | ---     |
| 0      | 4B        | Magic number          | x65x69x63x61        | 0       |
| 4      | 4B        | Cipher suite          | Unsigned 32-bit int | 4       |
| 8      | 512B      | Author signature      | Bytes               | 8       |
| 520    | 64B       | File hash             | Bytes               | 208     |
| 584    | 4B        | .eica spec version    | Unsigned 32-bit int | 248     |
| 588    | 64B       | Recipient muid (eics) | Bytes               | 24C     |

Total length 652B | 28C

1. Magic number: "eica" (65 69 63 61) for an asymmetric .eic.
2. Cipher suite, see above
3. Author signature: note atypcal signature, explained above
4. File hash
5. eica version: **currently 0.0.6.** 
6. Recipient muid: the single recipient of the file.

Inner container:
----------

| Offset | Length    | Name                 | Format      |
| ------ | --------- | -------------------  | ----------- |
| 0      | 64B       | Inner hash           | Bytes       |
| 64     | 64B       | Author               | Bytes       |
| 128    | 64B       | Symmetric target     | Bytes       |
| 192    | 32B       | Target symmetric key | Bytes       |

Total size: 224B (encrypted size 512B) | 3A8

1. The inner hash: Digest of all remaining bytes in the inner container. It is included as an integrity assurance.
2. The author's muid
3. Target muid: points at the muid of the .eics/.eicd resource that the symmetric key unlocks. By keeping this encrypted, there is no public link between the author and the recipient.
4. Target symmetric key: encryption key for the .eics/.eicd resource.

**As a quick note:** the maximum size of an RSA-4096 container is around 440 bytes, depending on the algo. In the future, a second symmetric 32B HMAC key may be added for verification speed on the EICs file. That said, I cannot currently think of a good way to include an HMAC for the EICa, even though it would benefit from being able to more cheaply verify content compared to post-unlocking verification. Note that this also means that only the intended consumer (the recipient) can verify the file.

Dynamic
==========

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

Outer header:
------------------

| Offset | Length    | Name                | Format              | Dynamic updates? | Hexoff |
| ------ | --------- | ------------------- | -----------         | ------------     | ----   |
| 0      | 4B        | Magic number        | x65x69x63x64        | No               | 0      |
| 4      | 4B        | Cipher suite        | Unsigned 32-bit int | No               | 4      |
| 8      | 512B      | Author signature    | Bytes               | Must             | 8      |
| 520    | 64B       | File hash           | Bytes               | Must             | 208    |
| 584    | 64B       | Header hash         | Bytes               | No               | 248    |
| 648    | 4B        | .eic spec version   | Unsigned 32-bit int | No               | 288    |
| 652    | 4B        | Buffer request      | Unsigned 32-bit int | May              | 28C    |
| 656    | 64B       | Previous file hash  | Bytes               | Must             | 290    |
| 720    | 8B        | Payload length      | Unsigned 64-bit int | May              | 2D0    |
| 728    | 64B       | Author muid         | Bytes               | No               | 2D8    |
| 792    | 64B       | Zeroth payload hash | Bytes               | No               | 318    |

Total length 856B | 358

1. Magic number: "eicd" (65 69 63 64).
2. Cipher suite, see above.
3. Author signature
4. File hash
5. Header hash: calculated once, at the original creation of the dynamic file. It is static for every eicd. It is included for integrity verification and ease of lookup and is calculated from the concatenation of the eicd version, author muid, and zeroth payload hash, in that order.
6. eicd spec version: **currently 0.0.4.**
7. Buffer request: An unsigned 32-bit integer representing the maximum number of requested buffer frames. Again, this is a request, and it may or may not be honored by the storage provider.
8. Previous file hash: the file hash/muid for the previous file in the buffer. For the first frame, it is initialized with a copy of the header hash. This hash forms a secure chain preserving eicd buffer frame order.
9. Payload length
10. Author muid: note that this will always remain constant, and another author wishing to "fork" the object must create a new EICd object, as this is included in the header hash. By definition the fork therefore loses downwards referencing.
11. Zeroth payload hash: The hash of the payload (byte 852:end) of the original dynamic object (after encryption). This is necessary to be able to request eicd files based on their headers alone (otherwise, every file from the same author would be identical).

The payload
-------------

The encrypted portion of the EICd file, once unlocked, is a single binary blob. Again, EICd files are not meant to encompass the self-descriptive property of EICs files. They are a very specific type of asymmetric, semi-persistent communication. Yes, because the payload is arbitrary binary data, it can contain literally anything, but it is not meant to serve as a mutable replacement for EICs files in any way. If, for example, you wanted to construct a dropbox-like folder sync mechanism using binary files, you would first need a base object for the directory, and then a number of individual dynamic objects for the individual files.

Static
==========

Static EIC files are, abstractly speaking, a symmetrically-encrypted, network-aware key: value store with a sophisticated form of semantic metadata. Each eics is a composition of its keys, and, were the eics to be destroyed, those keys would be lost. However, it might also be an aggregation of other eics. These aggregations are handled by the object's heritage (see below).

Outer header:
------------------

| Offset | Length    | Name                | Format              | Hexoff |
| ------ | --------- | ------------------- | -----------         | ---    |
| 0      | 4B        | Magic number        | x65x69x63x73        | 0      |
| 4      | 4B        | Cipher suite        | Unsigned 32-bit int | 4      |
| 8      | 512B      | Author signature    | Bytes               | 8      |
| 520    | 64B       | File hash           | Bytes               | 208    |
| 584    | 4B        | .eic spec version   | Unsigned 32-bit int | 248    |
| 588    | 64B       | Author muid         | Bytes               | 24C    |
| 652    | 8B        | Payload length      | Unsigned 64-bit int | 28C    |

Total length 660B | 294

1. EICs magic number: "eics" (65 69 63 73).
2. Cipher suite, see above
3. Author signature
4. File hash
5. eic spec version: **currently 0.0.7.**
6. Author muid
7. Payload length

**Notes:**

There is a particular type of forward *security* offered here, is it is impossible (for all practical purposes) to reconstruct a new payload for a given muid, even if a malicious attacker had both the symmetric key for the resource and the private key of the author. However, forward *secrecy* is a much more complicated topic.

Inner header:
------------

| Offset   | Length | Name                     | Format              |
| ------   | ------ | ------------             | -----------         |
| 0        | 4B     | Inheritance length *i*   | Unsigned 32-bit int |
| 4        | 4B     | Anteheritance length *a* | Unsigned 32-bit int |
| 8        | 4B     | Manifest byte length *m* | Unsigned 32-bit int |
| 12       | 4B     | ToC length *c*           | Unsigned 32-bit int |
| 16       | *i*    | Inheritance              | See above           |
| 16 + *i* | *a*    | Anteheritance            | See above           |
| ...      | *m*    | Manifest                 | See below           |
| ...      | *c*    | Table of contents        | See above           |
| ...      | ?      | Content                  | See above           |

Total length: ?

1. Inheritance: an array of muids. There is no padding nor delimiter between muids, so the inheritance length must be a multiple of 64B. If no inheritance is needed, the length will be zero, and the field omitted. **Order is important.** See the Heritage section.
2. Anteheritance: same format as inheritance. See the Heritage section.
3. Manifest: controls implementation of the EICs key: value store. ZBG encoded, see below.
4. Table of contents: describes the inner archive. Each blob contained within the archive gets a line in the ToC consisting of: SHA-512 hash, 64-bit unsigned int offset, 64-bit unsigned int length. May be empty (ToC length 0; field is then skipped.) Offsets are currently from the first byte after the ToC (at least for now). To enforce EICs deterministic reproduceability, should be ordered ascending by the address hash.
5. Content: as described in the ToC, a flat, un padded, un delimited archive containing an arbitrary number of binary blobs. May be empty (but that would be atypical).

**Manifest:** every fully-resolved EICs file can be thought of as two combined structures: a series of content-addressed binary blobs, and a dictionary describing their contents. The keys, loosely speaking, constitute a local namespace within the container. Multiple keys may address a single piece of content, but there should be no duplicate keys (even after resolving inheritance/anteheritance; see Heritage). The file should be formatted as a single toplevel zbg dictionary. It should not be treated as a standalone file and should *not* include its own magic number or header. Keys may be arbitrary binary values, but keep in mind that non-text encodings may introduce compatibility issues with certain implementations. Values may be either arrays, dictionaries, or a ToC address. References to addresses in inherited or anteherited ToC's are also valid. Values **must** be addresses for content, not the content itself. This is inefficient for blobs smaller than a few hundred bytes, but it is necessary to support heritage. If the manifest allowed values in addition to hash references, those values would be unreachable by inherited or anteherited eics files. By forcing the manifest to use content references, a performance compromise is made to encourage deduplication and ease of use.

Heritage
===============

*Content* on the eicnet is static, but *concepts* are not. The fundamental idea behind heritage in the eicnet is to support *conceptual addressing*. Heritage provides a way for a newer eics object to reference or update an older eics object while preserving both the history and accessibility of the original object. In short, heritage affects the concept without affecting the content.

Inheritance is a familiar concept, and we'll use it to explain the mechanics of eicnet heritage. It is a one-way transaction: though both the parent and child remain addressable via their respective muids, the child inherits attributes from the parent while the parent remains unchanged. There are two steps to the process when referencing the child. First, the consumer (not the author) adds all of the hash-addressed binary blobs from the parent to her content map for the child. Then, the manifest mapping of the child is chained on top of the parent. At this point, the eics is "resolved".

Anteheritance is similar to inheritance, but reverses the relationship between parent and child. Referencing the parent yields a modification, while the child remains unchanged by the parent. Note that because the static parent was written before the static child, the parent contains no explicit link to the child. Therefore the eics consumer *must already be aware of the child* to apply the anteheritance.

Note that heritage is completely unrestricted in terms of authorship and key reuse. In other words, a child eics may be authored by a different muid than a parent, and the child may or may not reuse the parent key (though they should never reuse a nonce!). When reusing keys, new eicas may or may not be generated, and lacking an explicit access file, consumers should always try the parent key. But, just because someone has anteherited or inherited an eics, does **not** mean that everyone will agree to that heritage. It is up to the consumer to allow or disallow a child to anteherit, and accessing a parent does not automatically imply that the consumer will likewise access an inheriting child.

Since pure anteheritance leaves the child unmodified by the parent, it renders the child incapable of making manifest references to content stored within the parent. This is easily remedied: if the child both inherits and anteherits the parent, both eics will have identical states (provided the anteheritance is accepted by the consumer). This is generally not recommended for use with multiple anteheritance, as each successive inheritance/anteheritance pair increases the likelihood of unpredictable namespace collisions in the manifest mapping. They also may make your anteheritance significantly less likely to be honored.

Finally, particularly in light of the above, it bears mentioning that circular referencing is possible, but meaningless. Keep in mind that the heritage of a file simply describes the search order for manifest mappings. If we've already searched a static file for a given key, there is no need to re-search the same file. Circular references therefore terminate when they point to an muid that has already been searched.

Some examples
--------------

**A very simple email clone:**

*Parent eics*

ToC:

| Blob        | Hash/address |
| ---         | ---          |
| 'anna'      | hashblob1    |
| 'bob'       | hashblob2    |
| 'hello'     | hashblob3    |
| messageblob | hashblob4    |

Manifest:

| Manifest key | Content address |
| -------      | ---------       |
| 'subject'    | hashblob3       |
| 'from'       | hashblob1       |
| 'to'         | hashblob2       |
| 'message'    | hashblob4       |

*Child eics*

ToC:

| Blob          | Hash/address |
| ---           | ---          |
| 'Re: hello'   | hashblob5    |
| messageblob_2 | hashblob6    |

Manifest:

| Manifest key | Content address |
| -------      | ---------       |
| 'subject'    | hashblob5       |
| 'from'       | hashblob2       |
| 'to'         | hashblob1       |
| 'message'    | hashblob6       |

The child inherits the parent, adding two more pieces of content (addressed by hashblob5 and hashblob6). The child still has access to all of the previous 4 hash blobs and need not redefine them within the ToC. The child updates only the necessary manifest keys to point to the correct content.

**A link posted to a Reddit clone:**

*Parent eics*

ToC:

| Blob                                 | Hash/address |
| ---                                  | ---          |
| 'carol'                              | hashblob1    |
| 'http://www.google.com'              | hashblob2    |
| 'DuckDuckGo is a cool search engine' | hashblob3    |

Manifest:

| Manifest key | Content address |
| -------      | ---------       |
| 'user'       | hashblob1       |
| 'link'       | hashblob2       |
| 'title'      | hashblob3       |

*Child eics*

ToC:

| Blob                             | Hash/address |
| ---                              | ---          |
| 'Google is a cool search engine' | hashblob4    |

Manifest:

| Manifest key | Content address |
| -------      | ---------       |
| 'title'      | hashblob4       |

*Competing child eics*

ToC:

| Blob                     | Hash/address |
| ---                      | ---          |
| 'https://duckduckgo.com' | hashblob5    |
| 'david'                  | hashblob6    |

Manifest:

| Manifest key | Content address |
| -------      | ---------       |
| 'link'       | hashblob5       |
| 'editor'     | hashblob6       |

Carol corrects her link's title by anteheriting. Meanwhile, a competing anteheritor updates her link instead -- and note the manifest key addition. Consumers may, on an individual basis, choose to honor either, both, or neither anteheritances. Respectively, this could result in:

1. 'user' 'carol' submits 'http://www.google.com' with 'title' 'Google is a cool search engine'
2. 'user' 'carol' submits 'https://www.duckduckgo.com' with 'title' 'DuckDuckGo is a cool search engine', updated by 'editor' 'david'
3. 'user' 'carol' submits 'https://www.duckduckgo.com' with 'title' 'Google is a cool search engine', updated by 'editor' 'david'
4. 'user' 'carol' submits 'http://www.google.com' with 'title' 'DuckDuckGo is a cool search engine'

A note on the determinism and ordering of heritage
-----------------------

For any consumer, at any given time, with access to a given set of eics, the final eics mapping (after heritage resolution) would (ideally) be consistent. That is to say, so long as the consumer's information exposure remains constant, her net eics should (ideally) always be the same, regardless of software framework, device, etc. The problem here is that defining a specific search order is inherently limiting. While we can make recommendations, we really cannot assert a single linearization process for the network as a whole. Instead, we'll just say: both inheritance and anteheritance should be treated as ordered lists, searched from left to right. But, when using the same linearization algorithm for those lists, all heritage is determinate. And in a hypothetical world where every consumer has access to the sum total of eicnet information, heritage would be very straightforward to implement.

However, not every consumer has access to the same information. Even if two consumers are accessing the same inheriting child, if one consumer can't open a parent eics, the two consumers will have different mappings. Similarly, if their anteheritance preferences differ, so might their mappings. A consumer might also opt to change her resolution preferences over time, resulting in a differing final product even for the same consumer. So in this sense, it is very important to gracefully handle resolution failure. Use the best available information at all times (even old information is better than inaccessible information), and notify consumers of full failure when possible.

Using A#, Ref, I# to denote anteherited, referenced, and inherited eics respectively, resolution proceeds as:

1. Start with the ordered inheritance list as defined in the Ref eics: [Ref, I1, I2, I3]
2. Consumer prepends anteherited eics: [A1, A2, A3, Ref, I1, I2, I3]
3. Consumer linearizes that multiple inheritance tree
4. Perform key lookup against the linearized tree.

Now, as for our recommendation: we suggest linearizing with a slightly modified [C3](http://en.wikipedia.org/wiki/C3_linearization) algorithm that aims to robustly "best guess" the circular dependency problem when C3 fails. In general, since authors cannot control anteheritance, the linearization algorithm must err very heavily on the side of "attempt to resolve at all costs" to prevent clever content declarations from inducing load failures via anteheritance.