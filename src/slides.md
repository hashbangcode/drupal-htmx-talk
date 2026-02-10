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
- Writer at `#! code` (www.hashbangcode.com)
- NWDUG co-organiser
![bg h:50% right:40%](../src/assets/images/lily58.png)

<!--
- Doing Drupal for 15 years.
- Programming in general for 20.
- This is a picture of a Lily58 mechanical keyboard.
-->

---

<!-- _footer: "" -->
## Source Code
- Talk is available at:
<small>https://github.com/hashbangcode/</small> or via QR code.
- All code seen can be found at <small>[http://bit.ly/](https://github.com/hashbangcode/)</small>
- More resources at <small>[www.hashbangcode.com](https://www.hashbangcode.com)</small>

![bg h:50% right:40%](../src/assets/images/qr_slides.png)

<!-- 
- Scan the QR code for the talk repo. Which has links to all of the resources you need.
- There's also a Drupal and Storybook article on my site.
-->

---

# Using HTMX To Make Interactive Elements In Drupal

---

## HTMX In Drupal

- HTMX added to Drupal in 11.3 as a core component.
- This is the new standard for ajax requests.
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
- Gives access to ajax and CSS Transisions.
- Small - 16K (minified and gzipped).
- Uses HTML attributes.
- Plugins extend functionality.
- Platform agnostic.
<!--
Using HTMX means that you don't need to write any JavaScript to make ajax requests.
Everything is powered through HTML attributes.
-->
---

## Adding HTMX

- Download and include the JavaScript file.

```html
<script src="js/htmx.min.js"></script>
```

- There are CDN options available.

<!--
- Just needs one file to be present.
-->
---

## HTMX Attributes

- 11 attributes are commonly used.
- There are another 20-30 that add more functionality.

For example:
```html
<button 
  hx-get="index.php" 
  hx-target="#request-output"
>Submit</button>
```
---
<!-- _footer: "" -->
# HTMX Attributes

```html
<button 
  hx-get="index.php" 
  hx-target="#request-output"
>Submit</button>
<div id="request-output"></div>
```
This example will:
- `hx-get` = Send a "get" request to the index.php path.
- `hx-target` = Write the response in the element with the ID "request-output".

<!--
- We could also add 'hx-trigger'. The event that triggers the request. As the default is 'click' for buttons it isn't needed here.
- What does the response look like?
- It just needs to return HTML.
-->
---

## HTMX Requests

- All requests from HTMX will have the 'hx-request' header.
- Certain attributes will add further headers to the request.

---

## Responding To The Requset

Your response should be in pain HTML.

We could respond with

```html
<p>Button clicked!</p>
```
Whish would be injected into our page like this.

```html
<div id="request-output"><p>Button clicked!</p></div>
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

We detect the hx-request header and the correct HTTP method and respond.

```php
if (Htmx::isHtmxRequest() && Htmx::isGet()) {
    echo '<p>Button clicked at ' . date('r') . '.</p>';
}
```

The div now looks like this:

```html
<div id="request-output"><p>Button clicked at Sun, 08 Feb 2026 16:00:23 +0000.</p></div>
```

---

## HTTP Verbs

Different HTTP verbs are available.

- `hx-get` does a "get" request.
- `hx-post` does a "post" requset.
- `hx-delete` does a "delete" requset
- `hx-patch` does a "patch" requset
- `hx-put` does a "put" requset


---

## Select

Select Out Of Band

---

## Swap

Swap Out Of Band

---

## Triggers

```html
<div hx-post="index.php" hx-trigger="click">0</div>
```

```html
<div hx-post="index.php" hx-trigger="once"></div>
```

```html
<div hx-post="index.php" hx-trigger="revealed"></div>
```

```html
<div hx-post="index.php" hx-trigger="load delay:500ms"></div>
```

```html
<input hx-get="index.php" hx-trigger="input changed delay:1s" />
```


---

## CSS Transitions

- HTMX adds classes to elements during different events.
- `htmx-added` is added to new content before it is added to the page.
- We can use these classes to animate what HTMX is doing with CSS transitions.

---

## CSS Transitions

The following will fade in a bit of content inside the `request-output` element as it is loaded into the page.

```css
#request-output p {
    opacity: 1;
    transition: opacity 1s ease-in;
}
#request-output p.htmx-added {
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
- The preferred way to do with is via a metatag.

```html
<meta name="htmx-config" content='{"defaultSwapStyle":"outerHTML"}'>
```
<!--
This means that every time an element is added to the page it will use outerHTML, rather than the default of innerHTML.
This will replace the elements instead of injecting data into them.
This configuration option changes how the response is handled.
-->

---
## Configuring HTMX
- Configuration options can be set through JavaScript.
```html
<script>
htmx.config.defaultSwapStyle = 'outerHTML';
</script>
```

---

examples
- fetch the time
- website counter
- comment form
- form validator
- infinite scroll

---

# HTMX in drupal

---
## HTMX in drupal

The standard usage of HTMX in Drupal is to use the `data-hx-' prefix for attributes.

Eg: 
```html
<button 
  data-hx-get="index.php" 
  data-hx-target="#request-output"
>Submit</button>
<div id="request-output"></div>
```

---

## Drupal Libraries

- `core/htmx` - The core HTMX library.
- `core/drupal.htmx` - Additional scripts for Drupal.

---

## Drupal Integration

- The `Htmx` class.
  - Wrapper around the Htmx libraries and attribute injection.

<!--
Of couse, there is technically nothing to stop you from just including the HTMX library and adding the attributes to your HTML.
The Htmx class just facilitates this.
-->
---

##

use attributes in the same way

---

## The Htmx Class



---

## HTMX Trait

---

HTMX Routes

---

examples
- load a page via htmx
- cascading date select with HTMX
- block config forms

---

---

## Example Links
- [Link to website](https://www.hashbangcode.com)
- [Link to slide](#5)
---

<!-- _footer: "" -->
## Page without a footer
- Nope, no footer.

---

## Small Text Slide
<p class="small-text">This is small text.</p>

---

## Talk template with image
- Something.
![bg right:50%](../src/assets/images/hashbangcode_logo.png)

---

<!-- _footer: "" -->
![bg h:100%](../src/assets/images/hashbangcode_logo.png)

---

# Resources

- [htmx.org](https://htmx.org/)
- [HTMX Labs](https://htmxlabs.com/)

---

## Questions?

- Slides: https://github.com/hashbangcode/drupal-htmx-talk

![bg h:50% right:40%](../src/assets/images/qr_slides.png)

---

## Thanks!

- Slides: https://github.com/hashbangcode/drupal-htmx-talk

![bg h:50% right:40%](../src/assets/images/qr_slides.png)
