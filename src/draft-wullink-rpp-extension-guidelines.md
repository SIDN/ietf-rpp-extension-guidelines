%%%
title = "Guidelines for Extending the RESTful Provisioning Protocol (RPP)"
abbrev = "RPP Extension Guidelines"
area = "Internet"
workgroup = "Network Working Group"
submissiontype = "IETF"
keyword = [""]
TocDepth = 4
date = 2026-11-02

[seriesInfo]
name = "Internet-Draft"
value = "draft-wullink-rpp-extension-guidelines-00"
stream = "IETF"
status = "bcp"

[[author]]
initials="M."
surname="Wullink"
fullname="Maarten Wullink"
abbrev = ""
organization = "SIDN Labs"
  [author.address]
  email = "maarten.wullink@sidn.nl"
  uri = "https://sidn.nl/"

[[author]]
initials="P."
surname="Kowalik"
fullname="Pawel Kowalik"
abbrev = ""
organization = "DENIC"
  [author.address]
  email = "pawel.kowalik@denic.de"
  uri = "https://denic.de/"

%%%

.# Abstract

The RESTful Provisioning Protocol (RPP) defines a base set of data objects, component objects, and operations for provisioning objects. This document defines guidelines and a normative mechanism for extension specifications that add data elements, associations, operations, result codes, or other functionality, without modifying the base specifications itself.

{mainmatter}

# Introduction

The RESTful Provisioning Protocol (RPP), as defined in [@!I-D.ietf-rpp-core], [@!I-D.ietf-rpp-data-objects], and [@!I-D.ietf-rpp-json], provides a base set of data objects, component objects, and operations for the provisioning of registry data. Deployments may need to add functionality beyond this base set, for example to support policies specific to a registry, a jurisdiction, or a class of registered objects. RPP is designed to accommodate such needs through extension specifications, so that additional functionality can be introduced without modifying the base specification that defines the object or operation being extended.

Because independently developed extension specifications may be deployed together, and because RPP relies on JSON Schema, as defined in [@!I-D.ietf-rpp-json], to validate protocol messages, extensions need to follow a common, predictable pattern. Without such a pattern, extensions that are individually valid could conflict when combined at a given deployment, or could require ad-hoc, per-combination schema and code to be written for every deployment profile. This document defines that common pattern.

This document identifies the set of extension points along which an RPP base specification MAY be extended, including new data objects, new data elements on existing objects, new operations, new operation inputs and outputs, external data types, new RPP result codes, error object extension fields, additional query parameters, and additions to the RPP Discovery document. For each extension point, this document states the applicable registration requirements.

This document further discusses considerations for client and server implementations that need to compose, validate, and process RPP messages that may include data contributed by more than one extension.

An extension specification MAY be published as an Internet-Draft and RFC when the extension requires coordination, interoperability and broad distribution. The intended status of such a document depends on what is extended and on the amount of general interest in the extension. An extension that is intended for a closed environment or a limited audience does not need to be published, and MAY be documented in a form that is appropriate for its audience. Such a private extension SHOULD still follow the structure and the rules in this document, so that it can be combined with other extensions at a deployment.

This document does not itself define any RPP extension. It defines the rules that extension specifications, and the deployments that support them, are expected to follow.

**TODO: Make sure there is no normative language in the sections below that should be moved to the core specifications**

# Terminology

In this document the following terminology is used.

URL - A Uniform Resource Locator as defined in [@!RFC3986].

Terms of the RPP data model, such as Data Object, Component Object, Process Object, Common Data Type, data element, cardinality, mutability, Identifier, Unique Identifier, Input Data Element, Parameter, Visibility, Direct Access, Input, Output, Reference, Owner Data Object, and This Object, are used as defined in [@!I-D.ietf-rpp-data-objects].

Consumer - A server or client that generates, processes or validates RPP messages.

Discovery document - The JSON document, published by an RPP server at a well-known location, that describes the capabilities of the server, including the extensions it supports, as defined in [@!I-D.ietf-rpp-core].

JSON Schema - A vocabulary for describing and validating JSON documents, as defined in [@JSON-SCHEMA]. This document uses JSON Schema draft 2020-12.

