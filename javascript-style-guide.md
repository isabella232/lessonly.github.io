---
layout: page
title: JavaScript Style Guide
permalink: /javascript/
---

## General Guidelines

- Put all JavaScript within `.js` or `.js.coffee` files (not `<script>` tags unless you have a good reason).

## Use lowerCamelCase

For variable and function names in Javascript, use lowerCamelCase.

    // Not so good
    var llamas_count = 99;
    function do_something() {};

    // So good!
    var llamasCount = 99;
    function doSomething() {};

## Use the `data-behavior` pattern

When attaching JavaScript behavior to DOM elements, use a `data-behavior` attribute instead of a class or ID. Classes and IDs can change for reasons unrelated to behavior (e.g. styling, anchoring), which makes JavaScript brittle.

Instead of this:

    <input class="do_something">
    $('.do_something').doSomething();

Use this:

    <input data-behavior="do_something">
    $('[data-behavior~=do_something]').doSomething();

We've settled on lowercase with underscores (i.e. "snake_case") for `data-behavior` attributes.
