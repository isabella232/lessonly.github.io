---
layout: styleguide
title: Ruby Style Guide
main: true
---

## Patterns / Concepts

There are specific architectural patterns and concepts that we utilize within the Lessonly application. Below are page specfic resources detailing when to use and how to implement the respective theory.

{% include subpage_listing.html %}

## Overview

It’s highly recommended you use a Ruby version manager like [rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io/), or [chruby](https://github.com/postmodern/chruby) so you can try out different versions if necessary.

Regarding coding style, we follow [Github's Ruby Style Guide](https://github.com/styleguide/ruby), which is largely based on the community [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide), edited by Bozhidar Batsov.

## Additional House Rules

These override either Github’s or Batsov’s styleguide where applicable:

### Use double-quotes whenever possible.

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

This makes it clear that the intention is specifically to round the number _down_.

```ruby
# Unclear: uses to_i to truncate the float
(progress.completed_percent * 100).to_i

# More clear: uses floor to round the float down
(progress.completed_percent * 100).floor
```

### Use leading periods when chaining methods on multiple lines.

Too much method chaining is often a bad code smell that something is responsible for too much at once, but if do you happen to find yourself on the multi-line choo choo method chain train please chain responsibly with _leading_ periods. e.g.

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

## Gemfile

We try to use [pessimistic version constraints](http://guides.rubygems.org/patterns/#pessimistic-version-constraint) for all gems in the Gemfile. These use the `~>` operator followed by at least a major and minor gem version (e.g. `"~> 4.2"`). This prevents a gem from being accidentally upgraded by more than a minor version, and Gems following [Semantic Versioning](http://semver.org/) promise to introduce non-backwards-compatible changes only in major versions.

## PrivateAttr vs private attr_reader vs instance variables

Currently in the code there are three styles of accessors:

- Use of `PrivateAttr`
- Use of `private` with accessors below
- Just using instance variables for information hiding

`PrivateAttr` is the preferred method for declaring private accessors even though both PrivateAttr and having the `attr_reader` below `private` accomplish the same thing. This is for consistency, so everything is defined in one place within the file (at the top). Using instance variables should be avoided.

Some of the differences are described below:

### PrivateAttr

The PrivateAttr functionality was added via [PR #2787](https://github.com/lessonly/lessonly/pull/2787) as a way to limit the public interface and use private methods rather than instance variables. One of the advantages of using PrivateAttr is that all of the instance variables will be listed at the top of the file for clarity.

```ruby
class MyClass
  extend PrivateAttr

  private_attr_reader :foo, :bar, :baz

  initialize(foo, bar, baz) do
    @foo = foo
    @bar = bar
    @baz = baz
  end
```

### private attr_reader

The same thing can be accomplished by placing the `attr_reader` below `private`. One of the disadvantages is that in large files it can be easy to overlook the `attr_reader` that is somewhere below `private` in the middle of the code.

```ruby
class MyClass

  initialize(foo, bar, baz) do
    @foo = foo
    @bar = bar
    @baz = baz
  end

  private

  attr_reader :foo, :bar, :baz
```

### instance variables

Use of instance variables should be avoided.

See the "Writing Code That Embraces Change" section in Chapter 2 of Sandi Metz's [_Practical Object Oriented Design in Ruby_](https://drive.google.com/a/lessonly.com/file/d/0BxpjY5cIh-FFZU84Q3p1VTNMQXM/view), for reasons to use private methods rather than instance variables. Specifically, about wrapping all instance variables in reader and/or writer methods with `attr_reader`, `attr_writer`, or `attr_accessor`, and if these methods don't need to be public then making them private.
