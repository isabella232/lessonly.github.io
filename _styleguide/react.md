---
layout: styleguide
title: React Style Guide
main: true
---

## General Guidelines

We primarly follow Airbnb's style guide when writing [React](https://github.com/airbnb/javascript/tree/master/react). Below we highlight both important guidelines and inconsistencies with Airbnb's style guide.

**Note**: Things marked (In Transition - Frontend Experiment) are currently only required to be used by the frontend engineers as an experiment before rolling out to the entire team. Feel free to follow these guidelines even if you are a fullstack engineer, if you would like!

## Hooks (In Transition - Frontend Experiment)
We prefer the use of function components and hooks over class components. Hooks were added to React in v16.8 and they let you use state and other React functionality without having to use a class. [doc link](https://reactjs.org/docs/hooks-intro.html). This means that we can get all of the functionality we need without needing to deal with complex lifecycle methods, and classes in general, and we can almost always use function components!

### Custom Hooks
In the `/components/shared/hooks` folder

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

What happens if there's more than one component defined in a file? First, leave a comment at the top of the file so that others reading the code will easily know that this file does not follow the standard convention. Second, before settling on a name for your file, talk to the team to ensure you've choosen the most descriptive name.

**Note:** Not previous files are named according to what the component is named. If you're searching for something, you may want to do a global search rather than just search file names.

### Component Name and File Name
We prefer that all component names match the file name. Additionally, imported name must match filename unless there is an import collision. 

### File Name Casing
No snake case anywhere in React. React components should use capital case. All other javascript filenames should use lower kebab case.

### General Naming
Do not use snake case anywhere except for feature flags. All non-react functions/variables should be camelCase.

## PropTypes  (In Transition - Frontend Experiment)
We prefer the use of propTypes whenever possible, and require the use of defaultProps for class components but not function components. Function components should specify the default values for props when deconstructing props at the top of the file. 

## FeatureFlag Interface  (In Transition - Frontend Experiment)
We prefer to utilize our FeatureFlag Component and Higher Order Component to optionally render components that are protected by feature flags. These components already have access to all the features that are enabled for the current user or company and are easily able to lookup the ones that are enabled fast! We access the features using a feature array and referring to the features with their snake_case format. We use snake_case to make it easier for full stack and backend engineers to trace and remove feature flags all the way through our system.

### Usage
Conditionally Show New Functionality

```js
import React from 'react';
import FeatureFlag from '~components/shared/FeatureFlag';

const PracticeComment = ({ comment }) => {
  return (
    <FeatureFlag features={['practice_comment']}>
      {comment}
    </FeatureFlag>
  );
}
```
Refactor Old to Show New Functionality

```js
import React from 'react';
import FeatureFlag from '~components/shared/FeatureFlag';
import NewVideo from '~components/NewVideo';
import OldVideo from '~components/OldVideo';

const VideoComponent = ({ videoId }) => {
  return (
    <FeatureFlag
      features={['new_video_provider']}
      fallback={<OldVideo id={videoId} />}
    >
      <NewVideo id={videoId} />
    </FeatureFlag>
  );
};
```

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

## Dependency Info

### Testing
We use jest and enzyme for writing unit tests in React.

### Drag and Drop
We use react-dnd for our drag and drop needs throughout the application.

### Charting
We use highcharts for charts throughout the application.

### Bootstrap
We are trying to remove bootstrap, do not use it going forward.


## File Structure
- `__tests__` folders for tests these exist in each folder local to the components being tested
- UI folder - UI library components, reusable components 
- Shared folder - other shared frontend components, used in more than one location, put components in here that are actually used more than once
