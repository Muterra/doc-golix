Who owns your data?
=========

[From Muterra](http://www.muterra.io)

Whether through direct participation or indirect interaction, it is essentially impossible to avoid leaving an internet footprint in today's world. This connectivity comes at a tremendous cost: when you upload data, you forfeit power over it. Individual websites *may* provide you some measure of external privacy, but this protection seldom applies to the service itself. In an age when the internet is such a core part of everyday life, for many people, non-participation is simply not an option, and this relationship between site and user is untenable at best, non-consensual at worst.

The lack of inherent confidentiality is not just a personal concern. The absence of a universal privacy standard irreparably fractures development: it forces every site and every service to reconcile between the uncountable URLs and APIs that make up the modern web, and this hodgepodge is as woefully difficult to secure as it is to describe.

We offer a new paradigm. It is the logical conclusion of "cloud architecture": a single global data repository, unified by common protocol, but easily decentralized. All uploads are private and protected. Information is shared directly between actors, regardless of physical location, and only by explicit choice. Data is found equally easily by human and machine, and API descriptions are uniform and programmatic.

This system turns the chaotic tangle of computers behind the web into a clear and coherent information vault. This is the mesh-made-muse.

Web vs Muse
============

Disclaimer: this is a non-technical explanation with some huge simplifications. Like a book made into a movie, it isn't 100% accurate, but it hopes to capture the general idea of both the web and the muse. If you want a more detailed layman's explanation, take a look at our [Web/Muse 101](/web_muse_101.md) guide. **Continue [below](#the-technical-explanation) for a more technical explanation,** but we suggest starting here regardless.

The web today
--------------

The internet is a pretty fragmented place. Let's say you upload a picture of your dog, and a week later you can't find it. Is it on Facebook? Imgur? Dropbox? Everywhere you go, there's a different way to look. Can you imagine how hard it would be to keep your Dropbox synced with your Facebook photos? What about sharing write access to a Dropbox folder to a friends list on Facebook? When you're writing code for the web, you're faced with those kinds of problems all the time. When people talk about putting things "in the cloud", they're really talking about a thousand different clouds, each with their own special requirements and rules. Why isn't saving data *to the internet* as simple as saving it to your hard drive?

Probably the most compelling cause for this complexity is privacy. Despite the common misconception that connections on the web are private and confidential, much of your internet traffic is left public. When you "talk" to a website, by default any communication to and from that site is a lot like putting a postcard in a mailbox shared by 3 billion people. *You* might only be looking at your own postcards, but there's absolutely nothing stopping *anyone else* from looking at them, too. When you first come online, you are sharing everything -- absolutely everything, email, browsing history, the whole works -- you're sharing everything you do with each and every other person online, and hoping that they don't read it. Sharing your bank password with 3 billion people is obviously a very bad idea, so because the [underlying technology](http://en.wikipedia.org/wiki/Application_layer) is missing any built-in privacy protection, we're forced to create this complicated system to keep your data safe.

And yet, despite how important privacy and security are, they're still a technological afterthought. Arguably the biggest obstacle here is that any meaningful definition of privacy requires a robust way of knowing *exactly* who you're talking to. This isn't usually something you need to consider when you're talking in person, but imagine your spouse has a perfectly identical twin. Now you need some fundamental way to unambiguously differentiate between indistinguishable people. That's exactly the difficulty online: on the web, *every* computer looks the same. The easiest solution is to create a password-protected account, and because the fundamental protocols of the web don't support sharing your account "identity" between sites, you're forced to create a new one for every site you visit.

The mesh, made a Muse
----------------

The core thing we've done is to reverse all that. We started with the goal of making the *entire internet* behave as one very large, very simple hard drive. On this single global data repository, everything is private by default, which is made possible by universal identity definition. These universal identities are then used to readdress communications *directly* between people and things, largely eliminating the need for website intermediaries.

First and foremost, such a network needs persistence. In the future it may be possible to build this on a distributed, peer-to-peer system like [maidsafe](http://en.wikipedia.org/wiki/MaidSafe) or [BitTorrent](http://simple.wikipedia.org/wiki/BitTorrent), but for the time being, existing internet infrastructure can be used for this purpose. By separating storage into its own protocol, the muse makes it easy to implement new storage systems, either as a replacement for, or simultaneously with, existing "storage providers". One practical advantage of this paradigm is that the muse can be used *identically* at any scale: there is no difference between sharing data amongst users on a single machine, to computers on the same WiFi network, to websites on opposite sides of the planet.

Since this kind of unified internet storage requires all information to start as private, it also needs a sharing mechanism: the "access provider". Access providers are built on the network persistence, and through them "public information" arises. Different access providers may offer advantages and disadvantages, and though the simplest access provider (which requires only storage) manages sharing directly and explicitly between people and things, *public* access providers function as a sort of digital publisher, allowing data to be shared with larger groups of people. Like storage providers, access providers are both scalable (you can share with one user or many) and modular (you can use one access provider or many).

Finally, the muse defines an identity system. Identities, which are also built on network persistence, are the muse equivalent of a web account. Like access providers, the most basic identity provider is created naturally by the storage foundation, but more complex identity providers can supply arbitrarily complex forms of identity verification. For example, a biometric identity provider might require your fingerprint in part of its verification process. And once again, identity providers are scalable and modular.

These three components handle identity infrastructure, sharing management, and content storage, together forming the muse "service layer". It addresses three of the most complicated problems on today's web effectively and efficiently, and on top of it are built traditional internet applications.

The technical explanation
=====================

This section is intended to offer a lot of technical clarifications to the above explanation. We *recommend* that you read the non-technical explanation first to get an overall understanding of the system, but it isn't necessarily required reading. This is just a mile-high overview of the muse; **if you'd like a more in-depth technical discussion, please read [our whitepaper](/whitepaper.md).**

The web today
-----------

The mesh, made a Muse
------------