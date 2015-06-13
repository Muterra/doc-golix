[From Muterra](http://www.muterra.io). **Warning:** this repo is considered public pre-release information. It is in varying levels of completeness, but being provided as a glimpse into our development process. Understand that it may or may not meet our quality standards. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

This document describes *why* we're doing what we're doing. If you'd like to know specifically *what* we're doing, you should read our [whitepaper](/whitepaper.md). If you'd like to know *how* we're doing it, you should read our [yellowpaper](/yellowpaper.md).

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

And yet, despite how important privacy and security are, they're still severely lacking. Arguably the biggest obstacle here is that any meaningful definition of privacy requires a robust way of knowing *exactly* who you're talking to. This isn't usually something you need to consider when you're talking in person, but imagine your spouse has a perfectly identical twin. Now you need some fundamental way to unambiguously differentiate between indistinguishable people. That's exactly the difficulty online: on the web, *every* computer looks the same. The easiest solution is to create a password-protected account, and because the fundamental protocols of the web don't support sharing your account "identity" between sites, you're forced to create a new one for every site you visit.

The muse
----------------

The core thing we've done is to reverse all that. We started with the goal of making the *entire internet* behave as one very large, very simple hard drive. On this single global data repository, everything is private by default, which is made possible by universal identity definition. These universal identities are then used to readdress communications *directly* between people and things, largely eliminating the need for website intermediaries.

First and foremost, such a network needs persistence. In the future it may be possible to build this on a distributed, peer-to-peer system like [maidsafe](http://en.wikipedia.org/wiki/MaidSafe) or [BitTorrent](http://simple.wikipedia.org/wiki/BitTorrent), but for the time being, existing internet infrastructure can be used for this purpose. By separating storage into its own protocol, the muse makes it easy to implement new storage systems, either as a replacement for, or simultaneously with, existing "storage providers". One practical advantage of this paradigm is that the muse can be used *identically* at any scale: there is no difference between sharing data amongst users on a single machine, to computers on the same WiFi network, to websites on opposite sides of the planet.

Since this kind of unified internet storage requires all information to start as private, it also needs a sharing mechanism: the "access provider". Access providers are built on the network persistence, and through them "public information" arises. Different access providers may offer advantages and disadvantages, and though the simplest access provider (which requires only storage) manages sharing directly and explicitly between people and things, *public* access providers function as a sort of digital publisher, allowing data to be shared with larger groups of people. Like storage providers, access providers are both scalable (you can share with one user or many) and modular (you can use one access provider or many).

Finally, the muse defines an identity system. Identities, which are also built on network persistence, are the muse equivalent of a web account. Like access providers, the most basic identity provider is created naturally by the storage foundation, but more complex identity providers can supply arbitrarily complex forms of identity verification. For example, a biometric identity provider might require your fingerprint in part of its verification process. And once again, identity providers are scalable and modular.

These three components handle identity infrastructure, sharing management, and content storage, together forming the muse "service layer". It addresses three of the most complicated problems on today's web effectively and efficiently, and on top of it are built traditional internet applications.

The one-mile overview
=====================

This section is intended to offer a slightly more detailed exploration of the muse. It builds on a basic understanding of the project, so we *recommend* that you read the above explanation first to get some context. Unfortunately, at this level, some technical language is unavoidable, but we'll try to offer wiki links for further reading. If you'd like an in-depth implementation discussion, check out [our whitepaper](/whitepaper.md); if you want implementation *instructions*, check out [our yellowpaper](/yellowpaper.md).

Failures of the web today
-----------

It is very, very difficult to make informed decisions about data on the web. That's true for everyone: developers, companies, consumers. Even ignoring a growing number of security concerns, given current architecture, information can only be compartmentalized within an individual service. Since most services don't share their methodologies, much less their source code, participation becomes all or nothing: trust the service with too much information, or don't use it at all. When the forfeiture of sovereignty and autonomy is a systemic condition of use, then the system is broken. It's one thing to abstain from social network sites out of privacy concerns, but good luck avoiding your hospital's internet-connected electronic records system. Make no mistake: this is a problem of dire importance.

It is absolutely vital for *any* individual -- developer, company, consumer -- to be capable of making granular decisions about data sharing. That kind of information [agency](http://en.wikipedia.org/wiki/Agency_%28philosophy%29) requires a meaningful protection of privacy, for which security is a necessary but insufficient condition. And since the market drives software development to the lowest common denominator, the industry has consistently treated security as an expensive afterthought; if we're to have any hope of widespread use, these hard privacy controls **must not** be optional. But the limited success of [PGP](http://en.wikipedia.org/wiki/Pretty_Good_Privacy) indicates that a universal privacy protocol would only be competitively viable if it provided significant economic benefit beyond data protection.

(Un)fortunately, the internet is so tremendously messy that creating market incentives for new protocols isn't all that difficult. And though security is often a lamentably minor consideration, it is eventually and unavoidably demanded. So clearly, there *is* money to be made by streamlining development and emphasizing interoperability. The rapid emergence of [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) APIs as a *de facto* standard is exactly such an effort, but commercial API aggregators like [IFTTT](http://en.wikipedia.org/wiki/IFTTT) suggest that RESTful architectures are an incomplete solution. This is especially true in the [IoT](http://en.wikipedia.org/wiki/Internet_of_Things) arena, where integrating a thermostat with tens or hundreds of smart grid APIs is a truly daunting task. RESTful endpoints notwithstanding, there are just too many URLs to manage easily, and developers are left to put band-aids on a broken address system. In the end, the familiar combination of DNS, domains, and file paths just doesn't scale to the billions of devices and services coming online by the day.

We shouldn't be so afraid, so hesitant, to question web addressing. URLs were standardized in [December 1994](https://en.wikipedia.org/wiki/Uniform_resource_locator#History), at a time when the total number of websites had [just passed 10000](https://en.wikipedia.org/wiki/List_of_websites_founded_before_1995#1994). Since then, the web has grown by [several million times](http://www.worldwidewebsize.com/), but the ```http://www.domain.com/file/path/here``` scheme has remained essentially unchanged. URLs, together with DNS, are meant to reconcile computer-required [topological](https://en.wikipedia.org/wiki/Network_topology) data locations with human-required [semantic](https://en.wikipedia.org/wiki/Semantic_memory) locations. Put simply: a web address is intended to correlate data's physical address with its conceptual address. Now, with decades of hindsight, we can say with conviction that a URL is inappropriate for this purpose: popular services like Imgur eschew URL legibility entirely, and 404 errors are so common that the term has [entered conversational English](http://www.webster-dictionary.org/definition/404). If our URLs are incomprehensible to both human *and* machine, why are we still using them?

At this point, the only real utility of DNS is to assure [authenticity](https://en.wikipedia.org/wiki/Information_security#Basic_principles). But without [SSL/TLS](https://www.google.com/search?q=band+aids&tbm=isch) and their accompanying [certificate authorities](https://en.wikipedia.org/wiki/Certificate_authority), domain-based verification is a very false security. This muddled approach to server authenticity likely won't scale to accommodate the coming Internet of Things, which will see a truly unprecedented number of internet-serving "things". If even 1% of the [20+ *billion* new devices](http://www.forbes.com/sites/gilpress/2014/08/22/internet-of-things-by-the-numbers-market-estimates-and-forecasts/) expected to come online by 2020 are functioning as servers, securing them would require a roughly [50-fold increase](http://news.netcraft.com/archives/2015/05/13/counting-ssl-certificates.html) in certificate issuance. These numbers are unsustainable, and the time to act is now.

Throw out the rundown, bring on the Muse
------------------

The muse is designed from the ground to answer these challenges with a scalable, performant alternative. It is built on a foundation of data [agency](http://en.wikipedia.org/wiki/Agency_%28philosophy%29) and minimizes forced abstractions.


#**This is the current edit point. Everything below this is pre-draft.**

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

We won't hold your hand through security. You are free to securely make insecure decisions: nothing will stop you from sharing your bank account with a scammer. We will, however, give you the tools to make your own decisions, and guarantee that the starting state is secure.

Let there be no illusion here: [https does not ensure privacy](https://www.facebook.com), and t

https://en.wikipedia.org/wiki/Adhesive_bandage


Of course, it isn't possible to authenticate something without having a concept of identity. CA's do this already: they provide varying levels of investigation into the actual identity of the organization registering an SSL certificate. CA's are by far the most prolific, successful example of "single sign-on". But other than scale, there's very little conceptual difference between a CA intended for businesses, a CA intended for persons, or a CA intended for internet-connected devices: all of them offer some kind of mechanism by which Alice can verify that this computer claiming to be Bob is, in fact, Bob.


What I most care about is being able to make decisions. We as developers have an overwhelming tendency to prescribe our pet abstractions to everyone who uses our software. Some people might call this "designing with the worst developer in mind", and there's a lot of evidence that this makes for shit software. So instead of trusting the server to keep our data safe, since they won't, we should be able to secure it ourselves. A *decision* to share should be just that: a conscious, informed choice that I'm saying exactly X piece of information to exactly Y third party. But to say that, we need hard, algorithmically guaranteed privacy.


No way of forcing https, and no way to ensure security.  You aren't always the person making that decision -- think your network admin, healthcare provider, etc.

You can't fix privacy without eliminating client/server architecture, and you can't eliminate client/server without fixing privacy.
