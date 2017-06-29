---
layout: page
title: Ruby Style Guide
permalink: /styleguide/ruby/
---

It’s highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github’s or Batsov’s styleguide where applicable:

###  Use double-quotes whenever possible.

```ruby
# bad
name = 'foo'

# good
name = "foo"
```

### Close your parens when invoking a method.

```ruby
# bad
many_things.include? thing

# good
many_things.include?(thing)
```

### Use the Ruby 1.9-style hash syntax with symbol keys wherever possible.

For example: `key: 'value'`. Only use `"hash" => "rocket"` syntax when hash keys are required to be strings.

### Use a single newline above and below "private" and "protected" in classes.

```ruby
# bad
cool_hash = { :lol => "nope" }

# good
cool_hash = { lol: "nope" }

# hm okay
cool_hash = { "lol" => "nope" }
```

### Use a single space within ERB tags.

e.g. `<%= "foo" %>`, not `<%="foo"%>`

```erb
<!-- bad -->
<%="foo"%>

<!-- good -->
<%= "foo" %>
```

### Use full words for variables except where a single letter is conventional.

For examples of where a single letter is conventional, consider `f` for Rails form objects, `i` for iterators, etc.

```ruby
# not so clear
assignee.assignments.map(&:assignable).each { |a| ...

# so clear!
assignee.assignments.map(&:assignable).each { |assignable| ...
```

### Avoid mixing inline and multi-line conditionals: they hurt readability.

```ruby
# Easy to miss the second condition at the end
if some_condition
  do_something_with_a_really_long_method_name if some_other_condition
end

# It's easier to notice both conditions when they're in the same place
if some_condition && some_other_condition
  do_something_with_a_really_long_method_name
end
```

### Try to use boolean values in conditionals.

...rather than relying on `nil` to be `false` and "anything else" to be `true`. It adds clarity to the purpose of the conditional.

```ruby
# Uses the presence of an object as a boolean
if current_user
  # Do something
end

# Explicitly gets a boolean value representing the presence of a user
if current_user.present?
  # Do something
end
```

### `freeze` strings when their values should never change.

...for example, in constants. It will make the string immutable, which has two advantages. It optimizes memory usage (because Ruby points to the same `String` object instead of instantiating a new `String` on each reference), and also prevents tempering, because in Ruby, constants aren't.

```ruby
DEFAULT_TITLE = "Untitled"
DEFAULT_TITLE << "foo"
DEFAULT_TITLE.inspect # => "Untitledfoo"

DEFAULT_TITLE = "Untitled".freeze
DEFAULT_TITLE << "foo" # => RuntimeError: can't modify frozen string
```

### Use `floor` instead of `to_i` when coercing a float to an integer without rounding.

This makes it clear that the intention is specifically to round the number *down*.

```ruby
# Unclear: uses to_i to truncate the float
(progress.completed_percent * 100).to_i

# More clear: uses floor to round the float down
(progress.completed_percent * 100).floor
```

### Use leading periods when chaining methods on multiple lines.

Too much method chaining is often a bad code smell that something is responsible for too much at once, but if do you happen to find yourself on the multi-line choo choo method chain train please chain responsibly with *leading* periods. e.g.

```ruby
# bad
LatestProgress.
  where(user: manageable_users, lesson: company_lessons).
  completed_yesterday.
  progresses.
  includes(:lesson, user: [:custom_user_fields, :custom_user_field_values]).
  present?

# good
LatestProgress
  .where(user: manageable_users, lesson: company_lessons)
  .completed_yesterday
  .progresses
  .includes(:lesson, user: [:custom_user_fields, :custom_user_field_values])
  .present?
```

### Prefer keyword arguments whenever it isn’t abundantly obvious what an argument is.

```ruby
# The method name makes it clear what the argument is...
# no need to name the argument.
def assignment_status(assignment)

# Even with multiple arguments, if the method name makes
# the order clear, no need to name the arguments.
def add_user_to_groups(user, groups)

# But when the method name gives little hint of the type of argument,
# specify them by name for maximum clarity.
def notify_of_new_assignments(user:, new_assignments:)
```

### Explicitly declare modules.

```ruby
# So good
module MyModule
  class MyClass

# No good
class MyModule::MyClass
```

