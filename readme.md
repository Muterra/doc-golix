**Warning:** this repo is considered public pre-release information and is in varying levels of completeness. If you have questions or comments, please [contact us](mailto:badg@muterra.io).

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

# Very brief technical overview and some pseudocode application examples

1. End-to-end symmetric encryption for all data, eliminating site-specific privacy concerns.
2. A unified protocol interface for storing persistent objects on any network data host
3. Asynchronous communication for the entire network, constructed using that storage protocol as a buffer
4. Static and dynamic content-based addresses (muids) bound to individual data containers by any network participants
5. Object deletion via garbage collection of any unreferenced containers
6. Container key exchange through symmetric inter-agent API sessions initiated with asymmetric API handshakes
7. Network "identity" definition through self-hosted public key infrastructure

**"Server" with private personal content:**

```python
# Environment variables
ws_store = websockets_storage_gateway('https://www.muterra.io')
public_access_provider = key_distributor('https://www.muterra.io')

# Creating and subscribing to oneself to listen for API requests
self = muse_identity
ws_store.publish(self)
ws_store.subscribe(self.muid)

# Generating and publishing the static homepage
static_homepage = encrypted_container(raw_content)
ws_store.publish(static_homepage)
static_homepage.share(public_access_provider)

# Creating Alice's personal content
dynamic_content = encrypted_container(raw_personal_content)
ws_store.publish(dynamic_content)

# Establishing an API channel with Alice and sending her personal content
alice = ws_store.get(alice_muid)
alice_channel = receive_API(alice)
dynamic_content.share(alice_channel)
```

**Alice's "client" accessing above site (asynchronous with above):**

```python
# Environment variables
ws_store = websockets_storage_gateway('https://www.muterra.io')
public_access_provider = key_distributor('https://www.muterra.io')

# Creating and subscribing to oneself to listen for API requests
self = muse_identity
ws_store.publish(self)
ws_store.subscribe(self.muid)

# Getting the static homepage
static_ciphertext = ws_store.get(static_muid)
key = public_access_provider.request(static_muid)
raw_content = decrypt_container(static_ciphertext, key)

# Opening an API channel with the server and receiving personal content
server_channel = request_API(server_muid)
dynamic_content_muid = server_channel.backlog[0]
dynamic_ciphertext = ws_store.get(dynamic_content_muid)
key = server_channel.request(dynamic_content_muid)
raw_dynamic_content = decrypt_container(dynamic_ciphertext, key)
```

**Private asynchronous sensor data transmission:**

```python
# Environment variables
ws_store = websockets_storage_gateway('https://www.muterra.io')
public_access_provider = key_distributor('https://www.muterra.io')

# Creating and subscribing to oneself to listen for API requests
self = muse_identity
ws_store.publish(self)
ws_store.subscribe(self.muid)

# Create the initial object and static binding; store the resulting muid
# Yes, this is an awkward initialization, life is better with actual code
raw_temp = b'0'
temp_container = encrypted_container(raw_temp)
temp_binding = dynamic_binding([temp_container.muid])
ws_store.publish(temp_container, temp_binding)

# Getting a reading
while True:
    temperature = hardware.get_temp()
    temp_container = encrypted_container(temperature)
    temp_binding.update(temp_container.muid)
    ws_store.publish(temp_container, temp_binding)
```

**TOR-style nested routing using nested Muse API pipes:**

```python
def onion_route(traffic):
    with websockets_storage_gateway('https://www.muterra.io') as ws:
        nested_message = muse_storage(message)
        ws.publish(nested_message)
```

**LAN fallback when connection interrupted:**

```python
ws_store = websockets_storage_gateway('https://www.muterra.io')
lan_store = lan_storage_gateway('192.168.0.14')
try:
    ws_store.publish(message)
except NetworkError:
    lan_store.publish(message)