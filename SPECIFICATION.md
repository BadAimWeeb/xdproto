# XDProto Public Specification

Version: draft-01 / Last modified: 22/06/2022

## 1. Overview

XDProto is a set of rules that allow the uses of servers behind NAT (no port forwarding required), and make scaling & load balancing 
much easier by using the power of P2P protocols (such as libp2p/IPFS).

This specification contains 2 protocols (Server-Server protocol and Client-Server protocol).

## 2. Copyright Notice

This copyright notice also applies to all implementation of this protocol.

```
Copyright 2022 BadAimWeeb

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

## 3. Terms and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", 
"NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [BCP 14](https://www.rfc-editor.org/info/bcp14) 
[[RFC2119]](https://datatracker.ietf.org/doc/html/rfc2119) [[RFC8174]](https://datatracker.ietf.org/doc/html/rfc8174) when, 
and only when, they appear in all capitals, as shown here.

Commonly used terms in this specification are described below.

- Server: A machine that accept connections and resolve requests from client.
- Client: A machine that request the server to resolve requests it sends.
- Implementation: A library that implement this specification for other program to easily use this specification 
without doing it from scratch.
- Application: A program that implement this specification or uses implementations of this specification.

## 4. Transportations

This specification currently uses 3 transportations: pure WebSocket, socket.io (HTTP(s)/WebSocket) and IPFS (mainly WebSocket/WebRTC - TCP/QUIC can be used if 
application is a native application). 

Implementation MUST support pure WebSocket and IPFS transportations. Application doesn't need to implement all required transportation, only ones that are needed.

### 4.1. socket.io

This transportation is the most reliable as client will directly connect to server through HTTP+WS, but harder to config as you will need to forward ports on server
and tell the client exactly where the server is.

Server SHOULD also support HTTP(s) as web application running in browsers don't allow mixed content (HTTP in HTTPS context).

Client MUST send packet in `XDC` channel, and receive packet in `XDS` channel. The packet is encoded as raw string (raw byte => char) in arg0.

### 4.2. Pure WebSocket

This transportation is used when socket.io library isn't exist yet (for example in C++). It works like socket.io but without all the fancy thing like HTTP polling, 
channel/event, broadcasting (we don't need that anyway).

Implementation MUST support this transport.

The packet is encoded and transmitted as raw binary (Uint8Array in JS).

### 4.3. IPFS/libp2p

This transportation is used when the server is behind firewall/NAT or you want your application to easily connect to the server without knowing where the server is.

Implementation MUST support this transport.

The automatic server discovery feature is implemented using PubSub as event name `xdproto_discovery`, and the packet is transmitted over libp2p with protocol `xdproto/1.0`.

## 5. Packet format

The unencrypted format should be only sent when full handshake is not done yet:
```
<Message header (XDPROTO): 58 44 50 52 4F 54 4F> <Packet type, 2 bytes> <MsgPack binary serialization of the object received/sent>
```

TBD
