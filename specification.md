# The OpenMFE Microfrontend Specification

Version `1.0`

*This specification document describes mandatory traits which a microfrontend following the OpenMFE standard must expose. At the same time, it acknowledges that microfrontends can be built in different ways, many of which are not covered by this specification. The term "microfrontend" in the scope of this document only refers to applications which aim to be compliant with the specification.*

*The aim of this specification is to allow compliant microfrontends to be loosely coupled to their integration environments, and to allow tooling to be built for microfrontend-based systems without knowing implementation details. The aim of this specification is not to serve as guidance for building an architecturally correct microfrontend, as this is beyond its scope.*

*Note: Capitalised modal verbs (MUST, SHOULD, etc.) and their negation (MUST NOT, SHOULD NOT, etc.) are used in the sense of [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).*

**1  Definitions**

**1.1**  *Microfrontend*: A self-contained web application, integrable into at run-time as a web component, and complying with the provisions of this specification. Even though a microfrontend is conceptually a full-stack application, this specification governs only client-side integration aspects; therefore the term microfrontend hereafter refers to the web component.

**1.2**  *Host page*: An HTML web page into which one or more microfrontends are embedded.

**1.3**  *Web component*: The frontend part of a microfrontend, containing all HTML, CSS, JavaScript and other ressources that are the microfrontend's UI. The web component is loadable through a single file, though may chain-load additional resources at its discretion.

**2  Formats**

