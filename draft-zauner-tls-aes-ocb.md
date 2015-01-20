---
title: AES-OCB (Offset Codebook Mode) Ciphersuites for Transport Layer Security (TLS)
abbrev: AES-OCB Ciphersuites
docname: draft-zauner-tls-aes-ocb-latest
date: 2015-01-20
category: std

ipr: #XXX/TODO: IPRs 560, 1591, 1682, 1683
area: General
workgroup: TLS Working Group
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: A. Zauner
    name: Aaron Zauner
    organization: Independent
    email: azet@azet.org

normative:
  RFC5246:
  RFC6066:
  RFC5116:
  RFC4279:
  RFC2119:
  RFC5288:
  RFC6655:
  RFC6066:
  RFC4279:
  RFC6347:

informative:
  RFC7253:
  AES:
    title: Specification for the Advanced Encryption Standard (AES)
    date: 2001-11-26
    author:
      org: National Institute of Standards and Technology
    seriesinfo:
      NIST: FIPS 197
  OCB:
    title: "OCB: A Block-Cipher Mode of Operation for Efficient Authenticated Encryption"
    author:
      - ins: P. Rogaway
      - ins: M. Bellare
      - ins: J. Black
    seriesinfo:
      CCS01: ACM Conference on Computer and Communications Security (CCS ’01), ACM Press, pp. 196-205
    date: 2001


--- abstract
This memo describes the use of the Advanced Encryption Standard (AES)
in the Offset Codebook Mode (OCB) of operation within Transport Layer Security (TLS)
and Datagram TLS (DTLS) to provide confidentiality and data origin authentication.
The AES-OCB algorithm is highly parallelizable, provable secure and can be
efficiently implemented in software and hardware providing high performance.

--- middle
# Introduction
This document describes the use of the Advanced Encryption Standard (AES)
in the Offset Codebook Mode (OCB) of operation within Transport Layer Security (TLS)
and Datagram TLS (DTLS) to provide confidentiality and data origin authentication.
The AES-OCB algorithm is highly parallelizable, provable secure and can be
efficiently implemented in software and hardware providing high performance.

Authenticated encryption, in addition to providing confidentiality for the plaintext
that is encrypted, provides a way to check its integrity and authenticity. Authenticated
Encryption with Associated Data, or AEAD {{RFC5116}}, adds the ability to check the
integrity and authenticity of some associated data that is not encrypted. This document
utilizes the AEAD facility within TLS 1.2 {{RFC5246}} and the AES-OCB-based AEAD
algorithms defined in {{RFC5116}}. Additional AEAD algorithms are defined, which use
AES-OCB but which have shorter authentication tags, and therefore are more suitable
for use across networks in which bandwidth is constrained and message sizes may be small.

The ciphersuites defined in this document use ECDHE, DHE or Pre-Shared-Key (PSK) as their
key establishment mechanism; these ciphersuites can be used with DTLS {{RFC6347}}. Since
the abiltiy to use AEAD ciphers was introduced in DTLS version 1.2, the ciphersuites defined
in this document cannot be used with earlier versions of that protocol.

# Conventions Used in This Document
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in {{RFC2119}}.

# Forward-secret AES-OCB Ciphersuites {#fssuites}
The ciphersuites defined in this document are based on the AES-OCB
authenticated encryption with associated data (AEAD) algorithms
AEAD_AES_128_OCB_TAGLEN96 and AEAD_AES_256_OCB_TAGLEN96 described
in {{RFC7253}}. The following forward-secret ciphersuites are defined:

      CipherSuite TLS_DHE_RSA_WITH_AES_128_OCB = {TBD1, TBD1}
      CipherSuite TLS_DHE_RSA_WITH_AES_256_OCB = {TBD2, TBD2}
      CipherSuite TLS_ECDHE_RSA_WITH_AES_128_OCB = {TBD3, TBD3}
      CipherSuite TLS_ECDHE_RSA_WITH_AES_256_OCB = {TBD4, TBD4}
      CipherSuite TLS_ECDHE_ECDSA_WITH_AES_128_OCB = {TBD5, TBD5}
      CipherSuite TLS_ECDHE_ECDSA_WITH_AES_256_OCB = {TBD6, TBD6}

