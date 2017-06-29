---
layout: page
title: JSON API Style Guide
permalink: /styleguide/json/
---

Unless otherwise noted, these rules apply only to internal JSON endpoints, not necessarily [our public API](https://docs.lessonly.com).

## Use JSON for request bodies.

Rails controllers handle JSON `params` as easily as form data, and JSON is more concise and flexible (especially when handling arrays and objects). Also, our endpoints always return JSON, so it’s more consistent also always to send it.
