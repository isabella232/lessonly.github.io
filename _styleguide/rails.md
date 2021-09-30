---
layout: styleguide
title: Rails Style Guide
main: true
---

While we don’t follow it explicitly, the [community Rails Style Guide](https://github.com/bbatsov/rails-style-guide) (also edited by Bozhidar Batsov) is a solid reference when in doubt.

### Dealing with SQL

#### Never use string interpolation with raw SQL

While it _can_ be done safely, string interpolation in raw SQL opens the door to SQL injection attacks. Consider the following code to get users with progress on a Lesson:

```ruby
User.joins("INNER JOIN progresses ON progresses.user_id = users.id AND progresses.lesson_id = #{some_lesson_id}")
```

while we might be sure _today_ that `some_lesson_id` is a value we control, tomorrow someone could call a method containing this code but pass in `params[:lesson_id]`, at which point a malicious user could send `params[:lesson_id]` like `1; DROP TABLE users;` and [delete all our user data](https://xkcd.com/327/). So it’s best to avoid string interpolation in SQL entirely.

The best way to do this is to use ActiveRecord or Arel instead of raw SQL, which will prevent SQL injection through quoting or prepared statements. For example, instead of the above, we can make this query safer with any of the following:

```ruby
# Sanitizes some_lesson_id
User.joins(:progresses).where(progresses: { lesson_id: some_lesson_id })
User.joins(:progresses).merge(Progress.where(lesson_id: some_lesson_id))
# Quotes some_lesson_id
User.joins(:progresses).where("progresses.lesson_id = ?", some_lesson_id)
```

We should prefer the sanitizing approaches above, which turns malicious input like `1; DROP TABLE users` into `1` and the app will continue to work, whereas the quoting approach will turn it into `'1; DROP TABLE users'` which will raise a server error because of a type mismatch (string and not integer).

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

### Models

#### Prefer explicit mapping between attribute and database integer for Enums

Without specifying an [enum](https://api.rubyonrails.org/classes/ActiveRecord/Enum.html)
key's database integer, the order of the enum determines its mapping to the database.
If the order changes in the future, existing records could point to a different
enum value. By using explicit mapping, enum attributes always point to the same
mapped value.

```ruby
# Prefer explicit mapping to integer value
enum scenario_type: {
  webcam_recording: 0,
  screen_recording: 1,
  audio_recording: 2
}

# Avoid order dependent mapping
enum scenario_type: %i(webcam_record screen_recording audio_recording)
```

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

### Controllers

#### Avoid `except` in `_action` callbacks (or avoid such callbacks entirely)

Applying an `_action` callback to all controller actions `except` some will automatically (and perhaps unexpectedly) opt us into this behavior in all future controller actions. This violates the [Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment) and has led to bugs in the past.

```ruby
# Not good: actions added later will make this query even when unnecessary
before_action :find_the_thing, except: :index

# Better: only the action that needs this behavior will get it
before_action :find_the_thing, only: :show

# Even better: only the action that needs this behavior will get it,
# AND it’s obvious where the behavior is coming from
def show
  find_the_thing
  # ...
end
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
