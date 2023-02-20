# ONE Record API: Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 2.0.0-dev (not released yet)

---
### ONE Record API Specification

#### Removed
- removed Memento (incl. TimeGate, Timemap)

#### Changed
- moved Security part of ONE Record API specification to separate file [IATA-1R-Security-Specification.md](IATA-1R-Security-Specification.md)
  
#### Added

- added UTF-8 as default content encoding
- added 0 as default revision number for AuditTrail and LogisticsObjects

#### Fixed

- cleaned [OpenAPI](IATA-1R-API-OpenAPI.yaml) example 

---
### ONE Record API Ontology

#### Removed

- removed not used classes, data properties, object properties
- removed Memento (classes, data properties, object properties)
- removed ServerInformation#companyId
- removed AuditTrail#errors, auditTrail#loInitialSnapshot

#### Added

- added Notification#changedProperties
- added further enums to notification#eventType, i.e. CHANGEREQUEST_ACCEPTED, CHANGEREQUEST_FAILED, CHANGEREQUEST_PENDING, CHANGEREQUEST_REJECTED, EVENT_RECEIVED
- added PENDING enum to ChangeRequest#status
- added DelegationRequest#status as required property
- added Operation#s to enable updating not only properties of primitive types, e.g. int, string, but also embedded objects, e.g. Shipment#totalGrossWeight<Value> in LogisticsObjects

#### Changed 

- replaced all data classes of the ONE Reoord cargo ontology to maintain independency between ONE Record API and cargo data model, e.g. replaced Notification#LogisticsObject with Notification#LogisticsObjectRef
- renamed CompanyInformation to ServerInformation, because it mainly contains meta information about the ONE Record API and its configuration
- replaced ServerInformation#company and ServerInformation#companyId (formerly CompanyInformation) with ServerInformation#dataOwner
- merged PatchRequest into ChangeRequest
- renamed Operaton#op enums to UPPERCASE, e.g. changed del to DEL
- renamed Details to ErrorDetails
- changed ChangeRequest#requestingParty to ChangeRequest#requestedBy
- changed ChangeRequest#timestamp to ChangeRequest#requestedAt
- changed ChangeRequest#requestingParty<Branch> to ChangeRequest#requestedBy<LogisticsObjectRef>
- changed property type of Notification#topic, Notification#changedProperties, OperationObject#datatype, ServerInformation#serverEndpoint, ServerInformation#supportedLogisticsObjects, ErrorDetails#property, ErrorDetails#resource, Subscription#callbackUrl  to xsd:AnyURI

---