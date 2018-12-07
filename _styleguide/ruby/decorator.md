---
layout: styleguide
title: Decorator Pattern
---

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

When deciding whether to use a Decorator or a Presenter, consider whether you're adding behavior to _a single object_. If so use a Decorator, otherwise choose a Presenter.

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
