# ONE Record API Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 2.0.0-dev (not released yet)

---

### ONE Record API Specification

#### Changed

- moved Security part of ONE Record API specification to separate file [IATA-1R-Security-Specification.md](IATA-1R-Security-Specification.md)
- restructured Introduction section
- use Logistics Object instead of its abbreviation LO 

#### Removed

- removed Memento (incl. TimeGate, Timemap)
  
#### Added

- added UTF-8 as default content encoding
- added 0 as default revision number for AuditTrail and LogisticsObjects
- added class diagram as supporting document, see [IATA-1R-API-Class-Diagram.md](IATA-1R-API-Class-Diagram.md)
- added `Implemenation Guidelines` section to reduce ambiguity
- added JSON files with examples mentioned in the API specification
- added overiew of permissions for Access Delegation

#### Fixed

- cleaned [OpenAPI](IATA-1R-API-OpenAPI.yaml) example 

---

### ONE Record API Ontology

#### Changed 

- renamed CompanyInformation to ServerInformation, because it mainly contains meta information about the ONE Record API and its configuration
- replaced ServerInformation#company and ServerInformation#companyId (formerly CompanyInformation) with ServerInformation#dataOwner
- merged PatchRequest into ChangeRequest
- renamed Operaton#op enums to UPPERCASE, e.g. changed del to DEL
- renamed Details to ErrorDetails
- renamed ChangeRequest#requestingParty to ChangeRequest#requestedBy
- renamed ChangeRequest#timestamp to ChangeRequest#requestedAt
- changed ChangeRequest#requestingParty<Branch> to ChangeRequest#requestedBy<https://onerecord.iata.org/cargo/Organization>
- renamed ErrorDetails#attribute to ErrorDetails#property
- renamed ServerInformation#supportedLogisticsObjects to ServerInformation#supportedLogisticsObjectTypes
- changed property type of Notification#topic, Notification#changedProperties, OperationObject#datatype, ServerInformation#serverEndpoint, ServerInformation#supportedLogisticsObjects, ErrorDetails#property, ErrorDetails#resource, Subscription#callbackUrl, Subscription#topic, Subscription#subscribedTo
- renamed Subscription#myCompanyIdentifier to Subscription#subscriber
- changed Subscription#cacheFor<xsd:int> to Subscrption#expiresAt<xsd:dateTime>
- renamed DelegationRequest#operations to DelegationRequest#permissions

#### Removed

- removed not used classes, data properties, object properties
- removed Memento (classes, data properties, object properties)
- removed ServerInformation#companyId
- removed AuditTrail#errors, auditTrail#loInitialSnapshot
- removed ChangeRequest#requestorCompanyIdentifier
- removed ServerInformation#errors
- removed Subscription#subscribedTo
- removed LogisticsObjectRef data class and use https://onerecord.iata.org/cargo/LogisticsObject instead


#### Added

- added Notification#changedProperties
- added further enums to notification#eventType, i.e. EVENT_RECEIVED, CHANGEREQUEST_ACCEPTED, CHANGEREQUEST_FAILED, CHANGEREQUEST_PENDING, CHANGEREQUEST_REJECTED, DELEGATION_REQUEST_PENDING, DELEGATION_REQUEST_ACCEPTED, DELEGATION_REQUEST_REJECTED, DELEGATION_REQUEST_FAILED, SUBSCRIPTION_REQUEST_PENDING, SUBSCRIPTION_REQUEST_ACCEPTED, SUBSCRIPTION_REQUEST_REJECTED, SUBSCRIPTION_REQUEST_FAILED
- added PENDING enum to ChangeRequest#status
- added Operation#s to enable updating not only properties of primitive types, e.g. int, string, but also embedded objects, e.g. Shipment#totalGrossWeight<Value> in LogisticsObjects
- added DelegationRequest#status as required property, initial value SHOULD be PENDING
- added ChangeRequest#errors
- added ChangeRequest#callbackURL
- added Notification#changeRequest
- added DelegationRequest#errors
- added DelegationRequest#description
- added SubscripionRequest. Subscription is the response for the scenario where publisher initiates the Subscription and asks the subscribers for their Subscription information. SubscriptionRequest is used for scenario where the subscriber initiates a SubscriptionRequest towards the Publisher.
- added Subscription#topicType to indicate if topic is a LogisticsObject type or a specific LogisticsObjectIdentifier
---