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
- Avoid mixing inline and multi-line conditionals: they hurt readability.
      
      # Easy to miss the second condition at the end
      if some_condition
        do_something_with_a_really_long_method_name if some_other_condition
      end
      
      # It's easier to notice both conditions when they're in the same place
      if some_condition && some_other_condition
        do_something_with_a_really_long_method_name
      end

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

## Gemfile

We try to use [pessimistic version constraints](http://guides.rubygems.org/patterns/#pessimistic-version-constraint) for all gems in the Gemfile. These use the `~>` operator followed by at least a major and minor gem version (e.g. `"~> 4.2"`). This prevents a gem from being accidentally upgraded by more than a minor version, and Gems following [Semantic Versioning](http://semver.org/) promise to introduce non-backwards-compatible changes only in major versions.
