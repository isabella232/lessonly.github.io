---
layout: page
title: Rails Style Guide
permalink: /rails/
---

While we don’t follow it explicitly, the [community Rails Style Guide](https://github.com/bbatsov/rails-style-guide) (also edited by Bozhidar Batsov) is a solid refrence when in doubt.

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

### Views

- Avoid referencing instance variables in partials. It's alright for `show.html.erb` to reference the `@lesson` declared in `LessonsController#show`, but if it calls out to `render "stats"`, `_stats.html.erb` should not know about `@lesson`. Instead pass it in directly: `render "stats", lesson: @lesson`. Eventually, we'll want to use that partial in another context where `@lesson` isn't defined: being explicit now saves us time later.

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

### ActiveRecord bang! methods

ActiveRecord provides "bang" versions of methods like `create`, `update`, and `save` which raise an error if they fail instead of returning false. Prefer these in all cases where you're not explicitly handling the failure, so we find out when they fail.

    # Not so good
    def update_reports!
      uptime_report.update(viewer: user) # you'll never know if I fail
      tps_report.update(viewer: user)
    end

    # So good!
    def update_reports!
      uptime_report.update!(viewer: user)
      tps_report.update!(viewer: user)
    end

    # Also good! (Because we handle the failure case)
    if @user.update(receive_emails: user_params)
      redirect_to @user, notice: "Your preference has been updated."
    else
      flash.now.error "There was a problem updating your preference."
      render :edit
    end
    
### Time Zones

Prefer `Time.current` and `Date.current` in place of `Time.now` and `Date.today`. The `.current` methods will properly take the `Time.zone` into account if set, which `.now` and `.today` do not, leading to inconsistent behavior.
