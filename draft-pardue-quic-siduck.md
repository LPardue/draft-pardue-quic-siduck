---
title: Simple Datagram Usability and Connectivity Kata
abbrev: SiDUCK
docname: draft-pardue-quic-siduck-latest
category: experimental

ipr: trust200902
area: Transport
workgroup: QUIC
keyword: Internet-Draft

stand_alone: yes
pi: [toc, docindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
  -
    ins: L. Pardue
    name: Lucas Pardue
    email: lucaspardue.24.7@gmail.com

normative:

informative:

--- abstract

This document describes a simple application protocol for testing
implementations of the QUIC DATAGRAM frame names. SiDUCK (Simple Datagram
Usability and Connectivity Kata) defines a new ALPN ID, "siduck-00", along with
a basic offer and acknowledgement interaction using datagram payload data.

--- middle

# Introduction

The DATAGRAM frame {{!I-D.pauly-quic-datagram}} for QUIC
{{!I-D.ietf-quic-transport}} is an extension for the unreliable delivery of
datagrams. Applications using DATAGRAM negotiate it using Transport Parameters
but the application-specific contents are left for a higher-level mechanism.

HTTP/3 DATAGRAM {{?I-D.schinazi-quic-h3-datagram}} defines the usage of QUIC
DATAGRAM frames when the application protocol running over QUIC is HTTP/3
{{?I-D.ietf-quic-http}}, which is negotiated using ALPN {{RFC7301}} with an
identifier such as "h3-24". The HTTP/3 DATAGRAM frame is transmitted as the QUIC
DATAGRAM frame Datagram Data field. It contains a flow identifier field that can
be used for demultiplexing applications, and an HTTP/3 Datagram Payload field
whose semantics are defined by individual applications.

Since the QUIC DATAGRAM and HTTP/3 DATAGRAM specifications leave the semantic
higher-order behaviour undefined, it is difficult to prove the interoperability
of implementations. This document defines SiDUCK (Simple Datagram Usability and
Connectivity Kata), a new application that maps directly to QUIC DATAGRAM and
uses the Datagram Data field directly to support a simple client-server echo
service.

Using QUIC DATAGRAM directly provides the benefit of avoiding complexity of
HTTP/3 session establishment and flow identifier allocation and demultiplxing.
SiDUCK's simpler application semantics allow implementers to exercise the
interoperability of foundational layers of DATAGRAM, building progressive
assurance that benefits the design and robustness of other DATAGRAM-using
applications.

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in {{!RFC2119}}.

The terms sh-token and sh-boolean are imported from
{{!STRUCTURED-HEADERS=I-D.ietf-httpbis-header-structure}}.

Example HTTP requests and responses use the HTTP/2-style formatting from
{{?RFC7540}}.

This document uses the variable-length integer encoding from
{{!I-D.ietf-quic-transport}}.


# Negotiating SiDUCK

SiDUCK is an application protocol that runs atop QUIC. The token "siduck" is
used to identify SiDUCK in ALPN, used during QUIC connection establishment. Only implementations of the final, published RFC can identify themselves as "siduck". Until such an RFC exists, implementations MUST NOT identify themselves using this string.

> **RFC Editor's Note:**  Please remove the following prior to publication of a
> final version of this document.

Implementations of draft versions of the protocol MUST add the string “-“ and
the corresponding draft number to the identifier. For example,
draft-pardue-quic-siduck-00 is identified using the string “siduck-00”.

Non-compatible experiments that are based on these draft versions MUST append
the string “-“ and an experiment name to the identifier. For example, an
experimental implementation based on draft-pardue-quic-siduck-09 which extends
the permitted message types to include "honk" might identify itself as “siduck-09-goose”. Note
that any label MUST conform to the “token” syntax defined in Section 3.2.6 of
{{!RFC7230}}.

# Protocol Behaviour

A SiDUCK session consists of a client and server. Once a SiDUCK session is
established, the client SHOULD send a QUIC DATAGRAM frame with the Datagram Data
field containing "quack". A server that receives a quack, MUST respond with a
QUIC DATAGRAM frame with the Datagram Data field containing "quack-ack".

A client MUST NOT send any other message than "quack". A server that receives a non-quack message MUST close the connection with an error of type SIDUCK_ONLY_QUACKS_ECHO.

# The SIDUCK_ONLY_QUACKS_ECHO error code

This document defines the SIDUCK_ONLY_QUACKS_ECHO QUIC Application error code. It has the value 0x101.

# Known Issues / Problems

* The design does not permit large DATAGRAM frames. Sending these in a simple
  application protocol would be beneficial and within the scope of SiDUCK's
  goals.
* The design does not specify requirements on Transport Parameters. For example,
  since streams are not used, the spec could require that endpoints send
  max_streams_{bidi|uni} with the value 0.


# Security Considerations

There are not beleived to be any further security considerations beyond those
presented in {{!I-D.ietf-quic-transport}}.

# IANA Considerations

TBD

--- back

# Acknowledgements

Thanks to Andy Stitzer who achieved interop of an unpublished early SiDUCK
specification during the IETF 106 Hackathon.
