---
layout: page
title: Ruby Style Guide
permalink: /ruby/
---

It’s highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github’s or Batsov’s styleguide where applicable:

- Use double-quotes whenever possible.
```ruby
# bad
  name = 'foo'

# good
  name = "foo"
```
- Close your parens when invoking a method.
```ruby
# bad
  many_things.include? thing

# good
   many_things.include?(thing)
```
- Use the Ruby 1.9-style hash syntax (`key: 'value'`) with symbol keys wherever possible. Only use `"hash" => "rocket"` syntax when hash keys are required to be strings.
- Use a single newline above and below "private" and "protected" in classes.
```ruby
# bad
  cool_hash = { :lol => "nope" }

# good
  cool_hash => { lol: "nope" }

# hm okay
  cool_hash = { "lol" => "nope" }
```
- Use a single space within ERB tags, e.g. `<%= "foo" %>`, not `<%="foo"%>`
```ruby
# bad
  <%="foo"%>

# good
  cool_hash => { lol: "nope" }

# hm okay
  cool_hash = { "lol" => "nope" }
```
- Use full words for variables except where a single letter is conventional (e.g. `f` for Rails form objects, `i` for iterators, etc.).

      # not so clear
      assignee.assignments.map(&:assignable).each { |a| ...

      # so clear!
      assignee.assignments.map(&:assignable).each { |assignable| ...
- Avoid mixing inline and multi-line conditionals: they hurt readability.

      # Easy to miss the second condition at the end
      if some_condition
        do_something_with_a_really_long_method_name if some_other_condition
      end

      # It's easier to notice both conditions when they're in the same place
      if some_condition && some_other_condition
        do_something_with_a_really_long_method_name
      end
- Try to use boolean values in conditionals rather than relying on `nil` to be `false` and "anything else" to be `true`. It adds clarity to the purpose of the conditional.

      # Uses the presence of an object as a boolean
      if current_user
        # Do something
      end

      # Explicitly gets a boolean value representing the presence of a user
      if current_user.present?
        # Do something
      end
- `freeze` strings when their values should never change (e.g. in constants). It will make the string immutable, which has two advantages. It optimizes memory usage (because Ruby points to the same `String` object instead of instantiating a new `String` on each reference), and also prevents tempering, because in Ruby, constants aren't.

      DEFAULT_TITLE = "Untitled"
      DEFAULT_TITLE << "foo"
      DEFAULT_TITLE.inspect # => "Untitledfoo"

      DEFAULT_TITLE = "Untitled".freeze
      DEFAULT_TITLE << "foo" # => RuntimeError: can't modify frozen string

## Service Objects

We use the service object pattern to encapsulate complex logic within self-contained objects. Instead of a 20-line controller action, we’ll call `DoSomethingComplicated.perform(args)` in the controller and move all of the complex logic into the `DoSomethingComplicated` class. This not only helps organize and isolate complex code, but also makes it easier to test.

Service objects are called with `ServiceName.perform`. They should be named imperatively after the actions they perform (e.g. `AssignUser`, `CreateGroup`).

Simple service objects can remain as class methods internally:

    class DoSomething
      def self.perform(thing)
        thing.perform_action
        SomethingMailer.notify(thing.owner).deliver_later
      end
    end

but after more than a few lines of code, it will make sense to instantiate an object and extract instance methods for clarity:

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

While perhaps overkill in this simple case, it's valuable to be able to scan a service object's `#perform` method and see, on each line, all of its responsibilities.

## Jobs

We use jobs (found in `/jobs`) to perform actions in the background. We have a simple naming convention: `VerbNoun(s)Job`. For example, a job that sends an assignment notification may be called `SendAssignmentNotificationJob` or a job that creates a set of tags may be called `CreateTagsJob`.

Each job should `include Sidekiq::Worker` and define a `perform` method which either performs the work or calls methods that perform the work. Here is an example:

    class EditThingJob
      include Sidekiq::Worker

      def perform(thing_id, attributes = {})
        thing = Thing.find(thing_id)
        thing.update(attributes)
      end
    end

A more complex job would define private methods to keep `perform` simple and easy to read.

### Jobs that spawn more jobs

In some cases, it makes sense to run a job that simply runs many smaller jobs. For example, every morning we send assignment notifications to every user who has an incomplete assignment due in one weeek, due in one day, one day overdue, or one week overdue. In these cases, the job that performs an action on a single object should follow the normal naming convention. The job that spawns more jobs should be named `VerbAllNounsJob`. Each job class should be independent of each other; neither should be namespaced by the other. For example:

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

The above example is very much simplified, but demonstrates the desired pattern.

### Changing a job's name

On occasion, it may be necessary to change a job's name. This can cause some problems when several instances of that job are scheduled to be run in the future when the rename is deployed. We can mitigate this by defining a new class with the old job's name that simply extends the new job.

    class NewJob
      def perform
        ...
      end
    end

    class OldJob < NewJob
    end

In this case, if `OldJob` is already queued, it will still run as it extends `NewJob`.

## Gemfile

We try to use [pessimistic version constraints](http://guides.rubygems.org/patterns/#pessimistic-version-constraint) for all gems in the Gemfile. These use the `~>` operator followed by at least a major and minor gem version (e.g. `"~> 4.2"`). This prevents a gem from being accidentally upgraded by more than a minor version, and Gems following [Semantic Versioning](http://semver.org/) promise to introduce non-backwards-compatible changes only in major versions.
