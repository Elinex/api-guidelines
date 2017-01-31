# Changes and Versioning
> _The fundamental principle is that you can’t break existing clients, because you don’t know what they implement, and you don’t control them. In doing so, you need to turn a backwards-incompatible change into a compatible one._
>
> _– [Mark Nottingham](https://www.mnot.net/blog/2011/10/25/web_api_versioning_smackdown)_

Any change to an API MUST NOT break existing clients.

Any change to:
- **resource identifier** (resource name / URI) including any **query parameters** and their semantics
- **resource metadata** (e.g. HTTP headers)
- **actions** the resource affords (e.g. available HTTP Methods)
- **relations** with other resources (e.g Links)
- **representation format** (e.g. HTTP request and response bodies)

MUST follow the [Rules for Extending](core-principles/rules-for-extending.md).

## Identifier Stability (no URI versioning)
A change MUST NOT affect **existing** resource identifiers (name / URI). Furthermore, a resource identifier MUST NOT contain a semantic version to convey a version of resource or its representation format.

#### Example
Adding a new action to existing resource with identifier `/greeting` doesn't change its identifier to `/v2/greeting` (or `/greeting-with-new-action` etc.).

## Backward-incompatible Changes
A change to _resource identifier_, _resource metadata_, _resource actions_ and _resource relations_, that can't follow the [Rules for Extending](core-principles/rules-for-extending.md) MUST result into a **new resource variant**. Existing resource variant MUST be preserved.

A change to _representation format_ SHOULD NOT result into a new resource variant.

#### Example
Currently optional URI Query parameter `first` on an existing resource `/greeting?first=John&last=Appleseed` needs to be made required. Since this change violates the 3rd rule of extending and could break existing clients a new variant of the resource is created with different URI `/named-greeting?first=John&last=Appleseed`.

### Representation Format Changes
> A representation format is the serialization format (media type) used in request and response bodies and typically it represents a resource or its part, possibly with additional hypermedia controls.

If the change can't follow the Rules for Extending the representation format media type MUST be changed. If the media type has been changed the previous media type MUST be available via [Content Negotiation](core-principles/content-negotiation.md). 

If the media type conveys the version parameter, the version parameter MUST follow [Semantic versioning](http://semver.org/).

#### Example
Media type _before_ a breaking change:

```
application/vnd.example.resource+json; version=2
```

Media type _after_ a breaking change:

```
application/vnd.example.resource+json; version=3
```

## API Description Versioning
API Description in the OpenAPI specification format MUST have the `version` field. The `version` field MUST follow [Semantic versioning](http://semver.org/):

> Given a version number MAJOR.MINOR.PATCH, increment the:
> 
> - MAJOR version when you make **incompatible** API changes,
> - MINOR version when you add functionality in a **backwards-compatible** manner
> - PATCH version when you make **backwards-compatible bug fixes**


#### Example
Following API Description

```yaml
swagger: '2.0'
info:
  version: '2.1.3'
  title: '[Demo] Inventory API'
  description: 'Inventory service API'
```

Has MAJOR version 2, MINOR version 1 and PATCH version 3.

