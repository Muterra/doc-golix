![Golix logo](/assets/branding-logo-padded.png)

**Warning:** this repo is considered public pre-release information and is in varying levels of completeness. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

# Introduction

**Abstract:** Golix provides end-to-end encryption for distributed agent-based networks, especially internet-connected physical devices (IoT), while substantially simplifying their development. This document begins with a brief overview of the Golix protocol, followed by a more detailed discussion of its most important aspects.

Other documents:

+ [See it in action](https://github.com/Muterra/py_hypergolix_demos)
+ [Python protocol implementation](https://github.com/Muterra/py_golix)
+ [System whitepaper](whitepaper.md)
+ [Full technical specification](spec.md)
+ [Security whitepaper](security-whitepaper.md)
+ [Changelog](changelog.md)

-----

The Golix protocol is the result of several observations:

1. Third-party servers are more economically scalable than personal servers, and will therefore always be attractive to certain consumers
2. Trusting third-party servers with information access control and retention lifetime may be inappropriate for some (or all) kinds of information
3. Certificate issuance does not map well to massively distributed systems like social networks or smart devices
4. Using IP addresses, URLs, and authentication tokens to communicate between nodes on such distributed systems profoundly complicates their development

As a result, Golix:

1. Embraces third-party centralized servers, but also supports federation
2. Uses cryptography to enforce data access control, and a consensus process to enforce data lifetimes, minimizing necessary server trust
3. Establishes trust-on-first-use consistent entity "identities" from a combination of public keys (addressed by their hash fingerprints)
4. Addresses information and identities by the cryptographic hash digest of its content

Golix is a relatively low-level protocol and is not intended for *direct* use in a human-readable network. Conceptually, it is very similar to a virtual private network, except instead of connecting physical devices (eg an individual computer), it connects abstract entities (eg a particular user) -- as represented by a particular set of public/private keypairs.

Golix is the core technology powering [Hypergolix](https://www.hypergolix.com), a local background service that drastically simplifies IoT development. If you'd like to stay updated, please join the [Hypergolix mailing list](https://www.hypergolix.com/#page-top). **Note that the Golix Protocol used to be called "the Muse protocol".** It was re-branded as Golix in February 2016.

## Sponsors and backers

Golix finances are very tight. If you like what we're doing, please consider [sponsoring the project](https://opencollective.com/golix#sponsor)...

  <a href="https://opencollective.com/golix/sponsors/0/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/0/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/1/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/1/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/2/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/2/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/3/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/3/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/4/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/4/avatar"></a>

... or [becoming a backer](https://opencollective.com/golix).

  <a href="https://opencollective.com/golix/backers/0/website" target="_blank"><img src="https://opencollective.com/golix/backers/0/avatar"></a>
  <a href="https://opencollective.com/golix/backers/1/website" target="_blank"><img src="https://opencollective.com/golix/backers/1/avatar"></a>
  <a href="https://opencollective.com/golix/backers/2/website" target="_blank"><img src="https://opencollective.com/golix/backers/2/avatar"></a>
  <a href="https://opencollective.com/golix/backers/3/website" target="_blank"><img src="https://opencollective.com/golix/backers/3/avatar"></a>
  <a href="https://opencollective.com/golix/backers/4/website" target="_blank"><img src="https://opencollective.com/golix/backers/4/avatar"></a>
  <a href="https://opencollective.com/golix/backers/5/website" target="_blank"><img src="https://opencollective.com/golix/backers/5/avatar"></a>
  <a href="https://opencollective.com/golix/backers/6/website" target="_blank"><img src="https://opencollective.com/golix/backers/6/avatar"></a>
  <a href="https://opencollective.com/golix/backers/7/website" target="_blank"><img src="https://opencollective.com/golix/backers/7/avatar"></a>
  <a href="https://opencollective.com/golix/backers/8/website" target="_blank"><img src="https://opencollective.com/golix/backers/8/avatar"></a>
  <a href="https://opencollective.com/golix/backers/9/website" target="_blank"><img src="https://opencollective.com/golix/backers/9/avatar"></a>
  <a href="https://opencollective.com/golix/backers/10/website" target="_blank"><img src="https://opencollective.com/golix/backers/10/avatar"></a>
  <a href="https://opencollective.com/golix/backers/11/website" target="_blank"><img src="https://opencollective.com/golix/backers/11/avatar"></a>
  <a href="https://opencollective.com/golix/backers/12/website" target="_blank"><img src="https://opencollective.com/golix/backers/12/avatar"></a>
  <a href="https://opencollective.com/golix/backers/13/website" target="_blank"><img src="https://opencollective.com/golix/backers/13/avatar"></a>
  <a href="https://opencollective.com/golix/backers/14/website" target="_blank"><img src="https://opencollective.com/golix/backers/14/avatar"></a>
  <a href="https://opencollective.com/golix/backers/15/website" target="_blank"><img src="https://opencollective.com/golix/backers/15/avatar"></a>
  <a href="https://opencollective.com/golix/backers/16/website" target="_blank"><img src="https://opencollective.com/golix/backers/16/avatar"></a>
  <a href="https://opencollective.com/golix/backers/17/website" target="_blank"><img src="https://opencollective.com/golix/backers/17/avatar"></a>
  <a href="https://opencollective.com/golix/backers/18/website" target="_blank"><img src="https://opencollective.com/golix/backers/18/avatar"></a>
  <a href="https://opencollective.com/golix/backers/19/website" target="_blank"><img src="https://opencollective.com/golix/backers/19/avatar"></a>
  <a href="https://opencollective.com/golix/backers/20/website" target="_blank"><img src="https://opencollective.com/golix/backers/20/avatar"></a>
  <a href="https://opencollective.com/golix/backers/21/website" target="_blank"><img src="https://opencollective.com/golix/backers/21/avatar"></a>
  <a href="https://opencollective.com/golix/backers/22/website" target="_blank"><img src="https://opencollective.com/golix/backers/22/avatar"></a>
  <a href="https://opencollective.com/golix/backers/23/website" target="_blank"><img src="https://opencollective.com/golix/backers/23/avatar"></a>
  <a href="https://opencollective.com/golix/backers/24/website" target="_blank"><img src="https://opencollective.com/golix/backers/24/avatar"></a>
  <a href="https://opencollective.com/golix/backers/25/website" target="_blank"><img src="https://opencollective.com/golix/backers/25/avatar"></a>
  <a href="https://opencollective.com/golix/backers/26/website" target="_blank"><img src="https://opencollective.com/golix/backers/26/avatar"></a>
  <a href="https://opencollective.com/golix/backers/27/website" target="_blank"><img src="https://opencollective.com/golix/backers/27/avatar"></a>
  <a href="https://opencollective.com/golix/backers/28/website" target="_blank"><img src="https://opencollective.com/golix/backers/28/avatar"></a>
  <a href="https://opencollective.com/golix/backers/29/website" target="_blank"><img src="https://opencollective.com/golix/backers/29/avatar"></a>

# Entity-agents and their identities

In the physical world, every deliberate change of state is the result of autonomous action by a physical entity. Golix uses cryptographic "identities" -- basically, a collection of public/private keypairs -- as a direct digital analogue to such physical entities. In exactly the same way as a person is required to build a house, on a Golix network, an identity is required to create data.

As the basic unit of agency on a Golix network, these entities have three capabilities:

1. They may always create new information
2. They may always retain existing information
3. They may always share existing information

However, the borders of agency on a Golix network are relatively strict. Golix information, once shared, cannot be explicitly controlled: every party with knowledge of that information will always ultimately retain the power to re-share that information, in exactly the same way that a conversation can always be repeated to a third party. Influence over others' actions on a Golix network remains explicitly social; as in the physical world, there is no algorithmic limit to second-party agency. Similarly, the creator of information is afforded no special privileges on a Golix network.

It is important to note that these identities have no inherent relation to the physical world. At a protocol level, Golix entities are identified **exclusively** by the fingerprint of their public keys. Golix provides no inherent verification mechanism; these must be constructed on top of the protocol. As such, in terms of physical identities, a Golix ```identity``` may be anonymous, pseudonymous, or eponymous, entirely depending on the content the identity creates and the services it interacts with.

# Content

All information on a Golix network is private by default; entities use client-side encryption prior to upload to enforce this confidentiality without trusting third-party servers. Because these third-party "persistence providers" (basically, Golix-compliant servers) retain content until it is explicitly removed, Golix is best described as an at-rest encryption scheme. However, unlike existing at-rest protocols (for example, PGP), Golix data is infinitely and dynamically sharable. In other words, any privileged entity (*ie* any ```identity``` with the information's symmetric key) may share anything with anyone at any time. This is only possible through complete separation of data encapsulation from key encapsulation.

The lifetime of this content, once uploaded, is determined by consensus agreement. Data containers themselves are fully volatile; analogously to reference-counting memory-managed programming languages, data only persists so long as it has been referenced by a Golix binding. There are no restrictions on binding: any entity may bind to any content at any time. Only the binding entity may remove the binding; however, their ```identity``` address (again, the public key fingerprint) is retained as public metadata in the binding, meaning:

1. The data's binder, and not its author, can be held accountable for (*ie* billed for) its persistence
2. Anyone requesting takedown of the data (copyright claims, right to be forgotten, etc) may pursue social, legal, or economic action against the binder directly, without involving persistence providers

Furthermore, a specific kind of binding (appropriately but unoriginally termed a "dynamic binding") supports dynamic content. Dynamic bindings may only be modified (updated or removed) by their binder, and are crucial for applying the static Golix address system to mutable content.

# Addresses

All Golix addresses are cryptographic hash digests, which makes server path information irrelevant to client requests. Because all Golix data is encrypted, we can also safely remove authorization restrictions on downstream physical information delivery. Together, these have the effect of wholly decoupling the network infrastructure from communications between entities. Communication state is entirely separate from connection state; "session" management and authentication is handled entirely client-side, purely based on the availability of private keys in memory, while the underlying client manages all connection information ephemerally and automatically.

This is incredibly powerful; not only does it allow applications to completely ignore IP addresses, it allows them to self-authenticate. In contrast to those *network-oriented* addresses necessary for physical data delivery, hash addressing allows for an *agent-oriented* architecture: network topology becomes irrelevant to applications, which focus directly on "who" said "what" to "whom".

To explicitly reiterate, note that hash addressing is also applied to the Golix entities themselves. This is especially important for portable devices; by assigning an autonomous device its own dedicated Golix ```identity```, it becomes trivially reachable and discoverable by any other ```identity``` **regardless of the underlying connection infrastructure**.