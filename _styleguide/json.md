---
layout: styleguide
title: JSON API Style Guide
main: true
---

Unless otherwise noted, these rules apply only to internal JSON endpoints, not necessarily [our public API](https://docs.lessonly.com).

## Use JSON for request bodies

Rails controllers handle JSON `params` as easily as form data. JSON is a more concise and flexible format for our endpoints to remain consistent. Because of this we prefer to send Content-Type `application/json` in requests.

## Always return top level JSON objects from endpoints

Working with JSON objects with named keys is easier than working with unnamed Arrays, Integers, and Strings. It is also more understandable for future development.

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

// Avoid returning integers
5

// Avoid returning strings
"some_return_value"

// Recommend returning JSON objects
// Now we understand there is an Attempts key with an array within it
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
