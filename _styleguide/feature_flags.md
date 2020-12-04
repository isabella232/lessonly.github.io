---
layout: styleguide
title: Feature Flags Style Guide
main: true
---

### Feature Flags

#### Add Translation Key for New Features

```ruby
# Path to feature-related translation keys in config/locales/en.yml
en:
  admin:
    company_details:
      features:
        feature_name: 'Classification: Name of Feature'
# Without this addition, feature_name will be shown in Active Admin as "Feature Name".
```

#### Favor Calling Feature Checks & Toggles Directly on Policy

```ruby
# Old Way, Avoid
company.has_feature?(:feature_name)
company.enable_feature!(:feature_name)
company.disable_feature!(:feature_name)

# New Way, Recommended
# This way is favored because we can perform feature checks on more types of objects.
# The "old way" only allowed for company, now we can also pass in users & groups.
AccessControl::Features::ExamplePolicy.new(company).feature_name?
AccessControl::Features::ExamplePolicy.new(company).grant!(:feature_name)
AccessControl::Features::ExamplePolicy.new(company).revoke!(:feature_name)
```
