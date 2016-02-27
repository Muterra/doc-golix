# Current revision: 26 Feb 2016

+ Reorganized spec documentation to list cipher suites and address algos first
+ Added dedicated identity container (MIDC)
+ Added ancillary format for secret sharing

# Previous revisions

## 20 Feb 2016, #e27e7bf

+ Major readme revision for better expressibility
+ Removed shameless plugs
+ Switched numeric identification of ciphersuites 1 and 2 in preparation for possible removal of SIV mode
+ Changed lengths of any MUID lists into absolute lengths, to ease support of potential future address algorithms of different lengths. Updated version numbers accordingly.
+ Moved all of the asymmetric operations into a single primitive (MEAR) with differing payloads. Added another payload definition for arbitrary content, to support future protocol development.
+ Clarified available size of asymmetric payload and appropriateness of use
+ Changed all secondary file extensions to .muid and tertiary to object identifiers (ex .meoc)
+ Added support for different static/dynamic address algorithms in dynamic binding

## 18 Jan 2016, #4fcba8f

+ Clarified spec re: salt length in signature (should match hash length = 64 bytes)
+ Added some shameless plugs

## 11 Dec 2015, #9a6e2ff

+ Changed all DH from 2048-bit/Group #14 to ECDH/Curve25519
+ Changed external links to reflect muterra.io and ethyr.net reorganization

## 13 Nov 2015, #10d6a60

+ Removed deterministic nonce generation in spec
+ Changed cipher suite 0x1 to SIV mode with MAC prepended to ciphertext in spec
+ Added cipher suite 0x2 as CTR mode with nonce accompanying key distribution in spec
+ Added important questions to top of readme

## 10 Nov 2015, #83eb7e5

Initial draft release.