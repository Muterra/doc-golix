**Draft warning:** this is a public draft dated 27 Oct 2015, but should not be considered final. If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html). Our previous whitepaper was very shy on technical details and has been moved to a [design philosophy](/design_philosopy.md) document.

# Introduction

Question: what is a social network?

Social systems are communicative. They are defined by interactions between entities. Broadly speaking, these entities must be capable of independent action; that is to say, they must possess a degree of individual [agency](https://en.wikipedia.org/wiki/Agency_%28philosophy%29). A non-agent component in a social interaction -- for example, a megaphone -- is simply a tool of its owner-agent. We don't talk *to* megaphones; we talk *through* them.

Networks are defined by the links between their interconnected nodes. *Digital* networks are simply information exchanges between discrete computational devices. In a digital world, these linked data handlers -- computers, phones, servers, *devices* -- are our megaphones.

It stands to reason that a social network should be simply the combination of these two concepts. Individual digital agents, connecting *through* their devices, *to* other agents. It makes no more sense to address our communications to the devices themselves than it would to talk to a megaphone while ignoring the person holding it. And yet crucially, we lack a protocol that defines and protects the difference. We intend Muse to fill this need.

## Defining the philosophical requirements

The Muse protocol's philosophical goal is to algorithmically protect individual agency on any digital network. In this context, we assume that the null state of any agent is a total absence of information. From that, we define agency as the ability to create, retain, receive, and share data; an agent may choose to retain any information she creates or receives, and she may share any information she retains. We abstract these four capacities into three "social actions": authorship, retention, and communication.

We impose a strict dichotomy between agents and non-agents: agents **must** be capable of **all** social actions, and non-agents **must** be capable of **none**. Furthermore, we treat the abstention from social actions as implicit; in other words, an agent must be free not only to retain data, but also to ignore it.

Finally, we assert that agency *at the Muse protocol level* is a walled garden, and that no agent or non-agent may be capable of restricting another's agency. From a practical standpoint we therefore require the protocol to maintain full content agnosticism on any Muse-implementing network.

## Defining the technical requirements

We translate the philosophical requirements for data retention, communication, and authorship into three technical concerns: content, sharing, and identity management, respectively. As they are the basis for our definition of agency, these three functions must then be fulfilled by the Muse protocol.

To satisfy our null state, all content must be private by default; it must therefore be permanently encrypted upon authoring. Similarly, to enforce our walled garden, content must have provable integrity. As a practical concern we require content to be of publicly-provable agent origin. We offset this threat to network-content agnosticism with the explicit requirement that digital identities carry no inherent physical meaning. Put differently: content compliant with the Muse protocol must always be publicly authenticated by a single digital agent, but that agent's identity may be anonymous, pseudonymous, or eponymous. 

Given the requirement for universal encryption, sharing management can be technically described as keysharing. Because non-agents may not take network action, within the protocol, content can only be shared by agents. In a similar vein, as agents must be capable of non-action, protocol-compliant data need not be shared. However, once an agent has received a content key, if she chooses to retain it, the walled garden constraint prevents anyone (including the content's author) from restricting her ability to subsequently re-share it. As such, *any* communication may result in a sharing "cascade"; at the Muse protocol level, this is an unavoidable consequence of agency.

Finally, for content to have verifiable integrity and authenticity, agent-authors must be capable of generating cryptographic signatures. In addition to the compromise behind forced public authorship with no inherently meaningful identification, agents should further be capable of privately provable identity aliasing. That is, any Alice should be capable of forming multiple digital identities, and proving to any (but not every) Bob that these aliases are equivalent.

## Defining the scope

For a general-purpose protocol, the importance of scope declaration really cannot be understated. Our goal is to create a [zero-cost abstraction](http://blog.rust-lang.org/2015/05/11/traits.html) that consolidates shared social infrastructure into a singular mechanism, regardless of data transport or application. And given our broad definition of "social network", protocol use cases far exceed web sites and apps: they could be as broad as distributed atmospheric sensor networks or autonomous robot swarms. As such, predicating the protocol on the use of a particular server stack, API, or emerging technology seems highly inappropriate.

Furthermore, many of these use cases lack traditional relationships between agents. Subjecting a temperature sensor to a [FOAF](https://en.wikipedia.org/wiki/FOAF_%28ontology%29) description, for example, seems patently absurd. With the Muse protocol we deliberately avoid such high-level constructs. We specify no relationships between agents, nor restrict any interactions between them, nor what physical means they use to communicate. These are all concerns for different levels of abstraction. The Muse protocol is concerned only and exactly with converting any network-oriented transport mechanism into a standardized agent-oriented social overlay.

Finally, robust transport and application independence require the Muse protocol to support the least restrictive set of possible network architectures. To this end we make the following assumptions:

+ Asynchrony at sufficient speed is a superset of synchrony
+ Arbitrary binary data is a superset of any encoded information
+ One-to-any is a superset of one-to-none, one-to-one, and one-to-many
+ Static content is a superset of dynamic content (dynamic content is a set of related, time-dependent static frames)

# Content management

Consolidating technical requirements and applying scope limitations leaves the following minimum requirements for Muse content:

+ Binary format
+ Asynchronous availability
+ All content uniquely and statically identifiable on the network
+ All data contained within a private, encrypted body
+ Exactly one piece of public metadata: a unique identifier for the content's author
+ Hashed for integrity verification
+ Signed for authentication

Including a public identifier for content authorship is compromise between information privatization and network utility. It's both a defensive and offensive measure. It allows agent-authors to maintain their authorship across all communities, alleviating problems associated with [posting content to competing networks](http://www.urbandictionary.com/define.php?term=Freebooting). It also allows the network itself to police bad actors, and provides a general-purpose point of contact for remediation.

## Persistence and its standardization

Because asynchronous systems allow agents to be online or offline at any time, they require a networked data persistence mechanism. Because the Muse protocol avoids all transport routing information, this persistent data buffer is unavoidably dependent upon the underlying Muse-implementing transport layer.

In keeping with the protocol's scope restriction, we do not specify the details of this persistence implementation; instead, transport-specific conventions are more flexibly defined within overlay standards.

However, in the interest of protocol uniformity, we define both the commands to be implemented, and their sequence of transmission, within the Muse spec. Interactions with the persistence layer can therefore be contained within pluggable transport layer implementation packages, and any network node with access to multiple persistence systems can function as a bridge between them. 

All persistence providers must, at a minimum, support the following commands:

+ Ping
+ Publish object
+ Get object 
+ Subscribe to address
+ Unsubscribe from address
+ List address subscriptions
+ Acknowledge (ack) transmission
+ Non-acknowledge (nak) transmission
+ List agents with bindings to an object

## Data retention and removal

Object deletion is notably absent from the persistence provider command set. There is currently a great deal of controversy surrounding the balance between an individual's so-described "right to be forgotten" and the general public's "right to remember". The Muse protocol is designed around the assumption that ephemerality is unenforceable: that is to say, individual agents have very limited direct control over the longevity of their footprints, whether digital *or* physical. However, since digital networks are generally designed to eliminate physical durability as an application concern, the lifetime of an object on a social platform is, for the most part, a wholly social question.

Muse represents a consolidation of shared social infrastructure into a single unified protocol, so it must be capable of establishing a deterministic framework for network information deletion. Any other outcome would place social restrictions on the persistence mechanism, which, as a part of the transport layer, is intended to be strictly independent of social concerns. This is no small task.

Our solution is inspired by high-level memory-managed programming languages. Objects themselves have no defined lifespan. Instead, their retention is governed by object "bindings". Bindings declare to the persistence provider that the object is under use; when none remain, the persistence provider garbage collects the object. Bindings may be placed by any agent on any content; however, to prevent malicious retention, the unique identifier for the agent is retained as public metadata in the binding. In this way, any agent may apply pressure to the binding party to remove the binding, but the persistence system remains entirely uninvolved in this social (or legal) process. Object bindings represent a compromise between unavoidably infinite and indeterminately finite data lifespans.

## Addressing content

As a mediating party between machine-based transport networks and agent-based social networks, Muse content requires a Muse unique identifier (MUID) -- essentially, our equivalent of a URL. We consider human-readable names to be an application-specific concern; once again, a distributed sensor network may have no need for them. Their use for objects adhering to the Muse protocol is therefore out-of-scope. Instead, we desire a simple, machine-readable, unique address for all content.

Instead of a centralized content identifier (for example: a unique integer assigned by a trusted oracle), Muse uses cryptographic hash functions to deterministically assign collision-resistant addresses for content. Choosing a sufficiently strong hash function and allowing for future algorithm changes can minimize the risk of redundant addressing, whilst simultaneously adding an extra layer of security; any attempt to modify existing content should result in a new address.

This address scheme works quite well for static content, but it also fails to provide any form of dynamic referencing. Now, on the one hand, all individual observations are static: if we were to record the temperature of a room at 1-minute intervals for the rest of time, any particular data point would be forever immutable. From this perspective, one could argue that information mutability is, in reality, an application concern -- that a network like Muse has no business with dynamic addresses. However, given that the assumption of the one-way nature of secure hash functions precludes the ability to predict addresses in advance, as a practical concern, a very primitive form of dynamic addressing is exceptionally useful. A profound number of basic network operations (both physical and social) either require or are greatly simplified by a consistently-addressed, time-variant data stream. As such, we argue that the potentially unwanted abstraction overhead of any particular dynamic address system is justified, so long as applications are free to ignore it with no performance consequences.

Therefore, the Muse protocol defines a second kind of object binding. Like static bindings, they prevent object garbage collection; unlike static bindings, they generate a new address for existing content. Like static objects, they can only be created (or updated) by a single agent; however, that agent need not be the author of the objects bound by the address. Dynamic bindings are constructed as an ordered list of bound static MUID(s). The meaning of this list may be application-specific, but is generally assumed to be a buffer, and in such a use is formulated with the newest entry first. The dynamic address is generated once, as the hash of the first set of bound MUID(s), and is then included in all subsequent revisions of the binding.

## Content management primitives

At this point we can fully enumerate the required **public** components in a Muse encrypted object container (MEOC) record:

1. File hash / MUID
2. Author MUID
3. Author's signature of the file hash
4. Symmetrically encrypted payload

As well as a Muse static object binding (MOBS) record:

1. MUID of the agent placing the binding (the binding agent)
2. Binding agent's signature
3. MUID for the object to bind (the target)

And a Muse dynamic object binding (MOBD) record:

1. MUID of the binding agent
2. Binding agent's signature
3. Dynamic address
4. MUID(s) for static target(s)

And a Muse address debinding (MDXX) statement:

1. MUID of the debinding agent
2. Debinding agent's signature
3. Address to be debound

# Sharing and access management

Consolidating technical requirements and applying scope limitations leaves the following minimum requirements for Muse sharing:

+ One-to-any
+ Simple key sharing with static, non-revocable keys
+ Sharing must be initiated by a uniquely identifiable agent
+ Sharing must be directed at a uniquely identifiable agent
+ Connections between the initiator and target agents should not be publicly visible

Given the nature of Muse content containers, including cryptographic access management in the same delivery mechanism seems inefficient and unsustainable. As such, the Muse protocol wholly separates content from key distribution. Normally, relating keys to content resources would present a logistical challenge; however, with all Muse data being directly and only content-addressable, this problem is mostly inapplicable. Also note that public information sharing is out-of-scope, but can be simply constructed as a scripted network agent.

Once keys are separated from content, we can start to build communication primitives from dynamically-bound objects. In the Muse protocol, these channels are known as "pipes". Since Muse-compliant content is always singularly authored, each multidirectional pipe is a combination of unidirectional half-pipes.

## Key/content separation

Many (perhaps most) protocols include some kind of key management within the ordinary application message exchange. For example, the PGP protocol includes both key distribution and encrypted content in the same email. OTR similarly mixes the application message body with exchange key management. This approach is troublesome with arbitrary (and potentially inconsistent) numbers of conversation partners; put simply, it does not scale into a many-to-many network environment, even when individual messages are authored by single parties. As such, the Muse protocol entirely separates key management from content distribution.

The chief downside to this approach when creating a high-level protocol like PGP or OTR is that it does not integrate conveniently with most message-based transport exchanges. This is particularly true for email, where building a usable client for such a system would imply a very heavy-handed post-receipt merger of the separate content and key messages.

However, as a standalone entity, the Muse protocol bears no such limitation. Our biggest concern, then, with this separation of key and message, is how to create a reliable, secure, unambiguous link between the two. This problem can be easily solved by sharing keys paired not with the content itself, but with that content's MUID.

## Inter-agent "pipes"

Half-pipes, once created, are simply a dynamic binding with key state shared between multiple (usually two) agents. The simplest example would use the same key for every dynamic frame. Alice generates her initial frame and dynamic binding, shares the binding with Bob (along with the decryption key), and Bob subscribes to the dynamic address at the implementation level. Alice can then update the binding with new frames, establishing a consistent unidirectional communication channel (the half-pipe) with Bob. To construct a full pipe, Bob repeats the process with Alice. Note that the Muse standard **requires** Bob to use a different symmetric key for his half-pipe. More sophisticated pipes implement key rotation for forward secrecy.

These pipes may be used for any purpose, but are generally intended for one-to-one API communication. Because half-pipes have a consistent shared key state, once one is shared with another agent, it is impossible to revoke access without creating a new half-pipe. As such, in most cases, the optimal strategy is to make a single pipe for each possible pair.

Since agents may expose more than one API, and to avoid accidental or malicious confusion when matching half-pipes into full pipes, pipes should contain an API identifier in the first half-pipe frame. However, this operation -- and in particular, its applicability to dedicated keysharing pipes -- is defined within an overlay standard.

Finally, once a pipe-based transaction is complete, the terminating agent (Alice) pushes a special termination code to her upstream half-pipe. She may then either wait for an off-pipe response acknowledgement (ACK), or she may immediately debind her half-pipe. Either action should be interpreted by her parter Bob as a closure of the pipe, and he must respond by issuing an ACK, and should then remove any bindings he may have placed on Alice's half-pipe.

## Pipe handshakes

Conspicuously absent from this basic description of pipes is the initial keysharing process.  Because the Muse protocol is capable of asynchronous operation, and pipe initiation may not always be between previously "acquainted" agents, the only way to eliminate metadata publicly linking the pipe participants is for the pipe handshake to use public/private cryptography.

This process is fully symmetric and can be initiated by either party. First, Alice creates her half-pipe. She then uses Bob's public key to privately announce the half-pipe's dynamic MUID and its key to Bob, and he responds with either a pipe ACK (if successful) or pipe NAK (if unsuccessful). If a full pipe is desired, Bob can then repeat this process for Alice.

Of course, for Bob to respond, this request must be provably Alice's. Once again, though, we are explicitly avoiding publicly linking Alice and Bob in the handshake request. This has the somewhat undesirable consequence that Bob can only verify Alice's signature *after* decrypting the request. Though this prevents a malicious party from successfully initiating an API pipe on someone else's behalf, it also means that signature verification cannot be performed by the network. This opens a potential DoS-style attack vulnerability: an agent's application could be overwhelmed by spoofed pipe requests with invalid signatures from invalid muids. Such an attack would compromise the victim's ability to initiate new pipes, but presumably leave its existing pipes minimally affected.

Mitigations against this style of attack will likely need to be developed, but it's worth mentioning that, since pipe requests are strictly a one-to-one transaction, they need not be signed asymmetrically. Rather, if both agents' public identities include a Diffie-Hellman public key, the request can be signed using a MAC with a key derived from their DH shared secret. This substantially decreases signature verification time, and therefore increases the computational footprint required for a successful attack. It also has the added benefit of inherent immunity to attempts to link Alice and Bob together by testing signature validity against a dictionary of public keys.

## Key state management

Once a pipe is established, given that Muse agents are device-independent, how do the connected parties remember encryption keys?

Though state can be easily transferred between agents using pipes, state management in the Muse applications themselves can be quite complicated. This will be an area of rapid early development for Muse applications as best practices and overlay protocols evolve.

Fortunately, key memory is a relatively direct form of persistence. It happens at a low level; in general, keys should only be handled by heavily-reviewed protocol implementation processes. Questions like application state sandboxing simply don't apply here: **no** downstream application is trusted with keys. As such, we can directly approach the question of key lifetimes,which is (at the moment) a concern reserved for implementation best practices, and not within scope for the Muse protocol With that being said, actual format for the key state file(s) will be defined within an overlay standard.

## Sharing primitives

At this point we can fully enumerate the required components in a Muse encrypted pipe requestor (MEPR) record:

1. Recipient agent MUID (public)
2. Requesting agent MUID (private)
3. Target half-pipe MUID (private)
4. Half-pipe initial symmetric key (private)
5. Requesting agent MAC (public)

As well as a Muse pipe ACK (MPAK) record (using the same nomenclature as above):

1. Requesting agent MUID (public)
2. Recipient agent MUID (private)
3. Target half-pipe MUID (private)
4. Recipient agent MAC (public)

And a Muse pipe NAK (MPNK) record:

1. Requesting agent MUID (public)
2. Recipient agent MUID (private)
3. Target half-pipe MUID (private)
4. Recipient agent MAC (public)

Additionally, both the ACK and NAK records may include an optional, application-specific status code within the private container.

# Identity management

Consolidating technical requirements and applying scope limitations leaves the following minimum requirements for Muse identities:

+ Are self-contained, self-describing, and independent of any other identity
+ Can sign content
+ Can receive asymmetric content
+ Can generate Diffie-Hellman shared secrets
+ Can be privately aliased
+ Should be independent of any physical device

Also note that to effectively support device-independent identity, online private key storage is logistically necessary. As with all other network requirements, this need can be served through content containers; however, the format for these containers (and indeed the entire "login" process) is out-of-scope for the core Muse protocol.

Similarly, at this level of abstraction, high-level descriptions of relationships between agents ("Friends lists" and the like) are entirely inappropriate. Automation may be built *on top* of the Muse protocol, but in the interests of general applicability at minimum cost, it should not be "baked" into it.

## Identities within MEOC records

A key shortcoming of PGP-over-email is that public keys are distributed on a different transport mechanism than the messages themselves. Now, to be fair, if a key exchange process were also built upon SMTP, it would provide very little protection against man-in-the-middle attacks on initial contact. If Alice requests Bob's key over an insecure transport like email, an adversary Eve can simply intercept that key request, impersonating Bob from the outset. External key distribution helps slightly to mitigate this risk, but robust PGP-over-email implementations rely upon webs of trust. The difficulty here is that it entirely breaks the email workflow, and cannot be self-supporting. This situation is far less than ideal.

However, an alternative would be a protocol where "email addresses" were inseparable from their public key(s). This system, though still vulnerable to phishing attacks (as nearly all human-interfacing systems are), could never suffer from such a direct impersonation attack: if Eve replaced the public key in a malicious response to Alice's key request, Alice would be aware that the new address mismatched her request.

On a Muse-implementing network, MEOC records already provide this property. The container is uniquely identified through its MUID; changing its content results in the generation of a new MUID. By encapsulating all of an identity's public keys in a single MEOC, and referring to the keys' owner-agent by that container's MUID, we can adopt a MEOC's beneficial security properties without any added infrastructure overhead. The network, including agent identities, can be wholly self-hosting. 

Since MEOC containers are encrypted, this opens the possibility of creating identities that can only be contacted after an initial referral. Though we expect that the majority of agent identities will be public, there is certainly potential usefulness in these hidden, private identities. That said, for public usefulness, encrypted identity containers are a few points short of a winning game. This downfall can be easily remedied with a standardized "bootstrapping" symmetric key.

## Aliases and their selective deniability

A final desirable property of identities is aliasing. It's sometimes useful to have a second point of contact for any particular entity. This isn't exactly a pseudonym, since both aliases (at least in this context) can bear the same name; it's more like a having two phone numbers, one public, one private.

Now, a basic alias could be built by simply creating a new Muse identity. But without also providing some provable linkage between the private identity and the public one, this is more a pseudonym than an alias -- two individually consistent digital entities, certainly, but their relationship is no stronger than their word. A better assurance is a preexisting shared secret, but it is not without potential consequences.

Sharing an alias is inherently a vulnerable process. Alice may not entirely trust Bob, but want to talk with him anyway. In this case, it would nice if the aliasing shared secret weren't something Bob could use to authenticate Alice's alias to an outside observer, Eve. For that we have only one option: a secret trivially constructible by either Alice or Bob, originating from nothing more than their principal identities. That way, Alice can always claim Bob made the whole thing up; his use of their conversation as evidence to Eve is wholly predicated on Eve's trust of his word.

Given principal identities BobPrincipal and AlicePrincipal, and alias identity AliceAlias, and assuming that their respective identity containers already include a Diffie-Hellman public key, the mechanism works as follows:

+ Alice creates AliceAlias
+ AliceAlias contacts BobPrincipal
+ AliceAlias sends BobPrincipal the shared secret from a Diffie-Hellman exchange between BobPrincipal and AlicePrincipal.

## We three keys

Agent identity MEOCs must therefore contain three public keys:

+ Public encryption key
+ Public signing key
+ Public half of Diffie-Hellman pair

Their format, along with public identity bootstrapping information, is defined within an overlay standard devoted to Muse identities.

# The conclusion: social agency in a digital world

In the physical world, personal agency is the defining characteristic of animate existence. Meanwhile, our contemporary digital environment is running rife with disempowerment. The reigning pattern of "privacy by promise" in walled-garden platforms has a dramatic chilling effect on the internet. And from a development standpoint, since every new social application is forced to build its own platform infrastructure, this approach comes with tremendous economic cost.

Muse consolidates content, sharing, and identity management into a single encrypted protocol. It allows social applications to focus on social utility, and frees their distribution platforms from arduous and repetitive account management and authentication concerns. It is encrypted from participant to participant and supports arbitrary content with asynchronous availability. The Muse protocol is a paradigm shift: it represents an attempt to algorithmically protect individual agency in a digital world.