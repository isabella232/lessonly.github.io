---
layout: styleguide
title: Translations Style Guide
main: true
---

### Translations

#### Avoid using I18n within constants

```ruby
# Avoid
# This is evaluated once at load time and will always be in the default language (English).
ERROR_MESSAGE = I18n.t("common.error")

# Recommended
# This is evaluated every time it's called with the locale of the current request.
def error_message
  I18n.t("common.error")
end
```

#### Avoid using I18n outside of a method or lambda

```rb
# Avoid
# This is evaluated once at load time and will always be in the default language (English).
validates :name, presence: { message: I18n.t("common.error") }

# Recommended
# This is evaluated every time it's called with the locale of the current request.
# `record` is the ActiveRecord::Base instance with the validation error.
# `attribute` is a hash of information about the attribute being validated.
validates :name, presence: { message: ->(record, attribute) { I18n.t("common.error") } }
```
