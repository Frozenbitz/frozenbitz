---
title: Glossary for OPC UA 
description: A collection of usefull definitions and links to the standard reference when working with the current versions of OPC UA  
date: 2025-10-27 12:00:00 +0200
categories: [OPC UA]
tags: [opc ua]     # TAG names should always be lowercase
# image:
#   path: /assets/img/motra-maxi.png
#   lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAICAYAAADwdn+XAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADhSURBVChTdZBNSwJhFEbPK6Yi2rgRWggRFgli+UGj/RWZlWt/TBBE+9nV3o9foLQoSETQyVyEtByEILrgtBEX15lnee7h4eGaIAgCIuL7PsYYLMvSp31iGgDMvQ/anS6D4ZBef0C702XheVoDwIQtcF2X902a7+kLEKNwZVPO/OA4jlbDF4gIt8UTRqM3xuNX7NM8IqI1iCr43Wa4f3ymUmlQvb7h7uEJMVmtQVRBq1nDyqZYrtZ4n2tyxyladl1rEPWDPxFmiy8m0znJZILSxRmX5wUSR3GthhccgF2MBsA/Lo1L3mKMplQAAAAASUVORK5CYII=
---



### OPC UA Application

> _[Client](https://reference.opcfoundation.org/search/404?t=Client)_, which calls OPC UA _[Services](https://reference.opcfoundation.org/search/404?t=Services)_,_[](https://reference.opcfoundation.org/search/404?t=)_ or a _[Server](https://reference.opcfoundation.org/search/404?t=Server)_, which performs those _[Services](https://reference.opcfoundation.org/search/404?t=Services)_, or an OPC UA _[Publisher](https://reference.opcfoundation.org/search/404?t=Publisher)_ or an OPC UA _[Subscriber.](https://reference.opcfoundation.org/search/404?t=Subscriber.)_ [^1]

A more generic definition would be an application, that implements some of the models required by the standard (object model, service model, security model, ...) with respect to the communication profiles of part 7.


### OPC UA Services

The actual service definitions used to build a working server are mentioned at a number of different locations throughout the online reference. Generally speaking, while services can be part of the regular AddresSpace, there is a difference between the object model used to manage data and the service model used to issue requests to a server. The official references use different NodeSet schemata to create and parse services when building a custom API layer for a specific server.
This makes sense, since the service definitions (as part of the NodeSet2.xml) do not need to be used as part of the compilation step, when creating a custom application using the default namespace, companion specifications and custom extensions. Therefore having a separate set of definitions for setting up services to build APIs for servers does make sense.

> 4.2 Request/response Service procedures
> Request/response _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ procedures describe the processing of requests received by the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_, and the subsequent return of responses. The procedures begin with the requesting _[Client](https://reference.opcfoundation.org/search/407?t=Client)_ submitting a _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ request _[Message](https://reference.opcfoundation.org/search/407?t=Message)_ to the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_.
> Upon receipt of the request, the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_ processes the _[Message](https://reference.opcfoundation.org/search/407?t=Message)_ in two steps. In the first step, it attempts to decode and locate the _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ to execute. [...]
> If it succeeds, then it attempts to access each operation identified in the request and perform the requested operation. [...]
> To perform these operations, both the _[Client](https://reference.opcfoundation.org/search/407?t=Client)_ and the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_ may make use of the API of a _[Communication](https://reference.opcfoundation.org/search/407?t=Communication)_ _[Stack](https://reference.opcfoundation.org/search/407?t=Stack)_ to construct and interpret _[Messages](https://reference.opcfoundation.org/search/407?t=Messages)_ and to access the requested operation.[^2]

The two steps involved are locating a service on a server and invoking a specific operation as part of the service. Keep in mind, Services are abstract definitions. The standard defines multiple service sets, which are groups of related services, that can be part of a specific OPC UA implementation for a server. It is up to the library provider to implement these services. 

> Whether or not a _[Server](https://reference.opcfoundation.org/search/17?t=Server)_ supports a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ _[Set](https://reference.opcfoundation.org/search/17?t=Set)_, or a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ within a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ _[Set](https://reference.opcfoundation.org/search/17?t=Set)_, is defined by its _[Profile](https://reference.opcfoundation.org/search/17?t=Profile)_. _[Profiles](https://reference.opcfoundation.org/search/17?t=Profiles)_ are described in [OPC 10000-7](https://reference.opcfoundation.org/Core/Part4/v104/docs/?r=UAPart7).[^3]

A library would implement a basic set of services to support specific functions on a server, called Profiles. Profiles are groups of well known functions a server needs to support to work with other OPC UA applications. Profile definitions can be discovered by clients to determine what API is supported by the target server. Annex A covers the definitions for the NodeIDs and in our case the default NodeSet2.xml as well as the NodeSet2.Service.xml. Both of these are required when interacting with an OPC UA application since these provide the "well known" IDs required to parse a custom Namespace. 

> Clause [A.3](https://reference.opcfoundation.org/Core/Part6/v105/docs/?r=_Ref33903711) defines the numeric identifiers for all of the numeric _[NodeIds](https://reference.opcfoundation.org/search/409?t=NodeIds)_ defined by the OPC UA Specification. The identifiers are specified in a UTF8 encoded CSV file. The syntax is informative and provided to assist developers of tools. The _[UANodeSet](https://reference.opcfoundation.org/search/409?t=UANodeSet)_ is the normative source for all identifiers.[^5]

The current files and schemata to map between well known Node IDs and Services can be found on GitHub and the reference page of the Foundation: 

> The NamespaceUri for all NodeIds defined is http://opcfoundation.org/UA/
> The CSV released with this version of the standards can be found here:
> https://reference.opcfoundation.org/files/NodeIds.csv?u=http://opcfoundation.org/UA/&v=1.05.04 [^5]

This second aspect of the appendix B[^6] is quite important for our use case and solves the issue of how to decode Request and Response Messages to a server: 

> The complete Information Model Schema includes many types which are only used in _[Service](https://reference.opcfoundation.org/search/409?t=Service)_ _[Requests](https://reference.opcfoundation.org/search/409?t=Requests)_ and _[Responses](https://reference.opcfoundation.org/search/409?t=Responses)_ and should not be used by _[Servers](https://reference.opcfoundation.org/search/409?t=Servers)_ to populate their _[Address Space](https://reference.opcfoundation.org/search/409?t=Address%20Space)_.

and

> OPC UA provides a consistent, integrated _[AddressSpace](https://reference.opcfoundation.org/search/404?t=AddressSpace)_ and service model. [^7]

OPC UA makes a distinction between the different models. While the AddressSpace is used to create and manage the object model, the service model is a separate entity within the server.

### Extension Object or Prefix

This Object is part of the OPC UA message structure and required to to issue service requests and responses. The communication model for client and server is built around the abstract service definitions which are mentioned in part 4 of the standard documents. To trigger any service, we need to tell the server what service we need to invoke. When checking the message structure this is not immediately obvious how this would work, since we need to pass a specific NodeID to the server, corresponding to a service definition.
The extension object or prefix is used as part of the message body to locate a service and pass the request/response data as the rest of the body. 

### Protocol Layers - Protocol Bindings 

OPC UA operates on top of TCP with respect to the OSI layer model. Therefore OPC UA should be considered a typical application protocol. However, OPC UA also defines a set of custom layers when targeting industrial applications, which can be used to create devices for very specific use cases. UA Automation has a great overview that shows the different layers that can be used to create custom OPC applications[^8]

![General Overview to the OPC UA Layer Model by UA Automation](/assets/img/UAAuto_Overview.png)
_General Overview to the different OPC UA layer models by [UA Automation](https://documentation.unified-automation.com/uagds/1.0.1/html/L2OpcUaFundamentalsOverview.html)_

The base layer, here called protocol bindings, shows the different options that are currently supported for using OPC UA. Depending on the type of application and the communication requirements different transport protocols and encodings can be used to pass information between communicating parties.

> This document defines _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ between the abstract specifications and technologies that can be used to implement them. The _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ are organized into three groups: _[DataEncodings, SecurityProtocols](https://reference.opcfoundation.org/search/409?t=DataEncodings,%20SecurityProtocols)_ and _[TransportProtocols](https://reference.opcfoundation.org/search/409?t=TransportProtocols)_. Different _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ are combined together to create _[StackProfiles](https://reference.opcfoundation.org/search/409?t=StackProfiles)_. All OPC UA applications shall implement at least one _[StackProfile](https://reference.opcfoundation.org/search/409?t=StackProfile)_ and can only communicate with other OPC UA applications that implement the same _[StackProfile.](https://reference.opcfoundation.org/search/409?t=StackProfile.)_[^4]

#### UACP - OPC UA Communication Protocol

the opc ua transport layer. the communication protocol is used for a few things here: manage the custom transport size between UASC and custom hardware. since opc is not limited to desktop hardware, this mechanism can be used to determine different transport options for embedded or high performance hardware. 

uacp has different functions: manage transmit and receive buffers on both ends, create a duplex channel, create reverse connection in recent changes, handle message chunks.

the main header for the transport level protocol is made up of three fields: a message type that also supports dual use for UASC, a reserved flag, that is not used by this layer and a message size field. 

there is a level of overlap between uasc and uacp. this overlap is used as part of the secure handshake between two nodes to create a secured session. uasc defines custom message types, which should be understood by the uacp layer so the stack can forward messages between layers. 


### tbd. 

- UASC - OPC UA Secure Communication
- communication architecture
  - pubsub
  - request response
  - events and notifications, how are these different to pubsub?
- extensions
  - companion specifications
- global services

[^1]: https://reference.opcfoundation.org/Core/Part1/v105/docs/2.1.30
[^2]: https://reference.opcfoundation.org/Core/Part4/v105/docs/4.2
[^3]: https://reference.opcfoundation.org/Core/Part4/v104/docs/5
[^4]: https://reference.opcfoundation.org/Core/Part6/v105/docs/4
[^5]: https://reference.opcfoundation.org/Core/Part6/v105/docs/A.3#_Ref294144163
[^6]: https://reference.opcfoundation.org/Core/Part6/v105/docs/B
[^7]: https://reference.opcfoundation.org/Core/Part1/v105/docs/4.3
[^8]: https://documentation.unified-automation.com/uagds/1.0.1/html/L2OpcUaFundamentalsOverview.html
