---
title: Scapy & OPC - Understanding OPC UA
description: Understanding how the basic OPC UA protocol works and how we can create a decoder for Scapy.
date: 2025-10-29 12:00:00 +0200
categories: [MOTRA]
tags: [scapy, opc ua]     # TAG names should always be lowercase
image:
  path: /assets/img/scapy-motra.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAANCAYAAACzbK7QAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAALSSURBVDhPfZSxaxtnGMZ/p1NOd7YsGzlJT65U22noVuhSDIZC1KGUdgiUdGkDpu7Sv6BDKGQqJQSCl5J08BAytBgKIdnaThmyxUtbIieR2liW7VPU89nyne7z6e7tYHqOpNAHvuV9n+f98fF+fJqICENqt9vkcjmCIMB1XTzPwzRNCoUClmVRLpeHI9TrdYIgYP7cOfLj42ldexVgZWWFWq2GbduUSiXGxsYIwxDXdWk0GiwvL7OwsJD69/b2+O7Wj2Qk4eML7/Le4kkvBcRxjFIKwzBwHIcoigBwHAfbtslms/i+z9TUFP1+n2KxiGmaAGy1Wly9dpvnm5t89cUnXLr4wSjAdV1qtRoAURRRqVRoNpuICBMTE4gISimUUliWhaZpdLtdqtUquq5z/fufeLzR4OrXXzJXsUcBqQIPkj7kTw+UNzY22N3dJZfLMTc3h22fDIkkwos8iqem0bXMQG4Q0NtHwgPQDbSMPgIZliDc2b7NUaKYMcu0wi0+L11mTH/FkqXxEOl2eJx9kziOeXv2DFrWgPHp1NxsNpmZmcEXn/WDR/zp/4EbuVyZ/wYNjb96DZ4GT/jw9EdpJr1Psv4z/ScPmJycxLIsImsaOXCOmxLjOA5LS0usrq5SyBa4UKxSLb7PWeMsuqajRPGb+ythotLhx9n/FHYl3lyXTueF7OzsSLT1u0gciajN45OEsra2Jr1eL42IiKw8vyH32/dERMQ9cuVe++5A/2QjuTyZyjsUj9q8pu2TPWOD/wAvsNju6BB1+PTSxfRpqkTxw9ZN/NhncWrxuCaKrHYqHcnIkgFEIInh8BeSF3f4O/Mtnudx/vw8BfMQjEpqbakWz4Kn1INnzFqz1IM6n5Uuk9fzqWcU8LKiHfa7fXpKp5g/xJh4a9hBmIS0jxy2wxYV8w1eNwe/kf8HAPQ7kPhgzA53AIiSCLf/D37sY2g5ykOAfwFAppOgLiS9NgAAAABJRU5ErkJggg==
---

## Message Structure

The main message structure to create OPC UA data to be sent or received over a network, at least if we are using the OPC built in transport and security protocol, will be following this structure mentioned in the docs: 

