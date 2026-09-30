%%%
title = "Extension Guidelines for RESTful Provisioning Protocol (RPP)"
abbrev = "RPP Extension Guidelines"
area = "Internet"
workgroup = "Network Working Group"
submissiontype = "IETF"
keyword = [""]
TocDepth = 4
date = 2026-11-14

[seriesInfo]
name = "Internet-Draft"
value = "draft-wullink-rpp-extension-guidelines-00"
stream = "IETF"
status = "informational"

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

The RESTful Provisioning Protocol (RPP) defines a base set of data objects, component objects, and operations for provisioning registry data. This document defines guidelines and a normative mechanism for extension specifications that add data elements, associations, operations, result codes, or other functionality to objects and operations defined in a base RPP specification, without modifying the base specification itself. It specifies the recognized RPP extension points, the JSON Schema composition method extensions MUST use to remain independently combinable, the corresponding registration requirements, and considerations for client and server implementations that support one or more extensions.

{mainmatter}

# Introduction

The RESTful Provisioning Protocol (RPP), as defined in [@!I-D.ietf-rpp-core], [@!I-D.ietf-rpp-data-objects], and [@!I-D.ietf-rpp-json], provides a base set of data objects, component objects, and operations for the provisioning of registry data. Deployments may need to add functionality beyond this base set, for example to support policies specific to a registry, a jurisdiction, or a class of registered objects. RPP is designed to accommodate such needs through extension specifications, so that additional functionality can be introduced without modifying the base specification that defines the object or operation being extended.

Because independently developed extension specifications may be deployed together, and because RPP relies on JSON Schema, as defined in [@!I-D.ietf-rpp-json], to validate protocol messages, extensions need to follow a common, predictable pattern. Without such a pattern, extensions that are individually valid could conflict when combined at a given deployment, or could require ad-hoc, per-combination schema and code to be written for every deployment profile. This document defines that common pattern.

This document identifies the set of extension points along which an RPP base specification MAY be extended, including new data objects, new data elements on existing objects, new operations, new operation inputs and outputs, external data types, new RPP result codes, error object extension fields, additional query parameters, and additions to the RPP Discovery document. For each extension point, this document states the applicable registration requirements.

This document further discusses considerations for client and server implementations that need to compose, validate, and process RPP messages that may include data contributed by more than one extension.

This document does not itself define any RPP extension. It defines the rules that extension specifications, and the deployments that support them, are expected to follow.

# Terminology

In this document the following terminology is used.

URL - A Uniform Resource Locator as defined in [@!RFC3986].

Resource - An object having a type, data, and possible relationship to other resources, identified by a URL.

Base specification - The RPP specification, such as [@!I-D.ietf-rpp-data-objects], [@!I-D.ietf-rpp-json] or [@!I-D.ietf-rpp-core], that originally defines a data object, component object, or operation.

Extension specification - A specification that adds new data elements, associations, or operations to an object or operation defined in a base specification, without modifying the base specification itself.

Combined schema - The JSON Schema produced by composing the JSON Schema definition of a base object with the JSON Schema contributed by every extension specification supported by a given deployment or profile, as defined in [@!I-D.ietf-rpp-core].

# Conventions Used in This Document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT","SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [@!RFC2119].

In examples, indentation and white space are provided only to illustrate element relationships and are not REQUIRED features of the protocol.

# Restrictions on Extensions

An extension MUST NOT remove, redefine, or otherwise alter the semantics of a property defined by the RPP base specification.
An extension MUST only add new properties or associations, and MUST ensure that any new elements do not conflict with existing ones in the base specification.

**TODO: do we recommend to use namespaces, using prefixes, to avoid naming conflicts between properties added by different extensions?**

# RPP Extension Points

An extension specification MAY extend RPP along one or more of the following independent extension points. Each extension point has its own registration requirements and, where applicable, its own schema-composition method.

## New Data Objects

Extensions MAY define entirely new resource, component, or process objects, as described in [@!I-D.ietf-rpp-data-objects].

## New Data Elements on Existing Objects

