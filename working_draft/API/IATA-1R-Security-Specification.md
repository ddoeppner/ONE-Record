# ONE Record Security Specification

**Version 2.0.0-dev**

**Status: Draft; not yet approved by the COTB / CSC**

## Table of Contents

- needs to be generated

# Introduction

## Purpose

This section discusses the security options for the ONE Record API that governs the connectivity between ONE Record clients and servers on the Internet of Logistics. 

## Background

When exchanging data, each party needs to know with certainty the true identity of the other party and that they have the authority to receive or share the data. They also need to be certain that the data being shared is private, secured and confidential and cannot be intercepted or changed by any unauthorized third party. The ONE Record security framework works globally and for all stakeholders in the full logistics and transport supply chain, and in compliance with corporate and local data security requirements.

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image001.png" alt="img" style="zoom:150%;" />

IT and business experts from the industry  from ONE Record Task Force have explored and discussed the different connectivity configurations within the Internet of Logistics and two models – possibly complementary – were retained. This section presents these models and showcases the implications and benefits that they bring to the air cargo industry.

## Internet of Logistics Nodes

The connectivity between clients and servers on the Internet of Logistics (IoL) is governed by the ONE Record API and Security specifications. Since the IoL is proposed to include end-to-end transport and logistics chain connectivity, there are many configurations for interaction between stakeholders and their systems, both large and small, that may include all types such as shippers, forwarders, airlines, ground handlers, airports, customers as well as entities from other modes. The following non-exhaustive list of IoL Nodes types have been identified: 

\1. Single Node: A node that can receive and transmit data via the ONE Record API. It can act **both as a Client & Server** or as **a Client only**. Typically, a Single Node is operated by a single company.

\2. Multi-Company Node: A node that may be shared by **multiple companies** and is operated by a **“ONE Record as a Service”** service provider. This allows small and medium sized companies to exchange data without the need to implement their own node.

\3. Multi-User Node: A node that is shared by **many users**, possibly from different companies. Typically, this would be an app server that allows users to interact via an mobile app and retrieve or send data via ONE Record.

\4. Multi-Device Node: In addition to multi-company and multi-user nodes, there will be nodes that regroup **devices** such as **trackers** and that may connect via ONE Record.

Together these are referred to as **IoL Nodes**.

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image002.png" alt="img" style="zoom:40%;" />

IoL Nodes 

Below is an example of a small IoL network where the different types of IoL Nodes interact using the ONE Record API and Security specifications. 

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image003.png)

From an IoL perspective, these are all clients and services but, in the Multi-User, Multi-Company and Multi-Device cases, the actual IoL API is shared between multiple companies and/or users which impacts the identification, authentication and authorization models for ONE Record. 

## Definitions and Acronyms

Here are some definitions and acronyms used in the following sections:

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image004.png)

## Practical implementation of a first ONE Record Security working concept

ONE Record Security specifications are built around two concepts: **mutual TLS** (short for Transport Layer Security) and **OAuth2** (an authorization protocol). Mutual TLS secures all the Node-to-Node channels whereas OAuth2 adds an extra security layer for identification and authentication.

IATA has conceptualized a first practical implementation, which is promoted as the official security model for ONE Record. This implementation is split in two modules:

- **TLS authentication** **support**, including the definition of certificate profiles and practical use of digital certificates for client and server certificates;
- **Token-based authentication support**, including a practical implementation using OAuth2 as authorization protocol, based on the concept of a “Trusted Identity Provider”.

The next sections provide more detailed descriptions on the chosen security models and common use cases.

### TLS Authentication Model

The first ONE Record Security layer is based on Mutual TLS (with TLS1.2), where the Nodes identify themselves via digital certificates (X509) issued by Certificate Authorities (CA) that are recognized by the ONE Record community. Therefore, there is a prerequisite that the ONE Record Community operates and manages one or more CAs that meets the registration and issuance requirements of the Internet of Logistics. 

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image005.png)

TLS in ONE Record

#### Digital Certificates and PKI

