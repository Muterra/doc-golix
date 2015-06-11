[From Muterra](http://www.muterra.io). **Warning:** this repo is considered public pre-release information. It is in varying levels of completeness, but being provided as a glimpse into our development process. Understand that it may or may not meet our quality standards. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

Who owns your data?
=========

Whether through direct participation or indirect interaction, it is essentially impossible to avoid leaving an internet footprint in today's world. This connectivity comes at a tremendous cost: when you upload data, you forfeit power over it. Individual websites *may* provide you some measure of external privacy, but this protection seldom applies to the service itself. In an age when the internet is such a core part of everyday life, for many people, non-participation is simply not an option, and this relationship between site and user is untenable at best, non-consensual at worst.

The lack of inherent confidentiality is not just a personal concern. The absence of a universal privacy standard irreparably fractures development: it forces every site and every service to reconcile between the uncountable URLs and APIs that make up the modern web, and this hodgepodge is as woefully difficult to secure as it is to describe.

We offer a new paradigm. It is the logical conclusion of "cloud architecture": a single global data repository, unified by common protocol, but easily decentralized. All uploads are private and protected. Information is shared directly between actors, regardless of physical location, and only by explicit choice. Data is found equally easily by human and machine, and API descriptions are uniform and programmatic.

This system turns the chaotic tangle of computers behind the web into a clear and coherent information vault. This is the mesh-made-muse.

Web vs Muse: the view from orbit
============

Disclaimer: this is a non-technical explanation with some huge simplifications. Like a book made into a movie, it isn't 100% accurate, but it hopes to capture the general idea of both the web and the muse. If you want a more detailed layman's explanation, [we're working on it](/web_muse_101.md). **Continue [below](#the-one-mile-overview) for a more thorough exploration,** but we suggest starting here regardless. You can also read our [whitepaper](/whitepaper.md) or [yellowpaper](/yellowpaper.md), but they are significantly more involved.

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

The one-mile overview
=====================

This section is intended to offer a slightly more detailed exploration of the muse. It builds on a basic understanding of the project, so we *recommend* that you read the above explanation first to get some context. Unfortunately, at this level, some technical language is unavoidable, but we'll try to offer wiki links for further reading. If you'd like an in-depth implementation discussion, check out [our whitepaper](/whitepaper.md).

Failures of the web today
-----------

It is very, very difficult to make informed decisions about data on the web. That's true for everyone: developers, companies, consumers. Even ignoring a growing number of security concerns, given current architecture, information can only be compartmentalized within an individual service. Since most services don't share their methodologies, much less their source code, participation becomes all or nothing: trust the service with too much information, or don't use it at all. When the forfeiture of sovereignty and autonomy is a systemic condition of use, then the system is broken. It's one thing to abstain from social network sites out of privacy concerns, but good luck avoiding your hospital's internet-connected electronic records system. Make no mistake: this is a problem of dire importance.

It is absolutely vital for *any* individual -- developer, company, consumer -- to be capable of making granular decisions about data sharing. That kind of information [agency](http://en.wikipedia.org/wiki/Agency_%28philosophy%29) requires a meaningful protection of privacy, but so long as it continues to be market-driven, software will always be developed to the lowest denominator. And security, which is a necessary but insufficient condition for privacy, has been consistently treated as a secondary market concern. If we're to have any hope of information empowerment, these hard privacy controls **must** be implemented at the protocol level, and **must not** be optional. Let there be no illusion here: [https does not ensure privacy](https://www.facebook.com); an internet privacy protocol simply does not exist. Furthermore, the prevailing market winds suggest that such a thing would only be adopted if it provided significant economic benefit beyond privacy protection.

At the same time, the development 

-----------------

This is the current edit point. Everything below this is pre-draft.

What I most care about is being able to make decisions. We as developers have an overwhelming tendency to prescribe our pet abstractions to everyone who uses our software. Some people might call this "designing with the worst developer in mind", and there's a lot of evidence that this makes for shit software. So instead of trusting the server to keep our data safe, since they won't, we should be able to secure it ourselves. A *decision* to share should be just that: a conscious, informed choice that I'm saying exactly X piece of information to exactly Y third party. But to say that, we need hard, algorithmically guaranteed privacy.

Most of the confusion online today can be blamed on poor addressing schemes. Addressing is inconsistent at best. The existence of the search engine market is a testament to how poor our use of URLs has become. Entire businesses, like IFTTT, have sprung up around the task of API unification. The point of a URL is to be human-readable, and yet services like Imgur are a testament that the age of URL legibility is long past. So why are we using them? If our URLs aren't understandable, what advantage do they have over IP addresses?

At this point, the real utility of DNS is in assuring [authenticity](http://en.wikipedia.org/wiki/Information_security#Basic_principles). But is it the best way of doing so? Without TLS, using a domain to assure authenticity is a very false security. DNS has outlived is usefulness as a way for humans to find things; it would be a much stronger authenticity "notary" if it were explicitly redesigned for that purpose.

Of course, it isn't possible to authenticate something without having a concept of identity. CA's do this already: they provide varying levels of investigation into the actual identity of the organization registering an SSL certificate. CA's are by far the most prolific, successful example of "single sign-on". But other than scale, there's very little conceptual difference between a CA intended for businesses, a CA intended for persons, or a CA intended for internet-connected devices: all of them offer some kind of mechanism by which Alice can verify that this computer claiming to be Bob is, in fact, Bob.



No way of forcing https, and no way to ensure security.  You aren't always the person making that decision -- think your network admin, healthcare provider, etc.

You can't fix privacy without eliminating client/server architecture, and you can't eliminate client/server without fixing privacy.

Primary concerns:

+ Fucking... agency, dude
+ Fucking... privacy, mate
+ How do humans find information, vs how do computers find information
+ Unification of services and experience
+ Division of concerns

Possible discussion points:

+ Structure: DNS, HTTP/S, URLs mixing human-meaningful and machine-meaningful content, etc
+ APIs: very simply concept, incredibly complicated execution.
+ SOAP, REST, etc
+ Addressing: persistence fails, mirrors fail, URLs fail, everything fails
+ No state
+ No meaningful protections of creation rights

The mesh, made a Muse
------------

The muse is designed from the ground up around data [agency](http://en.wikipedia.org/wiki/Agency_%28philosophy%29). We won't hold your hand through security. You are free to securely make insecure decisions: nothing will stop you from sharing your bank account with a scammer. We will, however, give you the tools to make your own decisions, and guarantee that the starting state is secure.