---
layout: page
title: CSS Style Guide
permalink: /css/
---

## Structure CSS Around Components

Also called "object-oriented CSS", component-based CSS brings a number of benefits to customer and developer happiness (in the form of improved performance and maintainability). Fundamentally, it's about encapsulating styles into self-contained units. For example, here is some traditional CSS:

    // app/stylesheets/lessons.scss
    #lesson_viewer {
      background: #eee;
      h1 {
        font-size: 2em;
      }
      &.past_due {
        background: #f00;
      }
      &.expanded {
        height: 400px;
      }
    }

and here is how it would be written in a component-based way (using [SuitCSS naming conventions](https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md)):

    // app/stylesheets/components/lesson_viewer.scss
    .LessonViewer {
      background: #eee;
    }
    .LessonViewer-heading {
      font-size: 2em;
    }
    .LessonViewer--pastDue {
      background: #f00;
    }
    .LessonViewer.is-expanded {
      height: 400px;
    }

### The Basics

- Each component gets its own file within `stylesheets/components`.
- We use **only class names**: ID selectors bring specificity headaches, and element selectors are inflexible when we need a different element.
- The CSS is **flat**, with no nested classes, which improves rendering performance and reduces developer headaches when trying to override styles.
- Instead of nesting, the class names themselves indicate hierarchy:
  - the **component** itself has an upper-camel-cased class name, (e.g. `LessonViewer`).
  - Class names for **elements within the component** begin with the component name followed by a hyphen, and then a lower-camel-cased name (e.g. `LessonViewer-heading`).
  - Class names for **variations on a component** also begin with the component name followed by two hyphens, and then a lower-camel-cased name for the variation. Note that in the markup, the element with the `LessonViewer--pastDue` class also needs the base `LessonViewer` class.
  - **Temporary states** like `.is-expanded` (which might be added by JavaScript based on user action) get their own class name, which begins with `is-` followed by a lower-camel-case name for the state. But their styles are always scoped to the component's class to avoid conflicts with other components that have an `is-expanded` state.

### Pro Tips

- Not everything should be a component:
  - *Base styles* live within `stylesheets/global.scss` and affect the site as a whole. Avoid changing these unless you really intend to affect All Of The Things.
  - *Utility classes* live within `stylesheets/utilities.scss` and define single-purpose classes (starting with `u-`, e.g. `u-clearFix`) for simple, reusable things like clearing floats.
- Components can (and should!) contain other components. Just because a button is with an `.Element` component doesn't mean it must be `.Element-button`. If used elsewhere, make it its own `.Button` component.
- Components can inherit from other components. For example, if you have an `.Element--question` variation that starts needing its own variations (e.g. for different question types), consider making it its own `.QuestionElement` component so it can have a `.QuestionElement--freeResponse` variation. Just be sure the markup contains the base component as well: `class="Element QuestionElement"`

While these conventions are restrictive, this is intentional in order to keep the CSS simple. If you find yourself needing more complicated structures, consider how what you need might be broken up into multiple components.

## Use lower-hyphen-case for Variable and Mixin Names

CSS properties already use lower-hyphen-case, so let's be consistent in our Sass.

    // Not so good
    $borderColor: $fafafa;
    @mixin error_warning() {

    // So good!
    $border-color: $fafafa;
    @mixin error-warning() {

The only exception is if a library (like Bootstrap) includes their own mixins or variables.
