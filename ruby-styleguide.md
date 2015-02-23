---
layout: page
title: Ruby (and Rails) Style Guide
permalink: /ruby/
---

It's highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github's or Batsov's styleguide where applicable:

- Use the Ruby 1.9-style hash syntax (`key: 'value'`) with symbol keys wherever possible. Only use `"hash" => "rocket"` syntax when hash keys are required to be strings.
- Use a single newline above and below "private" and "protected" in classes

### Dealing with SQL

- Prefer ActiveRecord methods to raw SQL

      # not so good
      Model.where("published_at = ? AND title IS NOT NULL", Date.yesterday)

      # so good!
      Model.where(published_at: Date.yesterday).where.not(title: nil)

- Use the symbol syntax (rather than question marks) when interpolating SQL. It helps readability and avoids duplicate arguments:

      # not so good
      Model.where("title ILIKE ? OR description ILIKE ?", "%#{query}%", "%#{query}%")
      
      # so good!
      Model.where("title ILIKE :query OR description ILIKE :query", query: "%#{query}%")

- Uppercase SQL operators to make them visually distinct from table and column names.

      # not so good
      Model.joins("left join things on thing_id = things.id and another_id = things.another_id")

      # so good!
      Model.joins("LEFT JOIN things on thing_id = things.id AND another_id = things.another_id")