Extension Mapper (`rpp:extends`) - The property of an extension JSON Schema that maps the reference of an existing object definition to the local definition in the extension schema that contributes the new properties, as defined in [@!I-D.ietf-rpp-json].

Effective schema - The JSON Schema that is composed, for one base object definition, from that definition and the definitions of all extensions that a deployment supports, using the `rpp:extends` mappings of the extension schemas. The effective schema has its own `$id`. The effective schema is also referred to as the combined schema.

# Conventions Used in This Document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [@!RFC2119].

In examples, indentation and white space are provided only to illustrate element relationships and are not REQUIRED features of the protocol.

# Restrictions on Extensions

An extension MUST NOT remove, redefine, or otherwise alter the semantics of a property defined by the RPP base specification.
An extension MUST only add new properties or associations, and MUST ensure that any new elements do not conflict with existing ones in the base specification.

# RPP Extension Points {#rpp-extension-points}

An extension specification can extend RPP along one or more of the following independent extension points. Each extension point may have its own registration requirements.

## Component Objects

Extensions may define shared components that can be reused across multiple data objects, operations, or other extension points.

## Data Objects

Extensions may define entirely new data objects or extend an existing data object defined in the base specification or in another extension specification.

## Process Objects

Extensions may define entirely new process objects or extend existing ones defined in the base specification or in another extension specification.

### Input Data

Extensions may define new input data elements for an existing or new process object; these input data elements MUST be mapped to corresponding JSON Schema properties.

## Data Elements

Extensions may add new data elements (properties) to an existing data object, component object, or process object defined in a base specification.

## Operations

Extensions may define entirely new operations on existing or new data objects, or extend existing operations with additional inputs, outputs, or behaviour.

### Inputs and Outputs

Extensions may define both input and output data elements for operations; parameters are restricted to operation inputs only.

## External Data Types

Instead of adding properties to an object, an extension may reference a type defined entirely in an external specification (e.g. `External:RPP-JSContact-Profile:Card`), as described in [@!I-D.ietf-rpp-data-objects]. This is the appropriate mechanism when substituting or supplying a whole, independently versioned data type, rather than adding fields to an existing RPP-defined object.

## HTTP Endpoints

As defined in [@!I-D.ietf-rpp-core], every endpoint URL and HTTP method is derived mechanically from a Data Object's properties; no endpoint is defined independently of a corresponding Data Object. Consequently:

* A new Data Object automatically obtains a collection endpoint once its `"Identifier"` is registered.
* A new association with Direct Access set to `true` automatically obtains a sub-resource path, derived recursively from the parent resource's path.
* A new operation automatically obtains its HTTP method and URL path once its `"Identifier"` is registered.

## HTTP Query Parameters

As defined in [@!I-D.ietf-rpp-core], every HTTP query parameter is derived mechanically from an operation parameter; no HTTP query parameter is defined independently of a corresponding operation parameter. Consequently, any transient parameter for a read, query, update, and delete operation MUST be translated into an HTTP query parameter, named by the operation's parameter identifier.

## HTTP Headers

Extensions may define new HTTP headers for existing operations, as described in [@!I-D.ietf-rpp-core]. Any new headers MUST be registered in the appropriate IANA registry.

## Result Codes

Extensions may define new RPP result codes within the reserved result code classes. New result codes MUST be registered in the RPP Result Codes registry defined in [@!I-D.ietf-rpp-core] and the extension specification MUST include a mapping of the new result codes to their corresponding HTTP status codes.

## Problem Details

Extensions may add new fields to the Problem Detail error object to convey additional, extension-specific information about the cause of an error, as described in [@!I-D.ietf-rpp-core].

## Discovery

A server that supports one or more extensions MUST publish every supported extension in the `extensions` list of its RPP Discovery document, as defined in [@!I-D.ietf-rpp-core]. Each entry MUST contain the `name`, `id`, `version` and `url` of the extension, using the name, identifier and version defined in the Extension Overview section of the extension specification.

