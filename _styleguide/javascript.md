---
layout: styleguide
title: JavaScript Style Guide
---

## General Guidelines

We primarly follow Airbnb's style guide when writing [JavaScript](https://github.com/airbnb/javascript). Below we highlight both important guidelines and inconsistencies with Airbnb's style guide.

### Put all JavaScript within `.js` or `.jsx` files.

Do not use `<script>` tags unless you have a good reason.

## Use lowerCamelCase

For variable and function names in Javascript, use lowerCamelCase.

```js
// Not so good
var llamas_count = 99;
function do_something() {};

// So good!
var llamasCount = 99;
function doSomething() {};
```

## Prefer Single Quotes

Prefer single quotes in JavaScript and CoffeeScript strings, as they're more likely to contain double-quoted substrings like HTML attributes. Double quotes are fine in CoffeeScript when using string interpolation, though.

```js
// not good (we double-quote HTML attribute values)
$("<div class='my-class'/>")

// also not good (escaping is harder to read)
$("<div class=\"my-class\"/>")

// good
$('<div class="my-class"/>')

// just fine
message = "Hello, #{@user.get('name')}!"
```

## Use the `data-behavior` pattern

When attaching JavaScript behavior to DOM elements, use a `data-behavior` attribute instead of a class or ID. Classes and IDs can change for reasons unrelated to behavior (e.g. styling, anchoring), which makes JavaScript brittle.

Instead of this:

```js
// <input class="do_something">
$('.do_something').doSomething();
```

Use this:

```js
// <input data-behavior="do_something">
$('[data-behavior~=do_something]').doSomething();
```

We've settled on lowercase with underscores (i.e. "snake_case") for `data-behavior` attributes.
