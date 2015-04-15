---
layout: page
title: CSS Style Guide
permalink: /css/
---

In general, follow [Github's CSS styleguide](https://github.com/styleguide/css).

## Use lower-hyphen-case for Variable and Mixin Names

CSS properties already use lower-hyphen-case, so let's be consistent in our Sass.

    // Not so good
    $borderColor: $fafafa;
    @mixin error_warning() {

    // So good!
    $border-color: $fafafa;
    @mixin error-warning() {

The only exception is if a library (like Bootstrap) includes their own mixins or variables.
