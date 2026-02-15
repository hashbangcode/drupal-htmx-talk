---
theme: uncover
paginate: true
class:
  - lead

size: 16:9
style: |
  img[alt~="centre"] {
    display: block;
    margin: 0 auto;
    max-width: 80%;
  }
  .smaller-text {
    font-size: 0.75rem;
  }
  .small-text {
    font-size: 0.5rem;
  }
  p {
    text-align: left;
  }
  p.centre {
    text-align: center;
  }
  .hidden-bullets li {
    list-style-type: none
  }
  .left {
    width: 50%;
    float: left;
  }
  .right {
    width: 50%;
    float: right;
  }
footer: "Philip Norton [hashbangcode.com](https://www.hashbangcode.com) [fosstodon.org@hashbangcode](https://fosstodon.org/@hashbangcode) [fosstodon.org@philipnorton42](https://fosstodon.org/@philipnorton42)"
marp: true

---

# Using HTMX To Make Interactive Elements In Drupal

DrupalCamp England 2026

<!-- Speaker notes will appear here. -->

---

# Philip Norton
- Developer at Code Enigma
- Involved with Drupal for 20 years
- Owner of `#! code` (www.hashbangcode.com)
![bg h:90% right:40%](../src/assets/images/lily58.png)

<!--
- Doing Drupal for 15+ years.
- Programming for 20.
- This is a picture of a Lily58 mechanical keyboard.
-->

---

