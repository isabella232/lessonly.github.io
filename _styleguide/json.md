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

For example, if an endpoint returns `[...]` and we later decide to paginate it, we'll need to return an object instead of an array like `{ page: 1, totalPages: 5, lessons: [...]}` and any clients expecting an array will 💥 . We can avoid this by always passing an object `{ lessons: [...] }` even if the only key points to an array.

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

## Never use integer keys with top level JSON when a response sorting order matters

JSON objects that use integer keys which also depend on sorting are automatically sorted in different ways by different browsers. This makes responding with a sorted json object impossible.

```
// Avoid this format for responses
{
  2: "First",
  5: "Second",
  1: "Third"
}

// Some browsers will take the above and change it to
// the following automatically
{
  1: "Third",
  2: "First",
  5: "Second"
}

// Preferred
// Instead we prefer to use an Array of objects for sorting
{
  my_list: [
    {
      id: 2,
      name: "First"
    },
    {
      id: 5,
      name: "Second"
    },
    {
      id: 1,
      name: "Third"
    }
  ]
}
```
