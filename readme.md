A next-generation internet built around secure, private, scalable interactions between people and things
=========

[From Muterra](http://www.muterra.io)

*(Scroll down if this is confusing. There's a much better non-technical explanation below.)*

Today's web is public and centralized by default. DNS, HTTP, SMTP, and the other various technologies of the internet's application layer negotiate conversations between machines with no inherent awareness of the final entity (agent), human or otherwise, responsible for receiving and acting upon the transferred information. This network-oriented architecture, in contrast to an agent-oriented architecture, is one of the major limitations of the contemporary internet. Network-oriented architecture is characterized by static machine addressing, regardless of agent identity. It makes robust end-to-end security exceptionally difficult, and therefore poses a significant burden to meaningful digital privacy. It decreases the reliability of persistent data and relies heavily upon third parties for information semantics. Network-oriented architecture fails to decouple agent interactions from network operations, hindering modularization and distribution of both the agent and the network. Agent-oriented architecture, on the other hand, is characterized by static agent identity, regardless of machine addressing. This is accomplished by introducing a new abstraction layer that mediates between the network-oriented transport layer and a redefined, agent-oriented application layer. This new "service layer" is designed to be private by default and requires encryption of all data at rest. Persistence is supported by the network as a whole and information is inherently semantic. This new internet protocol stack negotiates location-based internet data streams into action-based endpoints more effectively than the existing world wide web, making it far more suitable for the ever-growing world of internet-connected physical devices generally termed the Internet of Things. Because its primary goal is to transform the loosely-connected tangle of internet services into a single amalgamated source of information, we've termed this new configuration the mesh-made-muse.

Wait... what?
============

For a layperson explanation, I'm going to use a metaphor with some huge simplifications. This isn't 100% technically accurate, but it gets the idea across effectively. 

The web today
--------------

First, let's look at the web today. Pretend every website has a single ~~server~~ post office, and every client has a ~~computer~~ mailbox. For the sake of explanation, pretend this is the entire internet:

+ Gmail's post office
+ Facebook's post office
+ Wikipedia's post office
+ Your home mailbox
+ Your work mailbox
+ Your friend Alice's mailbox
+ Your enemy Bob's mailbox

Let's say you want to send Alice an email in an unsecured environment. That looks like this:

1. You write Alice a~~n email~~ postcard.
2. You put the postcard in your home mailbox.
3. ~~HTTP~~ The mailman picks up your postcard
4. The mailman takes your postcard and dumps it in a big box *outside* the gmail post office.
5. A worker from the gmail post office picks up the box. Your postcard gets sorted by destination, but HTTP is a really lousy mailcarrier and won't take it to Alice without a new stamp, so the post office does that for you. The post office worker drops your postcard in another outside-the-post-office box for outgoing mail.
6. Another mailman takes your postcard to Alice's mailbox.

This is insecure because of what Bob can do:

1. Bob can read your postcards.
2. Bob can create fake postcards, impersonating you.
3. Bob can open his own post office and pretend to be Gmail

and where he can do it:

1. From your mailbox
2. From the boxes outside the post office
3. From Alice's mailbox

It also requires you to trust gmail's post office to:

1. Actually deliver the postcard to Alice
2. Not read the postcard, despite needing the address from it
3. Not alter the postcard in transit
4. Not do any of the other bad things that Bob can do

Okay, so this is... suboptimal. Really suboptimal. So we decide to shore it up a bit by ~~encrypting~~ putting the postcard in a pink security envelope. And we want to make sure Bob doesn't just open up his own (fake) post office, so we ask a ~~certificate authority~~ notary to sign every piece of mail that goes to and from gmail. Okay, so now, Bob can't claim to be gmail, and he can't read your postcard, and because he only has blue security envelopes (remember you have pink), he can't even send fakes. **However**, once you're out of pink envelopes, Bob can still impersonate you with the blue ones. To prevent that, you make an account with the gmail post office, and the two of you agree on a secret password. From then on, every time you send gmail a postcard, you'll include the password inside of the envelope. *Now we don't have to worry about Bob!* Okay, so now the whole process is:

1. You ask the gmail post office for a notarized postcard with their address on it
2. You write Alice a postcard.
3. You put the postcard and a password in a special security envelope that Bob can't open or fake.
4. You put the postcard in your mailbox, and the mailman takes the postcard to the gmail post office using the address from above.
5. Gmail opens up your envelope, sees the password (and therefore knows it's you), reads the destination (Alice), puts it in a new security envelope addressed to Alice, notarizes that, and puts it in outgoing mail
6. The postcard is delivered to Alice

This is ~~http**s**~~ a much better mail service, and it's the current recommendation on the web. Do you see some issues here?

1. What if someone impersonates the notary? (This is what Lenovo did with Superfish)
2. The post office can still read your postcards.
3. The post office can still create fake postcards, impersonating you.
4. What if the post office sends your postcard (or a copy of it) to someone *other* than Alice?

These are the privacy and security problems that the Muse seeks to address.