- **Digital Certificates** enable to:
- **Identify the entities** (persons, applications or objects) connecting to an application;
- **Encrypt** the data, to ensure the **confidentiality**, either during the communication, or permanently;
- **Digitally sign** the data, to ensure both:
  - the **authenticity**, and
  - the **integrity** of the data.

#### TLS Overview

TLS defines a handshake procedure to authenticate the parties and encrypt the communication channel.

Key points to consider:

- TLS Authentication can be used with certificates for server side only, or for both client and server-side authentication;
- A certificate can allow an application to act as client or as a server;
- The same certificate can be used for other purposes, like digitally signing the messages;
- TLS is standardized and supports common server applications and development frameworks.

#### TLS in ONE Record

IATA defines two digital certificate profiles:

- **Client Certificate**. This certificate can authenticate a 1R-Client, and it includes one or more “ONE Record IDs” in the form of URI, that represent the endpoints that are authorized for the 1R-Client, to receive ONE Record responses. These ONE Record IDs are included in the certificate as SAN URI extensions. IATA recommends that these certificates are issued by a publicly trusted CA that is operating according to the PKI industry regulations and that is conforming to a WebTrust or equivalent independent audit criteria.

- **Server Certificate**. This is valid for server authentication and it is similar to common TLS certificates used to protect web sites. The main particularity and requirement are that, to cover the needs of Multi-Company Nodes, the Server Certificate must contain all the internet domains that can be extracted from the list of authorized 1R-IDs.

Please see the [section below](#_PKI_Compliance) for more details about the client and server certificates requirements.

#### Use-Case: TLS Client/Server Authentication

The 1R-Client needs to establish a secure connection to the 1R-Server, and both parties need to authenticate the other, so both will be required to present a valid digital certificate to establish the connection.

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image006.png" alt="img" style="zoom:45%;" />

It is important to note that ONE Record uses a Publish/Subscribe approach, therefore:

- The 1R-Client sends a request to the 1R-Server and includes a callback URL;
- The 1R-Server will process asynchronously the request and will send a notification to the 1R-Client using the callback URL.

This means that when the response from the 1R-Server is ready, it will be sent to the 1R-Client as a new TLS flow, initiated by the 1R-Server, but it is now acting as a TLS client as it pushes data back to the 1R-Client. This means that both entities will operate alternatively as both client and server.

Traffic flow:

- The 1R-Client Initiates the request using HTTPS, i.e to an SSL server.

- The 1R-Server presents its certificate, and requests a Client certificate.

- The TLS connection is established if the 1R-Server and 1R-Client are properly configured to require TLS authentication with Client certificate, and the CA issuing the certificate is included in the CA list of the 1R-Server.

- The 1R-Client Accepts the connection if:

  - the Server certificate comes from a Trusted CA and

  - it is not expired or revoked

- The 1R-Server Accepts the connection if:

  - the Client certificate comes from a Trusted Source and

  - it is not expired or revoked, and 

  - the Client certificate contains a 1R-ID that is allowed to make 1R requests.

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image007.png" alt="img" style="zoom:40%;" />



### OAuth2 Authorization model

#### Overview 

The mutual TLS model proposed for Node-to-Node data exchange is not enough to ensure identification for the Multi-Company/User/Device configurations. 

Mutual TLS can only identify the owner of the digital certificate. When that server is shared by multiple companies and/or multiple users, this is insufficient for guaranteed identification of such companies and/or users for a 3rd party IoL node to apply its authorization and access policies. 

Although Multi-Company/User/Device Nodes could obtain a digital certificate for each of its platform users (i.e. companies) and ensure their identity as such using the mutual TLS model, the ONE Record Task Force proposes an complementary identification and authentication approach. 

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image008.png" alt="img" style="zoom:55%;" />

It is proposed to use the services of one or more Identity & Authentication Provider/s (IAP) that can register companies, users and other entities such as devices based on policies agreed by the ONE Record community. These IAPs provide authentication and validation as requested.

This second security model intends to cover the use cases where a multi-company/user/device client application is connecting to a ONE Record service, and the server needs to take an authorization decision based on the identity of the end client making the request. This authorization will be based on the use of claims-based technology, in particular:

- The end client is authenticated by a trusted identity provider who has a digital certificate issued by a ONE Record CA;
- The client can attach to the request an authorization token issued by the trusted identity provider;
- The server application can extract from the token the identity of the end client and can also ensure that his identity is endorsed by a trusted identity provider.

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image009.png)

