**WARNING:** This is a draft document dated May 2016. It is under review and is definitely not finalized. If you'd like to stay updated, consider joining our [mailing list](https://www.muterra.io/mailing-signup.html).

# Introduction

If Web 2.0 has been defined by the rise of the social internet, Web 3.0 will surely include the connected physical devices now being dubbed the "Internet of Things". Like its predecessors, the Internet of Things (IoT) will include and build upon the past. The emergence of social applications with physical components (currently these predominantly involve video sharing) attests to the importance of continued sharability. But a generally-accepted security solution for IoT has yet to materialize; and sites like Shodan -- which includes a search engine for vulnerable video feeds -- are yet another indication that this need is dire.

However, IoT security imposes substantially different challenges than Web 2.0. Broadly speaking there are two primary possible approaches, given existing best practices:

1. Issue certificates for each connected device, allowing end-to-end security
2. Issue certificates for a central server, requiring full trust in its owner

Even conservative growth estimates for IoT would imply that the first option, if pursued through existing certificate authority, would require orders of magnitude more certificates to be issued for IoT devices than in the entire previous history of the internet. Eschewing the certificate authorities might be possible, but with potentially dynamic IP addresses on both ends of the connection, sharability and reachability would remain substantial problems. And the second approach ultimately entails trusting companies with Facebook with access to internet-connected baby monitors. Clearly, neither option is particularly attractive.

An alternative approach would be to force end-to-end encryption of all IoT information, and implement a new addressing mechanism to communicate socially between devices. This would allow centralized, auditable services to concentrate transport-level security certificates without granting them access to data. This is an enticing compromise; however, with the requirement of scalable sharability, and more immediate constraints like dynamic data streams, existing protocols like PGP cannot fill this need.

Golix is a cryptgraphic protocol designed to fill this void. It allows for both asynchronous delivery and asynchronous sharing, making scalable encryption of data at rest possible even for highly-dynamic social systems. Critically, it requires no trust in a centralized server to achieve confidentiality of transferred data.

**Note on things that are missing:** need to talk about having asynchronous comms, and (very importantly) asynchronous/"dynamic" sharing. Asynchronous messaging with synchronous sharing isn't wholly asynchronous.

## Nomenclature notes

+ Terms being used with a specific technical meaning will be ```monospace``` formatted and link to their corresponding Glossary entry.

# Threat model

Golix protects the informational autonomy of an abstract digital ```entity-agent``` comprised of three public/private keypairs. That is to say, the Golix protocol affords these ```entities``` strict and exclusive control over:

1. creation,
2. retention,
3. and sharing

of any and all information ```possessed``` by the ```entity-agent```. Note that Golix does not include any concept of information ownership or intellectual property. Within a Golix context, information ```possession``` means only that an agent-entity retains that particular piece of information. As such, once information is shared, **the sharer's authority over the information stops at the point of share;** the recipient has explicit autonomy over their digital "memory" of that information, as does the sharer.

To enforce such agency cryptographically, Golix protects:

+ information *confidentiality* against ```illegitimate access``` by any adversary,
+ information *integrity* against deliberate or incidental modification by any adversary, and
+ information *authenticity* of authorship against impersonation by any adversary.

However, Golix provides only limited protections:

+ against meta-analysis of Golix data or traffic,
+ against flooding ```entities``` with requests, effectively (D)DoSing them, and
+ against malicious information retention or removal.

And Golix protections explicitly exclude:

+ any compromise of the underlying cryptographic primitives used by the protocol,
+ any compromise of ```entity``` (client) devices, and
+ any connection between physical entities and Golix ```entities```.

The following discusses what specific capabilities are afforded to various adversaries. Each capability builds upon (and includes) the previous category.

## *Any* adversary can...

Cryptographic compromises:

+ Use a collision of the address hash algorithm to tamper with existing information
+ Use a break of the signature algorithm to impersonate an ```entity``` in the future
+ Use a break of the public key encryption algorithm to intercept future key exchanges and compromise stored past key exchanges
+ Use a break of the symmetric encryption algorithm to expose the plaintext of past and future objects
+ Use a break of the exchange algorithm to impersonate an alias of an existing ```entity``` in the future
+ Use a break of the KDF algorithm to impersonate an alias of an existing ```entity``` in the future
+ Use a break of the exchange algorithm to forge key exchanges in the future
+ Use a break of the MAC algorithm to forge key exchanges in the future
+ Use a break of the KDF algorithm to forge key exchanges in the future

Client compromises:

+ Use a compromised client device to fully impersonate the client ```entity```
+ Use a compromised client device to gain access to the client ```entity``` private keys
+ Use a compromised client device to gain access to any client-stored content symmetric keys

Meta-analyses:

