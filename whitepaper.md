**Draft warning:** this is a public draft **in-process** dated 23 Oct 2015, but should not be considered final. If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html). Our previous whitepaper was very shy on technical details and has been moved to a [design philosophy](/design_philosopy.md) document.

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

Since Muse functions are cryptographically enforced, the first problem 

## Base requirements

Arbitrary binary data

## Asynchronous availability

### Persistence and its standardization

Storage provider command set

### Data retention and removal

## Addressing content

Including static and dynamic bindings

Justification of dynamic bindings, despite them being a less primitive object than static objects, and therefore potentially introducing unwanted abstraction overhead

## Cryptographic enforcement

Choice of public metadata

# Sharing and access management

## Base requirements

New content discovery is out-of-scope.

## Inter-agent "pipes"

## Key/content separation

## Online key exchange

Handshake

Standardized share pipes

# Identity management

## Base requirements

Must be independent of physical devices. Therefore, online private key storage, though that standard is out-of-scope.

## Selectively-deniable aliases

# Conclusion

# Scratchbook

Asynchrony required:

+ Agents do not have a 1:1 correspondence with devices.
+ Agents can go offline.
+ Therefore, an agent-based network must be asynchronous.

