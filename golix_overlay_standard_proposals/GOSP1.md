# WARNING: THIS DOCUMENT IS BOTH DEPRECATED AND UNDER REWRITE

# GOSP 1: Digital identity container

**Problem abstract:** The Muse requires a single object for digital identities, but does not define its structure. This document is a proposed format for the identity file.

**Goals:**

1. As per Muse spec, must include public signature key, public encryption key, and public Diffie-Hellman key.
2. Should be self-describing.
3. Should be self-hosted and should therefore bootstrap itself.

**Solution TL;DR:**

Formatted as a zbg-encoded dictionary with the following structure:

1. ASCII '```__class__```': <API class ghid>
1. ASCII '```version```': <version identifier>
2. <ciphersuite identifier>:
    1. ASCII '```sign```': <signature key>
    2. ASCII '```encrypt```': <encryption key>
    3. ASCII '```exchange```': <Diffie-Hellman key>

and raw binary encoding of the keys themselves:

1. n-bit 

The container author ghid should be 66 bytes of ```00```