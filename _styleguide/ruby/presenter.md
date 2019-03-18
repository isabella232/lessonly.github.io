---
layout: styleguide
title: Presenter Pattern
main: false
---

## Presenter Objects

Similar to how Service Objects abstract away complex business logic from controller actions, we use Presenter Objects to encapsulate complex data-gathering and displaying. As a general rule, if your controller action or mailer contains more than one instance variable, you may benefit from a presenter. For example, take this action:

```ruby
# app/controllers/dashboard_controller.rb
def index
  @overdue_assignments = current_user.assignments.overdue.includes(:assignee, :assignable)
  @incomplete_assignments = current_user.assignments.incomplete.includes(:assignee, :assignable)
  @completed_assignments = current_user.assignments.completed.includes(:assignee, :assignable)
end
```

with a presenter object, it might be reduced to the following:

```ruby
# app/controllers/dashboard_controller.rb
def index
  # used in views like `@dashboard.incomplete_assignments.each`
  @dashboard = DashboardPresenter.new(current_user)
end

# app/presenters/dashboard_presenter.rb
class DashboardPresenter

  def initialize(user)
    @user = user
  end

  def overdue_assignments
    assignments.overdue
  end

  def incomplete_assignments
    assignments.incomplete
  end

  def completed_assignments
    assignments.completed
  end

  private

  def assignments
    @user.assignments.includes(:assignable, :assignee)
  end
end
```

Consider how:

- The controller action is now simpler.
- The presenter is easier (and faster!) to test because it doesn't depend on the controller context of a web request.
- The presenter allows us more easily to DRY up repeated logic with private methods.
- The presenter is a great place to extract complex view logic related to collections. For instance, in the view we might have had

```erb
<% if (@overdue_assignments.empty? && @incomplete_assignments.empty? && @completed_assignments.empty?) %>
  <%= render "blank_slate" %>
<% end %>
```

which can now be something like:

```erb
<%= render "blank_slate" if @dashboard.empty? %>
```

with `delegate :empty?, to: :assignments` in our `DashboardPresenter`.