A client MUST NOT use an extension that is not listed in the Discovery document of the server. A server MUST reject a request that uses an extension that is not listed in its Discovery document with the RPP result code 02103 (Unimplemented extension) defined in [@!I-D.ietf-rpp-core], and MUST NOT include data of an unlisted extension in its responses. A server MAY restrict the use of a listed extension to specific clients, based on the identity and the authorisations of the client. The Discovery document is available without authentication, so a client can find extensions listed that it is not authorised to use. A server MUST reject a request from an unauthorised client for such an extension with an authorisation error.

New properties added by an extension to the RPP Discovery document MUST be included in the appropriate section of the document, following the structure defined in [@!I-D.ietf-rpp-core].

# Selecting an Extension Point {#selecting-extension-point}

An extension designer MUST use the simplest extension point that fulfils the requirement. The forms below are listed in order of increasing complexity and of increasing impact on implementations. A designer SHOULD only move to the next form when the answer to the question of the current form is "no".

1. Reuse an existing extension point of a base specification. The base specifications define open-ended elements to which new values can be added without adding new properties, for example the method of the authorisation information object, the labels of status objects, the labels of labelled and dictionary associations (such as the roles of the contacts of a domain name), and the keys of dictionary data types. Question: can the requirement be met by new values of such an element?
2. Add data elements to an existing object, or add transient data elements to the input or output of an existing operation, as described in (#extending-json-schema). Question: can the requirement be met by adding to an existing object or operation?
3. Define a new data object. A new data object SHOULD differ significantly from existing objects. If a new object replicates a significant number of the data elements of an existing object, the question of the previous form was probably answered incorrectly. Question: can the requirement be met by applying the uniform interface operations to a new object?
4. Define a new operation, or a new process object for a long-running or multi-step operation. Question: can the requirement be met by new operations on new or existing objects?
5. Add protocol-level elements, such as new HTTP headers, query parameters, result codes, Problem Detail fields or Discovery document properties. These elements apply independent of an object, and have the largest impact on implementations.

Before defining an extension, a designer SHOULD check the IANA RPP Extensions registry, the RPP Data Object Registry and the published extension specifications for an existing extension that provides the same function, and SHOULD reuse it. Multiple extensions that realise the same function fragment the ecosystem, and SHOULD be avoided.

# Identifying Extensions {#identifying-extensions}

Every extension must have a unique identifier, which is published in the `id` field of the extension entry in the Discovery document.

* The identifier of a standardised extension MUST be a URN, using the URN sub-namespace defined in [@!I-D.ietf-rpp-core], of the form `urn:ietf:params:rpp:extension:<name>`, and MUST be registered as described in (#iana-registries).
* The identifier of a private extension MUST be a globally unique URI, MUST NOT use the RPP URN sub-namespace, and SHOULD be based on a domain name that is controlled by the party that defines the extension.
* The version of an extension is published in the `version` field. The identifier of an extension SHOULD NOT contain the version, so that the identifier remains stable when the extension is revised.

**TODO: check if guidance is consistent with core spec. This must be aligned.**

# JSON Schema {#extending-json-schema}

This section defines the normative method an extension specification MUST follow when it extends the JSON Schema defined in a base specification. JSON Schema validation, as defined in [@!I-D.ietf-rpp-json], MUST keep functioning correctly when combining the base schema with additional schemas defined by one or more new extensions.

## Composition

Every data object defined in a base specification, when modified by an extension specification, must be extended using an extension specification. The extension specification MUST define a new JSON Schema document that composes the base object's schema with the new properties using `allOf`. The extension specification MUST NOT copy, redefine, or otherwise restate the JSON Schema definition of the base object. Instead, it MUST reference the base object's definition by its fully qualified `$id`-relative pointer (e.g. `https://www.iana.org/.../domainName.json#/$defs/domainName`) in an `allOf` branch, and add its own new properties in a separate branch.

An extension schema MUST declare which base object definitions it extends using the Extension Mapper (`rpp:extends`) property, as defined in [@!I-D.ietf-rpp-json]. The property maps the fully qualified reference of each base object definition to the local definition, within the extension schema, that contributes the new properties. This mapping allows an implementation to construct, for a given base object, the effective schema that combines the base definition with every supported extension, without any knowledge of the extensions in advance.

## Identification

A specification that defines JSON Schema for RPP objects MUST assign a stable `$id` to its schema document(s). Extension specifications MUST reference a base object's definition by its `$id` (e.g. `https://www.iana.org/.../domainName.json#/$defs/domainName`) rather than copying the base definition into their own schema.

## Independence of Extensions

Extensions MUST be independent of one another. An extension MUST NOT extend, change or otherwise depend on a definition that is introduced by another extension, such as a data object, data element or operation. The Extension Mapper (`rpp:extends`) property of an extension schema MUST only reference definitions of base specifications, and an extension MUST NOT require that another extension is supported.

When the functionality of an existing extension needs to be extended, a new version of that extension MUST be defined instead of a second extension that builds upon the first one. The new version keeps the identifier of the extension and updates the `version`, as described in (#identifying-extensions). A server publishes the version of the extension that it supports in its Discovery document.

## Consumer Implementation

A consumer, which can be a server or client, does not need to generate a distinct set of code classes for every base object and extension combination offered by a particular server. Because extension schemas are additive, self-contained, and independent of one another, a consumer implementation SHOULD instead maintain one set of code classes for each base object and a separate set of code classes for each extension it supports, keeping an extension's classes distinct from the base object's classes rather than flattening their properties together. Composing a base object with whichever extensions apply then becomes a runtime decision, rather than something fixed by code generation for every individual server.

A consumer learns at runtime which extensions a given server supports from the `extensions` list in that server's RPP Discovery response, as defined in [@!I-D.ietf-rpp-core]. It SHOULD attach, populate, and validate extension class instances only for the extensions the server has advertised as supported, and SHOULD ignore any properties in a response that belong to an extension it does not recognise or does not support.

Because an extension that is not attached to a given base object instance contributes no properties at all, a consumer naturally serialises only the base object together with the properties of whichever extensions are actually in use, without needing to distinguish this case from a property that is merely absent or `null` within a supported extension. Attached extensions are merged flat with the base object on the wire, consistent with the schema composition described in "JSON Schema Composition". Within an attached extension, an optional property without a value continues to follow Rule 2 and is omitted from the output rather than represented as `null`.

## Registration

A standardised extension specification MUST be registered in the RPP Extensions registry defined in [@!I-D.ietf-rpp-core], see (#iana-registries). The registration MUST contain the values of the Extension Overview section of the extension specification, see (#extension-format): the name, the identifier, the version, the RPP version, the `$id` of the JSON Schema document, the changed base objects and the new objects. The identifier of the extension in the RPP Discovery document is a URN below the RPP URN sub-namespace. The registration MUST include a dereferenceable URL to the JSON Schema document.

**TODO:** The registration registry in rpp-data-objects needs to be updated to support what is described here.

## Validation {#validation}

A deployment that supports a specific combination of extensions (a profile, as defined in [@!I-D.ietf-rpp-core]) MUST build, for every extended object type, a combined schema consisting of the base object's definition and the `allOf` branch contributed by every supported extension registered for that object type.

Before using a combined schema to validate a JSON instance, implementations MUST inject `"unevaluatedProperties": false` at every object-schema node of the combined schema, following the algorithm described in [@!I-D.ietf-rpp-json]. This injection MUST be performed only on the combined, per-deployment schema, and MUST NOT be present in the schema document published by any individual base or extension specification. This ensures undeclared properties are rejected while any supported combination of registered extensions validates successfully.

# Extension Specification Format {#extension-format}

An extension specification SHOULD follow the structure defined in this section, so that all extension specifications are uniform and can be read, reviewed and processed in the same way.

An extension specification contains at least the following sections, in this order:

1. Introduction: the purpose of the extension, the base specifications it builds upon, and its motivation.
2. Extension Overview: the name, the identifier, the version, the minimum RPP version the extension is compatible with, the `$id` of the JSON Schema document, the core RPP data objects updated by the extension, and the new data objects defined by the extension.
3. External Data Types: the types, defined in an external specification, that the extension references.
4. Result Codes: Any new result codes that the extension defines.
5. Problem Details: the fields that the extension adds to the Problem Detail error object.
6. HTTP Headers: the HTTP headers that the extension defines.
7. Discovery Document: the properties that the extension adds to the RPP Discovery document, in addition to its entry in the `extensions` list.
8. Updated Data Objects: Every existing data object, component object or process object that the extension updates.
   1. Data Elements: for every base data object, component object or process object that the extension changes, the added data elements and associations. Each added data element MUST be defined using the data element attributes defined in [@!I-D.ietf-rpp-data-objects] (name, identifier, cardinality, mutability, data type, description and constraints).
   2. Operations: every operation that the extension introduces.
9. New Data Objects: Every new data object, component object or process object that the extension introduces, including its object description, data elements and operations.
   1. Data Elements: for every new data object, component object or process object, the data elements and associations. Each added data element MUST be defined using the data element attributes defined in [@!I-D.ietf-rpp-data-objects] (name, identifier, cardinality, mutability, data type, description and constraints).
   2. Operations: every operation that the extension introduces.
10. Endpoints: the endpoint URLs, HTTP methods and query parameters that result from the new data objects, associations and operations, as derived by the rules in [@!I-D.ietf-rpp-core]. This section is informative.
11. JSON Schema: the JSON Schema document of the extension which defines the structure and constraints of the new and updated data objects.
12. Examples.
13. IANA Considerations: the registrations that the extension requires, as described in (#iana-registries).
14. Security Considerations.
15. Privacy Considerations.
16. Internationalization Considerations.

All sections MUST be present. An extension that does not use an extension point MUST state "None" in the corresponding section.

The extension specification MUST define exactly one JSON Schema document, which contains the definitions for all changed and all new objects of the extension. The document MUST have a top-level `$id`, MUST declare the changed base objects using the Extension Mapper (`rpp:extends`) as described in "JSON Schema Composition", and MUST NOT contain `unevaluatedProperties` or `additionalProperties` set to `false`, see "Validation". An extension specification MUST NOT split its schema over multiple documents.

The specification MUST include at least one valid JSON example of the request or response representation. Every valid example MUST validate successfully against the effective schema that is composed from the base schema and the JSON Schema document of the extension, as described in (#validation).

# IANA Registrations {#iana-registries}

RPP defines the IANA registries listed below. When an extension specification is intended to be published as a standard track extension, the new extension MUST be registered in the relevant registries. The IANA Considerations section of the extension specification MUST request these registrations. An extension specification MUST NOT define its own registry for values that belong in one of the already defined registries. If no suitable registry exists for a value that the extension needs to register, the extension specification MUST request the creation of a new registry in its IANA Considerations section, in the RPP registry group defined in [@!I-D.ietf-rpp-core].

Private extensions are not required to use these IANA registries, but should consider reusing currently registered values where possible. They MUST still use identifiers that do not conflict with already registered identifiers.

| Registry | Defined in | Registration procedure | An extension registers an entry when it |
|---|---|---|---|
| RPP Extensions | [@!I-D.ietf-rpp-core] | Expert Review | is published; every standardised extension MUST be registered, with its name, version and specification URL |
| RPP Data Object Registry | [@!I-D.ietf-rpp-data-objects] | Specification Required | introduces a new data object, component object or process object, adds a data element to an existing object, or defines a new operation or operation parameter |
| RPP Result Codes | [@!I-D.ietf-rpp-core] | Expert Review | defines a new result code |
| Link Relation Types | [@!RFC8288], used by [@!I-D.ietf-rpp-core] | as defined in RFC 8288 | defines a new link relation type, in addition to `rpp-process` |
| Media Types | [@!RFC6838], used by [@!I-D.ietf-rpp-core] | as defined in RFC 6838 | defines a representation format with its own media type, in addition to `application/rpp+json` |
| IETF URN Sub-namespace, `urn:ietf:params:rpp` | [@!I-D.ietf-rpp-core] | as defined in RFC 3553 | needs an identifier below the RPP URN namespace, for example `urn:ietf:params:rpp:extension:<name>` |

The registrations are used as follows:

* The extension MUST be registered in the RPP Extensions registry. The identifier of the extension in the RPP Discovery document is a URN below the RPP URN sub-namespace.
* New and changed objects, data elements, operations and operation parameters MUST be registered in the RPP Data Object Registry. Each entry MUST reference the extension specification, so that implementations can distinguish elements of the base specifications from elements defined by extensions.
* The `@type` value of every new object MUST be the object identifier that is registered in the RPP Data Object Registry, as required by [@!I-D.ietf-rpp-json].
* New result codes MUST be registered in the RPP Result Codes registry, in the range reserved for extensions.

The RPP data objects also reuse values from registries that are defined outside of RPP, such as the IANA Repository of IDN Practices (IDN tables). An extension that needs a new value from one of these registries MUST use the procedure of that registry.

# IANA Considerations

**TODO**

# Security Considerations

An extension inherits the security services of the protocol structure that it extends. For example, an extension of a data object inherits the authentication, authorisation and confidentiality services that apply to that object. An extension MAY specify additional security services, such as additional authorisation checks. An extension MUST NOT mandate the removal of a security service that is available for the protocol structure that it extends.

An extension is subject to the same security requirements as the base specifications. The Security Considerations section of an extension specification MUST describe the security threats that are introduced by the extension and the measures to counter them, following the guidelines in [@RFC3552].

A server MUST apply authentication and authorisation to the data elements, operations and other elements that an extension adds, in the same way as for the elements of the base specifications. A server MUST reject undeclared properties, as described in (#validation), and MUST reject the use of extensions that it does not support, as described in the Discovery document extension point.

# Internationalization Considerations

An extension MUST observe the Internationalization Considerations of the base specifications [@!I-D.ietf-rpp-core], [@!I-D.ietf-rpp-data-objects] and [@!I-D.ietf-rpp-json], and the IETF policy on character sets and languages described in [@!RFC2277]. Human-readable text that an extension adds to a request or a response MUST be capable of being expressed in the language that is negotiated between the client and the server.

# Privacy Considerations

**TODO**

# Change History

**TODO**

{backmatter}

# Example Extension {#extension-example}

This appendix is informative. It shows an extension specification that follows the structure defined in "Extension Specification Format", and it shows how the extension can be validated with a generic Java library. The example extension adds the property `extProperty1` to the Domain Name data object.

This example extension, its schema and the examples above can be validated with the generic Java library in the rpp-json-java-lib repository [@RPP-JSON-JAVA-LIB]. The library loads the base schema and the extension schemas supported by a deployment, builds effective schemas from the `rpp:extends` mappings, applies the injection of `unevaluatedProperties` described in (#validation), and validates JSON instances against the result. The repository contains this example extension, the example instances and unit tests, and its README describes how to build and run them.

## Introduction

This document provides an example of an extension specification that follows the guidelines defined in [@I-D.wullink-rpp-extension-guidelines]. The example demonstrates how to add a new property to an existing data object and how to define the corresponding JSON Schema and examples.

## Extension Overview

| Field | Value |
|---|---|
| Name | RPP Example Extension Property |
| Identifier | urn:ietf:params:rpp:extension:example-ext-property |
| Version | 1.0 |
| RPP version | 1.0 |
| Schema `$id` | https://rpp.example/schemas/ext-domain-extproperty1.json |
| Changed base objects | domainName |
| New objects | None |

## External Data Types

None.

## Result Codes

None.

## Problem Details

None.

## HTTP Headers

None.

## Discovery Document

None.

## Updated Data Objects

### Domain Name Data Object

* Identifier: domainName

#### Data Elements

The following data element is added to the Domain Name Data Object.

* Extension Property 1
  * Identifier: extProperty1
  * Cardinality: 1
  * Mutability: create-only
  * Data Type: String
  * Description: An example property that is added to the Domain Name Data Object by the extension.
  * Constraints:
    * The value MUST contain between 1 and 64 characters.
    * The value MUST be provided in the Create operation.

The Read operation returns `extProperty1` when the domain name was created with a value for it.

#### Operations

None.

## New Data Objects

None.

## Endpoints

None.

## JSON Schema

The extension defines a single JSON Schema document. The document declares, using the Extension Mapper (`rpp:extends`) property, that it extends the create and read definitions of the base object, and contributes the new property in a separate definition:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://rpp.example/schemas/ext-domain-extproperty1.json",

  "rpp:extends": {
    "https://rpp.example/rpp/schema.json#/$defs/domainName.create": "#/$defs/ext.domainName.create",
    "https://rpp.example/rpp/schema.json#/$defs/domainName.read": "#/$defs/ext.domainName.read"
  },

  "$defs": {
    "ext.domainName.create": {
      "type": "object",
      "properties": {
        "extProperty1": { "type": "string", "minLength": 1, "maxLength": 64 }
      },
      "required": ["extProperty1"]
    },
    "ext.domainName.read": {
      "type": "object",
      "properties": {
        "extProperty1": { "type": "string" }
      }
    }
  }
}
```

## Examples

The following instance is valid, because `extProperty1` is declared by the supported extension:

```json
{
  "@type": "domainName",
  "name": "example.example",
  "registrant": { "@type": "contact", "id": "c-1234" },
  "nameservers": [
    { "@type": "host", "hostName": "ns1.example.net" }
  ],
  "extProperty1": "some value"
}
```

The following instances are invalid. The first omits the required `extProperty1`, the second gives `extProperty1` the wrong type, and the third contains `extProperty2`, which no supported extension declares and which is therefore rejected by the injected `unevaluatedProperties` keyword:

```json
{ "@type": "domainName", "name": "example.example" }
```

```json
{ "@type": "domainName", "name": "example.example", "extProperty1": 42 }
```

```json
{
  "@type": "domainName",
  "name": "example.example",
  "extProperty1": "some value",
  "extProperty2": "not declared by any supported extension"
}
```

The same instance that uses `extProperty1` is rejected by a deployment that does not support the extension, since in that deployment `extProperty1` is an undeclared property. This is the behaviour required by "Validation": the set of accepted properties is determined only by the extensions that the deployment supports.

### Composition and Validation

For a deployment that supports the example extension, the effective schema for `domainName.create` references the base definition and the extension definition, and has its own `$id`:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://rpp.example/rpp/effective/domain.create.json",
  "$ref": "#/$defs/effective.domainName.create",
  "$defs": {
    "effective.domainName.create": {
      "allOf": [
        { "$ref": "https://rpp.example/rpp/schema.json#/$defs/domainName.create" },
        { "$ref": "https://rpp.example/schemas/ext-domain-extproperty1.json#/$defs/ext.domainName.create" }
      ]
    }
  }
}
```

## IANA Considerations

**TODO:**

## Security Considerations

**TODO:**
    
## Privacy Considerations

**TODO:**

## Internationalization Considerations

**TODO:**

{numbered="false"}
# Acknowledgements

**TODO**

<reference anchor="REST" target="http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm">
  <front>
    <title>Architectural Styles and the Design of Network-based Software Architectures</title>
    <author initials="R." surname="Fielding" fullname="Roy Fielding">
      <organization/>
    </author>
    <date year="2000"/>
  </front>
</reference>

<reference anchor="JSON-SCHEMA" target="https://json-schema.org/draft/2020-12/json-schema-core">
  <front>
    <title>JSON Schema: A vocabulary for describing JSON Data</title>
    <author>
      <organization>JSON Schema</organization>
    </author>
    <date year="2020"/>
  </front>
</reference>

<reference anchor="RPP-JSON-JAVA-LIB" target="https://github.com/SIDN/rpp-json-java-lib">
  <front>
    <title>rpp-json-java-lib: Java library for RPP JSON Schema extensions</title>
    <author>
      <organization>SIDN Labs</organization>
    </author>
    <date year="2026"/>
  </front>
</reference>

<reference anchor="I-D.wullink-rpp-extension-guidelines">
  <front>
    <title>Guidelines for Extending the RESTful Provisioning Protocol (RPP)</title>
    <author initials="M." surname="Wullink" fullname="Maarten Wullink">
      <organization>SIDN Labs</organization>
    </author>
    <author initials="P." surname="Kowalik" fullname="Pawel Kowalik">
      <organization>DENIC</organization>
    </author>
    <date year="2026"/>
  </front>
  <seriesInfo name="Internet-Draft" value="draft-wullink-rpp-extension-guidelines-00"/>
</reference>
