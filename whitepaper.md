**Draft warning:** this is a public draft dated 5 July 2015, but should not be considered final. 

# Introduction

Like many complex evolutionary systems, the web suffers from "dorm door" syndrome: we've added layer upon layer of paint to cover our past mistakes. We're on the cusp of a dramatic increase in networked devices, and we need a better approach. We've taken a long look at the web and identified three key problems:

1. Privacy is hard to provide and harder to demand
2. It's very difficult to locate things and people
3. Internet-connected devices and services are complicated to develop

that need to be solved under two constraints:

1. Information must be universal
2. Information must be self-contained

while satisfying one assumption:

1. The above proposal is economically compelling.

Put simply: information on the web is hard to protect, hard to find, and hard to use. Solving these problems requires a single system that everyone can use. Such a solution should (and must!) be financially beneficial to the internet community. The Muse addresses them with:

1. A universal data persistence system;
2. "Privacy by default" for that system, using three encrypted container files;
3. APIs as first-class citizens, built from those containers, including...
4. System-wide sharing and identity APIs, for the purpose of...
5. Eliminating network locations as an application concern when communicating on the internet

## The imposing problems and impending struggle

It's easy to conceptualize the web as a single coherent entity. We upload to "the cloud". I heard about you "online". Welcome to "the internet". Most people, I think, realize that it isn't actually a singular thing, that there are *actual people* on the other end of those words, but I think very few understand the scope of the disarray.

The internet -- the *inter-network*, a "network of networks" -- was developed in the 70's and 80's to unify separate computer groups with a common language. It allowed any two online computers to talk to each other in a consistent way. By the early 90's, the internet needed a way to manage and share documents between these connected machines, and so the "world wide web" was born. Most of the core architecture of the web (DNS, domains, URLs, websites) was established in those first few years, and hasn't changed much since.

