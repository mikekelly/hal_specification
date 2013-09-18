---
layout: default
---
# HAL - Hypertext Application Language
## A lean hypermedia type

* __Author:__   [Mike Kelly][1]
* __Created:__  2011-06-13
* __Updated:__  2013-09-18 (Updated)

## Summary
HAL is a simple format that gives a consistent and easy way to
hyperlink between resources in your API.

Adopting HAL will make your API explorable, and its documentation easily
disocverable from within the API itself. In short, it will make your API
easier to work with and therefore more attractive to client developers.

APIs that adopt HAL can be easily served and consumed using open source
libraries available for most major programming languages. It's also
simple enough that you can just deal with it as you would any other
JSON.

## About The Author
Mike Kelly is a software engineer from the UK. He runs an [API
consultancy][35] helping companies design and build beautiful, resilient
APIs that developers love.

## Quick links
* [A demo API using HAL called HAL Talk][27]
* [A list of libraries for working with HAL][33]
* [A list of hypermedia APIs using HAL][34]
* [Discussion group (questions, feedback, etc)][2]

## General Description
HAL provides a set of conventions for expressing hyperlinks in either
JSON or XML.

_The rest of a HAL document is just plain old JSON or XML._

Instead of using ad-hoc structures, or spending valuable time designing
your own format; you can adopt HAL's conventions and focus on building
and documenting the data and transitions that make up your API.

HAL is a little bit like HTML for machines, in that it is generic and
designed to drive many different types of application via hyperlinks.
The difference is that HTML has features for helping 'human actors' move
through a web application to achieve their goals, whereas HAL is
intended for helping 'automated actors' move through a web API to
achieve their goals.

Having said that, _HAL is actually very human-friendly too_. Its
conventions make the documentation for an API discoverable from the API
messages themselves.  This makes it possible for developers to jump
straight into a HAL-based API and explore its capabilities, without the
cognitive overhead of having to map some out-of-band documentation onto
their journey.

## Examples

Here is how you might represent a collection of orders with hal+json.
Things to look for:

* The URI of the main resource being represented ('/orders')
* The 'next' link pointing to the next page of orders
* A templated link called 'find' for searching orders by id
* Two properties of the orders resource; 'currentlyProcessing' and
  'shippedToday'
* Embedded order resources with their own links and properties

### application/hal+json
```javascript
{
    "_links": {
        "self": { "href": "/orders" },
        "next": { "href": "/orders?page=2" },
        "find": {
            "href": "/orders{?id}",
            "templated": true
        },
        "admin": [{
            "href": "/admins/2",
            "title": "Fred"
        }, {
            "href": "/admins/5",
            "title": "Kate"
        }]
    },
    "currentlyProcessing": 14,
    "shippedToday": 20,
    "_embedded": {
        "order": [{
            "_links": {
                "self": { "href": "/orders/123" },
                "basket": { "href": "/baskets/98712" },
                "customer": { "href": "/customers/7809" }
            },
            "total": 30.00,
            "currency": "USD",
            "status": "shipped"
        }, {
            "_links": {
                "self": { "href": "/orders/124" },
                "basket": { "href": "/baskets/97213" },
                "customer": { "href": "/customers/12369" }
            },
            "total": 20.00,
            "currency": "USD",
            "status": "processing"
        }]
    }
}
```


### application/hal+xml
```xml
<resource href="/orders">
  <link rel="next" href="/orders?page=2" />
  <link rel="find" href="/orders{?id}" templated="true" />
  <link rel="admin" href="/admins/2" title="Fred" />
  <link rel="admin" href="/admins/5" title="Kate" />
  <currentlyProcessing>14<currentlyProcessing>
  <shippedToday>20</shippedToday>
  <resource rel="order" href="/orders/123">
    <link rel="customer" href="/customers/7809" />
    <link rel="basket" href="/baskets/98712">
    <total>30.00</total>
    <currency>USD</currency>
    <status>shipped</status>
  </resource>
  <resource rel="order" href="/orders/124">
    <link rel="customer" href="/customer/12369" />
    <link rel="basket" href="/baskets/97213">
    <total>20.00</total>
    <currency>USD</currency>
    <status>processing</status>
  </resource>
</resource>
```
## The HAL Model

The HAL conventions revolve around representing two simple concepts: _Resources_ and _Links_.

### Resources
Resources have:

* Links (to URIs)
* Embedded Resources (i.e. other resources contained within them)
* State (your bog standard JSON or XML data)

### Links
Links have:

* A target (a URI)
* A relation aka. 'rel' (the name of the link)
* A few other optional properties to help with deprecation, content
  negotiation, etc.

Below is an image that roughly illustrates how a HAL representation is
structured: 

![The HAL Information model][4]

