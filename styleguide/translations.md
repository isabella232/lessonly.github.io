---
layout: page
title: Translations Style Guide
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

**JavaScript context**

```javascript
// Avoid
// This is hardcoded at build time, before translations are even loaded.
const ERROR_MESSAGE = t('common.error');

// Recommended
// This is evaluated every time with the locale of the current request.
const errorMessage = () => t('common.error');

// or with a getter function
const MESSAGES = {
  get ERROR() {
    return t('common.error');
  }
};
```