Extensions MAY add new data elements (properties) to a data object, component object, or process object defined in a base specification. This is the extension point covered in detail in(#extending-data-objects) below.

## New Operations

Extensions MAY define entirely new operations on existing or new data objects.

## URL Endpoints

RPP endpoint URLs are not an independent extension point. As defined in [@!I-D.ietf-rpp-core], every endpoint URL and HTTP method is derived mechanically from a Data Object's `"Identifier"`, from its operations, and from the Direct Access flag on its data elements; no endpoint is defined independently of a corresponding Data Object. Consequently:

* A new Data Object automatically obtains a collection endpoint once its `"Identifier"` is registered, by applying the `plural()` derivation rule.
* A new association with Direct Access set to `true` automatically obtains a sub-resource path, derived recursively from the parent resource's path.
* A new operation automatically obtains its HTTP method and URL path once its `"Identifier"` is registered.

An extension specification MUST NOT define ad-hoc endpoint URLs or HTTP methods. It MUST instead register the underlying data object, association, or operation as described in the corresponding extension point above, and rely on the derivation rules in [@!I-D.ietf-rpp-core] to determine the resulting endpoint.

## Operation Inputs and Outputs

Extensions MAY add both transient and persistent data elements to the input or output of an existing operation.

## External Data Types

Instead of adding properties to an object, an extension MAY reference a type defined entirely in an external specification (e.g. `External:RPP-JSContact-Profile:Card`), as described in [@!I-D.ietf-rpp-data-objects]. This is the appropriate mechanism when substituting or supplying a whole, independently versioned data type, rather than adding fields to an existing RPP-defined object.

## New RPP Result Codes

Extensions MAY define new RPP result codes within the reserved result code classes, registered in the RPP Result Codes registry defined in [@!I-D.ietf-rpp-core].
New result codes MUST be registered in the RPP Result Codes registry defined in [@!I-D.ietf-rpp-core].

## Error Object Extension Fields

Extensions MAY add new fields to the Problem Detail error object to convey additional, extension-specific information about the cause of an error, as described in [@!I-D.ietf-rpp-core].

**TODO: there is no schema for the Problem Detail error object defined in the base specification, fix this?**

## Additional Query Parameters

Extensions MAY define additional HTTP query parameters for existing operations, for example to further qualify a check or read request, as described in [@!I-D.ietf-rpp-core].
Any transient parameter for a read, query, update, and delete operation MUST be translated into an HTTP query parameter, named by the operation's parameter identifier.

## New Authentication and Authorization Methods

**TODO: is this an extension point?**

## Discovery document

New properties added by an extension to the RPP Discovery document MUST be included in the appropriate section of the document, following the structure defined in [@!I-D.ietf-rpp-core].

**TODO: there is no schema for the Problem Detail error object defined in the base specification, fix this?**

# Extending Data Objects {#extending-data-objects}

RPP is designed so that extension specifications can add new data elements to data objects, component objects, and operations without modifying the specification that originally defines them, as described in [@!I-D.ietf-rpp-data-objects]. This section defines the normative method an extension specification MUST follow when it adds properties to an object defined in a base specification, so that JSON Schema validation, as defined in [@!I-D.ietf-rpp-json], keeps functioning correctly across specifications.

## JSON Schema Composition

Every data object defined in a base specification, when modified by an extension specification, must be extended using an extension specification. The extension specification MUST define a new JSON Schema document that composes the base object's schema with the new properties using `allOf`. The extension specification MUST NOT copy, redefine, or otherwise restate the JSON Schema definition of the base object. Instead, it MUST reference the base object's definition by its fully qualified `$id`-relative pointer (e.g. `https://www.iana.org/.../domainName.json#/$defs/domainName`) in an `allOf` branch, and add its own new properties in a separate branch.

An extension schema MUST declare which base object definitions it extends using the `rpp:extends` property, as defined in [@!I-D.ietf-rpp-json]. The property maps the fully qualified reference of each base object definition to the local definition, within the extension schema, that contributes the new properties. This mapping allows an implementation to construct, for a given base object, the effective schema that combines the base definition with every supported extension, without any knowledge of the extensions in advance.

## Schema Identification

A specification that defines JSON Schema for RPP objects MUST assign a stable `$id` to its schema document(s). Extension specifications MUST reference a base object's definition by its `$id` (e.g. `https://www.iana.org/.../domainName.json#/$defs/domainName`) rather than copying the base definition into their own schema.

## Consumer Implementation

A consumer, which can be a server or client, does not need to generate a distinct set of code classes for every base object and extension combination offered by a particular server. Because extension schemas are additive, self-contained, and independent of one another, a consumer implementation SHOULD instead maintain one set of code classes for each base object and a separate set of code classes for each extension it supports, keeping an extension's classes distinct from the base object's classes rather than flattening their properties together. Composing a base object with whichever extensions apply then becomes a runtime decision, rather than something fixed by code generation for every individual server.

A consumer learns at runtime which extensions a given server supports from the `extensions` list in that server's RPP Discovery response, as defined in [@!I-D.ietf-rpp-core]. It SHOULD attach, populate, and validate extension class instances only for the extensions the server has advertised as supported, and SHOULD ignore any properties in a response that belong to an extension it does not recognize or does not support.

Because an extension that is not attached to a given base object instance contributes no properties at all, a consumer naturally serializes only the base object together with the properties of whichever extensions are actually in use, without needing to distinguish this case from a property that is merely absent or `null` within a supported extension. Attached extensions are merged flat with the base object on the wire, consistent with the schema composition described in "JSON Schema Composition". Within an attached extension, an optional property without a value continues to follow Rule 2 and is omitted from the output rather than represented as `null`.

### Java

For Java clients, a generic library can implement the schema handling described in this document without any code that is specific to a particular extension. The library loads the RPP base schema and the extension schemas supported by a server, uses the `rpp:extends` mappings to compose the effective schema of a base object with `allOf`, applies the `unevaluatedProperties` injection described in "Validation" to the composed schema only, and validates JSON instances against the result. Adding support for a new extension then consists of adding its schema document to the library; no new classes are required. The library in (#java-example) is one possible implementation, its source is available in the rpp-json-java-lib repository [@RPP-JSON-JAVA-LIB].

## Registration

An extension specification that adds properties to an existing object MUST register the added data elements in the Object and Operation Extension registry defined in [@!I-D.ietf-rpp-data-objects]. The registration MUST additionally include a dereferenceable URL to the JSON Schema document defining the `$defs` entry described in the Additive Schema Composition section above, and MUST identify the base object type(s) being extended by their registered identifier.

## Validation

A deployment that supports a specific combination of extensions (a profile, as defined in [@!I-D.ietf-rpp-core]) MUST build, for every extended object type, a combined schema consisting of the base object's definition and the `allOf` branch contributed by every supported extension registered for that object type.

Before using a combined schema to validate a JSON instance, implementations MUST inject `"unevaluatedProperties": false` at every object-schema node of the combined schema, following the algorithm described in [@!I-D.ietf-rpp-json]. This injection MUST be performed only on the combined, per-deployment schema, and MUST NOT be present in the schema document published by any individual base or extension specification. This ensures undeclared properties are rejected while any supported combination of registered extensions validates successfully.

# IANA Considerations

**TODO**

# Internationalization Considerations

**TODO**

# Privacy Considerations

**TODO**

# Change History

**TODO**

{backmatter}

# Java Example {#java-example}

This appendix is informative. It describes a generic Java library that reads the RPP base schema and a set of extension schemas, composes them into the effective schema of a base object as described in "JSON Schema Composition" and "Validation", and validates JSON instances against it. The library contains no code specific to any extension: supporting an additional extension only requires adding its schema document. The complete, buildable source, including unit tests, is available in the `java` directory of the rpp-json-java-lib repository [@RPP-JSON-JAVA-LIB]. All paths in this appendix are relative to the root of that repository. It uses the Jackson and networknt `json-schema-validator` libraries.

The example extension adds the property `extProperty1` to the Domain Name data object. The extension schema declares, using `rpp:extends`, that it extends the create and read definitions of the base object, and contributes the new property in a separate definition:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://rpp.example/schemas/ext-domain-extproperty1.json",

  "rpp:extends": {
    "https://rpp.example/rpp/schema.json#/$defs/domainObject.create": "#/$defs/ext.domain.create",
    "https://rpp.example/rpp/schema.json#/$defs/domainObject.read": "#/$defs/ext.domain.read"
  },

  "$defs": {
    "ext.domain.create": {
      "type": "object",
      "properties": {
        "extProperty1": { "type": "string", "minLength": 1, "maxLength": 64 }
      },
      "required": ["extProperty1"]
    },
    "ext.domain.read": {
      "type": "object",
      "properties": {
        "extProperty1": { "type": "string" }
      }
    }
  }
}
```

The library builds the following effective schema for `domainObject.create`. It references the base definition and the extension definition by their absolute `$id`-relative references, and has its own `$id`:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://rpp.example/rpp/effective/domain.create.json",
  "$ref": "#/$defs/effective.domainObject.create",
  "$defs": {
    "effective.domainObject.create": {
      "allOf": [
        { "$ref": "https://rpp.example/rpp/schema.json#/$defs/domainObject.create" },
        { "$ref": "https://rpp.example/schemas/ext-domain-extproperty1.json#/$defs/ext.domain.create" }
      ]
    }
  }
}
```

The library consists of the following classes in the package `nl.sidn.rpp.schema`:

* `RppSchemaLibrary` is the entry point. Schema documents are registered by their top-level `$id`, either individually (`addSchema`) or by loading every `*.json` file in a directory (`addSchemasFrom`). A deployment registers the base schema and the extension schemas it supports; the registered set corresponds to the profile of that deployment. The method `effectiveSchema` takes the absolute reference of a base object definition and the `$id` for the effective schema. It finds the extensions of that base object through their `rpp:extends` mappings, resolves each local extension reference against the `$id` of the extension schema, and builds the effective schema document shown above. It then enriches every registered schema and the effective schema with the injected keyword, and creates a validator that resolves all references from the registered set, so nothing is fetched from the network. The method rejects a base schema that has not been loaded and an effective `$id` that equals the `$id` of a loaded schema.
* `EffectiveSchema` is the result of `effectiveSchema`. It provides the effective schema document without injected keywords (`toJson`), the list of validation errors for a JSON instance (`validate`), and a boolean check (`isValid`). An empty error list means the instance conforms to the base object and to every supported extension.
* `UnevaluatedPropertiesInjector` implements the injection of `"unevaluatedProperties": false` described in [@!I-D.ietf-rpp-json]. It works on a copy of each schema, so the published schema documents are never modified. The keyword is added to every object-schema node that is a use site: the root, the schemas inside `properties`, `patternProperties` and `items`, and any node carrying a `$ref` or a combining keyword. It is not added to the branches inside `allOf`, `anyOf` and `oneOf`, or to the entries of `$defs`, because there it would reject the properties contributed by the other branches. Nodes that already declare `additionalProperties` or `unevaluatedProperties` are left unchanged.
* `Main` is a command line example. It loads the schemas from `java/schemas/base` and `java/schemas/extensions`, builds the effective schema for the Domain Name create definition, prints it, and validates every instance in `java/examples`.

The unit tests in `RppSchemaLibraryTest` exercise the composition of the effective schema, verify that the published schemas contain no injected keyword, and validate the example instances below, with and without the extension loaded.

The example requires a Java 17 or later JDK and Apache Maven. The schema and example directories are resolved relative to the working directory, so the commands MUST be run from the `java` directory of the repository:

```sh
git clone https://github.com/SIDN/rpp-json-java-lib.git
cd rpp-json-java-lib/java

# Run the unit tests
mvn test

# Run Main: prints the effective schema and validates each instance in examples/
mvn compile exec:java

# Run the unit tests, then Main
mvn test exec:java
```

Running `Main` prints the effective schema, followed by the validation result for every instance in `java/examples`, for example:

```
domain-create-invalid-undeclared.json: invalid
  $: property 'extProperty2' is not evaluated and the schema does not allow unevaluated properties

domain-create-valid.json: valid
```

The text of the validation messages depends on the default locale of the JVM. To obtain English messages, set the locale for the Maven JVM, for example `MAVEN_OPTS="-Duser.language=en" mvn compile exec:java`.

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

The same base schema and the same instance that uses `extProperty1` are rejected by a library instance that has not loaded the extension schema, since in that deployment `extProperty1` is an undeclared property. This is the behavior required by "Validation": the set of accepted properties is determined only by the extensions that the deployment supports.

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
