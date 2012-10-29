---
layout: default
---
# HAL - Hypertext Application Language

## A lean hypermedia type

* __Author:__ Mike Kelly ([mike@stateless.co][1])
* __Dates:__ 2011-06-13 (Created) 2012-10-22 (Updated)
* __Status:__ Draft

## Summary

HAL is a format you can use in your API that gives you a simple way of
linking. It has two variants, one in JSON and one in XML.

## RFC

The JSON variant of HAL (application/hal+json) has now been published as an internet draft:
[draft-kelly-json-hal][21].

## Libraries For Working With HAL
* [JSON Schema][28]
* [(Ruby) ROAR][16]
* [(Ruby) Frenetic][17]
* [(Ruby) Hyperclient][19]
* [(Ruby+CLI) halidator][29]
* [(JS) Backbone.HAL][20]
* [(PHP) Hal Library][12]
* [(PHP) Nocarrier\Hal][15]
* [(C#) Tavis.Hal][23]
* [(C#) HALClient][13]
* [(C#) WCF Media Type Formatter][14]
* [(Java) halbuilder-java][30]
* [(Scala) halbuilder-scala][30]
* [(Objective-C) HyperBek][31]
* [(Eiffel) HAL][22]
* [Test Representations][25]
* [HAL Browser][26]

### Example Hypermedia API using HAL
* [HAL Talk][27]

## Discussion Group

If you have any questions or feedback, you can message the
[HAL-discuss mailing list][2]. 

## General Description

HAL provides a set of conventions for expressing hyperlinks to, and
embeddedness of, related resources - the rest of a HAL document is just
plain old JSON or XML. 

Instead of using linkless JSON/XML, or spending time developing a custom
media type, you can just use HAL and focus on defining and documenting
the link relations that direct your clients through your API.

HAL is a bit like HTML for machines, in that it is generic and designed
to drive many different types of application. The difference is that
HTML has features for presenting a graphical hypermedia interface to a
'human actor', whereas HAL is intended for presenting a machine
hypertext interface to 'automated actors'.

For a pracitcal introduction to hal+json you can read this
article: [JSON Linking with HAL][3] 

## The HAL Model

HAL has two main components: Resources and Links.
* Resources can have their own state, links, and other embedded resources.
* Links have a link relation (rel) that signals how to interpret the target resource.

Below is an image that roughly illustrates how a HAL representation is
structured: 

![The HAL Information model][4]

## Examples

Here is how you could represent a collection of orders with the JSON variant of HAL:

{% highlight javascript %}
{
  "_links": {
    "self": { "href": "/orders" },
    "next": { "href": "/orders?page=2" },
    "find": { "href": "/orders{?id}", "templated": true },
    "admin": [
      { "href": "/admins/2", "title": "Fred" },
      { "href": "/admins/5", "title": "Kate" }
    ]
  },
  currentlyProcessing: 14,
  shippedToday: 20,
  "_embedded": {
   "orders": [{
       "_links": {
         "self": { "href": "/orders/123" },
         "basket": { "href": "/baskets/98712" },
         "customer": { "href": "/customers/7809" }
       },
       "total": 30.00,
       "currency": "USD",
       "status": "shipped",
     },{
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
{% endhighlight %}

Here is the same example using the XML variant of HAL:

{% highlight xml %}
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
{% endhighlight %}

## How to use HAL

HAL is two media types (application/hal+json & application/hal+xml) with
which applications are exposed as sets of link relations. 

HAL encourages the use of link relations to: 

*   Identify links and embedded resources within the representation
*   Infer the expected structure and meaning of target resources
*   Signalling what requests and representations can be submitted to target resources

## Compliance

An implementation is not compliant if it fails to satisfy one or more of
the MUST or REQUIRED level requirements. An implementation that
satisfies all the MUST or REQUIRED level and all the SHOULD level
requirements is said to be "unconditionally compliant"; one that
satisfies all the MUST level requirements but not all the SHOULD level
requirements is said to be "conditionally compliant." 

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
> "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
> document are to be interpreted as described in [RFC 2119][5]. 

## Media Type Identifiers
*   __application/hal+json__

    The JSON based variant of HAL

*   __application/hal+xml__

    The XML based variant of HAL

## Components

HAL provides hypertext capabilities via two elements:

1.  Resources
    
    For expressing the embedded nature of a given part of the representation.

2.  Links

    For expressing 'outbound' hyperlinks to other, related resources.

### Shared Attributes

The **Resource** and **Link** elements share the following attributes:

*   __href__
    
    REQUIRED
    
    For indicating the target URI.
    
    **href** corresponds with the '[Target IRI][6]' as defined in [Web Linking (RFC 5988)][7]

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
{% highlight javascript %}
{ "_links": { "self": { "href": "http://example.com/" } } }
{% endhighlight %}

### XML
{% highlight xml %}
<resource href="http://example.com/" />
{% endhighlight %}

## Recommendations

### Using URIs for Link relation values

Link relation values used in HAL representations SHOULD be URIs which,
when dereferenced, provide relevant details. This helps to improve the
discoverability of your application.

For XML, the [CURIE syntax][10] MAY be used for brevity.

For JSON, a 'curie' link can be used like so:

{% highlight javascript %}
{ ... '_links' : { 'curie': { 'href' : 'http://example.com/rels/{relation}', 'name': 'ex' }, ... }, ... }
{% endhighlight %}

## Acknowledgements

Thanks to Darrel Miller and Mike Amundsen for their invaluable feedback.

## Notes/todo

* Add deprecates property to link objects
* Transclusion ala esi for JSON variant? XML can reuse ESI?

 [1]: mailto:mike%40stateless.co
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
 [16]: https://github.com/apotonick/roar/blob/master/lib/roar/representer/json/hal.rb
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
