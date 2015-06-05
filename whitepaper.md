A next-generation internet built around secure, private, scalable interactions between people and things 
=========

Today's web is public and centralized by default. DNS, HTTP, SMTP, and the other various technologies of the internet's application layer negotiate conversations between machines with no inherent awareness of the final entity (agent), human or otherwise, responsible for receiving and acting upon the transferred information. This network-oriented architecture, in contrast to an agent-oriented architecture, is one of the major limitations of the contemporary internet. Network-oriented architecture is characterized by static machine addressing, regardless of agent identity. It makes robust end-to-end security exceptionally difficult, and therefore poses a significant burden to meaningful digital privacy. It decreases the reliability of persistent data and relies heavily upon third parties for information semantics. Network-oriented architecture fails to decouple agent interactions from network operations, hindering modularization and distribution of both the agent and the network. Agent-oriented architecture, on the other hand, is characterized by static agent identity, regardless of machine addressing. This is accomplished by introducing a new abstraction layer that mediates between the network-oriented transport layer and a redefined, agent-oriented application layer. This new "service layer" is designed to be private by default and requires encryption of all data at rest. Persistence is supported by the network as a whole and information is inherently semantic. This new internet protocol stack negotiates location-based internet data streams into action-based endpoints more effectively than the existing world wide web, making it far more suitable for the ever-growing world of internet-connected physical devices generally termed the Internet of Things. Because its primary goal is to transform the loosely-connected tangle of internet services into a single amalgamated source of information, we've termed this new configuration the mesh-made-muse.

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

--------------

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