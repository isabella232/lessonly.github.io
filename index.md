---
layout: default
---

# Lesson.ly Coding Style

## .editorconfig

The app contains an [`.editorconfig`](https://github.com/rainkingventures/lesson.ly/blob/master/.editorconfig) file which specifies most of the syntax conventions (spacing, etc.) we use. Be sure you're using [an Editorconfig plugin](http://editorconfig.org/#download) with your preferred text editor to ensure your code conforms to these conventions.

## General Guidelines

- **Don't commit commented-out code.** It's a distraction from the actually functional code. If code is obsolete or non-functional, delete it: we'll always have a record of it in Git.

## Language-Specific Styles

<ul class="post-list">
  {% for page in site.pages %}
    <li>
      <h2>
        <a class="post-link" href="{{ page.url | prepend: site.baseurl }}">{{ page.title }}</a>
      </h2>
    </li>
  {% endfor %}
</ul>