Using OAuth2 Tokens

#### Calling ONE Record API over TLS and OAuth2

- 1R API requires TLS mutual authentication;
- 1R API requires OAuth2 authentication using OpenID Connect Code Flow;
- 1R API validates TLS client authentication;
- 1R API validates ID token to check if the token is signed by a trusted IAP.

#### OAuth2 Auth - OpenID Connect Code Flow

1. Client prepares an authentication request containing the desired request parameters.
2. Client sends the request to the authorization server.
3. Authorization server authenticates the end-user.
4. Authorization server obtains end-user consent/authorization.
5. Authorization server sends the end-user back to the client with an authorization code.Client requests a response using the authorization code at the token endpoint.
6. Client receives a response that contains an ID token and access token in the response body.
7. Client validates the ID token and retrieves the end-user's subject identifier.

#### Token Validation Process

The security model enforces the concept of “trusted identity provider”, this implies that the server must ensure that the token is issued by a trusted party. This validation implies these steps:

- Parsing;
- Loading IAP’s public key;
- Validate signature;
- TRUST IAP validation through by doing validate if the public key linked with a trusted certificate.

## Federated trust centres

### PKI Compliance

Mutual TLS requires the use of digital certificates that were issued by trusted Certificate Authorities (CAs). It is proposed that trusted organizations like IATA and/or other industry bodies provide such CA services to their IoL stakeholders. 

It is essential that all these IoL CAs operate on the same basis, i.e. that they implement the same registration and operational policies. It is therefore proposed that these CA’s federate under a common agreement that they jointly develop, sign and maintain. 

#### Certificate profile 1 – Client 

It is recommended to use client certificates with only the clientAuthentication EKU, in order to simplify the issuance process and reduce audit complexity. Example of ONE Record Client Certificate:

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image010.png" alt="img" style="zoom:33%;" />

#### Certificate profile 2 – Server 

It is recommended to use TLS server certificates of type “Domain Validated”, that don’t contain company name or other identity attributes, in order to simplify the issuance process and reduce audit complexity. Example of ONE Record Server Certificate:

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image011.png" alt="img" style="zoom: 33%;" />



#### Requirements for Certificate Authorities (CAs)

- Issue and authenticate valid ONE Record certificates
- Must be internationally accredited to issue public certificates
- Meets ONE Record requirements for registration and service levels
- Is federated with other certificate authorities and identity & authentication services

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image012.png)

CA Requirements

### Trusted IAP

Similarly, the OAuth2 security model requires that Identity and Authentication Providers provide a service to their stakeholders that must also be operated on the same basis, i.e. using the same registration and operational policies.

It is therefore proposed that both CAs and IAPs operate under the same ONE Record and IoL data security agreement that allows CAs and IAPs to offer a federated trust environment to the transport and logistics industry.

Although ONE Record and IoL are open initiatives and aim to create a global network for data sharing and connectivity, these CA’s and IAPs’ will incur cost that they may pass on to the registrants. This should be considered the cost of security. It is very well possible that as the IoL grows, that the CA’s and IAP’s find business models that would allow them to provide these security services for free to their stakeholders.

#### Trusted IAP validation

 

![img](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image013.png) 

## Resources available for interested parties

With the purposes of implementing this security concept in pilot or real projects, IATA has made available to all interested parties a platform that provides the necessary services for:

- Issuing Client and Server Digital Certificates, for TLS Authentication. This will facilitate to obtain certificates conforming to the profiles defined by IATA and issued out of a publicly trusted certification authority;
- Obtaining digital identities compatible with OpenID Connect and OAuth2, for token-based authorization purposes.

Additionally, IATA can deliver guidelines and sample code that illustrate the processes for client and server authentication, using TLS and/or OAuth2 methods. 