+ Tie any piece of information to exactly one ```entity```: typically the ```entity``` that created the information, but occasionally the ```entity``` that received it
+ Calculate the exact, or near-exact, length of confidential information

Denials of service:

+ Flood a ```persister``` server with *traffic*
+ Flood a client device with *messages*

## An ```entity``` (*client*) adversary can...

Denials of service:

+ Flood a ```persister``` with *messages* 

Denials of availability:

+ Maliciously prevent deletion of arbitrary data
+ Maliciously delete possessed data

## A ```persister``` (*server*) adversary can...

Meta-analyses:

+ Analyze connection traffic to infer connections between a Golix ```entity``` and its viewed ```GHID```s
+ Analyze connection information to infer physical identity, IP address, probable time zone, etc

Denials of service:

+ Maliciously take itself offline
+ Flood a client device with traffic

Denials of availability:

+ Maliciously delete arbitrary data
+ Maliciously retain arbitrary data

## A *global passive* adversary can...

Meta-analyses:

+ Analyze traffic on the transport level to tie Golix ```entities``` to physical identities
+ Analyze traffic on the transport level to infer connections between multiple Golix ```entities```

## *No* adversary can...

Client compromises:

+ No adversary can directly escalate one client compromise into another client compromise.

Meta-analyses:

+ No adversary can perform social graph analysis of ```entities```.
+ No adversary can tie object ```recipient``` entities with object ```author``` entities
+ No adversary can deduce content types, content format, or any other content metadata, without access to the content itself

# Golix addresses and identities

## Addresses

+ Static
+ Dynamic

## Identities

+ Deniability != repudiation
+ Authentication != authorization
+ Authentication != verification

# Golix network primitives

Overview: 

1. to author content, first create an identity. 
2. Identities then publish encrypted containers. All object containers are publicly associated with the address of their author.
3. Like a memory-managed programming language, these containers are garbage collected by the persistence providers when no references to them remain. 
4. These references may be either static or dynamic bindings. There is no restriction on the creation of bindings (they need not be created by the content's author). They are publicly associated with the address of their binder (again, not necessarily the same as the content author).
5. Bindings may be removed through debinding records, but only by their binder.
6. Key exchanges are initiated through asymmetric request/response objects. Unlike other objects, these are publicly associated with their recipient.

## Identity container (GIDC)

1. Signature ```PUBKEY```
2. Encryption ```PUBKEY```
3. Exchange ```PUBKEY```
4. ```GHID```

## Object container (GEOC)

1. Author ```GHID```
2. Symmetrically encrypted payload {  
    + ```Arbitrary bytes```  
  }
3. ```GHID```
4. Author ```SIGNATURE```

## Static object binding (GOBS)

1. Binder ```GHID```
2. Target ```GHID```
3. ```GHID```
4. Binder ```SIGNATURE```

Unlike objects, bindings 

## Dynamic object binding (GOBD)

1. Binder ```GHID```
2. Previous frame ```GHID```s
3. Current target ```GUID```
4. Dynamic ```GHID```
5. Frame ```GHID```
6. Binder ```SIGNATURE```

## Debinding (GDXX)

1. Debinder ```GHID```
2. Target ```GHID```
3. ```GHID```
4. Debinder ```SIGNATURE```

Like bindings, debindings are subject to replay attacks.

Replay attacks!!

## Asymmetric request/response (GARQ)

1. Recipient ```GHID```
2. Asymmetrically encrypted payload {  
    + ```Author GHID```  
    + ```Target GHUD```  
    + ```Keyshare, ACK, NAK```  
   }
3. ```GHID```
4. Author ```HMAC```

# Golix state analysis

## Non-privileged ("server") state analysis

## Privileged ("client") state analysis

# Crypto primitives

# Discussion and conclusion

# Glossary

## GHID

The "Golix hash identifier": a unique content-based static address for content on a Golix-compliant network or device.

## Entity-agent

An abstract digital entity represented by three linked public/private keypairs and a ```GHID```. Also informally referred to as an agent, entity, or identity. Entity-agents are the Golix equivalent of a single consistent consciousness. **The Golix protocol does not inherently relate Golix entities with physical entities.** Any such connection must be made by applications.

## Possession

Within a Golix context, information possession is wholly unrelated to information authorship or any notion of intellectual property. Here, to possess information means only that a particular entity-agent retains it.

## Illegitimate access

The access of information by any party with whom that information has not affirmatively, explicitly been shared. This party is an arbitrary adversary, who is not limited to attacks within the Golix protocol.

*note: should this be renamed to unsanctioned access?*

## Persistence providers

The servers that physically store Golix data. Also referred to as persisters, persistence, and occasionally (and highly informally) servers.