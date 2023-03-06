# ONE Record API Specification

**Version:** 2.0.0-dev **Status:** Draft; not yet approved by the COTB / CSC

The version is incremented when API specifications are endorsed by the IATA Cargo Operations Technology Board (COTB).
The first version (v1.0) of the API specifaction was released in Aug 2020 (endorsed in Mar 2020).

## Table of Contents
> add ToC later

# Introduction

## Purpose

This ONE Record API specification is part of the IATA ONE Record standard. 
It defines a standard, programming language-agnotic interface for the interaction with the ONE Record Web API. 
This ONE Record API specification supports the effective implementation of ONE Record compliant APIs. 
It aims to provide detailed realistic use cases and examples for the various API features while maintaining the necessary technical depth for implementers.

## Prerequisites

It is assumed that the reader is familiar with the ONE Record data model, REST APIs (also known as RESTful APIs), and JSON-LD.

## Supporting Documents
- [Changelog](CHANGELOG.md) contains a list of all notable changes for each version of the ONE Record API specification.
- [ONE Record API Ontology](IATA-1R-API-Ontology.ttl) provides the vocabulary and data classes for the data model used in the ONE Record API.
- [ONE Record API Class Diagram](IATA-1R-API-Class-Diagram.md) describes the ONE Record API data classes, their properties as attributes, and the relationship that can exist between the classes.
- [OpenAPI specification](IATA-1R-API-OpenAPI.yaml) describes an example and recommended structure of an ONE Record API implementation
- [Postman Collection]() that contains demo requests that demonstrate how to interact with the various ONE Record API endpoints and can be used to playback the examples provided in this document. 

## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

## License

This document is licensed under MIT license (see [LICENSE](../../LICENSE) for details).

# Concepts

### Internet of Logistics

Creating the foundation for a global industry data architecture that allows all participants in the logistics and transport industry process to exchange data necessary for the planning and execution of their activities. 
This global logistics and transport data exchange is referred to as the `Internet of Logistics`.

### ONE Record API

