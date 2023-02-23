# ONE Record API Class Diagram

**Version:** 2.0.0-dev **Status:** Draft; not yet approved by the COTB / CSC

```mermaid
classDiagram   
    direction LR     
    
    class AuditTrail{
        + initialLogisticsObject: LogisticsObject
        + logisticsObjectRef: LogisticsObjectRef
        + latestRevsion: xsd:nonNegativeInteger
        + changeRequests[]: ChangeRequest [*]        
    }   
    AuditTrail "1" --> "1" LogisticsObject
    AuditTrail "1" --> "1" LogisticsObjectRef
    AuditTrail "1" --> "*" ChangeRequest

    class ChangeRequest{
        + callbackUrl: xsd:anyURI [0..1]
        + description: xsd:string [0..1]
        + errors[]: Error [*]        
        + logisticsObjectRef: LogisticsObjectRef                
        + operations[]: Operation [1..*]                
        + requestedAt: xsd:dateTime         
        + requestedBy: OrganizationRef    
        + revision: xsd:nonNegativeInteger
        + status: RequestStatus = PENDING
    }    
    ChangeRequest "1" --> "*" Error
    ChangeRequest "1" --> "*" LogisticsObjectRef
    ChangeRequest "1" --> "1..*" Operation
    ChangeRequest "1" --> "*" OrganizationRef    
    ChangeRequest --> RequestStatus        
   
    class DelegationRequest{
        + action: xsd:string
        + delegates[]: OrganizationRef [1..*]
        + description: xsd:string [0..1]
        + errors[]: Error [*]
        + permissions[]: Permission [1..*]                
        + requestedAt: xsd:dateTime         
        + requestedBy: OrganizationRef    
        + status: RequestStatus = PENDING
        + targetLogisticsObjects[]: LogisticsObjectRef [1..*]
    }
    DelegationRequest "1" --> "1..*" OrganizationRef : delegatedTo  
    DelegationRequest "1" --> "0..*" Error
    DelegationRequest --> Permission        
    DelegationRequest "1" --> "1..*" OrganizationRef : requestedBy
    DelegationRequest --> RequestStatus        
    DelegationRequest "1" --> "1..*" LogisticsObjectRef
    
    class Error{        
        + details[]: ErrorDetails [1..*]
        + title: xsd:string
    }
    Error "1" --> "*" ErrorDetails
    
    class ErrorDetails{
        + code: xsd:string
        + message: xsd:string [0..1]
        + property: xsd:anyURI [0..1]
        + resource: xsd:anyURI [0..1]
    }

    class LogisticsObjectRef{
        + logisticsObjectId: xsd:anyURI
        + logisticsObjectType: xsd:anyURI
    }

    class LogisticsObject{

    }

    class Notification{
        + changedProperties[]: xsd:anyURI [*]
        + changeRequest: ChangeRequest [0..1]
        + eventType: NotificationEventType
        + logisticsObject: LogisticsObject [0..1]
        + logisticsObjectRef: LogisticsObjectRef [0..1]
        + topic: xsd:anyURI
    }
    Notification "1" --> "0..1" ChangeRequest    
    Notification --> NotificationEventType
    Notification "1"--> "0..1" LogisticsObject
    Notification "1"--> "0..1" LogisticsObjectRef

    class Operation{
        + o: OperationObject
        + op: OperationEnum
        + p: xsd:anyURI
        + s: xsd:string
    }
    Operation "1" --> "1" OperationObject
    Operation --> OperationEnum

    class OperationObject{
        + datatype: xsd:anyURI
        + value: xsd:string   
    }

    class OrganizationRef{
        + organizationId: xsd:anyURI
        + organizationType: xsd:anyURI
    }

    class ServerInformation{
        + dataOwner: OrganizationRef
        + serverEndpoint: xsd:anyURI
        + supportedContentTypes[]: xsd:string [1..*]
        + supportedEncodings[]: xsd:string [*]
        + supportedLanguages[]: xsd:string [1..*]
        + supportedLogisticsObjectTypes[]: xsd:anyURI [1..*]
    }    
    ServerInformation "1" --> "1" OrganizationRef

    class Subscription{
        + callbackUrl: xsd:anyURI
        + contentTypes[]: xsd:string [*]
        + expiresAt: xsd:dateTime [0..1]                        
        + secret: xsd:string [0..1]
        + sendLogisticsObjectBody: xsd:boolean = FALSE
        + subscriber: OrganizationRef        
        + subscribeToLogisticsEvents: xsd:boolean = FALSE
        + topic: xsd:anyURI
    }    
    Subscription "1" --> "1" OrganizationRef

    class SubscriptionRequest{
        + description: xsd:string [0..1]
        + errors[]: Error [*]        
        + requestedAt: xsd:dateTime         
        + requestedBy: OrganizationRef    
        + status: RequestStatus = PENDING
        + subscription[]: Subscription [1..*]
    }       
    SubscriptionRequest "1" --> "1..*" OrganizationRef : requestedBy
    SubscriptionRequest --> RequestStatus        
    SubscriptionRequest "1" --> "1..*" Subscription

    class RequestStatus{
        <<Enumeration>>
        PENDING
        ACCEPTED
        REJECTED
    }

    class NotificationEventType{
        <<Enumeration>>
        OBJECT_CREATED
        OBJECT_UPDATED
        CHANGEREQUEST_PENDING
        CHANGEREQUEST_ACCEPTED                
        CHANGEREQUEST_REJECTED
        CHANGEREQUEST_FAILED
        EVENT_RECEIVED
    }
    class OperationEnum{
        <<Enumeration>>
        ADD
        DEL
    }
    class Permission{
        <<Enumeration>>
        GET
        PATCH
    }
    
```