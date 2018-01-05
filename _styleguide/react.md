---
layout: page
title: JavaScript Style Guide
---

## General Guidelines

We primarly follow Airbnb's style guide when writing [React](https://github.com/airbnb/javascript/tree/master/react). Below we highlight both important guidelines and inconsistencies with Airbnb's style guide.

## Quotes

We use single quotes unless we are defining attributes within a element (such as defining class names).

```js
// not good
<div className='ErrorContainer ElementLibrary-errorMessage lh-24'>
  {this.state.error}
</div>

// good
<div className="ErrorContainer ElementLibrary-errorMessage lh-24">
  {this.state.error}
</div>

// not good
if (this.state.sortOption === "title") {
  const sortedData = orderBy(data, [content => content.title.toLowerCase()]);
}

// good
if (this.state.sortOption === 'title') {
  const sortedData = orderBy(data, [content => content.title.toLowerCase()]);
}
```

## Naming Conventions

Naming is hard and will probably always be hard! Here's a few guidelines to (hopefully) make choosing names less painful.

We name our files according to the name of the component defined and exported within the file. For example, the code below would live in a file named `ElementLibrary.jsx`.

```js
import React, { Component } from 'react';

class ElementLibrary extends Component {
  ...
}

export default ElementLibrary;

```

When choosing names for new components, try to be as descriptive as possible. Ideally, anyone on the team will know the purpose of the component by reading its name.

Note: What happens if there's more than one component defined in a file? First, leave a comment at the top of the file so that others reading the code will easily know that this file does not follow the standard convention. Second, before settling on a name for your file, talk to the team to ensure you've choosen the most descriptive name.

## Imports

When importing code into a new component or file, we put all modules/libraries at the top of the file before any relative imports.

We default to naming an import according to the name of the default export unless there's good reason to stray from this standard.

## Spacing

We do not put spaces around curly braces when interpolating our JavaScript.

```js
// not good
<ElementLibrarySearch
  i18n={ this.props.i18n.library }
  search={ this.updateQuery }
  value={ this.state.query }
  clearSearchInput={ this.clearSearchInput }
/>

// good
<ElementLibrarySearch
  i18n={this.props.i18n.library}
  search={this.updateQuery}
  value={this.state.query}
  clearSearchInput={this.clearSearchInput}
/>
```
