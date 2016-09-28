<img align="center" src="/assets/branding-logo-padded.png">

**Warning:** this repo is considered public pre-release information and is in varying levels of completeness. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

# Introduction

## Almost plain English

**Golix is a cryptographic protocol:**

+ It's a set of rules
+ that computer programs can agree on in advance,
+ which mathematically prove (with a few assumptions)
+ that a piece of data comes from a specific person,
+ and that the data cannot be seen by any third-parties.

**It is trustless and self-authenticating:**

+ Unlike Google or Facebook, large companies built on Golix cannot see your data
+ Unlike Google or Facebook, you "log in" to Golix once locally (on *your* computer, not over the web), and that proves to everyone else that you are who you say you are
+ You don't need to contact a third party to log in, so it works (for example) within your home wireless network, even if your internet connection goes out

**It is fully social:**

+ You can share and re-share data, at any time, with anyone
+ Unless and until you explicitly share something, it is provably private

**It is especially well-suited to the "Internet of Things" (IoT):**

+ Golix works regardless of your internet connection
+ It is substantially simpler to develop on than existing IoT frameworks
+ It is designed to work with many, many more total devices than exist on the internet today
+ It doesn't expose your IoT devices to the "outside world"

## More specifically

If I'm ```Alice```, the point of Golix is that:

1. I, ```Alice```, am always ```Alice```, and ```Alice``` is always me. 
    1. If ```someone``` shares something with me, they are guaranteed to be sharing with ```Alice```. 
    2. If I share something with ```someone```, they are guaranteed it comes from ```Alice```.
2. When I, ```Alice```, create some piece of data, I am guaranteed that ```no one``` else can see it. This also includes the servers where the data is stored.
3. I *may* choose to share that data with ```anyone```, at any time. I may also choose *not* to share it.
    1. Once I share it with ```someone```, they may share it with ```anyone``` else, at any time, without any restriction.
    2. Those recipients may, in turn, re-share it with ```anyone``` else, and so on.
    3. There will be no public record of who has shared with whom.
4. The data that I, ```Alice```, create, cannot be altered by ```anyone``` at any time, including by myself.
5. That data will always be available at the same place.
    1. It will always be accessible from a singular, exclusive, consistent address.
    2. Any new servers that host it will preserve and reuse that address.
6. I may also create a unique, consistent address for dynamic content.
    1. This dynamic content will be composed of the unalterable pieces of data from (5).
    2. The referent of this dynamic address (which piece of data it points to) can only be changed by me, ```Alice```.
    3. ```Anyone``` can see and use this dynamic address.
7. I may at any time declare a piece of data "in use".
    1. This is a matter of public record. ```Anyone``` can see that I, ```Alice```, am using that data.
    2. ```Anyone``` else may also declare the data "in use", regardless of who created it.
8. Servers must retain data if, and only if, it is declared "in use".

These constraints are enforced by:

1. Cryptography (```Alice``` is identified only by her public key fingerprint).
2. Cryptography (```Alice``` encrypts all data client-side immediately upon creation).
3. Cryptography (Sharing is defined as the transfer of the secret key for the data, and the key is shared asymmetrically, **separately** from the data itself).
4. Cryptography (Data is statically addressed by its hash digest).
5. Cryptography (Data is statically addressed by its hash digest, as per above).
6. Cryptography (```Alice``` must sign all dynamic addresses she creates, and monotonicity is enforced through a hash chain).
7. Cryptography (```Alice``` must sign all data "bindings" she creates).
8. Auditing the servers (in the long term, this could be enforced via blockchain).

Note: here, ```Alice```, ```anyone```, ```no one```, and ```someone``` are *digital* entities. They may or may not have any relationship to *physical* entities: they could be a Nest thermostat, a person, a business, purely digital, etc.

# Technical discussion

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

# Contributing

Help is welcome and needed. Unfortunately we're so under-staffed that we haven't even had time to make a thorough contribution guide. In the meantime:

## Guide

+ Issues are great! Open them for anything: feature requests, bug reports, etc. 
+ Fork, then PR.
+ Open an issue for every PR.
  + Use the issue for all discussion.
  + Reference the PR somewhere in the issue discussion.
+ Please be patient. We'll definitely give feedback on anything we bounce back to you, but especially since we lack a contribution guide, style guide, etc, this may be a back-and-forth process.
+ Please be courteous in all discussion.

## Project priorities

Note: these needs are specific to external contributors. Internal development priorities differ substantially.

+ Contribution guide
+ Code of conduct
+ Protocol security audits
+ Slack channel (or similar)

See also:

+ [Hypergolix contributions](https://github.com/Muterra/py_hypergolix#contributing)
+ [Hypergolix demo contributions](https://github.com/Muterra/py_hypergolix_demos#contributing)

## Sponsors and backers

If you like what we're doing, please consider [sponsoring the project](https://opencollective.com/golix#sponsor) or [becoming a backer](https://opencollective.com/golix).

**Sponsors**

  <a href="https://opencollective.com/golix/sponsors/0/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/0/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/1/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/1/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/2/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/2/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/3/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/3/avatar"></a>
  <a href="https://opencollective.com/golix/sponsors/4/website" target="_blank"><img src="https://opencollective.com/golix/sponsors/4/avatar"></a>

-----

**Backers**

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