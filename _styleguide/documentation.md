---
layout: styleguide
title: Documentation Style Guide
main: true
---

# Documentation

In general, we follow the ruby style guide section on comments [from bbatsov](https://github.com/bbatsov/ruby-style-guide#comments).

> Good code is its own best documentation. As you're about to add a comment, ask yourself, "How can I improve the code so that this comment isn't needed?" Improve the code and then document it to make it even clearer.
> -- Steve McConnell

## Doc Directory

As we work on existing features or add new functionality, those should be documented in the `doc/` directory. The documentation in here should be brief fly overs that give an over all sense of how the pieces fit together. Exact class names should be referenced so that dev can refer to those to get more detailed information on how that piece functions.

## Ruby

For ruby, documentation should be added using the [yard](http://www.rubydoc.info/gems/yard/file/docs/GettingStarted.md) format. While Yard is built ontop of RDoc, we prefer to use the Markdown syntax. In some cases additional meta-data can be useful which is supported through the usage of [Tags](http://www.rubydoc.info/gems/yard/file/docs/Tags.md#taglist).

### General guidelines

- Classes should have a short descriptive comment about the role it plays.

- Public and private methods can be documented as needed.

- Private methods should be marked with the `@private` tag.

- Use meta-data tags when appropriate.

- Must use Markdown syntax.

### Example

```ruby
module Learning

  # Widget's are the basic element used for learning. Additional top level
  # documentation on the class goes here.
  class Widget

    # Returns true if the user parameter is assigned the current Widget
    #
    # @param user [Integer, User]
    # @return [Boolean]
    def assigned?(user)
      user = User(user)

      Assignment.where(assignee: user, assignable: self).exists?
    end

    private

    # Conversion method for ensuring that either User objects or user ids are
    # valid ways of finding the current user.
    #
    # @private
    # @param user [Integer, User]
    # @raise [InvalidParameterError] if the parameter is neither an Integer or
    #   Object
    # @return [User] an instance of the User object
    def User(user)
      case user
      when User
        user
      when Integer
        User.find(user)
      else
        raise InvalidParameterError, "user must either be an object or Integer"
      end
    end
  end
end
```
