---
layout: styleguide
title: Rails Style Guide
main: true
---

While we don’t follow it explicitly, the [community Rails Style Guide](https://github.com/bbatsov/rails-style-guide) (also edited by Bozhidar Batsov) is a solid reference when in doubt.

### Dealing with SQL

#### Prefer ActiveRecord methods to raw SQL

```ruby
# not so good
Model.where("published_at = ? AND title IS NOT NULL", Date.yesterday)

# so good!
Model.where(published_at: Date.yesterday).where.not(title: nil)
```

#### Use the symbol syntax (rather than question marks) when interpolating SQL.

It helps readability and avoids duplicate arguments:

```ruby
# not so good
Model.where("title ILIKE ? OR description ILIKE ?", "%#{query}%", "%#{query}%")

# so good!
Model.where("title ILIKE :query OR description ILIKE :query", query: "%#{query}%")
```

#### Uppercase SQL operators to make them visually distinct from table and column names.

```ruby
# not so good
Model.joins("left join things on thing_id = things.id and another_id = things.another_id")

# so good!
Model.joins("LEFT JOIN things ON thing_id = things.id AND another_id = things.another_id")
```

#### Prefer `size` over `count`

`count` performs a SQL `COUNT` query every time you call it, while `size` does the same only if the relation hasn’t been returned as an array. If it has, `size` is smart enough to return the length of the array without performing any additional queries. ([Read more.](http://work.stevegrossi.com/2015/04/25/how-to-count-with-activerecord/))

#### Avoid `pluck` in intermediate queries

`pluck` prevents the database from optimizing queries composed of subqueries (because it forces the database to treat subqueries as completely separate queries). Furthermore, `pluck` (with ids) adds memory overhead: every `pluck`ed id must be deserialized from the database into a Ruby `Integer` object only to be re-serialized into a SQL string for the outer query. For large customers this overhead can be significant.

```ruby
# no good - 2 queries and lots of Integer objects
User.where(id: lesson.progresses.pluck(:user_id))

# so good! - 1 faster query and no Integer objects
User.where(id: lesson.progresses.select(:user_id))

# also fine if we actually only want names and emails
emails = company.users.pluck(:name, :email)
```

Of course, if your _end goal_ is an array of simple attributes, that's exactly what `pluck` is for.

### Views

#### Avoid referencing instance variables in partials.

It's alright for `show.html.erb` to reference the `@lesson` declared in `LessonsController#show`, but if it calls out to `render "stats"`, `_stats.html.erb` should not know about `@lesson`. Instead pass it in directly: `render "stats", lesson: @lesson`. Eventually, we'll want to use that partial in another context where `@lesson` isn't defined: being explicit now saves us time later.

#### Avoid ERB views and server-rendered markup when possible.

Instead, we prefer to mount React components that fetch the data they need to render themselves via JSON endpoints. This approach reduces [time to first paint](https://developers.google.com/web/tools/lighthouse/audits/first-meaningful-paint), allows pages to be more resilient to errors, and works toward our end goal of separating data (server-side) and presentation (client-side).

Example:

Instead of:

```ruby
<%= react_component("TestComponent", props: {
    data: SomeSerializer.new(@data).as_json,
  }) %>
```

```js
import React, { Component } from "react";

class TestComponent extends Component {
  render() {
    const { data } = this.props;
    return <Something data={data} />;
  }
}
```

Prefer:

```ruby
<%= react_component("TestComponent") %>
```

```js
import React, { Component } from "react";

class TestComponent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      loading: true,
      data: {}
    };
  }
  componentDidMount() {
    request("/someserialized/data.json").then(data => {
      this.setState({ data, loading: false });
    });
  }
  render() {
    const { data } = this.state;
    return <Something data={data} />;
  }
}
```

### ActiveRecord shortcuts

#### Use `?` methods only for boolean values to avoid unexpected behavior.

For example, ActiveRecord treats `0` as `false`, while Ruby treats `0` as a `true` value.

```ruby
# Let's say we have a Progress object with a score of zero:
progress = Progress.create! lesson: a_lesson, user: a_user, score: 0.0

# do not do this to determine if the object has a value for score
progress.score?           # returns false

# do this
progress.score.present?   # returns true

# booleans are okay
company.assignment_notifications_disabled?  # returns the actual value
```

### ActiveRecord bang! methods

ActiveRecord provides "bang" versions of methods like `create`, `update`, and `save` which raise an error if they fail instead of returning false. Prefer these in all cases where you're not explicitly handling the failure, so we find out when they fail.

```ruby
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
```

### Time Zones

#### Prefer `Time.current` and `Date.current` in place of `Time.now` and `Date.today`.

The `.current` methods will properly take the `Time.zone` into account if set, which `.now` and `.today` do not, leading to inconsistent behavior.

#### Avoid `Time.parse` and `Date.parse`.

(These also don’t account for timezones), and instead prefer `Time.zone.parse` if you need a `Time` object, and calling `to_date` on the result if you need a `Date`.
