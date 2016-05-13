# Introduction

+ Scalable encryption of data at rest
+ Any-to-any sharing of data
+ Zero-trust
+ Pub/sub-capable

# Threat model

Will be shared / available to a network, but transport analysis is ignored

## Adversaries included

+ Confidential until shared
+ Social graph analysis
+ Authentication, without web of (or centralized) trust
+ Data integrity
+ Malicious deletion / retention

## Adversaries excluded

+ Everything re: transport
+ Authorization (aka access control)
+ Verification (aka real-world identity correspondance)
+ Deniability (note: different from repudiation)
+ Device compromise
+ Denial of service

## Adversaries partially included

+ DDoS of endpoint, ex: spamming handshake requests
+ Metadata analysis

# Golix addresses and identities

## Addresses

+ Static
+ Dynamic

## Identities

+ Deniability != repudiation
+ Authentication != authorization
+ Authentication != verification

# Golix network primitives

Overview: 

1. to author content, first create an identity. 
2. Identities then publish encrypted containers. All object containers are publicly associated with the address of their author.
3. Like a memory-managed programming language, these containers are garbage collected by the persistence providers when no references to them remain. 
4. These references may be either static or dynamic bindings. There is no restriction on the creation of bindings (they need not be created by the content's author). They are publicly associated with the address of their binder (again, not necessarily the same as the content author).
5. Bindings may be removed through debinding records, but only by their binder.
6. Key exchanges are initiated through asymmetric request/response objects. Unlike other objects, these are publicly associated with their recipient.

## Identity container (GIDC)

## Object container (GEOC)

## Static object binding (GOBS)

## Dynamic object binding (GOBD)

## Debinding (GDXX)

## Asymmetric request/response (GARQ)

# Golix state analysis

## Non-privileged ("server") state analysis

## Privileged ("client") state analysis

# Crypto primitives

# Discussion and conclusion