These ciphersuites make use of the AEAD capability in TLS 1.2 {{RFC5246}}.

Use of HMAC truncation in TLS (as specified in {{RFC6066}}) has no effect
on the ciphersuites defined in this document.

The "nonce" input to the AEAD algorithm is exactly that of {{RFC5288}}:
the "nonce" SHALL be 12 bytes long and is constructed as follows:

      struct {
         case client:
            uint32 client_write_IV;  // low order 32-bits
         case server:
            uint32 server_write_IV;  // low order 32-bits
         uint64 seq_num;
      } OCBNonce.

In DTLS, the 64-bit seq_num is the 16-bit epoch concatenated with the
48-bit seq_num.

These ciphersuites make use of the default TLS 1.2 Pseudorandom
Function (PRF), which uses HMAC with the SHA-256 hash function.
The ECDSA-ECDHE, RSA-ECDHE and RSA-DHE key exchanges are performed 
as defined in {{RFC5246}}.

# Pre-Shared-Key (PSK) AES-OCB Ciphersuites {#psksuites}
As in {{fssuites}}, these ciphersuites follow {{RFC7253}}.
The PSK, ECDHE_PSK and DHE_PSK key exchanges are performed as specified
in {{RFC4279}}. The following Pre-Shared-Key (PSK) ciphersuites are defined:

      CipherSuite TLS_PSK_WITH_AES_128_OCB = {TBD7, TBD7}
      CipherSuite TLS_PSK_WITH_AES_256_OCB = {TBD8, TBD8}
      CipherSuite TLS_DHE_PSK_WITH_AES_128_OCB = {TBD9, TBD9}
      CipherSuite TLS_DHE_PSK_WITH_AES_256_OCB = {TBD10, TBD10}
      CipherSuite TLS_ECDHE_PSK_WITH_AES_128_OCB = {TBD11, TBD11}
      CipherSuite TLS_ECDHE_PSK_WITH_AES_256_OCB = {TBD12, TBD12}

The "nonce" input to the AEAD algorithm is identical to the one defined in
{{fssuites}}. These ciphersuites make use of the default TLS 1.2 Pseudorandom
Function (PRF), which uses HMAC with the SHA-256 hash function.

# Applicable TLS Versions
These ciphersuites make use of the authenticated encryption with additional data
(AEAD) defined in TLS 1.2 {{RFC5288}}. Earlier versions of TLS do not have support
for AEAD; for instance, the TLSCiphertext structure does not have the "aead" option
in TLS 1.1. Consequently, these ciphersuites MUST NOT be negotiated in older versions
of TLS. Clients MUST NOT offer these cipher suites if they do not offer TLS 1.2 or
later. Servers which select an earlier version of TLS MUST NOT select one of these
ciphersuites. Because TLS has no way for the client to indicate that it supports
TLS 1.2 but not earlier, a non-compliant server might potentially negotiate TLS 1.1
or earlier and select one of the cipher suites in this document. Clients MUST check
the TLS version and generate a fatal "illegal_parameter" alert if they detect an
incorrect version.

# IANA Considerations
IANA is requested to assign the values for the ciphersuites defined in {{fssuites}}
and {{psksuites}} from the TLS and DTLS Ciphersuite registries. IANA, please note
that the DTLS-OK column should be marked as "Y" for each of these algorithms.

# Security Considerations
The security considerations in {{RFC5246}} apply to this document as well. The
remainder of this section describes security considerations specific to the
ciphersuites described in this document.

## (Perfect) Forward Secrecy
With the exception of two Pre-Shared-Key (PSK) ciphersuites, defined in {{psksuites}},
this document deals exclusively with ciphersuites that are inherently forward-secret.

## RSA as key-exchange
No ciphersuite is defined in this document that makes use of RSA as key-exchange.

## Nonce reuse
AES-OCB security requires that the "nonce" (number used once) is never reused.
The IV construction in {{fssuites}} is designed to prevent nonce reuse.

# Acknowledgements
This document borrows heavily from {{RFC5288}} and {{RFC6655}}.
