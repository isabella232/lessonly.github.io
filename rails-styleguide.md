---
layout: page
title: Rails Style Guide
permalink: /rails/
---

While we don’t follow it explicitly, the [community Rails Styleguide](https://github.com/bbatsov/rails-style-guide) (also edited by Bozhidar Batsov) is a solid refrence when in doubt.

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
      Model.joins("LEFT JOIN things ON thing_id = things.id AND another_id = things.another_id")

### ActiveRecord shortcuts

- Use `?` methods only for boolean values to avoid unexpected behavior. For example, ActiveRecord treats `0` as `false`, while Ruby treats `0` as a `true` value.

      # Let's say we have a Progress object with a score of zero:
      progress = Progress.create! lesson: a_lesson, user: a_user, score: 0.0

      # do not do this to determine if the object has a value for score
      progress.score?           # returns false

      # do this
      progress.score.present?   # returns true

      # booleans are okay
      company.assignment_notifications_disabled?  # returns the actual value
