# Current revision: 29 Feb 2016

+ Clarified use of dynamic binding historical frames
+ Updated debindings to be stateless, incremented version accordingly.
+ Explained debinding chains

# Previous revisions

## 28 Feb 2016, #8b925e8

+ Rebranded Muse -> Golix. No version changes as a result, though. Corrected some minor glaring errors in the process, and noted some things in the spec as deprecated (but did not redocument them yet).
+ Added text explicitly forbidding dynamic binding to anything other than a static GEOC
+ Fixed overlooked update in GDXX record re: chain count vs chain length. Updated version accordingly.
+ Changed pipe request identifier to ASCII "RQ"

## 27 Feb 2016, #6aff066

+ Updated dynamic binding to remove support for multiple identical start points, and removed old wording surrounding random seed for that purpose. Upped version number accordingly.

## 26 Feb 2016, #4bf3e65

+ Reorganized spec documentation to list cipher suites and address algos first
+ Added dedicated identity container (GIDC)
+ Added ancillary format for secret sharing

## 20 Feb 2016, #e27e7bf

+ Major readme revision for better expressibility
+ Removed shameless plugs
+ Switched numeric identification of ciphersuites 1 and 2 in preparation for possible removal of SIV mode
+ Changed lengths of any GUID lists into absolute lengths, to ease support of potential future address algorithms of different lengths. Updated version numbers accordingly.
+ Moved all of the asymmetric operations into a single primitive (GARQ) with differing payloads. Added another payload definition for arbitrary content, to support future protocol development.
+ Clarified available size of asymmetric payload and appropriateness of use
+ Changed all secondary file extensions to .guid and tertiary to object identifiers (ex .geoc)
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