[https://reference.opcfoundation.org/Core/Part6/v105/docs/6.7.2.2](https://reference.opcfoundation.org/Core/Part6/v105/docs/6.7.2.2)

When building the initial layers for Scapy, we will be referencing this document and subsequent types a lot.

## Understanding the Message Structure

First we want to look at the main representation of our protocol. While the OPC UA protocol defines it's own transport and security settings, downstream we have a number of services and messages that are kind of independent of the transport protocol. While there is some level of overlap, we first want to get familiar with the the general structure of messages, which we need to actually decode and build stuff.

From [5.2.9 Messages](https://reference.opcfoundation.org/Core/Part6/v105/docs/5.2.9)
> _[Messages](https://reference.opcfoundation.org/search/409?t=Messages)_ are _[Structures](https://reference.opcfoundation.org/search/409?t=Structures)_ encoded as sequence of bytes prefixed by the _[NodeId](https://reference.opcfoundation.org/search/409?t=NodeId)_ of for the OPC UA Binary _[DataTypeEncoding](https://reference.opcfoundation.org/search/409?t=DataTypeEncoding)_ defined for the Message.
> 
> Each OPC UA _[Service](https://reference.opcfoundation.org/search/409?t=Service)_ described in [OPC 10000-4](https://reference.opcfoundation.org/Core/Part6/v105/docs/?r=UAPart4) has a request and response _[Message](https://reference.opcfoundation.org/search/409?t=Message)_. The _[DataTypeEncoding](https://reference.opcfoundation.org/search/409?t=DataTypeEncoding)_ IDs assigned to each _[Service](https://reference.opcfoundation.org/search/409?t=Service)_ are specified in Clause [A.3](https://reference.opcfoundation.org/Core/Part6/v105/docs/?r=_Ref294144163).

The first part here is *very* important: Messages are Structures encoded as sequence of bytes **prefixed by the NodeId** (also called Prefix or ExtensionObject Prefix in the docs). We can observe this as part of the Wireshark capture when using the OPC UA dissector:


![Wireshark View of an OPC Header](/assets/img/wireshark-cap-opc-header.png){:.w-80 .normal}


Each request to the server (aka. a single Message) is prefixed with a NodeID. However, this prefix representation can change drastically depending on the use case! Also this prefix doesn't get mentioned too often as part of the official reference and can easily get forgotten when working on the protocol. The core documentation does explain how a NodeID contains a number of components [here](https://reference.opcfoundation.org/Core/Part6/v105/docs/5.2.2.9). These are typically the components found when working on a server locally and would be used when building server side implementations to interact with a local data source or any local processes. However, when working with data over the wire we need to respect the *DataEncoding* OPC UA uses.

> The _[DataEncoding](https://reference.opcfoundation.org/search/409?t=DataEncoding)_ of a _[NodeId](https://reference.opcfoundation.org/search/409?t=NodeId)_ varies according to the contents of the instance. For that reason, the first byte of the encoded form indicates the format of the rest of the encoded _[NodeId](https://reference.opcfoundation.org/search/409?t=NodeId)_.[^11]

For this reason, [Table 16 of 5.2.2.9](https://reference.opcfoundation.org/Core/Part6/v105/docs/?r=_Ref105731689) shows us how to interpret the prefix and the encoded values. Currently there are eight different encoding options, but we will likely only need the second one: the four byte representation. For the Prefix, there is no two byte option available, since the service ID cannot be represented using the two bytes available for NS ID and Target Node ID. The four byte representation has the encoding byte in the front, next the namespace ID (0-254) and finally a two byte field for the actual numerical identifier inside the server namespace. For our servers , when using the default configuration, this will be the option we see most often. I believe technically we could use another encoding here, but i found very little in the documentation so far, whether this was technically supported or not.  

```
+---------------+---------------+------------------------------+
| Encoding Byte | Namespace ID  |       Target Node ID         |
|     Byte      |     Byte      |           Short              |
|     0x01      |     0x00      |           0x1234             |
+---------------+---------------+------------------------------+
```

## Understanding the Service Interface

Next we need additional information on the requested service. As we can see from the Wireshark dissect, the client is requesting a "OpenSecureChannelRequest". This request is part of the service of a specific server and requires some intermediary steps to be executed.

> 4.2 Request/response Service procedures
> Request/response _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ procedures describe the processing of requests received by the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_, and the subsequent return of responses. The procedures begin with the requesting _[Client](https://reference.opcfoundation.org/search/407?t=Client)_ submitting a _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ request _[Message](https://reference.opcfoundation.org/search/407?t=Message)_ to the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_.
> Upon receipt of the request, the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_ processes the _[Message](https://reference.opcfoundation.org/search/407?t=Message)_ in two steps. In the first step, it attempts to decode and locate the _[Service](https://reference.opcfoundation.org/search/407?t=Service)_ to execute. [...]
> If it succeeds, then it attempts to access each operation identified in the request and perform the requested operation. [...]
> To perform these operations, both the _[Client](https://reference.opcfoundation.org/search/407?t=Client)_ and the _[Server](https://reference.opcfoundation.org/search/407?t=Server)_ may make use of the API of a _[Communication](https://reference.opcfoundation.org/search/407?t=Communication)_ _[Stack](https://reference.opcfoundation.org/search/407?t=Stack)_ to construct and interpret _[Messages](https://reference.opcfoundation.org/search/407?t=Messages)_ and to access the requested operation.[^10]

The two steps involved are decoding/locating a service on a server and invoking a specific operation as part of the service. Keep in mind, Services are abstract definitions. The standard defines multiple service sets, which are groups of related services, that can be part of a specific OPC UA implementation for a server. It is up to the library provider to implement these services. Usually a library would then implement a known set of services to support specific methods for a server targeting some industry or application. These sets of services are called profiles in OPC terms and are known functions a server has to support to allow for reliable communication across systems. The profiles are less important for us, since the profiles are involved as part of the certification and testing procedures. Profiles define what API a server or application should implement. This information can be queried and used to determine which services/requests an application supports to some. Which is mostly interesting to a client trying to connect.

> Whether or not a _[Server](https://reference.opcfoundation.org/search/17?t=Server)_ supports a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ _[Set](https://reference.opcfoundation.org/search/17?t=Set)_, or a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ within a _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ _[Set](https://reference.opcfoundation.org/search/17?t=Set)_, is defined by its _[Profile](https://reference.opcfoundation.org/search/17?t=Profile)_. _[Profiles](https://reference.opcfoundation.org/search/17?t=Profiles)_ are described in [OPC 10000-7](https://reference.opcfoundation.org/Core/Part4/v104/docs/?r=UAPart7).[^15]

... and ...

> This document defines _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ between the abstract specifications and technologies that can be used to implement them. The _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ are organized into three groups: _[DataEncodings, SecurityProtocols](https://reference.opcfoundation.org/search/409?t=DataEncodings,%20SecurityProtocols)_ and _[TransportProtocols](https://reference.opcfoundation.org/search/409?t=TransportProtocols)_. Different _[Mappings](https://reference.opcfoundation.org/search/409?t=Mappings)_ are combined together to create _[StackProfiles](https://reference.opcfoundation.org/search/409?t=StackProfiles)_. All OPC UA applications shall implement at least one _[StackProfile](https://reference.opcfoundation.org/search/409?t=StackProfile)_ and can only communicate with other OPC UA applications that implement the same _[StackProfile.](https://reference.opcfoundation.org/search/409?t=StackProfile.)_[^18]

Annex A covers the definitions for the NodeIDs and in our case the default NodeSet2.xml as well as the NodeSet2.Service.xml. Both of these are required when interacting with an OPC UA application since these provide the "well known" IDs required to parse a custom Namespace that is built on top of existing definitions (keyword here is: companion specification). 

> Clause [A.3](https://reference.opcfoundation.org/Core/Part6/v105/docs/?r=_Ref33903711) defines the numeric identifiers for all of the numeric _[NodeIds](https://reference.opcfoundation.org/search/409?t=NodeIds)_ defined by the OPC UA Specification. The identifiers are specified in a UTF8 encoded CSV file. The syntax is informative and provided to assist developers of tools. The _[UANodeSet](https://reference.opcfoundation.org/search/409?t=UANodeSet)_ is the normative source for all identifiers.[^9]

The current files and schemata to map between well known IDs and Nodes/Services can be found on GitHub as well as the reference page of the OPC Foundation: 

> The NamespaceUri for all NodeIds defined is http://opcfoundation.org/UA/
> The CSV released with this version of the standards can be found here:
> https://reference.opcfoundation.org/files/NodeIds.csv?u=http://opcfoundation.org/UA/&v=1.05.04 [^9]

This second aspect of the appendix B [^19] is quite important for our use case and solves the issue of how to decode the OpenSecureChannelRequest Message: 

> The complete Information Model Schema includes many types which are only used in _[Service](https://reference.opcfoundation.org/search/409?t=Service)_ _[Requests](https://reference.opcfoundation.org/search/409?t=Requests)_ and _[Responses](https://reference.opcfoundation.org/search/409?t=Responses)_ and should not be used by _[Servers](https://reference.opcfoundation.org/search/409?t=Servers)_ to populate their _[Address Space](https://reference.opcfoundation.org/search/409?t=Address%20Space)_.

Also interesting:
> OPC UA provides a consistent, integrated _[AddressSpace](https://reference.opcfoundation.org/search/404?t=AddressSpace)_ and service model. [^21]

There is a distinction between the service model and the regular AddressSpace.

## Working with NodeSets

There are generally two versions of the NodeSet files referenced by the standard, which is quite interesting. We have one version that covers the creation of the AddressSpace of a server or rather an OPC UA application and we have an additional file to be used to generate Service structures for generic tools. These are the mappings we are interested in, since these IDs are required to parse the Request and Response headers for the different services for all requests to the server instances.
As we can see from the original files on GitHub and the Service documentation, the entire structure of the protocol fields is encoded as part of the request structure inside the service reference schema[^12] which itself is a custom NodeSet2.xml file containing all the additional service nodes. As annex B states the usual NodeSet2.xml can be used to populate an address space and should therefore be used when building information models or compiling companion specifications. The additional NodeIDs provided by the NodeSet2.Service.xml can be used to build tooling to interact with services.

Regarding the OpenSecureChannelRequest, an encoded message header would look like this: 

```
  <UADataType NodeId="i=444" BrowseName="OpenSecureChannelRequest" Purpose="ServicesOnly">
    <DisplayName>OpenSecureChannelRequest</DisplayName>
    <References>
      <Reference ReferenceType="HasSubtype" IsForward="false">i=22</Reference>
    </References>
    <Definition Name="OpenSecureChannelRequest">
      <Field Name="RequestHeader" DataType="i=389" />
      <Field Name="ClientProtocolVersion" DataType="i=7" />
      <Field Name="RequestType" DataType="i=315" />
      <Field Name="SecurityMode" DataType="i=302" />
      <Field Name="ClientNonce" DataType="i=15" />
      <Field Name="RequestedLifetime" DataType="i=7" />
    </Definition>
  </UADataType>
```
{: file='NodeSet2.Services.xml'}

If we check the current Wireshark capture this checks out. We send a four-byte encoded NodeID to the server and append a Message containing the all the required fields for a *OpenSecureChannelRequest* as stated in the Service definition.


![Wireshark View of a SecureChannelRequest](/assets/img/wireshark-opc-secchannelreq.png){:.w-80 .normal}


If we use the different IDs for the Services we are interested in, we should be able to dissect most of the messages required to interact with the namespace of our servers. For the IDs we need to match the request and response types, as well as the required headers, as defined by the standard: 

> Each _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ request has a _[RequestHeader](https://reference.opcfoundation.org/search/17?t=RequestHeader)_ and each _[Service](https://reference.opcfoundation.org/search/17?t=Service)_ response has a _[ResponseHeader](https://reference.opcfoundation.org/search/17?t=ResponseHeader)_.[^16]

The request and response headers are generic structures that hold metadata needed for multiple different services. In addition to the different datatypes and requests from the client, these headers hold metadata that is usually shared between different services. As each service defines specific request and response headers, we also find Request and Response definitions for each service as part of the Nodeset2.Service.xml file. All Services provided by the core specification document 4 part 5[^15] provide a Node definition as well as specific request and response headers. 

## What's next

To summarize for our SCAPY decoding trip so far, we require: 
- A custom layer for the main binary protocol and the different message types 
- A custom layer for the encoded objects, which holds the encoded NodeID that has been requested
- A specific service request, containing all the headers and fields to call a specific service on a OPC UA application

With all of these in place, we should be able to dissect and interact with a OPC UA application using the default OPC Binary protocol.

 
## References


[^1]: https://www.reddit.com/r/learnpython/comments/1asbqfd/making_all_you_projects_an_editable_installable/
[^2]: https://codeberg.org/buhtz/tech-demo-python-packaging
[^3]: https://scapy.readthedocs.io/en/latest/development.html
[^4]: https://python-basics-tutorial.readthedocs.io/de/latest/test/tox.html
[^5]: https://codeberg.org/buhtz/tech-demo-python-packaging#demo-01-variant-setuptools
[^6]: https://github.com/claroty/opcua-exploit-framework/tree/main
[^7]: https://claroty.com/team82/research/opc-ua-deep-dive-a-complete-guide-to-the-opc-ua-attack-surface
[^8]: https://claroty.com/team82/research/team82-releases-homegrown-opc-ua-network-fuzzer-based-on-boofuzz
[^9]: https://reference.opcfoundation.org/Core/Part6/v105/docs/A.3#_Ref294144163
[^10]: https://reference.opcfoundation.org/Core/Part4/v105/docs/4.2
[^11]: https://reference.opcfoundation.org/Core/Part6/v105/docs/5.2.2.9
[^12]: https://github.com/OPCFoundation/UA-Nodeset/blob/UA-1.05.04-2025-01-08/Schema/Opc.Ua.NodeSet2.Services.xml
[^13]: https://reference.opcfoundation.org/Core/Part6/v104/docs/7
[^14]: https://reference.opcfoundation.org/Core/Part6/v104/docs/7.1.3
[^15]: https://reference.opcfoundation.org/Core/Part4/v104/docs/5
[^16]: https://reference.opcfoundation.org/Core/Part4/v104/docs/5.2
[^17]: https://reference.opcfoundation.org/Core/Part4/v104/docs/5.3
[^18]: https://reference.opcfoundation.org/Core/Part6/v105/docs/4
[^19]: https://reference.opcfoundation.org/Core/Part6/v105/docs/B
[^20]: https://claroty.com/team82/research/opc-ua-deep-dive-series-part-7-practical-denial-of-service-attacks
[^21]: https://reference.opcfoundation.org/Core/Part1/v105/docs/4.3