Like any other promising new technology, the web quickly outgrew its cradle. As simple communication between computers became complex sharing between persons, things, and services, new internet applications were forced to adapt that core architecture to demands it was never intended to serve. With [20 billion devices online already, and another 20 billion projected in the next five years](http://www.forbes.com/sites/gilpress/2014/08/22/internet-of-things-by-the-numbers-market-estimates-and-forecasts/), we have reached the tipping point. The longer we wait, the bigger these problems become, and the band-aids just aren't stopping the bleeding anymore.

### 1. Information control requires privacy

My strongest complaint about the internet is that I cannot effectively control my online data. That information autonomy, that information *[agency](http://en.wikipedia.org/wiki/Agency_%28philosophy%29)*, is only possible if content is *private by default*. In the same way that our thoughts are private until we speak them, our uploads **must** be private -- not just secure, but *private* -- until we share them. Any other arrangement, by definition, precludes individual agency; if participation is mandatory, automatic information sharing cannot be a condition of use.

On the web, this poses an immediate problem: security is a necessary but insufficient condition for privacy, but those 20-year-old foundations don't require it. Due to this, and despite the common misconception of a confidential "conversation" between user and website, most internet traffic is left public. So when you "talk" to one of these unsecured sites, it's a lot like sharing a mailbox with every other internet user: no matter how honest you are, there's nothing to stop anyone else from reading your mail.

Now obviously, since sharing your bank password with 3 billion fellow internet users is a very bad idea, plenty of secure sites do exist. But the current approach (https) affords security only on a per-site basis, so making informed decisions about data on the web is still exceptionally hard. Even if https were required for all websites, you'd still be forced to choose between trusting sites with *all* your information, or not using the services they provide. This paradigm is as relevant to Facebook's advertising practices as it is your hospital's electronic records system. Because websites are inseparably responsible for access to participation *and* privacy, users are left without recourse for denial of either. This dependency is what disempowers people on the internet, and without decoupling these concerns, individuals are incapable of digital agency. Yet no existing technology offers a privacy solution applicable to the internet as a whole.

### 2. Humans and computers find things differently

The biggest challenge in this private-by-default approach is that it makes information exponentially harder to find. Given how difficult internet data location already is, we can't really afford to make it any worse. So we're left to question a second web fundamental: addressing.

URLs are meant as a convenient, comprehensible data locator for both human and computer. They were standardized in [December 1994](https://en.wikipedia.org/wiki/Uniform_resource_locator#History), at a time when the total number of websites had [just passed 10000](https://en.wikipedia.org/wiki/List_of_websites_founded_before_1995#1994). Since then, the web has grown by [several million times](http://www.worldwidewebsize.com/), but the ```http://www.domain.com/file/path/here``` scheme has remained essentially unchanged. However, many (if not most) popular web services eschew URL legibility entirely, and 404 errors are so common that the term has [entered conversational English](http://www.webster-dictionary.org/definition/404). URLs have clearly not aged well.

There are a lot of practical consequences for the clumsiness of URLs. From a user's perspective, they mostly revolve around dynamic content: someone moving, mirroring, or otherwise modifying a web resource. In this case, the content remains conceptually similar, while the URL changes. Here there are typically two needs: preserve the availability of the original content, and link old to new. Once again, because there is no internet-wide solution, these concerns are only addressed on a site-by-site basis, leaving external (and often internal!) sites with stale links.

In hindsight, we can say fairly conclusively that people have different requirements for finding information than computers. Humans look for content conceptually, but computers need rigid, consistent locations. With billions of sites and users, it simply isn't possible for a developer to prescribe a universally-meaningful URL. So instead, users turn to search engines, hyperlinks, and social sites to resolve ambiguous human intent into deterministic machine location. This is the core failure of path-based URLs: by trying to serve as the semantic bridge between people and machine, they hinder both.

### 3. Development is exceptionally complicated

If you're replacing URLs, the easiest approach to computer data accessibility is to completely separate human semantics from machine location. So for the sake of argument, let's say that happens, and every internet file has a unique machine identifier that humans aren't meant to use. What happens to the developer? How does she reference existing content on external services?

This is already an astoundingly slow process, and entire companies have been built for the sole purpose of streamlining it. Now, URLs are definitely part of the problem, but web development has coalesced around them as the one [pseudo-standardized](https://en.wikipedia.org/wiki/Representational_state_transfer) way to get two websites to talk to each other. Fortunately, it's not terribly difficult to create an explicit, universal [description](https://en.wikipedia.org/wiki/Schema#Computer_science) of how services [talk to each other](https://en.wikipedia.org/wiki/Application_programming_interface) (the "schema" and "API", respectively). APIs are incredibly widespread, but schemas remain a comparatively unpopular way of describing them. Though schemas offer a lot of advantages, they don't fit very well intro traditional web development, and are sometimes at odds with URL-based API descriptions. Meanwhile, the APIs themselves are only getting more complicated, partly due to privacy concerns over the data they expose.

And finally, we've closed the loop. Protecting privacy requires improving accessibility, improving accessibility is meaningless without simplifying development, and simplifying development requires protecting privacy. If we're going to tackle any one problem effectively, we have to address all three cohesively.

## Converging on solutions

Admittedly, the scope of these problems is particularly broad, and there are a lot of ways to start improving things. At a certain point, you have to decide on a "good enough" answer, and though the Muse will certainly *start* with a smaller scale, it makes a great deal of sense to create something capable of improving the internet as a whole. As we're ignoring the existing web "[application layer](https://en.wikipedia.org/wiki/Application_layer)" and starting from scratch, the minimum viable solution should be both universal and self-contained.

### 1. Universality constraint

The final goal of the project is to bring coherency to the internet. This must be a universally-applicable effort; the Muse must be capable of managing any digital information. But this cuts to the heart of the problem of [standards proliferation](https://en.wikipedia.org/wiki/Format_war): it's functionally impossible to be *everything* for *everyone*. As crucial as standards are to optimizing collective effort, making fully comprehensive ones will ultimately force users into inappropriate abstractions for their needs.

Instead of being everything for everyone, we focus on being *something* for *anyone*. The Muse [won't force people to behave](http://www.paulgraham.com/design.html); the project is rooted in individual agency, and that's intended to be just as true for developers as it is for consumers. The Muse attempts to establish a bare minimum standard for any networked data communication. No more, no less.

If there's a hope of seeing the internet realize its potential as the sum total of human information, such a standard is an absolute imperative. The world as we know it is market-driven, and looks to the lowest denominator -- and that local minimum is to be *everything* for *someone*. Until we break out of that dead-end valley, the internet will only get messier.

### 2. Containment constraint

The project also has a need for self-containment. This applies to both the system as a whole, as well as the information on it: the Muse cannot simply assume the existence of supporting architecture, but must instead be able to create it using basic network primitives. Likewise, the creation and storage of information cannot rely upon any companion data. The former requirement is directly implied by the universality constraint, and the latter comes from observations about the limitations of today's internet metadata.

Metadata -- literally, data about data, like the location a photograph was taken -- is prolific on the contemporary web. But when it is implemented externally to the information, it is very easily lost. Take, for example, the original author of a video: a creator uploads the video to Youtube, where it is downloaded, and then re-uploaded to Facebook. Because this metadata exists only on Youtube, it is irreversibly lost when mirrored (whether or not that mirror is malicious or benign). Meanwhile, image metadata, though very easily removed, tends to be a whole lot "stickier", to the point where it's been used as evidence of Russian involvement in the current Ukrainian conflict. So for metadata to be effective, it must be internalized.

However, the far stronger argument for the containment constraint is that though it is often transparent to the end user, metadata can be incredibly revealing. Public metadata, like geolocation information on a Twitter feed, can be used to reconstruct a very detailed picture of someone's life. And from an information security standpoint, public metadata *by definition* [leaks confidential information](http://gizmodo.com/why-the-metadata-the-nsa-has-on-you-matters-512103968). So since privacy is a requirement for agency, and security a requirement for privacy, information must be self-contained.

## The economic assumption

The changes proposed by the Muse project are, in a word, ambitious. For them to be successful requires us to provide a compelling economic benefit from square one. Networks don't emerge overnight, so there is certainly a need for some backwards compatibility. But ultimately, the network must stand on its own financial footing.

(Un)fortunately, the existing internet is so tremendously messy that creating market incentives for new protocols isn't all that difficult. Clearly, there *is* money to be made by streamlining development and emphasizing interoperability. The rapid emergence of [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) APIs as a *de facto* standard is a step in the right direction, but commercial API aggregators like [IFTTT](http://en.wikipedia.org/wiki/IFTTT) suggest that RESTful architectures have not satisfied market demands for network coherency. This is especially true in the [IoT](http://en.wikipedia.org/wiki/Internet_of_Things) arena, where integrating a thermostat with tens or hundreds of smart grid APIs is a truly daunting task. In the end, basing APIs on the familiar combination of DNS, domains, and file paths just doesn't gracefully scale to the billions of devices and services coming online by the day.

And as messy and convoluted as it is now, the coming Internet of Things will utterly upend existing architecture. The aforementioned [Forbes meta-analysis](http://www.forbes.com/sites/gilpress/2014/08/22/internet-of-things-by-the-numbers-market-estimates-and-forecasts/) conservatively predicts a doubling of internet-connected devices by 2020, with 75% of the over 20 *billion* new connections coming from "sensor nodes and accessories". These kinds of "things" have very different infrastructure requirements: they are more far more likely to operate as servers, and far less likely to require user-defined entry points. If even 1% of those sensors and accessories are webfacing servers, securing them would require issuing roughly 50 times the [total number of SSL certificates ever issued](http://news.netcraft.com/archives/2015/05/13/counting-ssl-certificates.html). Though it would probably be possible to incrementally improve upon existing architecture to meet these demands, we have long since reached the point of diminishing returns. Given the limitations of the web as we know it, these growth predictions are fiscally unsustainable. The barrier to entry for a new protocol is low enough, and the potential rewards great enough, to justify its adoption. The time to act is now.

# The Muse project

The fundamental goal of the Muse project is to rebuild the internet to support the totality of humanity's information needs in a single cohesive way. To that end, we propose:

1. A universal data persistence system;
2. "Privacy by default" for that system, using three encrypted container files;
3. APIs as first-class citizens, built from those containers, including...
4. System-wide sharing and identity APIs, for the purpose of...
5. Eliminating network locations as an application concern when communicating on the internet

## 1. Standardized storage and persistence

Any information system needs an information medium. This role is currently filled by servers, which are (directly or indirectly) used by websites for their own individual needs. On the Muse, servers are [content-agnostic and content-unaware](#2-Privacy-via-encrypted-containers), and these "storage providers" are accessed entirely by API. Any participating node can elect to host any participant's data. Storage providers may opt for explicit peering agreements, and clients may always use multiple storage providers. By standardizing storage interfaces and eliminating switching costs, the Muse allows for complete interchangeability between hosting providers. And storage is independent of transport technology: this mechanism allows for seamless client switching between, for example, a [meshed Bluetooth](http://www.forbes.com/sites/michaelwolf/2015/02/26/with-mesh-bluetooth-strengthens-case-as-key-internet-of-things-technology/) P2P provider and a traditional TCP/IP client/server model.

This persistence system is designed for arbitrary binary data and is therefore not intended to be human-readable. Human accessibility is built elsewhere; information semantics are not and should not be the concern of data hosts. As such, domains (and therefore URLs) are inappropriate here. Thus, the Muse explicitly separates the way your computer finds data from the way your brain finds it. The machine searches a simple [flat](https://en.wikipedia.org/wiki/File_system#Flat_file_systems) repository, using a [zero-knowledge](https://en.wikipedia.org/wiki/Zero-knowledge_proof), [content-based](https://en.wikipedia.org/wiki/Content-addressable_storage) unique identifier. This creates a secure, reliable, unbreakable link: an muid. These muids are used to address **all** content on the Muse, and the simplicity of addressing provides strongly deterministic data retrieval -- gone are the days of the 404.

It's important to note that, though the underlying transport layers are free to follow client-server models, the storage providers themselves follow the [pub/sub](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) pattern, and the client-server distinction is essentially meaningless on the Muse.

## 2. Privacy via encrypted containers

Because these storage providers must be fully interoperable, they should also be truly content-agnostic and trustless: that is, they should treat all data equally, regardless of content, but should not be trusted to do so. This seeming contradiction can only be resolved if the hosts are fully unaware of data content, and the only way to enforce this is through [end-to-end encryption](https://en.wikipedia.org/wiki/End-to-end_encryption). Three "encrypted information container" (EIC) formats are specified for this purpose: static containers (EICs), dynamic containers (EICd), and access containers (EICa). 

Static and dynamic containers are [symmetrically](https://simple.wikipedia.org/wiki/Symmetric-key_algorithm) encrypted; access containers are encrypted [asymmetrically](http://simple.wikipedia.org/wiki/Public-key_cryptography). In addition to its zero-knowledge muid, each container has exactly one piece of public metadata: EICs and EICd files have public authors; EICa files have public recipients.

EICs files are:

1. [fully-persistent](https://en.wikipedia.org/wiki/Persistence_%28computer_science%29) (ie permanent)
2. [key: value stores](https://en.wikipedia.org/wiki/Associative_array) of
3. arbitrary binary content 
4. supporting [heritance](https://en.wikipedia.org/wiki/Inheritance_%28object-oriented_programming%29),
5. and automatically subscribe to any EICa that use their muid as the recipient.

EICd files are:

1. semi-persistent
2. unstructured (though we highly recommend [ZBG-encoding](/zbg_encoding.md) of **all** EICd for API compatibility)
3. binary [blobs](https://en.wikipedia.org/wiki/Binary_large_object)
4. with an optional buffer,
5. and may subscribed to.

EICa files are:

1. temporarily persistent (ie deleted once received)
2. rigidly-formatted 
3. binary constructs
4. used for API session handshakes,
5. and are automatically pushed to their recipient by storage providers (see EICs description above).

Together, these three containers form the basic primitives for all data operations on the Muse network.

## 3. Ease development by treating APIs as first-class citizens

The strict division of concerns between information storage/delivery and data operations on the Muse make APIs an absolutely critical component of the network. In fact, the entire Muse system can be thought of as an intertwined collection of APIs. So we treat APIs as first-class objects built directly on those basic network primitives. If EIC files form the Muse's skeleton, APIs are its arteries and veins.

Any muid *may* be used as an API [endpoint](https://stackoverflow.com/questions/5034412/api-endpoint-semantics), but only muids associated with [Muse identities](#4-Inherent-identity-and-sharing-mechanics) maintain associativity between the API request and response. Put differently: API connections should be addressed to the muid for the agent that's providing the API. To give an example, both a "self" and "link" post for a Muse clone of Reddit would be addressed to Reddit itself. Reddit would then do something with the data you send over the connection (in this example, display and distribute it). Because Reddit as an independent agent would have its own identity, it can also open an API channel back to your identity for a response.

Individual API requirements are defined in specially-formatted EICs files, using a standardized schema that supports arbitrary API complexity. As with any EICs, schemas support heritance. The muid of the schema itself is used directly as an API [class](https://en.wikipedia.org/wiki/Class_%28computer_programming%29) definition, and note that the schema muid is functionally unrelated to its author. So, using the above example, Reddit differentiates between "self" and "link" posts by the class declaration of the opened API. If a new service (we'll call it "Breddit") wanted to reuse those "self" and "link" APIs, the class muid remains unchanged, but connections would be addressed to muid of the new Breddit identity.

To connect to an API, the API client/consumer first creates an API session EICd for the API provider. This dynamic file describes the desired API class, and the EICd/EICs resources required to fulfill it. The client then initiates the API through an EICa (access) file, using the API provider as the recipient. The EICa contains the symmetric encryption key for the session EICd, which may be refreshed at any time with a new EICa. In the above example, your first EICd frame would reference the "self" post class with a link to a different EICd/EICs file containing the post body. Then, you would create an access file with Reddit as a recipient. Note that the EICa contains the key for **only** the session EICd; all other symmetric keys are distributed by the [sharing API](#4-Inherent-identity-and-sharing-mechanics).

## 4. Inherent identity and sharing mechanics

A private network without a sharing system can't communicate, and to be trusted, such a sharing mechanism also requires an "identity" definition and exchange system. But the details of those systems can be incredibly complex, and different applications are likely to have new and unforeseeable needs for them. Thus, it's really not possible to spell out one-size-fits-all solutions to identities and sharing.

Of the three EIC file types, only EICd and EICs (the two symmetrically-encrypted types) carry actual content. So for you to share content with another agent, you must share with them the content container's symmetric key. This function exactly (no more, and no less) is performed by "access providers". These providers have a standardized API: agents request access by the content's muid, and the provider responds with an encryption key (if one is available). Multiple key distribution systems are explicitly capable of coexisting; an agent may request access through any number of providers, and an author may choose to distribute through any or many access providers. Because all Muse agents are their own API endpoints, this API is also available for direct P2P communication.

Trusted third parties are therefore *possible*, but not *required*: the API is identical for both client-based and server-based services. A client-based access provider might share content privately, directly and specifically between exactly two people. Another provider, in contrast, might use a centralized server middleman to share content publicly, with anyone who requests it.

With sharing established, the Muse needs a way of verifying the identity of the agents you're sharing with. Agent identities need to accomplish three tasks: digital signing, asymmetric encryption, and deniable alias exchange. Digital signatures verify the author of EIC files. Asymmetric encryption is needed for EICa files (and therefore, API handshakes). The most interesting of these functions is deniable alias exchange: since Muse identities *may* be temporary (ie, named, pseudonymous, or anonymous), it's worthwhile for you to have a way to prove to another agent that two identities are "owned" by the same person, while maintaining outward deniability. This is accomplished with a [Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) exchange between new identities using the public keys from the old ones.

Here, the role of the [public key infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) is fulfilled by Muse "identity providers". Like access providers, the identity provider API is simple: you request an identity for an muid, and the provider responds with three asymmetric public keys: the signing key, encryption key, and exchange key.

Furthermore, the EICs file at the identity's muid must itself satisfy the identity provider API. This bootstraps identities, allowing webs-of-trust to seamlessly coexist with trusted third-party identity verification, key escrow services, direct exchange of identity between individuals, etc.

## 5. Agent-oriented architecture: the conclusion

The internet shuttles information from computer to computer, but we use it to connect from person to person. At the end of the day, that's the single largest functional disconnect between the way the web works and the way it was designed. And though it is necessary and unavoidable, this network-oriented description of data's path between machines is insufficient for our technical needs. Communications are, by their nature, *agent-oriented*: they are said by someone, to someone; much of the fragmentation of the web today is an attempt to reconcile these two worlds.

The Muse presents a unified standard for communication directly between persons and services, regardless of their respective network locations. This paradigm, which already forms the basis for internet accounts, becomes codified and streamlined. In so doing, users are afforded agency, and sites need not waste precious development time reinventing the wheel. The muse is designed to make creation as easy as possible; it's the internet, but as you always wanted it: the sum total of human information, just a few clicks away.

Given the messy state of the internet today and the indications of massive future growth, this is an audacious but necessary change. It is, however, only possible to implement if agents have a definable identity. That identity must be shared, and the global needs for these identity and sharing mechanisms cannot possibly be predicted in advance; they must instead grow on their own terms through a singular interface. By shifting these systems from websites to the network that supports them, the privacy burden is likewise relocated; there must then be an independent, self-contained way for users to ensure their own security. For simplicity, and to bridge the gap between network locations and human conceptual search, this system should be as easy as possible for computers to navigate.

This system is the Muse.