## How HAL is used in APIs
HAL is designed for building APIs in which clients navigate around the
resources by following links.

Links are identfied by link realtions. Link realtions are the lifeblood
of a hypermedia API: they are how you tell client developers about
what resources are available and how they can be interacted with, and
they are how the code they write will select which link to traverse.

Link relations are not just an identifying string in HAL, though. They
are actually URLs, which developers can follow in order to read the
documentation for a given link. This is what is known as
"discoverability". The idea is that a developer can enter into your API,
read through documentation for the available links, and then
follow-their-nose through the API.

HAL encourages the use of link relations to: 

*   Identify links and embedded resources within the representation
*   Infer the expected structure and meaning of target resources
*   Signalling what requests and representations can be submitted to target resources

## How to serve HAL
HAL has a media type for both the JSON and XML variants, whos names are
`application/hal+json` and `application/hal+xml` respectively.

When serving HAL over HTTP, the `Content-Type` of the response should
contain the relevant media type name.

## The structure of a HAL document

### Minimum valid document

A HAL document must at least contain an empty resource.

##### hal+json
An empty JSON object:

```javascript
{}
```

##### hal+xml
An empty `resource` element:

```xml
<resource />
```

### Resources

In most cases, resources should have a self URI

##### hal+json
Represened via a 'self' link:
```javascript
{
    "_links": {
        "self": { "href": "/example_resource" }
    }
}
```

##### hal+xml
Represented via a `@href` property on a `resource` element.
```xml
<resource href="/example_resource" />
```

### Links

Links must be contained directly within a resource:

##### hal+json
Links are represented as JSON object contained within a `_links` hash
that must be a direct property of a resource object:

```javascript
{
    "_links": {
        "next": { "href": "/page=2" }
    }
}
```

##### hal+xml
Links are represented as `link` elements that must be a direct
descendant of a `resource` element:

```xml
<resource>
  <link rel="next" href="/page=2" />
</resource>
```

#### Link Relations
Links have a relation (aka. 'rel'). Link rels are the main way of
distinguishing between a resource's links. Think of it like a key.

##### hal+json
Link relations are used as the keys within the `_links` hash:

```javascript
{
    "_links": {
        "next": { "href": "/page=2" }
    }
}
```

##### hal+xml
Link relations are contained in `link` elements' `@rel` attribute.

```xml
<resource>
  <link rel="next" href="/page=2" />
</resource>
```

#### API Discoverability
Link rels should be URLs which reveal documentation about the
given link. This makes your application "discoverable", meaning it's
easy for someone to jump into your API and find the relevant slice of
documenation for each of the links they are presented with.

URLs are generally quite long and a bit nasty for use as keys. To get
around this, HAL provides "CURIEs" which are basically named tokens that
you can define in the document and use to express link relation URIs in
a friendlier, more compact fashion i.e.  `ex:widget` instead of
`http://example.com/rels/widget`. The details are available in the
[section on CURIEs][36]

### Representing Multiple Links With The Same Relation
A resource may have multiple links that share the same link relation.

##### hal+json

```javascript
{
    "_links": {
      "items": [{
          "href": "/first_item"
      },{
          "href": "/second_item"
      }]
    }
}
```

##### hal+xml
```xml
<resource>
</resource>
```

Single links (if doubt, go with multiple)

##### hal+json
```javascript
{
    "_links": {
    }
}
```

##### hal+xml
```xml
<resource>
</resource>
```

### CURIEs

##### hal+json
HAL gives you a reserved link relation 'curies' which you can 

```javascript
{
    "_links": {
        "ex:widget": { "href": "/page=2" }
    }
}
```

##### hal+xml
```xml
<resource>
  <link rel="next" href="/page=2" />
</resource>
```



*   __rel__
    
    REQUIRED
    
    For identifying how the target URI relates to the 'Subject **Resource**'. The Subject **Resource** is the closest parent **Resource** element.
    
    This attribute is not a requirement for the root element of a HAL representation, as it has an implicit default value of 'self'
    
    **rel** corresponds with the '[relation parameter][8]' as defined in [Web Linking (RFC 5988)][7]
    
    **rel** attribute SHOULD be used for identifying **Resource** and **Link** elements in a HAL representation.

*   __name__
    
    OPTIONAL
    
    For distinguishing between **Resource** and **Link** elements that share the same **rel** value. The **name** attribute SHOULD NOT be used exclusively for identifying elements within a HAL representation, it is intended only as a 'secondary key' to a given **rel** value.

Note: the following attributes have corresponding [target attributes][9]' as defined in [Web Linking (RFC 5988)][7]

*   __hreflang__
  
    OPTIONAL

    For indicating what the language of the result of dereferencing the link should be.