The latter introduces load order dependencies (`MyModule` must already be loaded or Ruby will raise a `NameError`) and requires every reference to other `MyModule` constants within `MyClass` to also be fully-qualified.

## Service Objects

We use the service object pattern to encapsulate complex logic within self-contained objects. Instead of a 20-line controller action, we’ll call `DoSomethingComplicated.perform(args)` in the controller and move all of the complex logic into the `DoSomethingComplicated` class. This not only helps organize and isolate complex code, but also makes it easier to test.

Service objects are called with `ServiceName.perform`. They should be named imperatively after the actions they perform (e.g. `AssignUser`, `CreateGroup`).

Simple service objects can remain as class methods internally:

```ruby
class DoSomething
  def self.perform(thing)
    thing.perform_action
    SomethingMailer.notify(thing.owner).deliver_later
  end
end
```

but after more than a few lines of code, it will make sense to instantiate an object and extract instance methods for clarity:

```ruby
class DoSomething
  def self.perform(thing)
    new(thing).perform
  end

  def initialize(thing)
    @thing = thing
  end

  def perform
    do_the_action
    notify
  end

  private

  def do_the_action
    @thing.perform_action
  end

  def notify
    SomethingMailer.notify(@thing.owner).deliver_later
  end
end
```

While perhaps overkill in this simple case, it's valuable to be able to scan a service object's `#perform` method and see, on each line, all of its responsibilities.

## Presenter Objects

Similar to how Service Objects abstract away complex business logic from controller actions, we use Presenter Objects to encapsulate complex data-gathering and displaying. As a general rule, if your controller action or mailer contains more than one instance variable, you may benefit from a presenter. For example, take this action:

```ruby
# app/controllers/dashboard_controller.rb
def index
  @overdue_assignments = current_user.assignments.overdue.includes(:assignee, :assignable)
  @incomplete_assignments = current_user.assignments.incomplete.includes(:assignee, :assignable)
  @completed_assignments = current_user.assignments.completed.includes(:assignee, :assignable)
end
```

with a presenter object, it might be reduced to the following:

```ruby
# app/controllers/dashboard_controller.rb
def index
  # used in views like `@dashboard.incomplete_assignments.each`
  @dashboard = DashboardPresenter.new(current_user)
end

# app/presenters/dashboard_presenter.rb
class DashboardPresenter

  def initialize(user)
    @user = user
  end

  def overdue_assignments
    assignments.overdue
  end

  def incomplete_assignments
    assignments.incomplete
  end

  def completed_assignments
    assignments.completed
  end

  private

  def assignments
    @user.assignments.includes(:assignable, :assignee)
  end
end
```

Consider how:

- The controller action is now simpler.
- The presenter is easier (and faster!) to test because it doesn't depend on the controller context of a web request.
- The presenter allows us more easily to DRY up repeated logic with private methods.
- The presenter is a great place to extract complex view logic related to collections. For instance, in the view we might have had

```erb
<% if (@overdue_assignments.empty? && @incomplete_assignments.empty? && @completed_assignments.empty?) %>
  <%= render "blank_slate" %>
<% end %>
```

which can now be something like:

```erb
<%= render "blank_slate" if @dashboard.empty? %>
```

with `delegate :empty?, to: :assignments` in our `DashboardPresenter`.

## Decorators

Just as we use Presenter Objects to encapsulate complex logic related to collections, we use Decorator Objects to do the same for single model instances. The goal is to keep presentation-related logic out of models.

Decorator Objects all inherit from Ruby's `SimpleDelegator` class, which is initialized with an object and delegates any methods it doesn't understand to that object. This way, a `UserDecorator` instance behaves exactly like a `User` instance would in a view context, but with additional presentation-related behavior layered on top that we wouldn't want cluttering up our model itself.

```ruby
# app/decorators/uer_decorator.rb
class UserDecorator < SimpleDelegator

  def formal_address
    case title
    when "Pope" then "Your Holiness, Pope #{name}"
    when "Judge" then "Your Honor, #{name}"
    when "Court Jester" then "Your Royal Silliness, #{name}"
    else name
    end
  end
end

decorated_user = UserDecorator.new(current_user)
decorated_user.name #=> "Francis"
decorated_user.title #=> "Pope"
decorated_user.formal_address #=> "Your Holiness, Pope Francis"
current_user.formal_address #=> NoMethodError
```

