**Warning:** this repo is considered public pre-release information and is in varying levels of completeness. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

# Keep it simple, stupid

Muse is a decentralized encrypted social media protocol.

It is currently under development by [Muterra, Inc](https://www.muterra.io), co-evolving with the [Ethyr](https://www.ethyr.net) social network. More information about Muse, Muterra, and Ethyr can be found at our [blog](https://www.ethyr.net/blog). If you'd like to stay updated, consider joining our [mailing list](https://www.ethyr.net/mailing-signup.html).

## Okay, too simple

Muse is an open, encrypted, and decentralized protocol for anything social. The goal is to consolidate and standardize all of the shared infrastructure that any new social application requires (content management, sharing management, and identity management). By doing that, we can minimize the barriers to creating new applications, while maximizing personal agency and privacy.

Crucially, **all operations on Muse are cryptographically enforced**. The protocol eschews "privacy by promise" and opts instead to create a general-purpose, many-to-many encrypted network with asynchronous/offline retrieval and real-time capability. **It does not require everyone to operate their own server**, and nor does platform-based hosting compromise privacy or security for applications built on Muse.

Like the Rust programming language, Muse aspires to be a [zero-cost abstraction](http://blog.rust-lang.org/2015/05/11/traits.html): what you don't use, you don't pay for; what you do use, you couldn't hand-code any better. Unlike previous federated social protocols (Diaspora, Tent, Hubzilla, etc), Muse operates at a highly-restricted abstraction: it produces authenticated, confidential, verified byte messages from arbitrary, insecure bytestream sources, but places no restrictions or specifications of what those messages might be.

Quick technical overview:

1. End-to-end symmetric encryption for all data, eliminating site-specific privacy concerns.
2. A unified protocol interface for storing persistent objects on any network data host
3. Asynchronous communication for the entire network, constructed using that storage protocol as a buffer
4. Static and dynamic content-based addresses (muids) bound to individual data containers by any network participants
5. Object deletion via garbage collection of any unreferenced containers
6. Container key exchange through symmetric inter-agent API sessions initiated with asymmetric API handshakes
7. Network "identity" definition through self-hosted public key infrastructure

## How does Muse change things?

In short, Muse is an overlay network that encrypts everything *not just from device to device*, but from entity to entity. It readdresses communications directly between the participants' digital identities (essentially their public keys), instead of (for example) the IP addresses of their devices. The protocol implementation abstracts away data transport, so physical addresses are transparent to applications, making them free to focus on high-level concepts like "Paypal, tell Alice her account balance".

We use the internet like this:

    1. Bob sends Alice a message that they're out of toilet paper
    2. Alice tells Paypal to withdraw money from her bank account
    3. Paypal asks Alice's bank for money
    4. Alice's orders TP from Amazon
    
Alice, Bob, Paypal, Amazon, etc are all "agents" (they can independently manipulate data). The way we actually *use* the internet, then, is *agent-oriented*. However, networks only know how to talk like this: 

    1. 73.36.202.142 sends 88.41.145.167 "we're out of toilet paper"
    2. 88.41.145.167 sends (wwww.paypal.com = 66.211.169.66) "withdraw money from Alice's bank account"
    3. (wwww.paypal.com = 66.211.169.66) sends (www.wellsfargo.com = 159.45.2.145) "withdraw money from Alice's account"
    4. 88.41.145.167 sends (www.amazon.com = 72.21.206.6) "Alice orders TP"

In reality this *network-oriented* architecture is even more convoluted, because Alice and Bob both have network infrastructure barriers (firewalls, NATs, ISPs, dynamic IP addresses, etc) impeding or preventing them from directly talking to each other. In practice, the first step actually works like this:

    1. 73.36.202.142 sends (www.gmail.com = 173.194.33.149) "Alice: we're out of toilet paper"
    2. 88.41.145.167 asks (www.gmail.com = 173.194.33.149) for new messages
    3. (www.gmail.com = 173.194.33.149) responds with "From Bob: we're out of toilet paper"

The Muse protocol sits on top of the transport layer (that's the part responsible for actually delivering messages), and below the application layer (Alice's email client, Paypal, Amazon, etc). It translates Bob's application-level "send Alice a message" command into a format that the transport layer can deliver to Alice, regardless of what ```123.123.123.123``` location she's at (and therefore regardless of what computer she's on). It does this by universally defining:

1. How to keep data private
2. How to store and transfer private data
3. How Bob shares private data with Alice
4. What, exactly, Bob means by "Alice"

**Why should you care?**

This network vs agent disconnect is one of the most fundamental problems facing the internet today. It is in large part responsible for data data disempowerment: you're forced to use sites like ```(www.gmail.com = 173.194.33.149)``` to talk to someone for precisely this reason, even though Google can (and does!) read all of your messages. If you don't want to share with Google, you can't share with Alice, either -- and when those middleman websites aren't secured, anyone else can eavesdrop on those conversations as well. Muse protects against that at a protocol level, regardless of the application or site you're using.

Second, if you're a developer, this problem is a huge pain in the ass. The process of converting ```Alice``` into ```88.41.145.167``` is **tremendously** time-consuming and doesn't always work well. You get bogged down defining accounts, securely storing passwords, dealing with the little details about how ```(wwww.paypal.com = 66.211.169.66)``` talks to ```(www.wellsfargo.com = 159.45.2.145)```, etc. Muse, on the other hand, makes development as simple as ```Bob sends Alice a message```, and privacy as simple as ```since he didn't CC Google, Google can't see it.``` It's built to digitally complement the social expectations humans have evolved over millennia. If you'd like to know more, the project's [whitepaper](/whitepaper.md) is a good place to start.

# Problem flow

This outlines, from first principles, the protocol design decisions that lead to the Muse. It is exceptionally brief and does not justify the answers. However, it does explain the entire protocol architecture.

1. **Problem:** What is content?  
   **Solution:** Content is any arbitrary binary data. All content is encapsulated within containers that assure confidentiality, integrity, and authenticity.  

    1. **Problem:** How does an agent assure confidentiality?  
       **Solution:** Encrypt the container content.  

        1. **Problem:** How should content be encrypted?  
           **Solution:** It's of arbitrary length, so definitely symmetrically (as per usual!)  
        2. **Problem:** How does another agent access the encrypted file in a many-to-many network?  
           **Solution:** Use a *separate* key-sharing mechanism (see below).  

    2. **Problem:** How does an agent assure integrity?  
       **Solution:** They hash the encrypted container.
    3. **Problem:** How does an agent assure authenticity?  
       **Solution:** They asymmetrically sign the container hash.
    4. **Problem:** How is the content identified on the network?  
       **Solution:** All containers are deterministically and uniquely content-addressed. In other words, content is identified by a collision-resistant cryptographic hash.
    5. **Problem:** How can this data be made asynchronously-available?  
       **Solution:** Any Muse-implementing network requires a persistence system. These are transport-specific. A conformant physical network node stores data on agents' behalf(s). Nodes may also bridge between transport-specific Muse implementations to automatically sync network state between them. Uploading is implicit, and the persistence system must understand several commands defined within the Muse spec.  

        1. **Problem:** What commands must a persistence system accept?  
           **Solution:** Publish, get, subscribe, unsubscribe, ack, nak, list node subscriptions, list object binders.  
        2. **Problem:** How are these persistence systems standardized?  
           **Solution:** Each particular transport mechanism defines its own overlay standard for command format.  

    6. **Problem:** How does the persistence system know to retain data?  
       **Solution:** Agents bind addresses to objects, reminiscent of a "call-by-assignment" programming language. Bindings may be created by any agent, regardless of data authorship. This prevents problematic deletion. Objects are always static, but bindings may also be dynamic (which creates a secondary address).  

        1. **Problem:** How does the persistence system know when to remove data?  
           **Solution:** When all bindings have been removed through "debind" commands, the persistence system garbage collects the object.  
        2. **Problem:** How can an author-agent remove undesired content that has been bound by a different agent?  
           **Solution:** Binding records include the binder as public metadata. Persistence systems must include a command to list the agents who have bound to a particular piece of content. The author may then exert social/political/legal pressure on those binders for them to remove the binding.  

2. **Problem:** What is sharing?  
   **Solution:** An exchange of symmetric encryption keys.  

    1. **Problem:** How is this accomplished in a many-to-many network?  
       **Solution:** Separate the key exchange from the content itself. Content is uniquely and trivially addressable, and access is shared one-to-one between agents. Note that agents may be computational, so public information may be automatically shared across communities of any size.  
    2. **Problem:** How do you perform secure online key exchange?  
       **Solution:** Initially, through a special asymmetrically-encrypted handshake object. These are distributed like any other Muse content, but contain a public reference to their agent-target. Unlike standard objects, their author is named privately, within the container body.  
    3. **Problem:** Doesn't this hinge on the secrecy of the target's asymmetric private key? Can we get forward secrecy, etc?  
       **Solution:** Absolutely. The handshake object *could* be used directly for every key exchange, but that would be both insecure and inefficient. The preferred method is to use the handshake to bootstrap a dynamic bidirectional communication pipe between two agents, and then use that for key exchange. The API definition for that key exchange pipe is out-of-scope for Muse itself, but candidates will be defined within overlay standards. Because it is encapsulated within the Muse symmetric pipe, it can be any binary message format.  

3. **Problem:** What is an agent?  
   **Solution:** An agent produces, accesses, shares, or retains content.  

    1. **Problem:** An agent must be uniquely identifiable and network-available.  
       **Solution:** Put the agent's entire identity within a single, standard content container on the network. These containers are themselves encrypted, so if the identity is public, it must then be bootstrapped (this process is defined in an overlay standard). Use their identity container's content address as their unique identifier.  
    2. **Problem:** The agent requires an asymmetric public key for signing content.  
       **Solution:** Add that key to the container file.  
    3. **Problem:** The agent requires an asymmetric public key for receiving encrypted pipes.  
       **Solution:** Add that key to the container file. 
    4. **Problem:** How does an agent invisibly (to external parties) transition to a new identity?  
       **Solution:** Through a Diffie-Hellman-based identity exchange process.  

        1. **Problem:** Can this transition be made selectively deniable?  
           **Solution:** Yes, through clever sequencing of the exchange.  
        2. **Problem:** How does an agent perform this exchange online?  
           **Solution:** The DHE public key must be made available at the time of identity creation, and is therefore stored with the rest of the identity's public keys (aka: add it to the container file).  

    5. **Problem:** Agents are meant to exist independently of physical devices, so they need online private key storage.  
       **Solution:** Out-of-scope, but defined within an overlay protocol.  
    6. **Problem:** How does the agent independently discover new content addresses?  
       **Solution:** Out-of-scope.  

# Some pseudocode

**Creating an application-transparent multi-transport node:**

```python
import muse

class MultiPersist(muse.persist.ProviderBase):
    ''' Naively broadcasts all operations across multiple transport 
    systems. An actual implementation of this idea would (hopefully!) be
    smarter than this.
    '''
    def __init__(self, websockets_url, serial_port, onion_address):
        super().__init__()
        self._persisters = [
                                muse.persist.Websocket(websockets_url), 
                                muse.persist.Serial(serial_port), 
                                muse.persist.Tor(onion_address)
                            ]

    def _lazy_wrapper(self, method, *args, **kwargs):
        ''' Creates a quick general-purpose wrapper for functions.
        For any method, serially calls to the persistence providers in
        self, adding the response to a list.
        
        For the record, normally I would do some memoization here, but
        in the interests of balancing clarity...
        '''
        result = []
        for persister in self._persisters:
            result.append(getattr(self, method)(*args, **kwargs))
        return result
        
    def _notify(self):
        ''' Callback for updates pushed from other sources. Included 
        for clarity.
        '''
        return super()._notify()
        
    def ping(self):
        return self._lazy_wrapper('ping')
        
    def publish(self, obj):
        return self._lazy_wrapper('publish', obj)
        
    def get(self, muid):
        return self._lazy_wrapper('get', muid)
        
    def subscribe(self, muid):
        return self._lazy_wrapper('subscribe', muid, self._notify)
        
    def unsubscribe(self, muid):
        return self._lazy_wrapper('unsubscribe', muid)
        
    def list_subs(self):
        return self._lazy_wrapper('list_subs')
        
    def ack(self):
        return self._lazy_wrapper('ack')
        
    def nak(self):
        return self._lazy_wrapper('nak')
        
    def list_binders(self, muid):
        return self._lazy_wrapper('list_binders', muid)
```

**Using that to build a simple messaging system:**
```python
import muse
import queue

class Messenger():
    ''' Note that, courtesy of rapant asynchrony, this is too naive to 
    actually work. However, I think it gets the point across.
    '''
    def __init__(self, persister, my_identity, friend_muids):
        ''' Setting up communication pipes between this identity and all
        other friends is the hardest part, since it's an asynchronous 
        handshake process.
        '''
        self.persister = persister
        self.identity = my_identity
        self.friends = friend_muids
        self._pipes = {}
        self.inbox = queue()
        self.messages = queue()
        
        # Register callback for persister to add to my inbox
        persister.notifier(self.inbox)
        # Add a subscription to any handshake with my muid as a recipient
        persister.subscribe(my_identity.muid)
        
        for friend in friend_muids:
            # Open an API channel with your friend
            pipe_up = muse.share.pipe(my_identity, friend)
            persister.publish(pipe_up)
            # Wait for their response through persister callback
            pipe_down = False
            while not pipe_down:
                note = self.inbox.get()
                if note.author == friend:
                    pipe_down = note
            # Asynchronously wait for shares from friend
            persister.subscribe(pipe_down.muid, notify=self.receive)
            self._pipes[friend] = {'up': pipe_up, 'down': pipe_down}
            
    def send(self, message):
        # Add the message to my internal state record
        self.messages.put(message)
        # Turn the message into a muse encrypted object container
        meoc, key = muse.meoc(self.identity, message)
        persister.publish(meoc)
        # Share the message muid and key with each friend through their pipes
        for pipe_pair in self._pipes.values():
            # Structure the muid and key into a message according to an 
            # overlay protocol
            keyshare = muse.mosp.keyshare_make(meoc.muid, key)
            pipe_pair['up'].push(keyshare)
            
    def receive(self, message):
        ''' Asynchronously wait for incoming messages.
        '''
        # Use the overlay protocol to digest the message into muid and key.
        muid, key = muse.mosp.keyshare_unmake(message)
        # Retrieve that object from the persistence share
        meoc = self.persister.get(muid)
        # Decrypt and read the message
        message = muse.meoc.read(meoc, key)
        # Add it to internal state
        self.messages.put(message)
        
persister = MultiPersist(example.websocket.url, COM1, example.onion)
# Create an anonymous identity and push it to persistence
my_secrets, identity_meoc = muse.identity.create()
persister.publish(identity_meoc)
# Build friends lists
friends = [
            muid1thatlookslikeahash,
            muid2thatlookslikeahash,
            muid3thatlookslikeahash
          ]
client = Messenger(persister, my_secrets, friends)
```