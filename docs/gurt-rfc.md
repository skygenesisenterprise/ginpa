---
title: "GURT: General Universal Request Transport Protocol"
abbrev: "GURT"
docname: draft-ietf-gurt-protocol-00
category: std
consensus: true
submissiontype: IETF
ipr: trust200902
area: Transport
workgroup: Transport Area Working Group (tsvwg)
keyword: Internet-Draft
stand_alone: yes
pi: [toc, sortrefs, symrefs]
author:
  -
    ins: "Sky Genesis Enterprise"
    name: "Sky Genesis Enterprise"
    organization: "Sky Genesis Enterprise"
    email: contact@skygenesisenterprise.com

normative:
  RFC2119:
  RFC5246:
  RFC8446:
  RFC7230:
  RFC7231:
  RFC7540:
  RFC7541:

informative:
  RFC793:
  RFC2616:
  RFC7235:
  RFC7540:

--- abstract

This document specifies the GURT (General Universal Request Transport) protocol, a secure, efficient, and extensible transport protocol for client-server communication over the Internet. GURT builds upon the principles of HTTP/2 and QUIC, providing mandatory TLS encryption, request multiplexing, and support for extensions while maintaining simplicity and performance.

--- middle

# Introduction

The GURT protocol is designed to provide a modern, secure, and efficient alternative to traditional HTTP-based protocols. It addresses limitations in existing protocols by mandating end-to-end encryption, supporting request multiplexing, and providing a framework for extensions.

## Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 {{RFC2119}}.

## Protocol Overview

GURT operates over TCP connections secured with TLS 1.3. The protocol consists of:

- A mandatory handshake phase for connection establishment
- Encrypted message exchange using a binary format
- Support for request/response multiplexing
- Extensible header and body formats
- Built-in security features

# Protocol Architecture

## Connection Establishment

All GURT connections MUST begin with a TLS handshake using TLS 1.3 {{RFC8446}}. The client initiates the connection with a ClientHello message containing the "gurt/1.0" application-layer protocol negotiation (ALPN) extension.

Upon successful TLS handshake, the client sends a GURT handshake message to establish the protocol session.

## Message Format

GURT messages consist of a header and an optional body, separated by a double CRLF sequence.

### Message Structure

```
GURT-METHOD path GURT/version\r\n
Header-Name: Header-Value\r\n
...\r\n
\r\n
[body]
```

### Methods

GURT defines the following standard methods:

- HANDSHAKE: Initiates protocol session
- GET: Retrieves a resource
- POST: Creates or updates a resource
- PUT: Replaces a resource
- DELETE: Removes a resource
- HEAD: Retrieves resource metadata
- OPTIONS: Describes available methods
- PATCH: Partially modifies a resource

### Status Codes

GURT uses HTTP-compatible status codes with additional protocol-specific codes:

- 101: Switching Protocols (handshake response)
- 200-299: Success responses
- 300-399: Redirection responses
- 400-499: Client error responses
- 500-599: Server error responses

# Security Considerations

## Mandatory Encryption

All GURT connections MUST use TLS 1.3 with perfect forward secrecy. TLS 1.2 MAY be supported for backward compatibility but is NOT RECOMMENDED.

## Certificate Validation

Clients and servers MUST validate TLS certificates according to RFC 5280. Self-signed certificates are NOT allowed in production environments.

## Authentication

GURT supports HTTP Basic and Bearer token authentication as defined in RFC 7235 {{RFC7235}}. Implementations SHOULD support mutual TLS authentication.

## Attack Mitigation

### Denial of Service Protection

Servers MUST implement rate limiting and connection limits. Maximum message size is limited to 10 MB by default.

### Injection Attacks

All user inputs MUST be validated and sanitized. Headers and bodies MUST be properly encoded.

### Man-in-the-Middle Attacks

The mandatory TLS encryption prevents MITM attacks. Certificate pinning SHOULD be implemented for additional security.

# Performance Considerations

## Connection Pooling

Clients SHOULD implement connection pooling to reduce connection establishment overhead. Maximum connections per host SHOULD be configurable (default: 4).

## Compression

GURT supports gzip compression for message bodies. Clients and servers SHOULD negotiate compression during handshake.

## Multiplexing

Multiple requests MAY be multiplexed over a single connection. Servers MUST process requests in the order received unless priority headers are specified.

## Caching

Implementations SHOULD support HTTP-style caching headers (Cache-Control, ETag, Last-Modified).

# Error Handling

## Error Response Format

Error responses MUST include a status code and descriptive message:

```
GURT/1.0 500 Internal Server Error\r\n
Content-Type: application/json\r\n
\r\n
{"error": "Internal server error", "code": "INTERNAL_ERROR"}
```

## Connection Errors

Upon connection errors, clients SHOULD implement exponential backoff retry logic. Maximum retry attempts SHOULD be configurable.

## Timeout Handling

Default timeouts:
- Connection timeout: 10 seconds
- Request timeout: 30 seconds
- Handshake timeout: 5 seconds

# Extensions

## WebSocket-like Communication

GURT supports bidirectional communication through the UPGRADE method:

```
UPGRADE /websocket GURT/1.0\r\n
Upgrade: websocket\r\n
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==\r\n
\r\n
```

## Push Notifications

Servers MAY push messages to clients using the PUSH method:

```
PUSH /notifications GURT/1.0\r\n
Content-Type: application/json\r\n
\r\n
{"type": "notification", "message": "New update available"}
```

## Request Prioritization

Clients MAY specify request priority using the Priority header:

```
Priority: urgent\r\n
```

# Testing and Conformance

## Unit Tests

Implementations MUST include comprehensive unit tests covering:
- Message parsing and serialization
- TLS handshake validation
- Error condition handling
- Extension support

## Integration Tests

End-to-end tests MUST verify:
- Client-server communication
- TLS encryption
- Multiplexing behavior
- Error recovery

## Performance Benchmarks

Implementations SHOULD provide benchmarks for:
- Connection establishment time
- Request/response throughput
- Memory usage under load
- Concurrent connection handling

## Fuzz Testing

Protocol implementations MUST undergo fuzz testing to identify potential security vulnerabilities and parsing errors.

# IANA Considerations

## Port Registration

The default GURT port (4878) has been registered with IANA.

## ALPN Protocol ID

The "gurt/1.0" ALPN identifier has been registered.

## Method Registry

New GURT methods require IANA registration in the "GURT Methods" registry.

# Implementation Notes

## Client Libraries

Reference implementations are available in Rust, providing both client and server libraries with full TLS support.

## Server Implementations

The reference server supports all standard features including security middleware, rate limiting, and extension handling.

## Compatibility

GURT is designed to be compatible with existing HTTP infrastructure where possible, facilitating gradual adoption.

--- back

# Acknowledgments

Thanks to the IETF community for their feedback and contributions to this specification.

# Author's Address

Sky Genesis Enterprise
Email: contact@skygenesisenterprise.com