**2.1**  *URL*: Whereever this specification expects a URL, it MUST be either a [valid URL](https://datatracker.ietf.org/doc/html/rfc**1738**) or an absolute path, or a path relative to the manifest file. All URLs MUST be publicly accessible. If the URL expression is an absolut path, it will be resolved relative to the schema/hostname/port of the manifest file. If it is a relative path, it will be resolved relative to the manifest file's URL.

**2.2**  *YAML*: Whereever this specification expects YAML (also: YML), it must comply with the [YAML specification](https://yaml.org/spec/**1.2.2**/).

**2.3**  *Markdown*: Wherever this specification allows or requires the use of Markdown, it must comply with the [CommonMark](https://commonmark.org/) specification. However, Markdown MUST NOT contain embedded HTML. Whereever the specification refers to "inline Markdown", it only allows the usage of Markdown elements that translate to inline HTML elements.

**2.4**  *JSON Schema*: Where this specification requires [JSON Schema](https://json-schema.org/), it MUST comply with the following provisions:

**2.4.1**  JSON Schema in the manifest SHOULD be expressed with YAML.

**2.4.2**  Each property description MUST, beneath the actual schema definition, contain a description field explaining the purpose of that property. It MAY contain an additional documentation field containing a URL to a Markdown file with extended documentation.

**2.4.3**  Required properties MUST be indicated through a required key (potentially combined with oneOf or anyOf in case they are optional or mutually exclusive).

**3  General Provisions**

**3.1**  The microfrontend MUST be implemented as a custom element, following the [W3C Custom Element v1 specification](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements). It MAY be built using vendor frameworks, but they MUST be fit for the intended purpose and not violate any of following microfrontend rules.

**3.2**  The microfrontend MUST be provided from a single URL as a JavaScript file. The JavaScript file MUST register the microfrontend using the `customElements.define` API when loaded via a `<script>` tag.

**3.3**  Once the microfrontend has been published under a URL, there MUST NOT be breaking changes published under the same URL.

**3.4**  The microfrontend MUST use Shadow DOM to encapsulate all markup and style nodes. No nodes, except for the root node, MUST exist outside of the Shadow DOM.

**3.5**  The microfrontend MUST add all HTML and CSS into its Shadow DOM.

**3.6**  The microfrontend MAY load additional fonts using the [FontFace specification](https://developer.mozilla.org/en-US/docs/Web/API/FontFace/FontFace) and register them via the `document.fonts` API.

**3.6.1**  The microfrontend MUST prefix font names upon registration with its own tag name.

**3.6.2**  The microfrontend MUST NOT add fonts by injecting `<style>` or `<link>` elements into the host page's DOM tree.

**3.7**  The microfrontend MUST NOT programmatically modify the host page's URL state in any way.

**3.8**  The microfrontend MUST NOT override the existing JavaScript APIs of the host page ("monkeypatching").

**3.9**  The microfrontend MUST NOT create global JavaScript variables or directly create properties on global objects.

**3.10.** The microfrontend MUST NOT listen to, or react to, state changes of its environment in any way, especially not of the URL or of the host document. Instead, it must rely on the host environment for changes to be propagated through attribute configuration changes.

**3.11.** The microfrontend MUST NOT define own link targets. Instead, the microfrontend MUST offer to set link targets via attributes. Where parametrisation is necessary, the microfrontend MAY offer placeholders.

**3.12.** The microfrontend MUST NOT set client-side or server-side cookies.

**3.13.** The microfrontend MUST comply with the HTML and CSS web standards and pass the validation tools provided by the W3C.

**3.14.** The name of a microfrontend MUST start with a vendor-specific prefix following a hyphen. If the microfrontend contains additional nested web components, their names MUST be carry the same prefix.

**3.15.** The microfrontend web component MUST be responsive with regard to the size of its containing element. It MUST react to resizing of its containing element.

**3.16.** The microfrontend web component SHOULD comply with [WCAG Level A](https://www.w**3.**org/WAI/WCAG**21**/quickref/) or better.

**4  Configuration**

**4.1**  If the microfrontend web component expects an initial configuration, it MUST allow this configuration via attributes on the custom element. It MUST NOT allow this configuration to happen via other mechanisms including, but not limited to events, function calls, or session storage.

**4.2**  Configuration attribute names MUST consist of 1 to 50 characters. They MUST only contain lowercase latin letters, numbers and dashes. They MUST only start and end with lowercase latin letters.

**4.3**  Attribute values SHOULD resemble scalar values; configuration values SHOULD NOT be expected in JSON or similar formats.

**4.4**  The microfrontend MUST react to attribute changes with appropriate behaviour, i.e. in the same way as if the updated configuration had been the initial one.

**4.5**  Configuration attributes MUST NOT be prefixed with `data-`.

**5  Communication**

**5.1**  If the microfrontend web component offers outbound communication, this MUST happen through CustomEvents, as specified in the [W3C DOM standard](https://dom.spec.whatwg.org/#interface-customevent).

**5.2**  Events MUST be dispatched on the microfrontend web component itself (i.e. not on an ancestor element such as document) through its dispatchEvent method.

**5.3**  The event's `detail` property MUST contain the event payload.

**5.4**  Event names MUST follow the [reverse domain name notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation), they MUST only contain lowercase latin letters, numbers, dashes and dots. They MUST start with the microfrontend web component's tag name as the root name, and they MUST contain at least one additional name level. They MUST only start and end with lowercase latin letters.

**5.5**  If the microfrontend receives invalid configuration or inbound communication it MUST throw an [`Error` object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error). It MAY use use own implementations, as long as the `Error` object is in the prototype chain.

**5.6**  If the microfrontend web component creates resources the scope of the host page, such as local/session storage, it MUST namespace the identifiers for the resources it creates. The same namespacing rules as for event names apply, as far as the syntax for identifiers of the given resource permit.

**5.7**  For the purposes of analytics, the microfrontend MUST use a standardised format to emit tracking events to its environment.

**5.7.1**  The name of the tracking event MUST be `openmfe.analytics`. The microfrontend MAY send different events with the name `openmfe.analytics`; the actual analytics event name will be derived from the payload as described below.

**5.7.2**  The `detail` object of the CustomEvent must be an object containing the following properties:

**5.7.2.1**  The `detail.name` property MUST contain the name of the tracking event.

**5.7.2.2**  The `detail.origin` property MUST contain the tag name of the microfrontend itself.

**5.7.2.3**  The `detail.id` property MUST contain the `id` attribute of the microfrontend element, or `null` if not applicable.

**5.7.2.4**  The `detail.variant` property MUST contain the `variant` name of the microfrontend in case of an A/B test, or `null` if not applicable.

**5.7.2.5**  The `detail.action` property MUST contain a short description of the activity that caused the event.

**5.7.2.6**  The `detail.category` property MUST contain an identifier for a group of events, or `null` if not applicable.

**5.7.2.7**  The `detail.data` property MUST contain the tracking payload.

**5.7.3**  The `openmfe.analytics` event MUST be documented in the manifest file as described below.

**6  Prerender Endpoint**

**6.1**  The microfrontend MUST provide a webservice endpoint which allows prerendering the inner HTML of the web component. The endpoint MUST adhere to the following rules.

**6.2**  The endpoint MUST be publicly available via HTTPS.

**6.3**  If the microfrontend's web component accepts configuration attributes, the endpoint MUST accept the same attributes as GET parameters. The response to this request MUST generate the HTML that is expected to be combined with an instance of the web component with the same configuration.

**6.4**  The endpoint MUST be prepared to be used as an Edge Side Include. The endpoint MUST send appropriate caching headers for the given request.

**6.5**  The generated output MUST be embeddable into the \<body\> element of an HMTL document. The containing element must be a block element.

**6.6**  IF the generated output contains class attributes, the class names must be prefixed with the name of the microfrontend tag.

**6.7**  The generated output MUST NOT reference external resources.

**7  Semantic Endpoint**

**7.1**  The microfrontend SHOULD provide a webservice endpoint which returns semantic information about microfrontend contents, matching the state of a microfrontend based on its configuration. IF such an endpoint is present, it MUST adhere to the following rules.

**7.2**  The endpoint MUST be available via HTTPS.

**7.3**  If the microfrontend's web component accepts configuration attributes, the endpoint MUST accept the same attributes as GET parameters. The response to this request MUST generate semantic information that can be combined with an instance of the web component with the same configuration.

**7.4**  The semantic information must be compliant with the JSON-LD specification and produce a JSON object in the "compacted" format.

**7.5**  The data provided by the endpoint MUST be cachable for at least two weeks; meaning that MUST NOT be likely to become stale within this period of time.

**8  Manifest**

**8.1**  The microfrontend MUST expose a manifest file at runtime under a publically accessible URL.

**8.2**  The manifest file MUST be a valid YAML file.

**8.3**  The manifest file MUST contain the following fields:

**8.3.1**  `version`: A string containing the version number of the specification by which this microfrontend is built (found at the top of this specification). The version field SHOULD be the first entry in the manifest file.

**8.3.2**  `name`: A string containing the name of the microfrontend, five to 50 characters long.

**8.3.3**  `source`: The URL from which the JavaScript file with the web component can be loaded.

**8.3.4**  `url`: An object containing the URLs to different endpoints:

**8.3.4.1**  `url.frontend`: The URL from which the JavaScript file with the web component can be loaded.

**8.3.4.2**  `url.prerender`: The URL to the prerender endpoint.

**8.3.4.3**  `url.semantic`: The URL to the semantic endpoint. IF no semantic endpoint is present, this field MUST be omitted or set to null.

**8.3.5**  `tag`: The custom element tag name of the microfrontend.

**8.3.6**  `publisher`: An object with details about the publishing entity of the microfrontend.

**8.3.6.1**  `publisher.name`: The name of the publisher, five to 50 characters long.

**8.3.6.2**  `publisher.email`: An e-mail address to contact the publisher.

**8.3.7**  `icon`: A URL to an SVG image file. The image MUST be square, and SHOULD be expected to be used against a white or light-grey background. It MUST not contain embedded scripts or animations. Styles SHOULD be expressed as native SVG properties rather than through CSS.

**8.3.8**  `description`: A string with a brief description of the microfrontend, 100 to 300 characters long. It MAY use inline Markdown.

**8.3.9**  `documentation`: A URL to a Markdown file which explains the functionality of the microfrontend in two to four paragraphs in a non-technical way from the business/customer value perspective.

**8.3.10**  `attributes`: A list of objects, where every object describes one attribute on the microfrontend element. Each object MUST contain the following properties:

**8.3.10.1**  `attributes[x].name`: A string indicating the attribute name, 1 to 50 characters long and only using lowercase latin letters and dashes.

**8.3.10.2**  `attributes[x].description`: A string containing a short description of the function of the attribute, 10 to 300 characters long. It MAY use inline Markdown.

**8.3.10.3**  `attributes[x].required`: A boolean indicating whether or not the attribute must be present at the initialisation of the microfrontend.

**8.3.10.4**  `attributes[x].schema`: A JSON Schema structure, describing the type and constraints of the attribute. If the type is not string it will be assumed that the value is a serialised JSON string.

**8.3.11**  `events`: An array of objects, where every object describes one attribute on the microfrontend element. Each object MUST contain the following properties:

**8.3.11.1**  `events[x].name`: A string indicating the attribute name, 1 to 50 characters long and only using lowercase latin letters and dashes.

**8.3.11.2**  `events[x].description`: A string containing a short description of the function of the attribute, 10 to 300 characters long. It MAY use inline Markdown.

**8.3.11.3**  `events[x].schema`: A JSON Schema structure, describing the type and constraints of the detail property of the event.

**8.3.12**  `semantic`: A JSON Schema structure, describing the JSON-LD output of the semantic endpoint.

**8.3.13**  `screenshots`: An array of objects, where every object refers to a screenshot of the rendered microfrontend. The array MUST have at least one entry. Each object in the array MUST contain the following properties:

**8.3.13.1**  `screenshots[x].url`: A URL to an image file containing a screenshot of the microfrontend's UI. The image MUST be in PNG, JPEG or WEBP format.

**8.3.13.2**  `screenshots[x].description`: A string containing a description of the image (to be used as a caption or similar), 10 to 150 characters long. It MAY use inline Markdown.

**8.3.14**  `examples`: An array of objects, where every object is an example of a real configuration to create a fully working instance of the microfrontend. The array MUST have at least one entry. Each object in the array MUST contain the following properties:

**8.3.14.1**  `examples[x].description`: A string containing a description of the example, 20 to 300 characters long. It MAY use inline Markdown.

**8.3.14.2**  `examples[x].attributes`: An object where each key is an attribute name and each value is an attribute value.

**8.3.15**  `repository`: A URL to the Git (or other version control system) repository.
