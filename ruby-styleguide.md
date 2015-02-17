---
layout: page
title: Ruby Style Guide
permalink: /ruby/
---

It's highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github's or Batsov's styleguide where applicable:

- Use the Ruby 1.9-style hash syntax (key: 'value') with symbol keys wherever possible. Only use `"hash" => "rocket"` syntax when hash keys are required to be strings.
- Use a single newline above and below "private" and "protected" in classes
