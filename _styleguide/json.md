---
layout: styleguide
title: JSON API Style Guide
main: true
---

Unless otherwise noted, these rules apply only to internal JSON endpoints, not necessarily [our public API](https://docs.lessonly.com).

## Always return JSON for request bodies

Rails controllers handle JSON `params` like form data. JSON is more concise and flexible (especially when handling arrays and objects) which means it is a more consistent standard for returning via endpoints.

```js
// Avoid returning arrays
[
  {
    id: 1
  },
  {
    id: 2
  }
]

// Recommend returning JSON
{
  attempts: [
    {
      id: 1
    },
    {
      id: 2
    }
  ]
}
```