The ONE Record API specifies the interface and interactions via Web API or Application Programming Interface, which allows all participants in the Internet of Logistics to connect their IT systems directly to the IT systems of their partners, using the state of the art web technologies.
To achieve this, the [ONE Record API](#one-record-api) specifies how data exchange between logistics and transport stakeholders can be achieved over HTTP. 
This API specification is associated with the [ONE Record Data Model](#one-record-data-model) that specifies the models and relationships within the transport and logistics data domain as common "language" between all stakeholders.

### ONE Record Data Model

The ONE Record data model is specified as an ontology which is also referred to as a semantic model. 
This ontology is maintained in [IATA-Cargo/ONE-Record on GitHub](https://github.com/IATA-Cargo/ONE-Record). 
The ONE Record Data Model ontology represents industry concepts, their properties, and their relationships between them. 
This ontology can be used by analysts, developers, and users to understand the data classes in ONE Record and their relationship to each other.

An online documentation based on the ontology can be found in the Tools section on the [ONE Record Developer Portal](https://onerecord.iata.org/).

### Logistics Objects (LO)

The ONE Record data model is organized as a set of data classes that represent real world concepts in transport and logistics, also referred to as digital twins. 
These generic data classes are referred to as `Logistics Objects` in the world of ONE Record. 
Each digital twin is an instance of a data classes that inherits from the Logistics Object data class. 
The data exchange that is facilitated by the ONE Record API is mainly the exchange of Logistics Objects, often shorted to `LO`. 
This is a departure from the traditional logistics and transport EDI systems that only exchanged documents. 
The concept of Logistics Objects is far more extensive since Logistics Objects can be more granular and have very specific usage in different use cases within the logistics and transport domain. 
An example of a Logistics Object would be a `Piece`, an `IoT Sensor` or an a `BookingRequest` and [many more](https://onerecord.iata.org/cargo).

**Owner of a Logistics Object**

The owner of a Logistics Object is the one who created it and thus has control over it. The Owner may also control the ONE Record server on which the Logistics Object is accessible via HTTP but this is not a requirement. 
Instead, the owner may use a 3rd party that implements, operates, and provisions the ONE Record API and will provide suitable access to the Owner to manage and control the Logistics Object, include it's creation, any changes, authoprization and access rights and more. 

*In a publisher-subscriber scenario, the Owner of a Logistics Object is also referred to as `Publisher`.* <br/>
*In an access delegation scenario, the Owner of a Logistics Object is also referred to as the `Delegator`.*

**User of a Logistics Object**

A User of a Logistics Object is anyone other than the Owner and who has access to the Logistics Object. 
Most likely the User will have a role in the logistics & transport of the consignment. 
The User MAY be external, from another company than the Owner, or internal from the same company but with a different function within that company. 

*In a publisher-subscriber scenario, the User of a Logistics Object is also referred to as `Subscriber`.* <br/>
*In an access delegation scenario, the User of a Logistics Object is also referred to as the `Requestor` or `Delegate`.* <br/>
*In a PATCH scenario, the User of a Logistics Object is also referred to as the `Requestor`.*

#### **Logistics Object Identifier (LOID)**

Every Logistics Object MUST have a globally unique identifier, an Unique Resource Identifier (URI). 

A valid Logistics Object URI SHOULD follow [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986) and SHOULD look like:

```
{scheme}://{host}[:port]/{basePath}/{ID}
```

The URI components are described below:

| URI Component | Description                                                                                                                                                                                                                                                                                      | Examples                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------- |
| scheme        | Transfer protocol used by the API                                                                                                                                                                                                                                                                | <ul><li>http</li><li>https</li></ul>                                                |
| host          | Host name, domain name or IP address (and port) of the host that serves the ONE Record API                                                                                                                                                                                                       | <ul><li>127.0.0.1:8080</li><li>1r.example.com</li><li>api.airline.com</li></ul>     |
| basePath      | URL prefix for all API paths, relative to the host root. The base path SHOULD contain at least the version of the ONE Record API                                                                                                                                                                 | <ul><li>/logistics-objects/</li><li>/v1/</li><li>/onerecord/v1/</li><li>/rest/public/</li></ul>       |
| ID            | An identifier for  the Logistics Object that is unique for this server (and thus extension globally). The ID to identify the Logistics Object may be meaningful or obscure. A meaningful ID might include a reference to the type of the Logistics Object itself. An obscure ID could be a UUID. | <ul><li>e17502db-9b2d-46cc-a06c-efb24aeca49b</li><li>waybill-123-12345678</li></ul> |

A LogisticsObject URI SHOULD be URL friendly, i.e. avoid unsafe characters that include blank/empty spaces and/or " < > # % { } \| \ ^ ~ [ ] `. 

It is RECOMMENDED to follow a kebab-case URL naming convention for URI components, i.e. only use lower case letters, numbers, forward slashes, and dashes. 
For example, use https://1r.example.com/logistics-object/waybill-020-12345678 instead of https://1r.example.com/logistics-object/Waybill_020-12345678 

The concrete generation of LogisticsObject URI and thus the design of an unique URI structure is up to each ONE Record API implementer.

Examples:

```
https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c
https://1r.example.com/v1/e17502db-9b2d-46cc-a06c-efb24aeca49b
https://api.airline.com/handling/onerecord/v1/waybill-123-12345678-1
https://onerecordcloud.com/organizations/airline/v2/6596bb81-f5a0-46d0-81be-c4d39531fc6a
https://api.airline.com/rest/public/onerecord/v2/6596bb81-f5a0-46d0-81be-c4d39531fc6a
```

#### **Organization Identifier**

In ONE Record, each party in the Internet of Logistics, e.g., a shipper, airline, or public authorities like customs that acts as an owner or user of Logistics Objects, requires an unique `Organization Identifier`. 
This MUST have a URI that points to a data object that inherits from [Organization](https://onerecord.iata.org/cargo#Organization). 
Therefore, this data object CAN be a [Company](https://onerecord.iata.org/cargo#Company), a [Carrier](https://onerecord.iata.org/cargo#Carrier), or a [PublicAuthority](https://onerecord.iata.org/cargo#PublicAuthority). 
It MUST uniquely identifies an organization in its data exchanges with other organizations that use ONE Record. 
As shown below, this Organization Identifier URI CAN share the same structure as a Logistics Object Identifier:

Examples:
```
https://1r.example.com/logistics-objects/957e2622-9d31-493b-8b8f-3c805064dbda
https://1r.example.com/v2/logistics-objects/24a8a9f0-92c1-4405-8dfb-71875e8cde0a
https://1r.example.com/v1/24a8a9f0-92c1-4405-8dfb-71875e8cde0a
https://1r.platform.com/v1/organizations/airline
```

### ONE Record Server

A ONE Record Server is a web server that provides the ONE Record API capabilities and some or all of the data model specifications. 
Technically, it could be just a simple HTTP web server. The ONE Record API and data model specifications may be added 
to existing (server) software implementations that also provide other services.

> 📝 **Note:** In this document, the general term ONE Record API is used instead of ONE Record server when referring to an actual software implementation of the API specification.


### Plug & Play API Connectivity

The implementation of ONE Record implies the implementation of web servers that comply with the ONE Record API and data model. 
As a result, each of these ONE Record servers can connect and exchange data with any other ONE Record server simply 
by accessing each others endpoints via URIs.

### Global Network of Data through "Linked Data"

A central approach of ONE Record is to enable the creation of a comprehensive network of 
data, i.e. data related to specific consignments and shipments are accessible as a 
unique and single record, i.e. "one record" in that network of linked data. To achieve this,
 the ONE Record data model is based on a cargo industry ontology using the RDF format. 
The ONE Record API specification sets `JSON for Linking Data (JSON-LD)` as the default RDF serialization. 
Other RDF serializations may also be used since they are interchangeable, 
e.g. [Terse RDF Triple Language (TTL)](https://www.w3.org/TeamSubmission/turtle/).

#### Used Namespace Prefixes in ONE Record Context

| Prefix | Namespace                                                            | Description                                                                                                                     |
| ------ | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| cargo  | [https://onerecord.iata.org/cargo](https://onerecord.iata.org/cargo) | This refers to the IATA ONE Record cargo ontology. Refer also to the ONE Record [Developer Portal](https://onerecord.iata.org/) |
| api    | [https://onerecord.iata.org/api](https://onerecord.iata.org/api)     | This refers to the IATA ONE Record API ontology. Refer also to the ONE Record [Developer Portal](https://onerecord.iata.org/)   |
| xsd    | http://www.w3.org/2001/XMLSchema                                     | W3C primitive data types (e.g. string, dateTime)                                                                                |
| acl    | http://www.w3.org/ns/auth/acl                                        | Ontology for WebAccessControl with Access Control Lists (ACL)                                                                   |

### JSON for Linking Data (JSON-LD)

JSON for Linking Data (JSON-LD) is one of the RDF serializations and to fully understand its syntax, requires a deep dive into RDF. 
For the purpose of this document, it is sufficient to understand the example below that represents a Logistics Object of type `Sensor`

RDF (and JSON-LD) has different forms that are all equivalent. 
For example, JSON-LD defines different types of transformations that can be applied to structure the data output.
Therefore, the same `Sensor` object can be formatted as extended (see example 1) - where the URIs of the properties are explicit - or formatted as compressed (see example 2).

Example 1:
```json
{
  "@type": "https://onerecord.iata.org/cargo/Sensor",
  "@id": "http://1r.example.com/logistics-objects/11ccfb7c-3643-41db-8098-740fccd97c93",
  "https://onerecord.iata.org/cargo/Sensor#sensorDescription": "A data logger with a temperature sensor.",
  "https://onerecord.iata.org/cargo/Sensor#sensorName":"TPx14-a",
  "https://onerecord.iata.org/cargo/Sensor#sensorSerialNumber": "142NL",
  "https://onerecord.iata.org/cargo/Sensor#sensorType": "Thermometer"
}
```
(see [examples/Sensor.expanded.json](examples/Sensor.expanded.json))



Example 2:
```json
{
  "@context": {
    "cargo": "https://onerecord.iata.org/cargo/"
  },
  "@id": "http://1r.example.com/logistics-objects/11ccfb7c-3643-41db-8098-740fccd97c93",
  "@type": "cargo:Sensor",
  "cargo:Sensor#sensorDescription": "A data logger with a temperature sensor.",
  "cargo:Sensor#sensorName": "TPx14-a",
  "cargo:Sensor#sensorSerialNumber": "142NL",
  "cargo:Sensor#sensorType": "Thermometer"
}
```
(see [examples/Sensor.compacted.json](examples/Sensor.compacted.json))

There are more forms that are equal to the example above. Copy and paste the first example on 
the [JSON-LD Playground](https://json-ld.org/playground/) to see this different forms.

A full specificaton of the JSON-LD 1.1 standard can be found [here](https://www.w3.org/TR/json-ld11/)

# Implementation Guidelines

This section lists general principles and best practices for the instantiation of a ONE Record API through implementation.
The lists are inspired by other API principles (e.g. [SBB](https://schweizerischebundesbahnen.github.io/api-principles/), 
[Zalando](https://opensource.zalando.com/restful-api-guidelines/))

## Principles

This section provides guidelines that MUST be followed by an implementer of the ONE Record API to ensure 
maximum compatibility with other ONE Record servers and clients on the Internet of Logistics.
When implementing and publishing a ONE Record API, these principles MUST be followed.

### Serialization and Data Formats

**JSON-LD Document Forms**

Every ONE Record API (and every compliant ONE Record client) MUST support at least the expanded document form, the compacted document form, and the flattened document form.
See https://w3c.github.io/json-ld-api/

Therefore, there are some points to mention about JSON-LD in the context of ONE Record:
- Every JSON-LD document MUST include `@id` property
- A JSON-LD document CAN include an `@context` that namespaces, vocabularies and more that facilitate the remainder of the data elements. In this context, there is a specification of two ontologies: the ONE Record cargo ontology (`cargo`) and the ONE Record API ontology (`api`). 
- A JSON-LD document MUST include an `@type` property that specifies the type of the data object, i.e. the ONE Record Logistics Object class (https://onerecord.iata.org/cargo/Sensor in the following example).

> 📝 **Note:** The [OpenAPI specification](IATA-1R-API-OpenAPI.yaml) includes only schemas that can be used to generate expanded document form. 
> Nevertheless, every ONE Record API and ONE Record client MUST support at least all three document forms.


**Content Encoding**

The request body and the response bodies MUST
- use `UTF-8 encoding`
- consist of valid Unicode strings, i.e. must not contain non-characters or surrogates 

It is RECOMMENDED to implement content compression for the ONE Record API, because it improves the data transfer speed and bandwith utilization. 

**Date and Time**

Date and time formats MUST follow [RFC 3339](https://www.rfc-editor.org/rfc/rfc3339#section-5.6).

| Type     | Description  | Example               |
| -------- | ------------ | --------------------- |
| date     | Should match | 2023-03-01            |
| datetime | Always UTC   | 2023-03-031T10:38:01Z |

Time durations and intervals MUST be conform to ISO 8601.
Whenever possible, explicit expiry dates `SHOULD` be used in favor of durations and intervals.

**Airline, Airport, Country, Currency, Language Codes**

| Type     | Description                                        | Example          |
| -------- | -------------------------------------------------- | ---------------- |
| Airline  | IATA Two Letter Codes                              | LH               |
| Airport  | IATA Three Letter Codes                            | FRA              |
| Country  | Two letter country codes. Follow ISO 3166 alpha-2  | CH               |
| Currency | Follow ISO 4217                                    | EUR              |
| Language | Follow IETF BCP 47 language tag                    | en-US            |



**Collections**

Since there may be a semantic difference between an empty collection and the absence of a propert of type collection, this difference MUST also be explicitly apparent in the response body.
For example, Shipment#containedPieces: [] differs from unset Shipment#containedPieces.

**Null values**

To safe bandwith, null valued properties `SHOULD` not be part of the JSON-LD bodies.

### Versioning

**ONE Record API Versioning**

Since in ONE Record the endpoint of a LogisticsObject is its URI - which MUST NOT change - API versioning via URI Path is **not** possible,
e.g. http://1r.example.com/v1/logistics-objects/e17502db-9b2d-46cc-a06c-efb24aeca49b

Instead, the `Accept` request header SHOULD be use, so that a ONE Record API can select one of the proposals and informs the ONE Record client using the `Content-Type` response header.

If no version is specified, the ONE Record API SHOULD use the latest API version implemented and MUST inform the ONE Record client about the API version using the `Content-Type` response header.

When a version is obsolete or not supported anymore, the ONE Record server should be redirected to the latest API version, through the **Location** header and 302 HTTP redirection status.


**Data Model Versioning**

Data Model versioning is done through JSON-LD.
ServerInformation#supportedLogisticsObjectTypes should provide information about supported data classes. This URIS should include the version of the data model, e.g. https://onerecord.iata.org/cargo/v3/Piece


**API Deprecations**
<!-- TODO: How can we deal with this? -->


**Data Versioning**

see [Historical Logistics Object](#historical-logistics-object)


### Error Handling

**HTTP Status Codes**

### Internationalization (i18n)

Internationalization (abbreviated i18n) enable ONE Record clients and servers to request and return data in a given language.

The i18n support is especially needed for addresses that may be written in Chinese or English, for example. If a Chinese shipper provides information only in Chinese characters, a European company may not be able to use this information. Therefore, it is necessary to specify the language in which the data SHOULD be returned.

The supported languages of the ONE Record API SHOULD be provided in the ServerInformation.

To ensure globally interoperablity every ONE Record API MUST implement American English as supported language (i.e. en-US). 
The request HTTP header `Accept-Language` SHOULD be used by the ONE Record client to specify the language of the response.

In order to retrieve the data in a desired language, the ONE Record client should send the following query parameter in the request: ?lang=language_code, where:

| Header              | Description                                                                                                          | Example  |
| ------------------- | -------------------------------------------------------------------------------------------------------------------- | -------- |
| **Accept-Language** | Standard language tag. If none is set the default SHOULD be en-US. See https://datatracker.ietf.org/doc/html/rfc5646 | en-US    |

**Example**

Request:

```http
GET /logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c HTTP/1.1
Host: 1r.example.com
Accept-Language: de-DE 
Accept: application/ld+json
```

Response:

```json
HTTP/1.1 404 Not Found
Content-Language: de-DE
Content-Type: application/ld+json

{   
   "@context": {
     "api": "https://onerecord.iata.org/api/",
     "@language":"de-DE"
   },
   "@type":"https://onerecord.iata.org/api/Error",
   "api:Error#title":"Logistics Object nicht gefunden",
   "api:Error#details":[
      {
         "@type":"api:ErrorDetails",         
         "api:ErrorDetails#code":"404",
         "api:ErrorDetails#message":"Es konnte kein Logistics Object mit der angegebenen URI in der Datenbank gefunden werden.",
         "api:ErrorDetails#resource":"https://1r.example.com/1a8ded38-1804-467c-a369-81a411416b7c"
      }
   ]
}
```
*([examples/Error_404.json](examples/Error_404.json))*

> In JSON-LD, the language can be set in the [context](https://www.w3.org/TR/json-ld11/#dfn-context) using the language key whose value must be a [string](https://infra.spec.whatwg.org/#javascript-string) representing a [BCP47](https://www.w3.org/TR/json-ld11/#bib-bcp47) language code or `null`.

## Best Practices

This section provies a collection of common knowledge about REST API implementation and implementation approaches for the ONE Record API.
When implementing and publishing a ONE Record API, it is RECOMMENDED to follow these best practices.

### Caching

Caching MAY be useful to inform a ONE Record client that a response can be cached locally and does not need to be requested again before a Time to Live (TTL) expires. `Subscription` information are an example.

It is RECOMMENDED to provide caching information via response HTTP header [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control). 
For example, set `"Cache-Control: max-age=86400"` to indicate that the requested resource has a time to live of 86400 seconds (= 24 hours). 

### Monitoring

In general, API monitoring helps to detect failures and performance issues with API calls and provide insights into API usage.
In particular, it MAY also help identify ONE Record clients that interact with functions and/or data model components that are marked as deprecated.

### OpenAPI Documentation

It is RECOMMENDED to provide a link to a technical API specification using OpenAPI.

# Overview: ONE Record API features

The following features summarize all of the API features

**Create and publish a Logistics Object** - Anyone who controls an ONE Record server can create a new Logistics Object based on the data model specification. Once created the Logistics Object is associated with a unique URI that becomes its API endpoint.

**Read a Logistics Object** - Logistics Objects can be retrieved by calling the URI of that Logistics Object. Access rights to that URI is managed by the Owner of the Logistics Object.

**Update a Logistics Object** - As a fundamental principle, *only* the Owner of a Logistics Object can make changes to it. Therefore, changes required by other parties are expressed as change request that will be executed by the actual owner.

**Subcribe to a Logistics Object for updates** - Once a Logistics Object has been created, the owner can propose subscriptions to other parties who will then be notified of any changes. Other parties may also request such a subscription at the discretion of the owner.

**Link Event with Logistics Objects**- Logistics Events like "arrival", "acceptance" etc. are central in the management of logistics and transports. Any type of event may be associated with any type of Logistics Objects. The series of events associated with an Logistics Object may be queried as well.

**Manage access to a Logistics Object** - Another fundamental principle is that owners of Logistics Objects fully control access rights to that Logistics Object. This also extends to *how* access is implemented. Even so, the API specification includes a proposal for implementation as one of the options.

**Delegate access to a Logistics Object to a third party** - By default, most Logistics Objects are not openly accessible. Most of the time, access is  granted to specific parties. Should these parties need to delegate access to their own stakeholders, then there is a mechanism for requesting such access to the owner of the Logistics Object.

**Manage and access versions of Logistics Objects** - Each time a logistics object is updated by a change in data, this change is recorded in an audit trail. This automatically creates a new version of the Logistics  Object. Each version of the logistics object SHOULD be found and retrieved.

**API security** - Although not strictly an API feature, ONE Record specifies security measures for implementation on web servers such that access to the server may be identified, authenticated and authorized.

# ONE Record API Endpoints

> 📝 **Note:** Implicit API endpoints
>
> Many Web APIs specify endpoints for specific API actions. 
> In ONE Record, these endpoints are not explicitly defined. 
> Each Logistics Object has a unique URI and that URI is also the endpoint for that Logistics Object for all API actions. 
>
> This may seem odd at first but it is entirely compatible with RDF technologies where data is accessed via queries and by following links within the data itself.
> As long as a party has the URI of a Logistics Object that they are interested in, then they can access all aspects of that Logistics Object, i.e. it's data, the possibility of requesting changes, associating events, browsing version history etc. directly on that Logistics Object URI.
>
> The *only* endpoint that needs to be known to a ONE Record client is the URI of a Logistics Object.

However, it is RECOMMENDED to implement the various API endpoints by following a pattern.


| HTTP Methods     | API Endpoint                                   | API Function                                                                                                                       |
| ---------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| GET              | /                                              | Retrieve ServerInformation                                                                                                         |
| POST             | /logistics-objects                             | Create a LogisticsObject                                                                                                            |
| GET, POST, PATCH | {LogisticsObjectIdentifier}                    | Retrieve Logistics Object and links to related resources                                                                           |
| GET              | {LogisticsObjectIdentifier}/acl                | Create or retrieve Access Control List (ACL) of a specific Logistics Object                                                        |
| GET              | {LogisticsObjectIdentifier}/audit-trail        | Retrieve Audit Trail of a Logistics Object                                                                                         |
| GET, POST        | {LogisticsObjectIdentifier}/logistics-events   | Create or retrieve LogisticsEvents to a Logistics Object                                                                           |
| GET              | {LogisticsEventIdentifier}                     | Retrieve a LogisticsEvent                                                                                                          |
| GET, POST        | /subscriptions                                 | Create or retrieve Subscription information for a LogisticsObject type or a specific LogisticsObjectIdentifier                     |
| GET, POST, PATCH | /subscription-requests                         | Create, retrieve, or update Subscription Request for for a LogisticsObject type or a specific LogisticsObjectIdentifier            |
| POST             | /notifications                                 | Receive Notifications                                                                                                              |
| GET, POST, PATCH | /delegation-requests                           | Create, retrieve, or update Access Delegation Request                                                                              |

## General HTTP Request Headers
### HTTP Request 
### HTTP Response
Authorization -> this is discusse in Security section.

# Get Server Information

As a ONE Record client, it is helpful to know the capabilities of a ONE Record API and to be able to retrieve this information in a systematic way.
Therefore, every ONE Record API MUST provide an endpoint that can be used to retrieve this rather technical server information. 
This `ServerInformation` include details about the API's ONE Record capabilities, the data owner (as a link to an [Organization Identifier](#organization-identifier)), 
the supported LogisticsObject types and the supported serialization formats. 
Every `authenticated ONE Record client` who knows this endpoint MUST be able to request this information from the ONE Record API.

**Request**
The following HTTP header parameters MUST be present in the GET server information request:

| Request Header   | Description                                                                  | Examples            |
| ---------------- | ---------------------------------------------------------------------------- | ------------------- |
| **Accept**       | The content type that you want the HTTP response to be formatted in.         | application/ld+json |

The HTTP body must contain a valid Logistics Object in the format as specified by the Content-Type in the header. 

**Response**

The following HTTP headers parameters MUST be present in the GET server information response:

| Response Header            | Description     | Example |
| -------------------------- | --------------- | ------- |
| **Content-Type**           | The content type  that is contained with the HTTP body. | application/ld+json   |
| **Content-Language**       | Describes the language(s) for which the requested resource is intended. | en-US |
| **Last-Modified**          | Date and time when the LogisticsObject was last time changed. See https://developer.mozilla.org/en-US/docs/Web/  | Tue, 21 Feb 2023 07:28:00 GMT                                                                                                                                                                                |
| **Link**                   | Contains one or more links to related resources, e.g. subscriptions or notifications endpoints | &lt;https://1r.example.com/notifications&gt;; rel="notifications", &lt;https://1r.example.com/subscriptions&gt;; rel="subscriptions" |

One of the following HTTP response codes MUST be present in the GET server information response: 


| Code    | Description                                                       | Response body     |
| ------- | ----------------------------------------------------------------- | ----------------- |
| **200** | The request to retrieve the ServerInformation has been successful | ServerInformation |

The response body includes the following data elements.

| Object Property               | Description                                     | Required |                                                |
| ----------------------------- | ----------------------------------------------- | -------- | ---------------------------------------------- |
| **dataOwner**                 | URI to the Company that is hosted on the server | y        | http://www.w3.org/2001/XMLSchema#anyURI        |
| **serverEndpoint**            | ONE Record API endpoint, incl. server URL       | y        | http://www.w3.org/2001/XMLSchema#anyURI        |
| **supportedLanguages**        | Languages supported by this server              | y        | http://www.w3.org/2001/XMLSchema#string (list) |
| **supportedContentTypes**     | HTTP content types supported by this server     | y        | http://www.w3.org/2001/XMLSchema#string (list) |
| **supportedLogisticsObjects** | Logistics Object types supported by this server | y        | http://www.w3.org/2001/XMLSchema#anyURI (list) |

**Example**

Request:
```http

GET / HTTP/1.1
Host: 1r.example.com
Content-Type: application/ld+json
Accept: application/ld+json
```

Response:

```bash
HTTP/1.1 200 OK
Content-Type: application/ld+json

{
  "@context": {
    "cargo": "https://onerecord.iata.org/cargo/",
    "api": "https://onerecord.iata.org/api/"    
  },  
  "@id": "https://1r.example.com/",
  "@type": "api:ServerInformation",  
  "api:ServerInformation#dataOwner": {
    "@type": "https://onerecord.iata.org/Company",
    "@id": "https://1r.example.com/logistics-objects/957e2622-9d31-493b-8b8f-3c805064dbda"
  }
  "api:ServerInformation#serverEndpoint": "http://1r.example.com",
  "api:ServerInformation#supportedLanguages": [
  	"en-US"
  	],
  "api:ServerInformation#supportedContentTypes": [
  	"application/ld+json"
  	],
  "api:ServerInformation#supportedLogisticsObjects": [
    "https://onerecord.iata.org/cargo/WayBill"
    "https://onerecord.iata.org/cargo/Shipment",     
    "https://onerecord.iata.org/cargo/Piece"    
  ]
}
```


# Handling Logistics Objects

## Create a Logistics Object

This API action is used to create a Logistics Object on a ONE Record server using the POST HTTP method. 
This CAN be any type of Logistics Object that is specified in the ONE Record data model. 
A list can be found [here](https://onerecord.iata.org/cargo#LogisticsObject).

Although the creation of a Logistics Object is specified in the ONE Record API specification, technically it falls outside the 
specification of ONE Record. The reason is that *only the owner of the Logistics Object* is allowed to create 
a Logistics Object with any process or technology. The only thing that matters is that the Logistics Object gets created with a Logistics Object Identifier 
that is accessible in the Internet of Logisticsv with a dedicated URI. 

Nevertheless, This API action is included for reference, because in many cases, the use of HTTP POST will be used.

As for all API interactions, the user must be authenticated and have the access rights to perform this action.

**Request**
The following HTTP header parameters MUST be present in the POST request:

| Request Header   | Description                                                                  | Examples            |
| ---------------- | ---------------------------------------------------------------------------- | ------------------- |
| **Accept**       | The content type that you want the HTTP response to be formatted in.         | application/ld+json |
| **Content-Type** | The  content type that is contained with the HTTP body. Valid content types. | application/ld+json |

The HTTP body must contain a valid Logistics Object in the format as specified by the Content-Type in the header. 

**Response**

The following HTTP headers parameters MUST be present in the POST response:

| Response Header | Description                                             | Examples                                                                      |
| --------------- | ------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Location**    | The URI of the newly created Logistics Object           | https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c |
| **Type**        | The type of the newly created Logistics Object as a URI | https://onerecord.iata.org/cargo/Piece                                        |

One of the following HTTP response codes MUST be present in the POST response: 

| Code    | Description                                                   | Response body |
| ------- | ------------------------------------------------------------- | ------------- |
| **201** | Logistics Object has been created                             | No content    |
| **400** | Invalid  Logistics Object                                     | Error         |
| **401** | Not authenticated                                             | Error         |
| **403** | Not authorized  to publish the Logistics Object to the server | Error         |
| **415** | Unsupported Content Type                                      | Error         |

**Example 1**

Request:
```http
POST /logistics-objects HTTP/1.1
Host: 1r.example.com

Content-Type: application/ld+json;version=2.0.0-dev
Accept: application/ld+json;version=2.0.0-dev

{
    "@type": "https://onerecord.iata.org/cargo/Piece",    
    "https://onerecord.iata.org/cargo/Piece#goodsDescription": "ONE Record Advertisement Materials",
    "https://onerecord.iata.org/cargo/Piece#handlingInstructions": [
        {
            "@type": "https://onerecord.iata.org/cargo/HandlingInstructions",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceType": "SPH",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceDescription": "Valuable Cargo",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceTypeCode": "VAL"
        }
    ]
}
```
*([examples/Piece.json](examples/Piece.json))*

Response:

```bash
HTTP/1.1 201 Created
Location: https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c
Content-Type: application/ld+json;version=2.0.0-dev
Type: https://onerecord.iata.org/cargo/Piece
```

**Example 2**

Request:

```http
POST /logistics-objects HTTP/1.1
Host: 1r.example.com

Content-Type: application/ld+json;version=2.0.0-dev
Accept: application/ld+json;version=2.0.0-dev

{
    "@type": [
        "https://onerecord.iata.org/Company",
        "https://onerecord.iata.org/Organization",
        "https://onerecord.iata.org/LogisticsAgent",
        "https://onerecord.iata.org/LogisticsObject"
    ],
    "https://onerecord.iata.org/cargo/Organization#name": "Acme Corporation",
    "https://onerecord.iata.org/cargo/Organization#shortName": "ACME",
    "https://onerecord.iata.org/cargo/Organization#contactpersons": [
        {
            "@type": [
                "https://onerecord.iata.org/Person",
                "https://onerecord.iata.org/Actor",
                "https://onerecord.iata.org/LogisticsAgent",
                "https://onerecord.iata.org/LogisticsObject"
            ],
            "https://onerecord.iata.org/cargo/Person#firstName": "Jane",
            "https://onerecord.iata.org/cargo/Person#lastName": "Doe",
            "https://onerecord.iata.org/cargo/Person#salutation": "Ms"
        }
    ]
}
```
*([examples/Company.json](examples/Company.json))*

Response:
```bash
HTTP/1.1 201 Created
Location: https://1r.example.com/logistics-objects/957e2622-9d31-493b-8b8f-3c805064dbda
Content-Type: application/ld+json;version=2.0.0-dev
Type: https://onerecord.iata.org/cargo/Company
```

In the following, the Piece with the [Logistics Object Identifier](#logistics-object-identifier-loid) `"https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c"`
and the Company with the [Organization Identifier](#organization-identifier) `"https://1r.example.com/logistics-objects/957e2622-9d31-493b-8b8f-3c805064dbda"` will be used for the following examples.

## Get a Logistics Object

Each Logistics Object in the Internet of Logistics MUST be accessed via its Logistics Objects Identifier (URI) using the HTTP GET method. 
If the requester is authorized to access this Logistics Object then the response body MUST include the requested Logistics Object. 

It is possible that the Logistics Object requested contains links to other Logistics Object for some of the data elements, 
rather than explicitly embedd that data. The former is RECOMMENDED.
If the User of the Logistics Object is interested in this linked data objects (which not necessary have to be on the same ONE Record server) and has the necessary access, 
the User of the Logistis Object CAN request those Logistics Objects via their linked URIs.
This enables the Owner of the Logistics Object the possibility to manage access on the level of Logistics Objects.

**Request**

The following query parameters are optional:

The following HTTP header MUST be present in the GET request:

| Request Header    | Description                                                                                                                                                                              | Examples                                                                                                                |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Accept**        | The content type that a ONE Record client wants the HTTP response to be formatted in. This SHOULD include the version of the data model, otherwise the latest data model MAY be applied. | <ul><li>application/ld+json</li><li>application/ld+json;version=2.0.0-dev</li><li>application/ld+json;version=1.2</li></ul> |
| **Authorization** | Provides credentials that authenticates the ONE Record client with the ONE Record API. This COULD be an base64 encoded JSON Web Token (JWT) or credentials.                              | Basic b25lOnJlY29yZA==                                                                                                  |

**Response**

A HTTP 200 response is expected to a successful GET request. 
The body of the response includes the Logistics Object in the RDF serialization format that has been requested in the `Accept` header of the request.

The following HTTP header MUST be present in a successful GET response, if mandatory:

| Response Header            | Description                                                                                                                                                                                                  | Example                                                                                                                                                                                |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Content-Type**           | The content type  that is contained with the HTTP body.                                                                                                                                                      | application/ld+json                                                                                                                                                                    |
| **Content-Language**       | Describes the language(s) for which the requested resource is intended.                                                                                                                                      | en-US                                                                                                                                                                                  |
| **Revision**               | The revision of the requested Logistics Object as a non-negative numerical value. This is particularly relevant if the query parameter `at=` is set to request a historical version of the Logistics Object. | 3                                                                                                                                                                                      |
| **Latest-Revision**        | The latest revision number of the Logistics Object as non-negative numerical value.                                                                                                                          | 3                                                                                                                                                                                      |
| **Last-Modified**          | Date and time when the LogisticsObject was last time changed. See https://developer.mozilla.org/en-US/docs/Web/                                                                                              |
| HTTP/Headers/Last-Modified | Tue, 21 Feb 2023 07:28:00 GMT                                                                                                                                                                                |
| **Link**                   | Contains one or more links to related resources, e.g. ACL or Audit Trail.                                                                                                                                    | &lt;https://1r.example.com/e0cd4106-f9db-448a-9a3c-72fcbe4c59fc/acl&gt;; rel="acl", &lt;https://1r.example.com/e0cd4106-f9db-448a-9a3c-72fcbe4c59fc/audit-trail&gt;; rel="audit-trail" |

Link Header
A GET response COULD return a `Link` header that provides URIs to additional, to the Logistics Object related resources:
- [Access Control List](#access-control-list)
- [Audit Trail](#audit-trail-of-logistics-object)
- [Access Delegation](#access-delegation)
- [Subscriptions](#subscriptions)

The required `rel` attribute describes the relationship between the requested Logistics Object and the linked resource.


The value of the `Link` header MUST follow the following structure:
Link: &lt;https://1r.example.com/e0cd4106-f9db-448a-9a3c-72fcbe4c59fc/acl>; rel="acl"
If more than one link is provided, they MUST be concattinated using ; (semicolon) as separator.
Link: &lt;https://1r.example.com/e0cd4106-f9db-448a-9a3c-72fcbe4c59fc/acl&gt;; rel="acl"; &lt;https://1r.example.com/e0cd4106-f9db-448a-9a3c-72fcbe4c59fc/audit-trail&gt;; rel="audit-trail"

Returning the `Link` header is not mandatory. And if the Logistics Object does not have additional resources that can be accessed, 
this Link header SHOULD not be present.

see https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link for more information.


| Code    | Description                                                      | Response body    |
| ------- | ---------------------------------------------------------------- | ---------------- |
| **200** | The request to retrieve the Logistics Object has been successful | Logistics Object |
| **401** | Not authenticated                                                | Error            |
| **403** | Not authorized to retrieve the Logistics Object                  | Error            |
| **404** | Logistics Object not found                                       | Error            |

**Example 1**

Request:
```http
GET /logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c HTTP/1.1
Host: 1r.example.com
Content-Type: application/ld+json
Accept: application/ld+json
```

Response:
```bash
HTTP/1.1 200 OK
Content-Type: application/ld+json
Content-Language: en-US
Location: https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c
Type: https://onerecord.iata.org/cargo/Piece
Revision: 1
Latest-Revision: 1
Link: <https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c/acl>; rel="acl", <https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c/audit-trail>; rel="audit-trail"

{
   "@context": {
     "api": "https://onerecord.iata.org/api/",
     "@language": "en-US"
   },
    "@type": "https://onerecord.iata.org/cargo/Piece",    
    "@id": " https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c",
    "https://onerecord.iata.org/cargo/Piece#goodsDescription": "ONE Record Advertisement Materials",
    "https://onerecord.iata.org/cargo/Piece#handlingInstructions": [
        {
            "@type": "https://onerecord.iata.org/cargo/HandlingInstructions",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceType": "SPH",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceDescription": "Valuable Cargo",
            "https://onerecord.iata.org/cargo/HandlingInstructions#serviceTypeCode": "VAL"
        }
    ]
}
```
*(cf. [examples/Piece.json](examples/Piece_with_id.json))*

**Example 2**
This is an example for an unsuccesful HTTP GET request.

Request:
```http
GET /Sensor_715824 HTTP/1.1
Host: 1r.example.com
Content-Type: application/ld+json
Accept: application/ld+json
```

Response:
```bash
HTTP/1.1 403 Forbidden
Content-Language: en-US
Content-Type: application/ld+json

{
    "@context": {
      "api": "https://onerecord.iata.org/api/",
      "@language": "en-US"
    },
    "@type":"https://onerecord.iata.org/api/Error",
    "api:Error#title":"Not authorized to retrieve the Logistics Object",
    "api:Error#details":[
       {
          "@type":"api:ErrorDetails",         
          "api:ErrorDetails#code":"403",
          "api:ErrorDetails#message":"Authenticated client could not be found in ACL for the Logistics Object",
          "api:ErrorDetails#resource":"https://1r.example.com/Sensor_715824"
       }
    ]
 }
```
*(cf. [examples/Piece.json](examples/Error_403.json))*

## Update Logistics Object (PATCH)

To update a data element in a Logistics Object, the HTTP PATCH method is used.  As per W3C HTTP standard, PATCH is used for partial changes to a resource. This differs from the HTTP PUT method that is used for the complete replacement of a resource.

In ONE Record, only the owner of the Logistics Object can make changes. However, users of the Logistics Object can make change requests to the owner, following a presecribed syntay that is described in this section.

One challenge with making changes to a Logistics Object is that the changes need to be approvable by the owner of the Logistics Object and the changes need to be traceable.

### Linked Data PATCH Format

Although in the specification of the ONE Record API there is not much focus on the context and use of [RDF](https://www.iata.org/contentassets/a1b5532e38bf4d6284c4bf4760646d4e/one_record_tech_insight_the_power_of_ontologies.pdf), it is important that the specification of RDF is fully respected to ensure that ONE Record remains compatible with RDF technologies, such as the use of graph queries. 

W3C has developed the [Linked Data PATCH Format](https://www.w3.org/TR/ldpatch/), a format for describing changes to apply to Linked Data. It defines a list of operations to be performed against a Linked Data resource, namely the addition or removal of RDF triples in a graph representing the resource. 

This Linked Data PATCH Format provides many options for updating RDF but since ONE Record uses JSON-LD as the RDF Serialization, the ONE Record API specification follows the  [JSON-LD PATCH](https://github.com/digibib/ls.ext/wiki/JSON-LD-PATCH) specification and limits the options for changing Logistics Objects to two actions only: delete and add. 

The combination of delete and add equates to replace. So effectively, any data element in a Logistics Object can be *deleted*, *added*, or *replaced*.

### PATCH in ONE Record

Some rules and recommendaions related to updating Logistics Objects:

- Only the Owner of a Logistics Object MAY make the change to the data.
- Any User of a Logistics Object CAN request a change on the Logistics Object, which is referred to as `Change Request`
- The Owner of a Logistics Object CAN apply the Change Request to the  Logistics Object changes unless there is a business or technical reason to reject it.
- Evaluation of a `Change Request` occurs as a single (atomic) event. Operations are sorted and processed as groups of delete and then add operations until the operations are applied, or else the entire PATCH fails. 
- If the PATCH is succesful, the Revision Number is incremented and the changes are recorded in the Audit Trail. Please refer to the sections on Versioning and Audit Trail for more details.
- It is RECOMMENDED to get the latest version of Logistics Object before creating a Change Request in order to make sure that the change is made towards the latest version of the Logistics Object.
- If a Change Request is rejected by the Owner of the Logistics Object, the revision number of the Logistics Object is not incremented but the requests is added to the Audit Trail of this Logistics Object, marked with the status `REJECTED`.

**Request**

The following HTTP header parameters MUST be present in the PATCH request:

| Request Header   | Description                                                                  | Examples            |
| ---------------- | ---------------------------------------------------------------------------- | ------------------- |
| **Accept**       | The content type that you want the HTTP response to be formatted in.         | application/ld+json |
| **Content-Type** | The  content type that is contained with the HTTP body. Valid content types. | application/ld+json |

The HTTP body MUST be a valid array of objects; each object represents a single operation to be applied to a target linked data resource. The format should be as specified by the Content-Type in the header. The following is an example of supported PATCH request body:

The ChangeRequest is a class of the API ontology.
The data that needs to be included in a ChangeRequest body is defined below. 
Please also refer to the example below for more clarity

| Property               | Description                                                                     | Required | Type                   |
| ---------------------- | ------------------------------------------------------------------------------- | -------- | ---------------------- |
| **description**        | Reason for the change                                                           | n        | xsd:String             |
| **logisticsObjectRef** | Logistics Object referred to                                                    | y        | api:LogisticsObjectRef |
| - logisticsObjectId    | Logistics Object Idenifier (URI) of the Logistics Object being changed          | y        | xsd:String             |
| - logisticsObjectType  | Type (class) of the Logistics Object being changed                              | y        | xsd:String             |
| **operations**         | to be performed on this Logistics Object                                        | y        | api:Operation          |
| - o                    | the "object" that is being changed                                              | y        | xsd:String             |
| - op                   | the operation required, i.e. "del" or "add                                      | y        | xsd:String             |
| - p                    | predicate" refers to the type of the element being changed                      | y        | xsd:String             |
| **requestedBy**        | the organization identifier of the entity that is requesting the change request | y        | api:LogisticsObjectRef |
| **revision**           | Revision number of the affected Logistics Object                                | y        | xsd:nonNegativeInteger |

**Response**

| Response Header            | Description                                                                                                     | Example             |
| -------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------- |
| **Content-Type**           | The content type  that is contained with the HTTP body.                                                         | application/ld+json |
| **Content-Language**       | Describes the language(s) for which the requested resource is intended.                                         | en-US               |
| **Last-Modified**          | Date and time when the LogisticsObject was last time changed. See https://developer.mozilla.org/en-US/docs/Web/ |
| HTTP/Headers/Last-Modified | Tue, 21 Feb 2023 07:28:00 GMT                                                                                   |

The response body has no content.

| Code    | Description                                                                                                                                                                              | Response body    |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| **204** | The update has been successful                                                                                                                                                           | No body required |
| **400** | The update  request body is invalid                                                                                                                                                      | Error            |
| **401** | Not  authenticated                                                                                                                                                                       | Error            |
| **403** | Not authorized  to update the Logistics Object                                                                                                                                           | Error            |
| **404** | Logistics  Object not found                                                                                                                                                              | Error            |
| **415** | Unsupported  Content Type, response when the client sends a PATCH document format that the server does not support for the resource  identified by the Request-URI.                      | Error            |
| **422** | Unprocessable  request, when the server understands the PATCH document and the syntax of the PATCH document appears to be valid, but the server is incapable of  processing the request. | Error            |


**Example**

In the example below, Piece#goodsDescription `"BOOKS"` is added and Piece#coload is set from `TRUE` to `FALSE`. 
This results in the following operations that MUST to be part of the ChangeRequest:

1) add the value `"BOOKS"` (xsd:string) to the property Piece#goodsDescription
2) delete the value `TRUE` (xsd:boolean) from Piece#coload
3) add the value `FALSE` (xsd:boolean) to Piece#coload

Request:
```http
PATCH /c2f0387a-f8bf-4cbc-a0a2-e40b2842c1d8 HTTP/1.1
Host: 1r.example.com
Content-Type: application/ld+json
Accept: application/ld+json

{
    "@type": "api:PatchRequest",
    "@context": {
        "cargo": "https://onerecord.iata.org/",
        "api": "https://onerecord.iata.org/api/",
        "host": "http://wwww.myhost.com/"
    },
    "api:PatchRequest#description": "Change sensor type",
    "api:PatchRequest#logisticsObjectRef": [
        {
            "@type": "api:LogisticsObjectRef",
            "api:LogisticsObjectRef#logisticsObjectId": "host:Sensor_715823",
            "api:LogisticsObjectRef#logisticsObjectType": "https://onerecord.iata.org/cargo/Sensor"
        }
    ],
    "api:PatchRequest#operations": [
        {
            "@type": "api:Operation",
            "api:Operation#o": [
                {
                    "@type": "api:OperationObject",
                    "api:OperationObject#datatype": "https://www.w3.org/2001/XMLSchema#string",
                    "api:OperationObject#value": "142NL"
                }
            ],
            "api:Operation#op": "del",
            "api:Operation#p": "https://onerecord.iata.org/cargo/Sensor#sensorSerialNumber"
        },
        {
            "@type": "api:Operation",
            "api:Operation#o": [
                {
                    "@type": "api:OperationObject",
                    "api:OperationObject#datatype": "https://www.w3.org/2001/XMLSchema#string",
                    "api:OperationObject#value": "231NL"
                }
            ],
            "api:Operation#op": "add",
            "api:Operation#p": "https://onerecord.iata.org/cargo/Sensor#sensorSerialNumber"
        }
    ],
    "api:PatchRequest#requestorCompanyIdentifier": "myCompany",
    "api:PatchRequest#revision": "1"
}
```

Response
```bash
HTTP/1.1 204 No Content
Content-Type: application/ld+json
```

## Historical Logistics Object
In ONE Record, data is updated in real time. However, there is a need to retrieve a specific version of a document, 
for example Master Air Waybill (MAWB), and we need to know which version of data was used at a specific point in time.. 
Every time a ChangeRequest is applied successfully, a new version of the Logistics Object is available via its URI. 

📝
Note: Revert to a previous version of a Logistics Object with PATCH are not supported as out of scope of ONE Record. 

After succesful accepted ChangeRequests, Logistics Object become updated and the content changes. With the `?at=` query parameter.

ONE Record APIs MUST enable the ONE Record client to request an historical version of a Logistics Object.

`at=` Parameter.
Example: https://1r.example.com/logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c?at=2019-09-26T07:58:30.000Z

This value needs to be in the past.
Howecer, if this parameter is before the creation date, an 404 is returned.
If this parameter is in the future, a 400 Bad Request is returned.

```http
GET /logistics-objects/1a8ded38-1804-467c-a369-81a411416b7c?at=2019-09-26T07:58:30.000Z
Host: 1r.example.com

```

```bash
Latest-Revision: 3
Revision: 1
```

## Audit trail of Logistics Object

Every time a Logistics Object is changed through a PatchRequest, as described in the previous section, the details of this request are added to an audit trail. When a PatchRequest is processed by the Logistics Object owner, this is referred to as a Change Request. 

The class ChangeRequest includes the full PatchRequest as well as details about the success or failure of this request, the date and time, any errors that may have occured as well as an initial Memento (which is described later in this specification. 

Note that the Logistics Object owner is responsible for adding Change Requests (following Patch Requests by Logistics Object user). 

Logistics Object users can only request to retrieve the Audit Trail. The user can specifify a date-time window.

#### HTTP Request

HTTP Request type: **GET**

A Logistics Object audit trail can be retrieved by performing a GET request to the URI appended by: "/audit-trail"

In order to retrieve the history of a Logistics Object between two dates, a query parameter should be added to the request URL as follows: "?updated-from=YYYYMMDDThhmmssZ&updated-to=YYYYMMDDThhmmssZ" 

#### HTTP Request Header

The following HTTP header parameters MUST be present in the PATCH request:

| **Header**       | **Description**                                                                                                                                                    |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept**       | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |
| **Content-Type** | The content type  that is contained with the HTTP body. Valid content types include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json                |

#### HTTP Response Body

The response body follows the API AuditTrail class structure.

| <div><u>AuditTrail</u></div> | Description                                                                                             | Required | Class                                          |
| ---------------------------- | ------------------------------------------------------------------------------------------------------- | -------- | ---------------------------------------------- |
| **changeRequests**           | List of change requests that were sent as PATCH for a Logistics Object                                  | y        | api:ChangeRequest                              |
| - companyId                  | Company that made the patch request                                                                     | y        | w3c:String                                     |
| - patchRequest               | PATCH body of a change request sent for a specific Logistics Object (described in the previous section) | y        | api:PatchRequest                               |
| - requestedBy                | The party that has requested the change request, i.e. the person or department within the company       | y        | https://onerecord.iata.org/cargo/CompanyBranch |
| - status                     | ACCEPTED or REJECTED                                                                                    | y        | Enumeration                                    |
| - timestamp                  | Timestamp of the change request                                                                         | y        | w3c:DateTime                                   |
| **errors**                   | Mandatory only if patchRequest was rejected. Otherwise Optional                                         | y/n      | api:Error                                      |
| - details                    | error details                                                                                           | n        | api:Details                                    |
| -- attribute                 | Field of the object for which the error applies                                                         | n        | vw3c:String                                    |
| -- code                      | Error code                                                                                              | y        | w3c:String                                     |
| -- message                   | Detailed error message                                                                                  | n        | w3c:String                                     |
| -- resource                  | Object for which the error applies                                                                      | n        | w3c:String                                     |
| - title                      | Brief description of the error                                                                          | y        | w3c:String                                     |
| **loInitialSnapshot**        | Initial content of the Logistics Object at the creation moment, represented via a Memento               | y        | api:LoInitialSnapshot                          |
| - created                    | Date and time of the memento creation                                                                   | Y        | w3c:String                                     |
| - createdBy                  | Name of the memento creator                                                                             | Y        | w3c:String                                     |
| - label                      | Label describing the memento                                                                            | n        | w3c:String                                     |
| - original                   | First version of the Logistics Object                                                                   | y        | w3c:String                                     |
| **logisticsObjectRef**       | Logistics Object referred to                                                                            | y        | api:LogisticsObjectRef                         |
| - logisticsObjectId          | LOID of the Logistics Object being changed                                                              | y        | w3c:String                                     |
| - logisticsObjectType        | Type (class) of the Logistics Object being changed                                                      | y        | w3c:String                                     |

#### Example Audit Trail response for a Logistics Object 

The example below shows an audit trail response body. For claity the main elements of the changeRequest object are collapsed.

Examples for the a changeRequest itself is shown below this AuditTrail example

```
Request header:
GET /mycompany/Sensor_715823/audit-trail?updated-from=20200905T000000Z&updated-to=20200909T000000Z HTTP/1.1
Host: http://wwww.myhost.com
Content-Type: application/ld+json
Accept: application/ld+json

Response body
{
  "@type": "api:AuditTrail",
  "@context": {
    "cargo": "https://onerecord.iata.org/cargo/",
    "api": "https://onerecord.iata.org/api/",
    "host": "http://myhost/"
  },
  "api:AuditTrail#latestRevision": "2",
  "api:AuditTrail#changeRequests": [
    {
      "@type": "api:ChangeRequest",
      "api:ChangeRequest#patchRequest": {...this contains a complete PatchRequest...},
      "api:ChangeRequest#companyId": "myCompany",
      "api:ChangeRequest#status": "ACCEPTED",
      "api:ChangeRequest#timestamp": "2020-09-07T11:55:45.768Z",
      "api:AuditTrail#loInitialSnapshot": {...this contains memento details (described in this doc...},
      "api:AuditTrail#errors": [...this contains errors that may have occured in the change request...],
      "api:AuditTrail#logisticsObjectRef": {...}
    },
    {
      "@type": "api:ChangeRequest",
      "api:ChangeRequest#patchRequest": {...this contains a complete PatchRequest...},
      "api:ChangeRequest#companyId": "myCompany",
      "api:ChangeRequest#status": "ACCEPTED",
      "api:ChangeRequest#timestamp": "2020-09-06T05:33:12.123Z",
      "api:AuditTrail#loInitialSnapshot": {...this contains memento details (described in this doc...},
      "api:AuditTrail#errors": [...this contains errors that may have occured in the change request...],
      "api:AuditTrail#logisticsObjectRef": {...}
    }  ]
}

Response header:
200 (OK)
Content-Type: application/ld+json
```

An example of a **api:AuditTrail#patchRequest** is shown in the previous section (Patch Request)

Example of an **api:AuditTrail#loInitialSnapshot** (of type Memento)

```
"api:AuditTrail#loInitialSnapshot": {
  "@type": "api:Memento",
  "api:Memento#created": "2021-03-03T17:12:01.913Z",
  "api:Memento#createdBy": "Andrew Cythe, Operations Manager, ZXZ Forwarding, Boston MA",
  "api:Memento#label": "Added a new tracking device to the shipment",
  "api:Memento#original": "host:mycompany/Sensor_715823"
},
```

An example of a **api:AuditTrail#logisticsObjectRef** (of type logisticsObjectRef)

```
"api:AuditTrail#logisticsObjectRef": {
  "@type": "api:LogisticsObjectRef",
  "api:LogisticsObjectRef#logisticsObjectId": "http://wwww.myhost.com/Sensor_715823",
  "api:LogisticsObjectRef#logisticsObjectType": "https://onerecord.iata.org/cargo/Sensor#sensorType"
}
```

An example of an `api:ChangeRequest#errors` object (of type Error) is shown in the next section.



 

# Publish & Subscribe with ONE Record

ONE Record proposes a Publish & Subscribe pattern to allow for a distributed network of ONE Record compliant platforms. 

This chapter describes the Publish & Subscribe model, its implementation and the requirements of a Client Subscription API which a company must implement to receive Logistics Objects from ONE Record Servers through subscriptions.

## Publish & Subscribe model

### Publish/subscribe topics and guaranteed delivery queue

Data is exchanged between applications using a notion of topics and delivery queues. While in transit, data is kept in message queues that ensure integrity and availability of the system. Should a subscribing application go down, messages are safely retained until the recipient is ready to read them again.

For each subscriber and each topic, a message queue is maintained automatically by the publisher to keep data in until the subscriber confirms it has received a particular object.

Two scenarios were identified for initiating the publish/subscribe process. For simplicity reasons, the security part was not detailed in the following diagrams.

### Scenario 1 – Publish/Subscribe initiated by Publisher

In the first use case, the subscription process is initiated by the owner of the Logistics Object.

There are two different shapes of the scenario:
 * The publisher wants to notify the subscriber about changes to Logistics Objects of a certain type (i.e. Waybills, Bookings)
 * The publisher wants to notify the subscriber about changes to a specific Logistics Object

For both cases the interaction between two ONE Record follows the following diagram.

```mermaid
sequenceDiagram

  participant PUB as Publisher
  participant SUB as Subscriber

  PUB->>SUB: propose subscription to LO or LO type
  SUB->>PUB: acknowledge proposal
  note over PUB, SUB: After subscription proposal process
  PUB->>SUB: notify about updates and events
```

**Step 1 - Publish a Logistics Object**

At any time the publisher may propose a subscription to a subscriber. If the proposal targets a specific Logistics Object it has to be accessible via its url.

**Step 2 - Retrieve Subscription information from companies that you want to give access to**

In order to acknowledge a proposal the subscriber answers with the subscription information.

**Step 3 - Push to the company’s ONE Record Clients**

Once the subscription information is received the publisher sends notifications to the subscribers using the details provided. If a subscriber was not available at the time, then the publisher would need to queue and retry to notify the subscriber.

### Scenario 2 – Publisher/Subscriber initiated by the Subscriber

In the second scenario, the subscriber initiates the subscription process by actively sending subscription information to the publisher. The subscription information can either target a type of Logistics Object or a specific Logistics Object.

```mermaid
sequenceDiagram

  participant SUB as Subscriber
  participant PUB as Publisher

  SUB->>PUB: subscribe to either LO or LO type
  PUB->>SUB: acknowledge subscription
  note over PUB, SUB: After subscription proposal process
  PUB->>SUB: notify about updates and events
```

## Publisher proposes a subscription 

When publisher wants to actively share information about a Logistics Object type or a specific Logistics Object with a subscriber, the publisher can request the potential subscriber to provide subscription information. To do so, the publisher sends a `GET` request to the subscriptions endpoint with the proposed Logistics Object type or Logistics Object using the query parameter `topic`.

### Http Request

```http request
GET /subscriptions?topic={lo-type|lo-url}
Accept: application/ld+json
``` 

####  HTTP Response Body

The response body includes the following elements:

| Subscription             | Description                                                                                                                           | Required                     |                                                         |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------|------------------------------|---------------------------------------------------------|
| contentTypes             | content types that the subscriber wants to receive in the notifications                                                               | n                            | http://www.w3.org/2001/XMLSchema#string                 |
| cacheFor                 | duration of the period to cache the subscription information in seconds                                                               | n                            | http://www.w3.org/2001/XMLSchema#int                    |
| callbackUrl              | callback URL of the Client Subscription API where the subscriber wants to receive Logistics Objects                                   | y                            |                                                         |
| errors                   | any non HTTP errors related to this request                                                                                           | no, unless there is an error |                                                         |
| myCompanyIdentifier      | company identifier of the subscriber                                                                                                  | y                            | http://www.w3.org/2001/XMLSchema#string                 |
| secret                   | a secret string or API Key that ensures that only companies with this secret information can POST to the subscriber callback endpoint | n                            | w3c:String                                              |
| sendLogisticsObjectBody  | Flag specifying if the publisher should send the whole Logistics Object or only the Logistics Object URI in the notification object   | n                            | w3c:Boolean                                             |
| subscribeToStatusUpdates | Flag specifying if the subscriber wants to receive updates for a Logistics Object                                                     | n                            | w3c:Boolean                                             |
| subscribedTo             | Company Identifier of the company the subscriber wants to subscribe to (used delegation scenario).                                    | y                            | w3c:URI                                                 |
| topic                    | The Logistics Object type to which the subscriber wants subscribe to                                                                  | y                            | https://onerecord.iata.org/cargo/LogisticsObject (list) |



#### Example of a response to a subscription proposal from a publisher to a subscriber 

```
Request header:
GET /mycompany?topic=https://onerecord.iata.org/cargo/WayBill HTTP/1.1
Host: http://wwww.myhost.com
Content-Type: application/ld+json
Accept: application/ld+json

Response Body
{
  "@type": "api:Subscription",
  "@context": {
    "cargo": "https://onerecord.iata.org/cargo/",
    "api": "api:",
    "publisher": "http://publisher-server",
    "subscriber": "http://subscriber-server"
  },
  "api:Subscription#contentTypes": " application/ld+json ",
  "api:Subscription#cacheFor": "86400",
  "api:Subscription#callbackUrl": "subscriber/callback",
  "api:Subscription#errors": ["...non HTTP errors related to this request..."],
  "api:Subscription#myCompanyIdentifier": "subscriber",
  "api:Subscription#secret": "C89583BA9B1FEEAB25F715A3BA2F3",
  "api:Subscription#sendLogisticsObjectBody": true,
  "api:Subscription#subscribeToStatusUpdates": true,
  "api:Subscription#subscribedTo": "publisher",
  "api:Subscription#topic": "https://onerecord.iata.org/cargo/WayBill"
}

Response header:
200 (OK)
Content-Type: application/ld+json
```

<!--- Further development proposal: A subscriber could also send specific field filter to which it wants to subscribe to. (e.g. destination countries).  -->

### 

| **Code** |     | **Description**                                                           | **Response body** |
| -------- | --- | ------------------------------------------------------------------------- | ----------------- |
| **200**  |     | The request to retrieve the Subscription Information has been  successful | Subscription      |
| **204**  |     | Request has been successful, but the server does not subscribe            | No response body  |
| **401**  |     | Not authenticated                                                         | Error             |
| **403**  |     | Not authorized to retrieve the Subscription Information                   | Error             |
| **404**  |     | Subscription Information not found                                        | Error             |



# Access Delegation

In ONE Record parties are enabled to grant other parties access to (parts of) their data. 
The standard allows parties to modify or withdraw these access rights to their data, whenever they wish.

Before a company can access a logistics object of another company, it needs to be authorized to do so and the server that hosts the logistics objects will determine whether to grant access. 
Typically, when a company creates a LogisticsObject and makes it available via its ONE Record API, the company will share the URI of that LogisticsObject with another company and grant them access by default. 

For example, a freight forwarder creates a [BookingOptionRequest](https://onerecord.iata.org/cargo#BookingOptionRequest), grants read access to the airline, and then sends the URI to the airline via ONE Record Notification or other channel.
At this point, only the forwarder and the airline can access this specific LogisticsObject, but no one else.

The party granting access is referred to as the `Delegator` and the party receiving the access is the `Delegate`. 


## Permissions

| Permission        | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| **GET**           | Allows to retrieve the content of a resource                    |
| **PATCH**         | Allows to accept/reject a ChangeRequest                         |
| **APPEND**        | Allows to add a resources to a list, e.g. append LogisticsEvent |


<img src="/Users/henkmulder/Library/Application Support/typora-user-images/image-20220404114548771.png" alt="image-20220404114548771" style="zoom:60%;" />



| Endpoint                  | API function                                                     |
| ------------------------- | ---------------------------------------------------------------- |
| /delegation-requests      | Request to delegate access to Logistics Object for a third party |

**Request**

HTTP Request type: POST

POST /delegation

Host: http://wwww.logistics-object-host.com/

Accept: application/ld+json


**HTTP Headers**

The following HTTP header parameters MUST be present in the POST request:

| **Header**       | **Description**                                                                                                                                                    |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept**       | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |
| **Content-Type** | The content type  that is contained with the HTTP body. Valid content types include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json                |

**Request**

| <u>DelegationRequest</u>  | Description                                                                 | Required | Class                  |
| ------------------------- | --------------------------------------------------------------------------- | -------- | ---------------------- |
| **targetLogisticsObject** | The reference to  the logistics object to which the access is requested     | y        | api:LogisticsObjectRef |
| **targetCompany**         | The party that  will receive the delegated rights                           | y        | api:CompanyInformation |
| **action**                | The action to perform: REVOKE or DELEGATE                                   | y        | "REVOKE","DELEGATE"    |
| **operations**            | The API  operations to which the access is requested: GET,  PATCH, or both. | y        | "GET", "PATCH"         |

##### Response

| **Code** |     | **Description**                              | **Response body** |
| -------- | --- | -------------------------------------------- | ----------------- |
| **204**  |     | Request for  delegation was successful       | No response  body |
| **401**  |     | Not  authenticated                           | Error model       |
| **403**  |     | Not authorized  to send a Delegation request | Error model       |

 Example of a delegation request for GET access to two sensor logistics objects a company that has a ONE Record with URL http://www.delegatedserver.com/.

```json
Request header:
POST /delegation
Host: myonerecordserver.com
Content-Type: application/ld+json
Accept: application/ld+json

{ 
    "@type": "iata-api:DelegationRequest",
    "@context": {
        "iata-cargo": "https://onerecord.iata.org/cargo/",
        "iata-api": "https://onerecord.iata.org/api/",
        "delegated-server": "http://www.delegatedserver.com/",
      	"lo-host": "http://wwww.logistics-object-host.com/"
    },
    "iata-api:DelegationRequest#action": "DELEGATE",
    "iata-api:DelegationRequest#operations": ["GET"],
    "iata-api:DelegationRequest#targetCompanies": [
        "delegated-server:CompanyInformation"
    ],
    "iata-api:DelegationRequest#targetLogisticsObjects": [
        {
          "@type": "api:LogisticsObjectRef",
          "api:LogisticsObjectRef#logisticsObjectId": "lo-host:Sensor_715823",
          "api:LogisticsObjectRef#logisticsObjectType": "https://onerecord.iata.org/cargo/Sensor#sensorType"
      	},	  
        {
          "@type": "api:LogisticsObjectRef",
          "api:LogisticsObjectRef#logisticsObjectId": "lo-host:Sensor_165371",
          "api:LogisticsObjectRef#logisticsObjectType": "https://onerecord.iata.org/cargo/Sensor#sensorType"
      }  

  ]
}


```



 

In the following use cases, we consider three parties: **FirstParty**, **SecondParty** and **ThirdParty**.

## Use Case 1: delegate & 3rd party pubsub

SecondParty request FirstParty access to an LO for Thirdparty. FirstParty then sets up a PubSub with ThirdParty.

<img src="/Users/henkmulder/Library/Application Support/typora-user-images/image-20220404104936690.png" alt="image-20220404104936690" style="zoom:80%;" />

#### Delegation Scenario 1

**Step 1 – Publish & Subscribe flow between FirstParty and SecondParty**

First step contains a Publish & Subscribe flow between FirstParty and SecondParty for a Logistics Object LO with is created on the FirstParty ONE Record Server.

**Step 2 – SecondParty requests delegation access to Logistics Object for ThirdParty**

The SecondParty sends a delegation request to the FirstParty in order to grant ThirdParty access to Logistics Object (GET, PATCH or both).

**Step 3 – Publish & subscribe flow between FirstParty and ThirdParty**

If FirstParty decides to grant ThirdParty access to Logistics Object, then it initiates a Publish & Subscribe flow that would allow ThirdParty get notifications related to Logistics Object.

## Use Case 2: delegate & access Logistics Object directly

SecondParty request FirstParty access to an Logistics Object for Thirdparty. ThirsParty then accesses the Logistics Object on FirstParty directly. In this scenario, ThirdParty didn't set up a pub/sub but received the URI of the from their partner, SecondParty.


<img src="/Users/henkmulder/Library/Application Support/typora-user-images/image-20220404105103059.png" alt="image-20220404105103059" style="zoom:80%;" />

#### Delegation Scenario 2

**Step 1 – Publish & Subscribe flow between FirstParty and SecondParty**

First step contains a Publish & Subscribe flow between FirstParty and SecondParty for a Logistics Object LO with is created on the FirstParty ONE Record Server.

**Step 2 – SecondParty requests delegation access to LO for ThirdParty**

The SecondParty sends a delegation request to the FirstParty in order to grant ThirdParty access to LO (GET, PATCH or both).

Same Request/Response as in [Step 2 – SecondParty requests delegation access to LO for ThirdParty](#_Step_2_–).

**Step 3 – ThirdParty effectuates a GET or PATCH request on LO to FirstParty**

If FirstParty decides to grant ThirdParty access to LO, then ThirdParty can perform a GET or PATCH request on the LO to FirstParty.

## Trust Chains

The concept of companies requesting a delegation of access to their partners can also be used by these partners themselves, who are now third parties. In the example below, the interline partner can request that the forwarder gives access to their ground handler. The forwarder will grant the access on the basis that they trust the airline who has trusted their interline partner who trusts their ground handler.

<img src="/Users/henkmulder/Library/Application Support/typora-user-images/image-20220404110048479.png" alt="image-20220404110048479" style="zoom:50%;" />

These chains of trust are based on business partnerships and trust in the transport chain. It ensures that the company who has shared a logistics object on a server, always knows who may access this and at any time, it can revoke all or part of the chain of trust. 



# Logistics Events

Logistics Events (also known as status updates) in ONE Record can be added to Logistics Objects by posting a LogisticsEvent object to the events endpoint of a LogisticsObject. 

LogisticsEvents can be submitted to any LogisticsObject, if the submitter has sufficient access.

## Create event (POST)

#### Request

HTTP Request type: POST

POST logisticsObjectURI/events

Host: myonerecordserver.net

Accept: application/ld+json

#### HTTP Header

The following HTTP header parameters MUST be present in the POST request:

| **Header**       | **Description**                                                                                                                                                    |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept**       | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |
| **Content-Type** | The content type  that is contained with the HTTP body. Valid content types include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json                |

#### Request body

| <u>Event</u>           | Description                                                                    | Required | Class                                     |
| ---------------------- | ------------------------------------------------------------------------------ | -------- | ----------------------------------------- |
| **linkedObject**       | Logistics object the event applies to                                          | y        | w3c:String                                |
| **performedBy**        | Company that is adding the event                                               | y        | https://onerecord.iata.org/cargo/Company  |
| **eventCode**          | Movement or milestone code. Refer CXML Code List 1.18, e.g. DEP, ARR, FOH, RCS | y        | w3c:String                                |
| **eventName**          | If no EventCode provided, event name - e.g. Security clearance                 | y        | w3c:String                                |
| **eventTypeIndicator** | Type of event being created:  "Actual" , "Expected" , "Planned" or "Requested" | y        | w3c:String                                |
| **dateTime**           | Date and time when  the event occurred                                         | y        | w3c:DateTime                              |
| **location**           | Location of where  the event occurred                                          | y        | https://onerecord.iata.org/cargo/Location |

#### Response

| **Code** |     | **Description**                                                         | **Response body** |
| -------- | --- | ----------------------------------------------------------------------- | ----------------- |
| **201**  |     | Event has been published to  the Internet of Logistics.                 | No response  body |
| **400**  |     | Invalid Event                                                           | Error model       |
| **401**  |     | Not  authenticated                                                      | Error model       |
| **403**  |     | Not authorized  to publish an event update to the Internet of Logistics | Error model       |
| **415**  |     | Unsupported  Content Type                                               | Error model       |

  

```
{
  "@type": "iata-api:Event",
  "@context": {
     "iata-cargo": "https://onerecord.iata.org/cargo/",
     "iata-api": "https://onerecord.iata.org/api/",
  },
  "iata-api:Event#dateTime": "2020-08-25T13:44:49.399Z",
  "iata-api:Event#eventCode": "DEP",
  "iata-api:Event#eventName": "Flight departure",
  "iata-api:Event#eventTypeIndicator": "Actual",
  "iata-api:Event#location": {"...Location object..."}
  "iata-api:Event#linkedObject": "http://wwww.myhost.com/Sensor_715823",
  "iata-api:Event#performedBy": {"...Company object..."}
  "id": "string"
}
```



## Retrieve events (GET)

#### Request

HTTP Request type: GET

GET logistics-object-URI/events 

Host: myonerecordserver.net

Accept: application/ld+json 

#### HTTP Headers

The following HTTP header parameters MUST be present in the GET request:

| **Header** | **Description**                                                                                                                                                    |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept** | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |

#### Response

A positive HTTP 200 response is expected to a GET request. The body of the response is expected to be the events list in the format that has been requested in the Accept header of the request.

| **Code** |     | **Description**                    | **Response body** |
| -------- | --- | ---------------------------------- | ----------------- |
| **200**  |     | Events retrieved  successfully     | Event             |
| **401**  |     | Not  authenticated                 | Error model       |
| **403**  |     | Not authorized  to retrieve Events | Error model       |
| **404**  |     | Logistics  Object Not Found        | Error model       |

 

# Access Control

In ONE Record, access to resources could be handled by using Access Control Lists (ACLs) stored in the backend systems of the ONE Record Servers and defined using the [Web Access Control standard from W3C](https://www.w3.org/wiki/WebAccessControl).

## Web Access Control

According to W3C specifications, Web Access Control it is a standard that enforces access control based on the Access Control List (ACL) RDF resource associated with the requested resource. It's concerned with giving access to agents (users, groups and more) to perform various kinds of operations (read, write, append, etc) on resources. In Web Access Control, an ACL consists of a set of **Authorizations**. Each Authorization is a single rule for access, such as "entities one and two may write to resource someresource", described with a set of RDF properties. 

#### Access Control List Resources

In a system that uses Web Access Control, each web resource has a set of **Authorization** statements describing:

1. Who has access to that resource (that is, who the authorized **agents** are).
2. What types (or **modes**) of access they have.

These Authorizations are either explicitly set for an individual resource, or (more often) inherited from that resource's parent folder or container. In either case, the Authorization statements are placed into separate WAC documents called Access Control List Resources (or simply ACLs).

An Authorization is written as a series of RDF triples, with the Authorization resource as the subject for each of these triples. The Authorization resource URI is a hash URI, because of the requirement for potentially multiple, distinct Authorizations to be included in a single ACL resource and it has an rdf: type of http://www.w3.org/ns/auth/acl#Authorization.

The complete WAC ontology can be found [here](https://www.w3.org/ns/auth/acl).

The location of the acl for a given resource may be discovered via a Link header with relation rel=”acl”. Given a URL for an individual resource or logistics object, a client can discover the location of its corresponding ACL by performing a GET request and parsing the rel=”acl” link relation.

Example:

GET  http://myServer/my-airline/logistics-object would return:

HTTP/1.1 200 OK

Link: <http://myServer/my-airline/logistics-object/acl>; rel="acl"

If a resource does not have an individual ACL (and therefore relies on an implicit ACL from a parent), this link header will still be present, but will return a 404.

**It MUST NOT be assumed that the location of an ACL resource can be deterministically derived from a Logistics Object identifier URI.** 

#### Example of Authorizations

##### Single Authorization

Below is an example ACL resource that describes that Party1 (as identified by its ONE Record Company Identifier https://1r.party1.com) has full access (Read, Write and Control) 
to one of their own web resources, located at https://1r.party1.com/06ebed0f-b5b2-4abf-b0e5-dc027f5ce6cc

```
# Contents of https://1r.party1.com/06ebed0f-b5b2-4abf-b0e5-dc027f5ce6cc/acl

@prefix acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization1>
    a             acl:Authorization;
    acl:agent     <https://1r.party1.com>;  # Company Identifier in the IoL
    acl:accessTo  <https://1r.party1.com/06ebed0f-b5b2-4abf-b0e5-dc027f5ce6cc>;
    acl:mode      acl:Read, 
                  acl:Write, 
                  acl:Control.
```

 

Agent = Company Identifier in the Internet of Logistics

In this case, Party1 is the Owner of the Logistics Object. Owners are agents that have Read, Write and Control permissions.

##### Group Authorization

```
# Group authorization, giving Read/Write access to two groups, which are
# specified in the 'work-groups' document.
<#authorization2>
    a               acl:Authorization;
    acl:accessTo    <https://party1.server.com/company/logistics-object2>;
    acl:mode        acl:Read,
                    acl:Write;
    acl:agentGroup  <https://party1.server.com/company/groups#Accounting>;
    acl:agentGroup  <https://party1.server.com/company/groups#Management>.
```

 

Corresponding work-groups Group Listing document:

```
# Contents of https://party1.server.com/company/groups
@prefix    acl:  <http://www.w3.org/ns/auth/acl#>.
@prefix     dc:  <http://purl.org/dc/elements/1.1/>.
@prefix  vcard:  <http://www.w3.org/2006/vcard/ns#>.
@prefix    xsd:  <http://www.w3.org/2001/XMLSchema#>.
 
<#Accounting>
    a                vcard:Group;
    vcard:hasUID     <urn:uuid:8831CBAD-1111-2222-8563-F0F4787E5398:ABGroup>;
    dc:created       "2018-09-11T07:18:19+0000"^^xsd:dateTime;
    dc:modified      "2019-08-08T14:45:15+0000"^^xsd:dateTime;
 
    # Accounting group members:
    vcard:hasMember  <https://party2.server.com/company5>;
    vcard:hasMember  <https://party3.server.com/company7>.
 
<#Management>
    a                vcard:Group;
    vcard:hasUID     <urn:uuid:8831CBAD-3333-4444-8563-F0F4787E5398:ABGroup>;
 
    # Management group members:
    vcard:hasMember  <https://party4.server.com/company1>.
```

 

##### Authenticated Agents (Default)

Authenticated access is a bit like public access, but it is not anonymous. Access is only given to clients who have logged on and provided a specific Company Identifier. This allows the server to track the entities who have used the resource.

```
@prefix   acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization2>
    a               acl:Authorization;
    acl:agentClass  acl:AuthenticatedAgent;                   
    acl:mode        acl:Read;                                 
    acl:accessTo    <https://party1.server.com/company/logistics-object>.
```

 

An application of this feature is to throw a resource open to all authenticated parties for a specific amount of time, accumulate the list of those who case as a group, and then later restrict access to that group, to prevent spam.

#### Modes of Access

The `acl:mode` predicate denotes a class of operations that the agents can perform on a resource.

**`acl:Read`**

This includes access to HTTP verb GET. 

##### `acl:Write`

This includes` `POST, and PATCH. (PUT and DELETE are out of scope of ONE Record).

##### `acl:Control`

All methods, if the request URI is an ACL.

###### `Out of scope of ONE Record`

##### `acl:Append`

gives a more limited ability to write to a resource - Append-Only. 

#### Inheritance

Use `acl:default`.

If you use `acl:accessTo` to protect a container, and add an `acl:default` predicate, that authorization rule by default will also apply to any of that container's children, unless that child has its own ACL.

The second is to use the `acl:accessToClass` property to state that the authorization rule applies to any resource with the named RDF type. 

#### Delegation

When delegating access to a resource to a third party, a new Authorization element should be added to the ACL.

## Access Control in ONE Record

In ONE Record, access to resources can be specified by using Access Control Lists (ACLs) associated to specific Logistics Objects (LOs). Each LO resource possesses a related ACL containing a set of **Authorization** statements that describe:

- **who** has access to that resource;
- **what types** (or **modes**) **of access** they have. 

Each Authorization is a single rule for access, such as "entities one and two may write to LO logisticObjectRef", described with a set of RDF properties. 

ONE Record recommends the use of the [ACL ontology](https://www.w3.org/ns/auth/acl) in order to express the Authorizations. As the ACL is specific to each ONE Record Server and it is not a mandatory requirement to make it available to external entities, any other kind of data model/ontology can be used instead.

Given an URI for an individual LO, a ONE Record Client can discover the location of its corresponding ACL by performing a GET request and parsing the rel=”acl” **Link** header. 

​                             ![Text Box: GET  http://myServer/my-airline/logistics-object would return the headers: HTTP/1.1 200 OK Link: <http://my-server/my-airline/logistics-object/acl>; rel="acl" ](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image001.png)

Example of GET LO returning an ACL

> 📝 **Note:** ONE Record Clients must not assume that the location of an ACL resource can be derived from an LO’s URI. 

ONE Record recommends the definition of three types of Authorization:

1. **Single Authorization** – when a single company identifier from the Internet of Logistics has access to the LO;
2. **Group Authorization** – when a group of company identifiers has access to the LO. The ONE Record Server can define internally groups of access such as Airlines, Ground Handlers, Customs, etc.
3. **Public” Authorization** – when every authenticated company identifier accessing the LO URI can retrieve the data.

ONE Record specifies three modes of access on LOs:

<img src="file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image002.png" alt="img" style="zoom: 50%;" />

> 📝 **Note:** In a delegation scenario, when delegating access to a resource to a third party, a new Authorization element should be added to the ACL.

## Use case

In this illustrative scenario, the entities would be **Shipper** (which has its own ONE Record Server) and **Forwarder**.

- Shipper creates a Shipment logistics object. Shipper is the owner of the phones, which constitute the Product. Each Product with a series number is an Item.
- Shipper packages the phones on wooden pallets, creates Pieces and handles them to Forwarder. 

![A screenshot of a social media post  Description automatically generated](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image003.png)

 

- Forwarder is responsible to create/update Transport Segment data for each Piece. 

```
# Contents of https://shipperserver.com/company/shipment.acl
@prefix acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization1>
    a             acl:Authorization;
    acl:agent     <https://forwarderserver.com/company>;  # Forwarder Identifier in the IoL
    acl:accessTo  <https://shipperserver.com/company/shipment/transportSegment>;
    acl:mode      acl:Read, 
                  acl:Write, 
                  acl:Control.
```

 

- Each Piece contains other information such as Goods Description which is only created/updated by Shipper. 

```
# Contents of https://shipperserver.com/company/shipment.acl
@prefix acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization1>
    a             acl:Authorization;
    acl:agent     <https://shipperserver.com/company>;  # Shipper Identifier in the IoL
    acl:accessTo  <https://shipperserver.com/company/shipment/goodDescription>;
    acl:mode      acl:Read, 
                  acl:Write, 
                  acl:Control.
 
```

 

- UPID information inside Piece could either be created by Shipper, in which case Shipper would be the owner of this data element. However, Shipper could handle the UPID creation/update to Forwarder, in which case Forwarder would be the owner of this data, meaning that only Forwarder could update this information. 

```
# Contents of https://shipperserver.com/company/shipment.acl
@prefix acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization1>
    a             acl:Authorization;
    acl:agent     <https://forwarder.com/company>;  # Forwarder Identifier in the IoL
    acl:accessTo  <https://shipperserver.com/company/shipment/UPID>;
    acl:mode      acl:Read, 
                  acl:Write, 
                  acl:Control.
```

 

- Same principle as UPID could apply for Total Weight element inside Piece.

```
# Contents of https://shipperserver.com/company/shipment.acl
@prefix acl:  <http://www.w3.org/ns/auth/acl#>.
 
<#authorization1>
    a             acl:Authorization;
    acl:agent     <https://forwarder.com/company>;  # Forwarder Identifier in the IoL
    acl:accessTo  <https://shipperserver.com/company/shipment/totalWeight>;
    acl:mode      acl:Read, 
                  acl:Write, 
                  acl:Control.
```

![A screenshot of a social media post  Description automatically generated](file:////Users/henkmulder/Library/Group%20Containers/UBF8T346G9.Office/TemporaryItems/msohtmlclip/clip_image004.png)

## Create ACL (POST)

#### Request

HTTP Request type: POST

POST logistics-object-URI/acl

Host: myonerecordserver.net

Accept: application/ld+json

 

Example:

**POST** https://www.onerecordcargo.org/my_airline/shipment_123456/acl

 

ONE Record does not define a specific model for ACL, but suggests the utilization of [Access Control Ontology defined by W3C](https://www.w3.org/ns/auth/acl).

 

#### HTTP Headers

The following HTTP header parameters MUST be present in the POST request:

| **Header**       | **Description**                                                                                                                                                    |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept**       | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |
| **Content-Type** | The content type  that is contained with the HTTP body. Valid content types include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json                |

 

#### Response

| **Code** |     | **Description**                                | **Response body** |
| -------- | --- | ---------------------------------------------- | ----------------- |
| **201**  |     | ACL has been published for  a Logistics Object | No response  body |
| **400**  |     | Invalid ACL                                    | Error model       |
| **401**  |     | Not  authenticated                             | Error model       |
| **403**  |     | Not authorized  to publish an ACL              | Error model       |
| **415**  |     | Unsupported  Content Type                      | Error model       |

 

## Retrieve ACL for a specific Logistics Object (GET)

#### HTTP Headers

The following HTTP header parameters MUST be present in the GET request:

| **Header** | **Description**                                                                                                                                                    |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Accept** | The content type  that you want the HTTP response to be formatted in. Valid content types  include:  ▪ application/x-turtle  or text/turtle  ▪ application/ld+json |

**Example**

Request:
```http
GET /1a8ded38-1804-467c-a369-81a411416b7c/acl HTTP/1.1
Host: 1r.example.com
Accept-Language: en 
Accept: application/ld+json
```

Response:
```json

```
A positive HTTP 200 response is expected to a GET request. The body of the response is expected to be the ACL list in the format that has been requested in the Accept header of the request.

| **Code** |     | **Description**                | **Response body**                     |
| -------- | --- | ------------------------------ | ------------------------------------- |
| **200**  |     | ACL returned successfully      | [ACL](https://www.w3.org/ns/auth/acl) |
| **401**  |     | Not authenticated              | Error                                 |
| **403**  |     | Not authorized to retrieve ACL | Error                                 |
| **404**  |     | Logistics Object/ACL not found | Error                                 |

# Error Handling

## Error model

This section describes the datatype definitions used within the ONE Record API for error handling.

#### Response HTTP Status Codes

The API response will includes standard HTTP Status Codes in it's reponse.

#### Response Body

The error response should contain the following fields:

| **Error**<div></div> | **Description**                                                            | **Required** | Class       |
| -------------------- | -------------------------------------------------------------------------- | ------------ | ----------- |
| **title**            | a short summary of the problem                                             | y            | w3c:String  |
| **details**          | details of the error                                                       | n            | api:Details |
| - code               | a ONE Record application-specific  error code expressed as a string value. | n            | w3c:String  |
| - attribute          | data element to which the error applies                                    | n            | w3c:String  |
| - resource           | URI of the object concerned                                                | n            | w3c:Strin   |
| - message            | Explanation specific to this problem                                       | n            | w3c:String  |

#### Example error response body

Since errors are always part of another response object, below is an example of an **api:AuditTrail#errors** element (of type Error). Note that is a list and there may be multiple errors reports.

```
"api:AuditTrail#errors": [
  {
    "@type":"api:Error",
    "api:Error#title": "Unknown sensor type",
    "api:Error#details": [
      {
        "@type":"api:Details",
        "api:Details#attribute": "https://onerecord.iata.org/cargo/Sensor#sensorType",
        "api:Details#code": "Invalid Device Type",
        "api:Details#message": "Sensor type 'ionization coil' is not a known option. ",
        "api:Details#resource": "http://wwww.myhost.com/Sensor_715823"
      }
    ]
  }
]
```

## Error Codes
<!--  needs to be added -->
### HTTP Error Codes
| **Code** |     | **Description**                 | **Response body** |
| -------- | --- | ------------------------------- | ----------------- |
| **401**  |     | Not  authenticated              | Error model       |
| **403**  |     | Not authorized  to retrieve ACL | Error model       |
| **404**  |     | Logistics  Object/ACL Not Found | Error model       |

### Cargo Error Codes

See CargoXML error codes.

# Glossary 

| **Term**                               | **Description**                                                                                                                                                                                                                                                                                                                                       |     |     |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | --- |
| **ACL**                                | Access Control  List                                                                                                                                                                                                                                                                                                                                  |     |     |
| **Authentication**                     | A process that validates  the identity of IoL participant                                                                                                                                                                                                                                                                                             |     |     |
| **Authorization**                      | A process that  determines whether a IoL participant is allowed to access a specific  Logistics Object                                                                                                                                                                                                                                                |     |     |
| **COTB**                               | Cargo Operations & Technology Board (COTB) reports to the Cargo Services Conference (CSC) at the International Air Transport Association. The COTB has authority over the ONE Record specifications. COTB decisions are formally endorsed by the CSC.                                                                                                 |     |
| **Identity & Authentication Provider** | A service that  allows Internet of Logistics participants register and obtain an Public Key  encrypted token identify themselves with ONE Record Servers and get access to  Logistics Objects                                                                                                                                                         |     |     |
| **Internet of Logistics (IoL)**        | A network of ONE  Record Clients and Servers that can share Logistics Objects over the internet  using the ONE Record standard data model, APIs and security                                                                                                                                                                                          |     |     |
| **JSON-LD**                            | JSON-LD is a  lightweight Linked Data format. It is easy for humans to read and write. It  is based on the already successful JSON format and provides a way to help  JSON data interoperate at Web-scale. JSON-LD is an ideal data format for  programming environments, REST Web services, and unstructured databases such  as CouchDB and MongoDB. |     |     |
| **JSON Web Token (JWT)**               | JSON specification for a token format that includes a user defined  payload and the option for encryption.                                                                                                                                                                                                                                            |     |     |
| **Linked Data**                        | Linked Data empowers people that publish and use information on the  Web. It is a way to create a network of standards-based, machine-readable  data across Web sites. It allows an application to start at one piece of  Linked Data and follow embedded links to other pieces of Linked Data that are  hosted on different sites across the Web.    |     |     |
| **Logistics Object**                   | A data object  that represents a meaningful entity in the logistics business. These may represent  documents like air waybills but may also be more granular such as company  details or a transport segment description. Logistics Objects are specified  in a common data model by IATA and transport and logistics partners.                       |     |     |
| **OAuth 2.0**                          | A protocol for delegation of authentication in a network of secure  systems, see https://oauth.net/2/                                                                                                                                                                                                                                                 |     |     |
| **ONE Record Client**                  | A software program that CAN access Logistics Objects on a ONE Record server.                                                                                                                                                                                                                                                                          |     |     |
| **ONE Record Server**                  | The platform that  hosts Logistics Objects on a web server on behalf of one or more organization                                                                                                                                                                                                                                                      |     |     |
| **ONE Record Subscriber API**          | A ONE Record  Client API that has dedicated endpoint(s) for receiving Logistics Objects via  a subscription                                                                                                                                                                                                                                           |     |     |
| **Participant**                        | Server that  access or shares data via the Internet of Logistics and that has registered  with an Accredited Identity Provider and has possession of a valid  certificate to prove this                                                                                                                                                               |     |     |
| **Publisher**                          | The Party that  makes their Logistics Objects available through a ONE Record Server                                                                                                                                                                                                                                                                   |     |     |
| **Subscriber**                         | The Party that  subscribes to Logistics Objects in order to receive updates automatically                                                                                                                                                                                                                                                             |     |     |
| **Uniform Resource Identifier (URI)**  | A Uniform Resource Identifier (URI) is a URL that uniquely identifies a Logistics Object                                                                                                                                                                                                                                                              |     |     |
| **WAC**                                | Web Access  Control                                                                                                                                                                                                                                                                                                                                   |     |     |

# Bibliography

- Access Control Ontology: https://www.w3.org/ns/auth/acl
- Web Access Control Specification from W3: https://www.w3.org/wiki/WebAccessControl
- Web Access Control specifications by Solid project: https://github.com/solid/web-access-control-spec
- Fedora project: https://wiki.lyrasis.org/display/FEDORA51/WebAC+Authorizations
- Access Control in Linked Data Using WebID: https://arxiv.org/pdf/1611.03019.pdf
- VCard ontology: [https://www.w3.org/2006/vcard/ns#%3E](https://www.w3.org/2006/vcard/ns#>)
- Luca Costabello (2013) Context-aware access control and presentation of linked data. https://tel.archives-ouvertes.fr/tel-00934617/document 
- JSON-LD 1.1 A JSON-based Serialization for Linked Data. https://www.w3.org/TR/json-ld11/
- JSON Web Token (JWT). https://jwt.io

# Development Team

## Maintainers

> A maintainer is a person who is responsible for the ongoing development of the ONE Record API specification and has commit access to the ONE Record main repository.
- [Henk Mulder](https://github.com/edesignextended), IATA 
 
 *(sorted alphabetically)*

## Contributors

> A contributor can be anyone who wants to contribute changes to the ONE Record API specification.
- [Andra Blaj](https://github.com/andrablaj)
- [Daniel A. Döppner](https://github.com/ddoeppner), Lufthansa Industry Solutions
- [Martin Skopp](https://github.com/mskopp), Riege Software
- [Philipp Billion](https://github.com/DrPhilippBillion), Lufthansa Cargo 
- Thomas Moreau, GEODIS

*(sorted alphabetically)*