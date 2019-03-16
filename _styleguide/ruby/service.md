---
layout: styleguide
title: Service Pattern
main: false
---

## Service Objects

We use the service object pattern to encapsulate complex or repeated logic within self-contained objects. Instead of a 20-line controller action, we’ll call `DoSomethingComplicated.perform(args)` in the controller and move all of the complex logic into the `DoSomethingComplicated` class. This not only helps organize and isolate complex code, but also makes it easier to test. It also reduces duplication of logic between our web app and our API, for example, since both can rely on the same service classes.

- New service classes should descend from our `Service` base class.
- Services should be named imperatively after the actions they perform. The convention is [Verb][Noun], e.g. `AssignUser`, `CreateGroup`.
- Services are called with the `::perform` class method, e.g. `ServiceName.perform(args)`, which is defined on `Service`. It will call `#perform` on a new instance of the service class, which is where we define the service’s logic.
- `::perform` always returns the instance of the service, which can be queried for status and results.
- Service instances return `true` for `#success?` by default. Call `fail!` at any point to have them respond to `#success?` with `false`.

For example:

```ruby
class CreateThing < Service
  attr_accessor :new_thing
  attr_reader :creator

  def initialize(creator:)
    @creator = creator
  end

  def perform
    self.new_thing = creator.things.create!
    notify_manager
  rescue ActiveRecord::RecordInvalid
    fail!
  end

  private

  def notify_manager
    ThingMailer.thing_created(manager, new_thing).deliver_later
  end

  def manager
    creator.manager
  end
end

# In use: app/controllers/things_controller.rb
def create
  create_thing = CreateThing.perform(creator: current_user)
  if create_thing.success?
    redirect_to(create_thing.new_thing, success: "Great job!")
  else
    render :new, error: "There was a problem."
  end
end
```

Note that you can generate new services with:

```sh
$ rails generate service DoSomething
```
