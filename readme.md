A next-generation internet built around secure, private, scalable interactions between people and things
=========

[From Muterra](http://www.muterra.io)

*(If this is confusing, there's a much better non-technical explanation [below](#wait-what).)*

Today's web is public and centralized by default. DNS, HTTP, SMTP, and the rest of the internet's application layer negotiate conversations between *machines*, lacking any inherent awareness of the "agent" responsible for receiving and acting upon the transferred information. This network-oriented architecture, in contrast to an agent-oriented architecture, is one of the major limitations of the contemporary internet. 

Network-oriented architecture is characterized by static machine addressing, regardless of agent identity: IP addresses talking to each other on behalf of unknown parties. From the agent's perspective, this fails to decouple agent interactions from network operations, hindering modularization and distribution of both network and services. Because of changing network configurations, it decreases the reliability of "persistent" data, and relies heavily upon third parties for information semantics. And because it is blind to the intended recipient of network traffic, network-oriented architecture makes robust end-to-end security exceptionally difficult, posing a significant burden to meaningful digital privacy.

Agent-oriented architecture is characterized by static agent identity, regardless of machine addressing: agents conversing directly, regardless of their client machines. It requires a new abstraction layer to mediate between the network-oriented transport layer and the redefined, agent-oriented application layer. This new "service layer" is, both by design and necessity, "private by default": encrypted at rest and unshared upon creation. The service layer provides persistence to the network as a whole, and information is inherently semantic. This new internet protocol stack negotiates network-location-based data streams into action-based endpoints more effectively than the existing world wide web, making it far more suitable for the ever-growing world of internet-connected physical devices. Because its primary goal is to transform the loosely-connected tangle of internet services into a single amalgamated source of information, we've termed this new configuration the mesh-made-muse.

[Jump to the technical explanation](#the-technical-explanation)

Wait... what?
============

For a layperson explanation, I'm going to use a metaphor with some huge simplifications. This isn't 100% technically accurate, but it gets the idea across effectively. 

The web today
--------------

First, let's look at the web today. Pretend every website has a single ~~server~~ post office, and every client has a ~~computer~~ mailbox. For the sake of explanation, pretend this is the entire internet:

+ Gmail's post office
+ Facebook's post office
+ Wikipedia's post office
+ Your mailbox
+ Your friend Alice's mailbox
+ Your enemy Bob's mailbox

Let's say you want to send Alice a message in an unsecured environment. That looks like this:

1. You write Alice a ~~message~~ postcard.
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

These are the privacy and security problems that the Muse seeks to address. But before we talk about the Muse, let's pretend that in our above scenario, you want to create an account to edit Wikipedia, using your Facebook profile picture, and using gmail for authentication. Under this scheme, the process goes:

1. You start talking with the gmail, wikipedia, and facebook post offices, as discussed above.
2. You send gmail a secure postcard, asking them to send wikipedia ~~a valet key~~ the last 4 characters of your password.
3. You send Wikipedia a secure postcard, including only those last 4 characters with the message instead of the whole password. This creates an account with them. On this postcard, you write the ~~API endpoint~~ address of Facebook's post office and your ~~account identifier~~ name, telling Wikipedia to use that as a user picture.
4. Wikipedia's post office sends Facebook's post office a secure postcard, asking them for the picture. If it's public, Facebook sends it right over. If it's private, Facebook (hopefully) sends you a postcard asking for permission to share it. Assuming you grant that,
5. Wikipedia constructs your account.

First of all, that *seems* very complicated, because it *is* very complicated. That aside, now we have a whole lot more problems:

1. Everyone is at least a little confused! If I'm coding some software that uses all of these post offices, I have a lot to keep track of.
2. What if Wikipedia, Facebook, or Gmail change their post office address?
3. What if you change your gmail password?
4. What if any of the post offices refuse to cooperate with the other ones?
5. What if Facebook doesn't ask your permission to share your picture? What if it isn't Wikipedia asking for it, but one of Facebook's advertisers, and you never asked the advertiser to do anything?
6. Why, exactly, is *Facebook* responsible for making decisions about my profile picture on my behalf?

You can see there's a **lot** going on here. These are the systemic issues that the Muse helps alleviate.

The mesh, made a Muse
----------------

The Muse changes pretty much all of that. Again, as a warning, this is an oversimplification, but with the Muse, the internet looks like this:

+ A single, universal post office
+ Gmail's mailbox
+ Facebook's mailbox
+ Wikipedia's mailbox
+ Your mailbox
+ Your friend Alice's mailbox
+ Your enemy Bob's mailbox

Here, there *is no concept of insecure transfer*. Every single ~~message~~ postcard is inside those special security envelopes. Let's walk through sending Alice another message, this time on the Muse:

1. You and Alice agree on a notary (or multiple notaries). You can make them as easy or as hard to impersonate as you'd like. This time, the notary checks *your* ID, instead of just the post office's.
2. You and Alice exchange addresses
3. You write Alice a postcard, sealing it in your colored security envelope, signing the back of it, and using your agreed-upon notary to verify the signature
4. You put the postcard in your mailbox, ~~metp~~ the mailman delivers it to Alice's mailbox.

Not only is that a lot simpler, but now you only have to trust one thing:

1. The post office delivers your message

Nowhere does Bob even enter the picture. To illustrate the difference further, and how profound of an effect this has on developing services on top of the Muse, what about creating the wikipedia profile example from earlier?

1. You create your account at the post office and get it notarized. Think of this as a PO box.
2. You send the post office a notarized, secure message. Within it is your profile picture. You password protect the picture with a random number that is automatically stored in a special box at the post office that only you can open. *The post office does not have access to the picture, nor the password.*
3. You send Facebook a notarized, secure postcard, requesting they use the profile picture at your PO box number, and send them a copy of the picture's password.
4. You send Wikipedia the same request.
5. They check with the notary, verifying your details.

That's it. The universal post office never moves, so you don't have to worry about a changing address. None of the mailbox addresses matter to you, only to the post office. You don't even need gmail to create your account; the post office takes care of that for you. And Facebook *cannot* refuse to work with Wikipedia, because Facebook isn't responsible for your picture. Meanwhile, the post office doesn't have any say in what's going on -- it can't see your messages, it can't compromise your pictures, and all it does is deliver letters. Your data never leaves your control.

The technical explanation
=====================

The web today
-----------

The mesh, made a Muse
------------