<!-- _footer: "" -->
## Source Code
- Talk is available at:
<small>https://github.com/hashbangcode/drupal-htmx-talk</small> or via QR code
- All code seen can be found at:
  - <small>[https://bit.ly/46arnzj](https://github.com/hashbangcode/htmx-php-examples) HTMX examples</small>
  - <small>[https://bit.ly/3OHAIIR](https://github.com/hashbangcode/drupal-htmx-examples) Drupal examples</small>
- More resources at <small>[www.hashbangcode.com](https://www.hashbangcode.com)</small>

![bg h:50% right:30%](../src/assets/images/qr_slides.png)

<!-- 
- Scan the QR code for the talk repo. Which has links to all of the resources you need.
- There's also some information about HTMX and links to these projects on hashbangcode.
-->

---

# Using HTMX To Make Interactive Elements In Drupal

---

## HTMX In Drupal

- HTMX added to Drupal in 11.3 as a core component.
- This is the new standard for ajax requests.
- All existing ajax features will be re-written in HTMX.

<!--
- Technically, it's been in Drupal since 11.2, but only as experimental.
- First I'm going to introduce you to HTMX.
- Run a couple of examples using PHP has a backend.
- Then look at using HTMX in Drupal.
-->
---

# HTMX

---

## What is HTMX?

- JavaScript framework.
- Allows ajax calls and CSS Transitions _without writing JavaScript code_.
- Small at 16K (minified and gzipped).
- No other dependencies.
- Platform agnostic.
- Plugins extend functionality.
<!--
Using HTMX means that you don't need to write any JavaScript to make ajax requests.
Everything is powered through HTML attributes.
-->
---

## What is HTMX?

- Behaviour is added to HTML attibutes.
- Any element can issue a web request.
- All responses should be in HTML.
<!--
No strange JSON formatted data.
The state of the application is in the HTML, rather than kept external to it. 
-->

---

## Installing HTMX

Download and include the single JavaScript file.

```html
<script src="htmx.min.js"></script>
```

There are CDN options available.

We are now ready to use HTMX.

<!--
- Just needs one file to be present.
-->
---

## HTMX Attributes

- Prefixed with `hx-` or `data-hx-`.
- 11 attributes are commonly used.
- There are another 20-30 that add more functionality.

---
<!-- _footer: "" -->
## HTMX Attributes - Example

```html
<button hx-get="index.php" hx-target="#div1">Submit</button>

<div id="div1"></div>
```

This example will:
- `hx-get` = Send a "get" request to the index.php path.
- `hx-target` = Write the response in the element with the ID "div1".

<!--
- We could also add 'hx-trigger'. The event that triggers the request. As the default is 'click' for buttons it isn't needed here.
- What does the response look like?
- It just needs to return HTML.
-->
---

## HTMX Requests

- All requests from HTMX will have the `hx-request` header.
- Certain attributes will add further headers to the request.

---

## Responding To The Requset

Your response should be in pain HTML.

We could respond to the previous request with:

```html
<p>Button clicked!</p>
```
Which would be injected into our page like this.

```html
<div id="div1"><p>Button clicked!</p></div>
```
<!--
Let's do something more interesting.
-->
---

## Responder Class In PHP

A simple class to respond to HTMX requests.

```php
class Htmx {
    public static function isHtmxRequest(): bool {
        return isset($_SERVER['HTTP_HX_REQUEST']) && $_SERVER['HTTP_HX_REQUEST'] === 'true';
    }

    public static function isGet(): bool {
        return $_SERVER['REQUEST_METHOD'] === 'GET';
    }
}
```

---

## Responding To The Request

We detect the `hx-request` header and the correct HTTP method before responding.

```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
    echo '<p>Button clicked at ' . date('r') . '.</p>';
}
```

The div now looks like this:

```html
<div id="div1"><p>Button clicked at Sun, 08 Feb 2026 16:00:23 +0000.</p></div>
```
---

# HTMX Common Attributes

---

## HTTP Verbs

Different HTTP verbs are available through attributes.

- `hx-get` does a "get" request.
- `hx-post` does a "post" requset.
- `hx-delete` does a "delete" requset
- `hx-patch` does a "patch" requset
- `hx-put` does a "put" requset

---

## hx-target

The target element to be swapped.

```html
<button hx-get="index.php" hx-target="#div1">Submit</button>

<div id="div1"></div>
```

---

## hx-trigger

Defines the action that will trigger the request.

```html
<div hx-post="index.php" hx-trigger="click">Click me</div>
```

- Can be left out for elements that have default triggers.
- Eg. `<button>` elements automatically have a `click` trigger.

---

## hx-trigger Examples

```html
<div hx-post="index.php" hx-trigger="click">Click me</div>
```

```html
<div hx-post="index.php" hx-trigger="click once">Click me</div>
```

```html
<div hx-post="index.php" hx-trigger="revealed"></div>
```

```html
<div hx-post="index.php" hx-trigger="load delay:500ms"></div>
```

```html
<input hx-get="index.php" hx-trigger="keyup delay:1s" />
```

---
<!-- _footer: "" -->
## hx-select

Select content to swap with the target element from the response.

HTMX:
```html
<button hx-get="index.php" hx-select="#response" hx-swap="outerHTML">Click</button>
```

Response:
```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
    echo '<p id="response">Button clicked at ' . date('r') . '.</p>';
    echo '<p>Some extra content that won\'t get displayed.</p>';
}
```

---
<!-- _footer: "" -->
## hx-select-oob

Select an "Out Of Band" element to also target in the response.

HTMX:
```html
<button hx-get="index.php" hx-select-oob="#div1">Send Request</button>
<div id="div1"></div>
```

Response:
```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
  echo 'Request Sent';
  echo '<div id="div1">Button clicked at ' . date('r') . '.</div>';
}

```
<!--
Out of band refers to the element and the result not being tied closely together. HTMX is based on the "locality of behavior" rule, which means things should happen close to where they originated.
This will:
- Replace the "Send Request" text with "Requset Sent".
- Replace the #div1 with the selected element from the request.
-->
---
<!-- _footer: "" -->
## hx-select-oob - Continued

You can comma separate this property to do multiple things.

HTMX:
```html
<button hx-get="index.php" hx-select-oob="#div1,#div2">Send Request</button>
<div id="div1"></div> <div id="div2"></div>
```
Response:
```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
  echo 'Request Sent';
  echo '<div id="div1">Button clicked at:</div>';
  echo '<div id="div2">' . date('r') . '.</div>';
}

```
<!--
This will:
- Replace the "Send Request" text with "Requset Sent".
- Replace the #div1 with the selected element from the request.
- Replace the #div2 with the selected element from the request.

Be careful, anything not part of the hx-select-oob attribute will be injected into the triggering element.
-->
---

## hx-swap

- Controls how an element will be swapped into the page.
- Defaults to `innerHTML` - replace the HTML contents.

  - `outerHTML` - Replace the element.
  - `textContent` - Replace contents without parsing as HTML.

---
## hx-swap - Continued

- `beforebegin`, `afterbegin`, `beforeend`, `afterend` - Place the response before or after the element and its children.
- `delete` - Delete the target element from the page.
- `none` - Do nothing with the response (but still process out of band items).

---
<!-- _footer: "" -->

## hx-swap-oob

Swap an "Out Of Band" element. Similar to `hx-select-oob` but the response tells HTMX what to swap.

HTMX:
```html
<button hx-get="index.php" hx-swap="none">Send Request</button>
<div id="div1"></div>
```
Response:
```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
    echo '<div id="div1" hx-swap-oob="true">Button clicked at ' . date('r') . '.</div>';
}
```
<!--
- The hx-swap="none" means that the triggering element won't be altered.
- We can piggy back other things that we want to inject into the page in the response.
-->
---


## CSS Transitions

- HTMX adds classes to elements during different events.
- `htmx-added` is added to new content before it is added to the page.
- We can use these classes to animate what HTMX is doing with CSS transitions.

---

## CSS Transitions

The following will fade in a bit of content inside the `div1` element as it is loaded into the page.

```css
#div1 p {
    opacity: 1;
    transition: opacity 1s ease-in;
}
#div1 p.htmx-added {
    opacity: 0;
}
```
<!--
When the element is first put onto the page HTMX will add the 'htmx-added' class. Which will be removed once the element is in place (or settled).
This means we can set the htmx-added to be transparent and then fade it in to the default using the transition property.
-->

---
## Configuring HTMX
- It is possible to configure HTMX via a number of settings.

- Can be set through meta tags (preferred)

```html
<meta name="htmx-config" content='{"defaultSwapStyle":"outerHTML"}'>
```

Or JavaScript.
```html
<script>
htmx.config.defaultSwapStyle = 'outerHTML';
</script>
```
<!--
This means that every time an element is added to the page it will use outerHTML, rather than the default of innerHTML.
This will replace the elements instead of injecting data into them.
This configuration option changes how the response is handled.

This changes the global swap style that HTMX uses.
Be careful! This can change how your application operates.
-->
---

# DEMO!
<!--
HTMX has the concept of inherited attributes. So you can make the whole form an ajax form.
-->
---

# HTMX In Drupal

---

## HTMX In Drupal

- HTMX is included in Drupal as a library.
- A few classes exist to assist with adding attributes and responding to requests.

---

## HTMX in Drupal

The standard usage of HTMX in Drupal is to use the `data-hx-` prefix for attributes.

Eg: 
```html
<button data-hx-get="index.php" data-hx-target="#div1">Submit</button>

<div id="div1"></div>
```

---

## Drupal Libraries

HTMX can be included into the page through a Drupal library.

- `core/htmx` - The core HTMX library.
- `core/drupal.htmx` - Additional scripts for Drupal.
- You rarely need to inject these libraries directly.

---

## Drupal Libraries

The library `core/drupal.htmx` includes three files:

- `htmx-assets.js` - Adds assets the current page requires.
- `htmx-behaviors.js` - Connect `Drupal.behaviors` to HTMX inserted content.
- `htmx-utils.js` - Helper functions for the other two files.


---

## Drupal Integration

- The `\Drupal\Core\Htmx` class is used as a wrapper around the HTMX libraries and attribute injection for render arrays.
It can also inject headers for responding to HTMX.
<br>
- `\Drupal\Core\Htmx\HtmxRequestInfoTrait` is used to detect HTMX requests and associated headers.

<!--
Of couse, there is technically nothing to stop you from just including the HTMX library and adding the attributes to your HTML.
The Htmx class just facilitates this.
-->
---
<!-- _footer: "" -->
## Htmx Class

- Has a number of methods that set up attributes.

<div>
<div class="left">

```php
$output['element'] = [
  '#type' => 'html_tag',
  '#tag' => 'p',
  '#value' => 'Content'
];

$htmx = new Htmx();
$htmx->get()
  ->swap('afterend')
  ->trigger('revealed')
  ->applyTo($output['element']);
```

</div>

<div class="right">

```html
<p
  data-hx-get="/route"
  data-hx-swap="afterend"
  data-hx-trigger="revealed">
Content
</p>
```

</div>

</div>

---

## Htmx Class

- The HTMX class can also be used with a different syntax.

```php
(new Htmx())
  ->get()
  ->swap('afterend')
  ->trigger('revealed')
  ->applyTo($output['element']);
```

---

## HTMX Drupal Response

- Drupal has the ability to respond to a HTMX response with HTML.
- This uses the class `Drupal\Core\Render\MainContent\HtmxRenderer`.
- Renders the render array as a little HTML document and sends this upstream.

---

## HTMX Drupal Response

HtmxRenderer is invoked by:
- `_wrapper_format=drupal_htmx` as a query on the incoming request.
- The `_htmx_route=true` on the route reponding to the request.

---

# HTMX With Controllers

---

## HTMX With Controllers

- Think about your approach.
  - Do you have two actions in your controller or one action that serve normal and one for HTMX requests?
- Two actions means you need to tag one route with the route option `_htmx_route: TRUE`.
- One route means you need to add some code to your controller footprint to react to HTMX requests.

---

## HTMX With Controllers

The `__htmx_route: TRUE` is used to create the HTMX response action.

```yml
mymodule_controller_action_htmx:
  path: '/htmx-response'
  defaults:
    _controller: '\Drupal\mymodule\Controller\MyModuleController::htmx'
  requirements:
    _permission: 'access content'
  options:
    _htmx_route: TRUE

```

---
<!-- _footer: "" -->
## HTMX With Controllers

The `HtmxRequestInfoTrait` trait needs access to the request stack service.

```php
namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\Core\Htmx\HtmxRequestInfoTrait;

class MyController extends ControllerBase {

  use HtmxRequestInfoTrait;

  public function __construct(protected RequestStack $requestStack) {}

  public function action() {}
}
```
<!--
This uses constructor property promotion and auto-wiring to inject the request stack into the controller class.
-->

---

## HTMX With Controllers

Then, in your action:

```php
  public function action() {
    if ($this->isHtmxRequest()) {
      // Respond to HTMX request.
    }
  
    // Respond to normal controller action.
    // Generate markup to set up the HTMX request.
  }
```

---

# HTMX With Forms

---
<!-- _footer: "" -->
## HTMX With Forms

- The trait `HtmxRequestInfoTrait` is part of the FormBase class.
- Important!
  - The HTMX response in Forms is the entire form, so you _need_ add a `hx-select` (or similar) to pick out the relevnat part of the response.
  - Also, forms need to be consistent. You can't just throw elements into the form markup as the elements need to exist in the form build.
<!--
If the elemnts don't exist in the form build they won't be part of the submit process.
Also, use post requests for HTMX in forms.
-->

---

## HTMX With Forms

Forms in the same way as constructors, you just need to decorate the elements in question.

```php
(new Htmx())
  ->post()
  ->target('*:has(>input[name="email"])')
  ->select('*:has(>input[name="email"])')
  ->trigger('keyup delay:1s')
  ->applyTo($form['email']);
```

- This decorates the form element with the HTMX elements.

---

# DEMO!

---

## Drupal Needs You!

- HTMX is in Drupal 11.3 as a fully featured system.
- All of the ajax subsystem now needs to be converted to HTMX.
- There's a lot of work to be done.
- https://www.drupal.org/community-initiatives/replace-ajax-api-with-htmx

---

## HTMX Module

- [https://www.drupal.org/project/htmx](https://www.drupal.org/project/htmx)
- Some extra tools and examples of HTMX in a Drupal setting.
- Has a Views plugin to display Views as HTMX.
- Has a HTMX debugger that logs all HTMX events in the browser console.

---

# Resources

- [htmx.org](https://htmx.org/) - https://htmx.org
- [HTMX Labs](https://htmxlabs.com/) - https://htmxlabs.com

---

## Questions?

- Slides: https://github.com/hashbangcode/drupal-htmx-talk

![bg h:50% right:30%](../src/assets/images/qr_slides.png)

---

## Thanks!

- Slides: https://github.com/hashbangcode/drupal-htmx-talk

![bg h:50% right:30%](../src/assets/images/qr_slides.png)
