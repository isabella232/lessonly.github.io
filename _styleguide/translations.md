---
layout: styleguide
title: Translations Style Guide
main: true
---

### Translations

#### Avoid using I18n within constants

**Ruby context**

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
