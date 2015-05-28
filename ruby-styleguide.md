---
layout: page
title: Ruby Style Guide
permalink: /ruby/
---

It’s highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github’s or Batsov’s styleguide where applicable:

- Use the Ruby 1.9-style hash syntax (`key: 'value'`) with symbol keys wherever possible. Only use `"hash" => "rocket"` syntax when hash keys are required to be strings.
- Use a single newline above and below "private" and "protected" in classes
- Use a single space within ERB tags, e.g. `<%= "foo" %>`, not `<%="foo"%>`
- Use full words for variables except where a single letter is conventional (e.g. `f` for Rails form objects, `i` for iterators, etc.).
      
      # not so clear
      assignee.assignments.map(&:assignable).each { |a| ...
      
      # so clear!
      assignee.assignments.map(&:assignable).each { |assignable| ...

## Gemfile

We try to use [pessimistic version constraints](http://guides.rubygems.org/patterns/#pessimistic-version-constraint) for all gems in the Gemfile. These use the `~>` operator followed by at least a major and minor gem version (e.g. `"~> 4.2"`). This prevents a gem from being accidentally upgraded by more than a minor version, and Gems following [Semantic Versioning](http://semver.org/) promise to introduce non-backwards-compatible changes only in major versions.
