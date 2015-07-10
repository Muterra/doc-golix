**Warning:** this repo is considered public pre-release information and is in varying levels of completeness. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

The Muse is a new internet abstraction layer that securely mediates between the network-oriented transport layer and the agent-oriented application layer, giving the latter an inherent definition of privacy, identity, and sharing. **What the hell does that mean?**

We use the internet like this:

    1. Bob sends Alice a message that they're out of toilet paper
    2. Alice tells Paypal to withdraw money from her bank account
    3. Paypal asks Alice's bank for money
    4. Alice's orders TP from Amazon
    
Let's call Alice, Bob, Paypal, Amazon, etc all "agents" (they can independently manipulate data). The way we actually *use* the internet, then, is *agent-oriented*. However, networks only know how to talk like this: 

    1. 73.36.202.142 sends 88.41.145.167 "we're out of toilet paper"
    2. 88.41.145.167 sends (wwww.paypal.com = 66.211.169.66) "withdraw money from Alice's bank account"
    3. (wwww.paypal.com = 66.211.169.66) sends (www.wellsfargo.com = 159.45.2.145) "withdraw money from Alice's account"
    4. 88.41.145.167 sends (www.amazon.com = 72.21.206.6) "Alice orders TP"

Which we'll call *network-oriented*. And in reality it's even more convoluted, because Alice and Bob both have a bunch of barriers (firewalls, NATs, ISPs, dynamic IP addresses, etc) preventing them from directly talking to each other. So then in practice, the first step actually works like this:

    1. 73.36.202.142 sends (www.gmail.com = 173.194.33.149) "Alice: we're out of toilet paper"
    2. 88.41.145.167 asks (www.gmail.com = 173.194.33.149) for new messages
    3. (www.gmail.com = 173.194.33.149) responds with "From Bob: we're out of toilet paper"

The Muse sits on top of the transport layer (that's the part responsible for actually delivering messages), and below the application layer (Alice's email client, Paypal, Amazon, etc). It translates Bob's application-level "send Alice a message" command into a format that the transport layer can deliver to Alice, regardless of what ```123.123.123.123``` location she's at (and therefore regardless of what computer she's on). It does this by universally defining:

1. How to keep data private
2. What, exactly, Bob means by "Alice"
3. How Bob shares something with Alice

Now, I can say that this is a big problem on the existing web, but why should you care? 

Well first, this network vs agent disconnect is one of the most fundamental reasons you have no control over your data: you're forced to use sites like ```(www.gmail.com = 173.194.33.149)``` to talk to someone, but then Google can (and does!) read all of your messages. If you don't want to share with Google, you can't share with Alice, either. Even worse, many of those middleman websites aren't secured, meaning anyone else can eavesdrop on those conversations. 

Second, if you're a developer, it's a huge pain in the ass: the process of converting ```Alice``` into ```88.41.145.167``` is **tremendously** time-consuming and doesn't always work well. You get bogged down defining accounts, securely storing passwords, dealing with the little details about how ```(wwww.paypal.com = 66.211.169.66)``` talks to ```(www.wellsfargo.com = 159.45.2.145)```, etc.

The Muse fixes that. It makes development as simple as ```Bob sends Alice a message```, and privacy as simple as ```Bob sends Alice a message, but since he didn't CC Google, Google can't see it.``` It's built to digitally complement the social expectations humans have evolved over millennia. If you'd like to know more, the project's [whitepaper](/whitepaper.md) is a good place to start.