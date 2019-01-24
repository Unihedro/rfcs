---
title: ILP Over HTTP
draft: 1
---

# ILP Over HTTP
> A bilateral communication protocol for server-to-server connections

## Motivation

Scaling Interledger infrastructure to handle large volumes of ILP packets requires horizontally scaling connectors. Using HTTP for bilateral communication enables service providers to leverage standard tools and services for hosting, load balancing, Distributed Denial of Service (DDoS) protection, and monitoring.

## Overview

In an ILP Over HTTP connection, both peers run HTTP servers with accessible HTTPS endpoints. When peering, the peers exchange their respective URLs, authentication tokens or TLS certificates, ILP addresses, and settlement-related details.

Each ILP Prepare packet is sent as the body of an HTTP request to the peer's server endpoint. The HTTP response body will contain either the ILP Fulfill or Reject packet.

## Specification

This is a minimal protocol built on HTTP. HTTP/2 is RECOMMENDED for performance reasons, although HTTP/1.1 MAY also be used. Implementations SHOULD support HTTP version negotiation via Application Protocol Negotiation (ALPN).

### Authentication

Peers MAY use any standard HTTP authentication mechanism to authenticate incoming requests. Bearer Tokens or TLS Client Certificates are RECOMMENDED.

### Request

```http
POST /ilp HTTP/2.0
Accept: application/octet-string
Content-Type: application/octet-string
Authorization: Bearer zxcljvoizuu09wqqpowipoalksdflksjdgxclvkjl0s909asdf
Host: connector.example
X-ILP-Destination: example.usd.connector.account

< Body: Binary OER-Encoded ILP Prepare Packet >
```

**Field Details:**

- **Path** - A connector MAY specify any HTTP path for their peer to send ILP packets to.
- **X-ILP-Destination Header** - (Optional) It is RECOMMENDED to set this header to the destination ILP address from the ILP packet. This enables standard HTTP load balancers to route packets to particular connector instances without parsing the ILP packet.
- **Body** - ILP Packet encoded using OER, as specified in [RFC 27: Interledger Protocol V4](./0027-interledger-protocol-4/0027-interledger-protocol-4.md).

### Response

```http
HTTP/2.0 200 OK
Content-Type: application/octet-string

< Body: Binary OER-Encoded ILP Fulfill or Reject Packet >
```

## Appendix A: Settlement

Peers MAY use HTTP to exchange settlement-related messages such as payment channel claims. It is RECOMMENDED to use different URL paths to differentiate settlement protocols.