*   __title__
   
    OPTIONAL

    For labeling the destination of a link with a human-readable identifier.

### Link Attributes

The following are attribute definitions applicable only to HAL's **Link** element.

*   __href__

    REQUIRED

    This attribute MAY contain a [URI Template (RFC6570)][18] and in which case, SHOULD be complemented by an additional __templated__ attribtue on the link with a boolean value true.

*   __templated__

    OPTIONAL

    This attribute SHOULD be present with a boolean value of true when the href of the link contains a [URI Template (RFC6570)][18].

### Resource Attributes

The following are attribute definitions applicable only to HAL's **Resource** element.

*    __href__
    
     REQUIRED
    
     Content embedded within a **Resource** element MAY be a full, partial, summary, or incorrect representation of the content available at the target URI. Applications which use HAL MAY clarify the integrity of specific embedded content via the description of the relevant **rel** value.

## Constraints

The root of a HAL representation MUST be a **Resource** that links itself to the URI of the resource being represented.

* hal+xml; this is exposed via the href attribute of the resource.
* hal+json; this is exposed via a 'self' link.

## HAL in JSON (application/hal+json)

Note: click the following for a [more accessible explanation of HAL in JSON][3]

Further details on the JSON variant of HAL:

*   **Resources** are represented as objects
*   **Resource** objects have two reserved properties: \_links and \_embedded
*   The \_links property contains **Link** objects against keys that match their relation
*   The \_embedded property contains embedded **Resource** objects against keys that match their relation
*   **Resource** objects MUST have a self link (\_link.self) which indicates the URI of the embedded resource
*   Relations with one corresponding **Resource**/**Link** have a single object value, relations with multiple corresponding HAL elements have an array of objects as their value.

## Minimum Valid Representation

### JSON
```javascript
{ }
```

### XML
```xml
<resource />
```

## Recommendations

### Using URIs for Link relation values

Link relation values used in HAL representations SHOULD be URIs which,
when dereferenced, provide relevant details. This helps to improve the
discoverability of your application.

For XML, the [CURIE syntax][10] MAY be used for brevity.

For JSON, a 'curies' link can be used like so:

```javascript
{ ... '_links' : { 'curies': [{ 'href' : 'http://example.com/rels/{relation}', 'name': 'ex' }], ... }, ... }
```

## RFC

The JSON variant of HAL (application/hal+json) has now been published as an internet draft:
[draft-kelly-json-hal][21].

## Acknowledgements

Thanks to Darrel Miller and Mike Amundsen for their invaluable feedback.

## Notes/todo

* Add deprecates property to link objects
* Transclusion ala esi for JSON variant? XML can reuse ESI?

 [1]: http://stateless.co/
 [2]: http://groups.google.com/group/hal-discuss
 [3]: http://blog.stateless.co/post/13296666138/json-linking-with-hal
 [4]: http://stateless.co/info-model.png
 [5]: http://tools.ietf.org/html/rfc2119
 [6]: http://tools.ietf.org/html/rfc5988#section-5.1
 [7]: http://tools.ietf.org/html/rfc5988
 [8]: http://tools.ietf.org/html/rfc5988#section-5.3
 [9]: http://tools.ietf.org/html/rfc5988#section-5.4
 [10]: http://www.w3.org/TR/curie/
 [11]: https://github.com/HalBuilder/halbuilder-java
 [12]: https://github.com/zircote/Hal
 [13]: https://github.com/ecomfi/halclient
 [14]: https://bitbucket.org/smichelotti/hal-media-type
 [15]: https://github.com/blongden/hal
 [16]: http://nicksda.apotomo.de/2012/04/roar-0-10-with-json-hal-support-and-representable-1-1-6-released/
 [17]: https://github.com/dlindahl/frenetic
 [18]: http://tools.ietf.org/html/rfc6570
 [19]: http://codegram.github.com/hyperclient/
 [20]: https://github.com/mikekelly/backbone.hal
 [21]: http://tools.ietf.org/html/draft-kelly-json-hal
 [22]: https://github.com/jvelilla/HAL
 [23]: https://github.com/tavis-software/hal
 [24]: https://github.com/HalBuilder/halbuilder-scala
 [25]: https://github.com/HalBuilder/halbuilder-test-resources/tree/develop/src/main/resources
 [26]: https://github.com/mikekelly/hal-browser
 [27]: http://haltalk.herokuapp.com/
 [28]: https://github.com/DaveJS/dave.schema.json
 [29]: https://github.com/deathbob/halidator
 [30]: http://gotohal.net/
 [31]: https://bitbucket.org/dcutting/hyperbek
 [32]: https://github.com/cndreisbach/halresource
 [33]: https://github.com/wharris/dougrain
 [34]:
 [35]:
 [36]: 
