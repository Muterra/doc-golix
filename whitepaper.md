**Draft warning:** this is a public draft **in-process** dated 22 Oct 2015, but should not be considered final. If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html). Our previous whitepaper was very shy on technical details and has been moved to a [design philosophy](/design_philosopy.md) document.

# Introduction

Question: what is a social network?

Social systems are communicative. They are defined by interactions between entities. Broadly speaking, these entities must be capable of independent action; that is to say, they must possess a degree of individual [agency](https://en.wikipedia.org/wiki/Agency_%28philosophy%29). A non-agent component in a social interaction -- for example, a megaphone -- is simply a tool of its owner-agent. Nobody talks *to* megaphones; one talks *through* them.

Networks are defined by the links between their interconnected nodes. More pertinent to this discussion, *digital* networks are nodular information exchanges. They arise from linked data handlers: computers, phones, servers, *devices*, exchanging information amongst themselves. In a digital world, these network nodes are our megaphones.

It stands to reason that a social network should be simply the combination of these two concepts. Individual digital agents, communicating *through* their devices, *to* other agents. In a social network, it makes no more sense to address an agent's device than it would to talk to a megaphone. And yet crucially, we lack a social protocol that defines such a transaction without the added bloat of a web API. The Muse protocol aims to fill this need.

## Defining the abstraction

Given this broad definition of a "social network", we must reexamine the requirements for a general-purpose social protocol. It isn't possible to be everything for everyone, but this translation between network hardware (the "megaphones") and the persons and things that use them is a *singular* task that *any* social application must solve. By carefully limiting Muse to this one problem, and offering a [zero-cost abstraction](http://blog.rust-lang.org/2015/05/11/traits.html) to solve it, we can attempt to build a universal social protocol.

However, it is critically important to avoid commingling such a protocol with any particular application, even one as unspecific as "social websites". By our definition, a distributed Bluetooth atmospheric sensor network is no less social than Facebook; our protocol must serve both applications equally efficiently. Muse is not built to define the relationships between agents, nor the physical links between their devices, but rather to facilitate their direct communication regardless of external factors. In other words: the Muse **protocol** is transport- and application-agnostic.

Therefore, Muse must only and always answer these three questions:

1. I am an agent. What is my digital equivalent?
2. I created data. How do I represent it?
3. I have information. How do I share it?

These three aspects of the Muse protocol encompass identity, content, and sharing management, respectively.

## Defining the technical requirements

First and foremost: any network action must be protocol-enforced. To use a concrete example, an agent's data privacy cannot be assured by the the promise of a federated server, *even if the server's owner is known to the agent;* it must be provided algorithmically by protocol implementations. A verified-correct implementation must always reject actions that violate the protocol's defined intent. At present, this can only be accomplished using cryptographic enforcement.

Furthermore, agency implies privacy. Information can only be created, retained, and shared; unless information is explicitly shared by network entities, the "cat" is irrevocably let out of the bag. As such, information on the network must be private-by-default -- that is to say, *all content* must be confidentially encrypted *at all times* during communication between entities.

# Content management

## Base requirements

Arbitrary binary data

## Asynchronous availability

### Persistence and its standardization

Storage provider command set

### Data retention and removal

## Addressing content

Including static and dynamic bindings

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

