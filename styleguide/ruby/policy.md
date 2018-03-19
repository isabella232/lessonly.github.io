---
layout: page
title: Policy Pattern
---

We use the policy pattern implemented by the [Pundit gem][1] to create a flexible authorization system.

## Policies Overview

* Policies are located within `app/policies`
* Each model has a policy associated with it
* **Policy actions** (`#create?`, `#update?`, `#destroy?`, etc) determine if the `current_user` can perform the given action for the record
* **Policy scopes** are responsible for answering "What records should a user even know exist?"

Policies may consist of two basic parts: [Scopes](#policy-scope) and [Actions](#policy-actions)

## Policy Scope

> Often, you will want to have some kind of view listing records which a particular user has access to. When using Pundit, you are expected to define a class called a policy scope.

* Scope **must** return an ActiveRecord::Relation to preserve chaining
* Scope **should** use the `scope` keyword to refer back to the model the policy is currently referencing.

### Example Scope

```ruby
class UserPolicy < ApplicationPolicy

  class Scope < ApplicationPolicy::Scope

    # Determine which users the current user has access to.
    # Useful for users whom manager others.
    def resolve
      case user.role
      when Role::ADMIN
        user.company.users
      when Role::MANAGER
        user.managed_users
      else
        User.none
      end
    end
  end
```

## Policy Actions

Policy actions refer directly to the controller action that needs to be authorized. For instance, SlothBears#update controller action should have a corresponding policy called SlothBearPolicy with a method that looks like:

```ruby
def update?
  # Determine if the current user is authorized to update SlothBears
end
```

Policy actions can also reference other actions if their purpose is similiar. For example users can't create new records if they first can't access the #new action. So if a user can create a record they can also start a new one.

```ruby
def new?
  create?
end

def create?
  user.is_admin? || user.has_super_powers?
end

# Not so good
# Use discretion when basing actions off one another
def destroy?
  create? # Just because a user can create doesn't mean they can destroy records
end
```

## Policy Helpers

* We have several helpers for determining the current user's role. These can be
used within scopes to great effect.

```ruby
case role
when Role::ADMIN
when Role::MANAGER
when Role::CREATOR
when Role::LEARNER
end
```

* Additionally, `user` and `company` are available within scopes.
* Check feature flags with `company.has_feature?`
* It is possible to access other policies from the current policy by using: `Pundit.policy_scope!(scope)` for scope and `Pundit.policy!(user, record)` for action. There are two helpers: `policy_scope!(scope)` and `policy!(record)` respectively.

## Controllers

* Controllers should include the `Policies` concern to ensure all actions are verified.

* **Only when necessary**: Controller verification checks may be skipped by using the following: `skip_after_action :verify_authorized`

### Authorize an action

* Authorize a same name action

```ruby
# Authorize an action
# Note: The method name in this case is automatically inferred by
# the authorize method and is equivalent to:
# authorize @user, :update?
def update
  @user = User.find(params[:id])
  authorize @user
end
```

* Authorize an action by a different predicate method

```ruby
# Authorize an action based on a different predicate method
# You can specify the predicate method by passing an additional
# argument to authorize with its symbol.
def some_other_action
  @user = User.find(params[:id])
  authorize @user, :update?
end
```

* Prefer **NOT TO** use policy_scope before authorizing.
The predicate method should know whether or not it is valid based on access to the record it is passed. In other words the `update?` method within the policy below should check the scope to make sure that the record is available to the current_user.

```ruby
# Not so good
def update
  @user = Pundit.policy_scope(User).find(params[:id])
  authorize @user
end

# So good
def update
  @user = User.find(params[:id])
  authorize @user
end

# app/policies/user_policy.rb
def update?
  company.users.include?(record) && user.can_do_a_thing?
end
```

### Load accessible records through the Policy Scope

```ruby
# Only loads records which the current_user's role has access to
# from the Policy scope
def filtered_collection
  collection = policy_scope(user.criteria)
end
```

### Controller Example

```ruby
module FeedbackCriteria
  class CustomCriterionController < ApplicationController
    include Policies

    def update
      @custom_criterion = FeedbackCriteria::CustomCriterion.find(params[:id])
      authorize @custom_criterion

      if @custom_criterion.update(custom_criterion_params)
        render json: { message: I18n.t("feedback_criteria.criterion.update_success", name: @custom_criterion.name) }
      else
        render status: 422, json: @custom_criterion.errors
      end
    end
  end
end
```

### Example: Dynamically instantiate polymorphic Policies

```ruby
def create
  taggable = policy_scope!(params[:taggable_type].safe_constantize).find(params[:taggable_id])

  # Futher controller action logic
end
```

### Example: Authorize record with specific policy

* There are cases where the record doesn't map directly to the policy name or the
policy itself is namespaced. We have a built-in method `authorize_with_policy` which
is specifically for this purpose.
* See: https://github.com/lessonly/lessonly/blob/master/app/controllers/concerns/policies.rb#L11

```ruby
def create
  authorize_with_policy Learning::LessonPolicy, current_lesson, :take?
end
```

## Specs

* For unauthorized controller actions, controller tests may utilize the `handle_unauthorized`
helper to ensure the proper action is taken.
* Policy specs should be broken out by action and the specific user role.
* All role levels of users should be tested.
* Policy Scope should also be tested to ensure proper records are being retrieved

### Example: Policy Spec

```ruby
describe AssignmentPolicy do

  #########################
  # Policy Action example #
  #########################
  describe "#create?" do

    specify "learners cannot" do
      expect(AssignmentPolicy.new(learner, Assignment).create?).to be false
    end

    specify "creators cannot" do
      expect(AssignmentPolicy.new(creator, Assignment).create?).to be false
    end

    specify "managers can" do
      expect(AssignmentPolicy.new(manager, Assignment).create?).to be true
    end

    specify "admins can" do
      expect(AssignmentPolicy.new(admin, Assignment).create?).to be true
    end
  end

  ########################
  # Policy Scope example #
  ########################
  describe AssignmentPolicy::Scope do
    # Some record setup work done here
    let(:scope) { AssignmentPolicy::Scope.new(user, Assignment).resolve }

    context "for an admin" do
      it "returns all assignments in the company" do
        expect(scope).to match_array [....]
      end
    end

    context "for a manager" do
      #....
    end

    context "for a creator" do
      #....
    end

    context "for a learner" do
      #....
    end
  end
end
```

[1]: https://github.com/varvet/pundit