If you find yourself adding methods to models that are only ever called from views, you should probably reach for a Decorator Object. Decorator Objects are great for extracting logic from views as well. Conditionals are a great example:

```erb
<p>
  <% if current_user.registered? %>
    <%= t("greetings.registered") %>
  <% else %>
    <%= t("greetings.unregistered") %>
  <% end %>
</p>
```

this could be cleaned up by defining a `greeting` method on our `UserDecorator`:

```ruby
class UserDecorator < SimpleDelegator

  def greeting
    if registered?
      I18n.t("greetings.registered")
    else
      I18n.t("greetings.unregistered")
    end
  end
end
```

used like:

```erb
<p><%= decorated_user.greeting %></p>
```

When deciding whether to use a Decorator or a Presenter, consider whether you're adding behavior to *a single object*. If so use a Decorator, otherwise choose a Presenter.

### Avoid HTML in Decorators

Decorators are great for getting logic out of templates, but some things belong in the templates. HTML is one of those things. While it’s possible to call HTML-generating methods like `link_to` or `content_tag` within decorators by passing in the view context...

```erb
<%= UserDecorator.new(current_user, self).profile_link %>
```

```ruby
class UserDecorator < SimpleDelegator

  def initialize(user, view_context)
    super(user)
    @view_context = view_context
  end

  def profile_link
    @view_context.link_to("Profile", @view_context.user_path(user))
  end
end
```

...this makes decorator objects more complicated, which makes them harder to test, and blurs the line between Ruby objects and HTML templates. It’s a fine line, though: we routinely use decorators to determine which class names to apply. As a general rule, just avoid anything in decorators that you need to pass in a `view_context` to do.

## Jobs

We use jobs (found in `/jobs`) to perform actions in the background. We have a simple naming convention: `VerbNoun(s)Job`. For example, a job that sends an assignment notification may be called `SendAssignmentNotificationJob` or a job that creates a set of tags may be called `CreateTagsJob`.

Each job should `include Sidekiq::Worker` and define a `perform` method which either performs the work or calls methods that perform the work. Here is an example:

```ruby
class EditThingJob
  include Sidekiq::Worker

  def perform(thing_id, attributes = {})
    thing = Thing.find(thing_id)
    thing.update(attributes)
  end
end
```

A more complex job would define private methods to keep `perform` simple and easy to read.

The job will be called via `perform_async`, which will eventually call `perform`. Please note that any arguments passed to `perform_async` will be serialized and, as such, should be simple. Keyword arguments are not available to the `perform` method due to this constraint.

### Jobs that spawn more jobs

In some cases, it makes sense to run a job that simply runs many smaller jobs. For example, every morning we send assignment notifications to every user who has an incomplete assignment due in one week, due in one day, one day overdue, or one week overdue. In these cases, the job that performs an action on a single object should follow the normal naming convention. The job that spawns more jobs should be named `VerbAllNounsJob`. Each job class should be independent of each other; neither should be namespaced by the other. For example:

```ruby
# /jobs/send_assignment_reminder_job.rb
class SendAssignmentReminderJob
  include Sidekiq::Worker

  def perform(assignment_id)
    # Do work
  end
end

# /jobs/send_all_assignment_reminders_job.rb
class SendAllAssignmentRemindersJob
  include Sidekiq::Worker

  def perform
    get_all_one_day_overdue_assignment_ids.each do |assignment_id|
      SendAssignmentReminderJob.perform_async(assignment_id)
    end
  end
end
```

The above example is very much simplified, but demonstrates the desired pattern.

### Changing a job's name

On occasion, it may be necessary to change a job's name. This can cause some problems when several instances of that job are scheduled to be run in the future when the rename is deployed. We can mitigate this by defining a new class with the old job's name that simply extends the new job.

```ruby
class NewJob
  def perform
    ...
  end
end

class OldJob < NewJob
end
```

In this case, if `OldJob` is already queued, it will still run as it extends `NewJob`.

## Gemfile

We try to use [pessimistic version constraints](http://guides.rubygems.org/patterns/#pessimistic-version-constraint) for all gems in the Gemfile. These use the `~>` operator followed by at least a major and minor gem version (e.g. `"~> 4.2"`). This prevents a gem from being accidentally upgraded by more than a minor version, and Gems following [Semantic Versioning](http://semver.org/) promise to introduce non-backwards-compatible changes only in major versions.
