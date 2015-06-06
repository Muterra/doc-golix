ZBG: Why not Zoidberg?
==============

Standalone files should use ASCII ```zbg0``` as magic numbers to start the file, then immediately begin with objects. Use of zbg encoding within other files need not be prepended, but may be if the file encapsulation requires it.

New manifest encoding: inspired by [BEncode](https://wiki.theory.org/BitTorrentSpecification#Bencoding) and ASN.1 / X.690, but tailored for binary content and hash addressing. All big-endian, like everything else EIC, and byte-oriented. All integers used in length fields should be unsigned.

Types supported:

+ 256- and 512-bit hashes
+ Lists
+ Dictionaries
+ Arbitrary octets

Nesting is fully-supported. List order is meaningful and preserved. Dictionaries must always be sorted ascendingly by the big-endian integer representation of their keys (for example, [b'a', b'b', b'aa'], as b'aa' is a larger integer than b'b'). Null objects are not supported, but empty objects are.

256-bit hashes (or arbitrary octets)
-----

+ Type flag: ASCII ```h```
+ No length declaration (32 bytes by definition)
+ value is raw octets (no cross encoding)
+ No terminator

512-bit hashes (or arbitrary octets)
-----

+ Type flag: ASCII ```H```
+ No length declaration (64 bytes by definition)
+ value is raw octets (no cross encoding)
+ No terminator

Lists
-----

+ Type flag: ASCII ```l```
+ No length declaration
+ Values are not delimited
+ Terminated with ASCII ```e```

Dictionaries
--------

+ Type flag: ASCII ```d```
+ No length declaration
+ Keys (must be hashes or octets) followed by value
+ Terminated with ASCII ```e```

Arbitrary octets
---------

+ Type flag: ASCII ```:```
+ Length declaration: First octet encodes the number of subsequent length octets. 11111111 shall not be used. Following octets describe value length in octets.
+ value in raw octets (no cross encoding)
+ No terminator

**Note:** since it is very difficult to distinguish between arbitrary bytes and "hash" objects, default implementation behavior should be to treat *all* 256- and 512-bit objects as hashes.

Examples
-------

Arbitrary octets: ASCII 'hello world' (length of 11)

```3A 01 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64 ```