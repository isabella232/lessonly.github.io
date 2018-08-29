---
layout: page
title: Testing Style Guide
---

### Rule #1

Write tests for your code, please.

### In RSpec tests, denote instance methods with "#" and class methods with "::"

For example:

```ruby
describe Thing do
  describe "::class_method" do # ...
  describe "#instance_method" do # ...
end
```

### When stubbing methods, use a block instead of `and_return`.

Because `expect().and_return()` sounds like you're expecting the method to return something, when in fact you're *forcing* it to. For example:

```ruby
expect(UserUpdate).to receive(:new).with(anything) { user_update }
```

### Prefer specifying the number of times an expected message should be received.

Unless you have a reason not to, this helps catch bugs where a message is improperly sent twice, e.g.

```ruby
expect(progress).to receive(:restart!).once
```

### When writing feature specs, the `feature` description is generally the *thing* under test (a noun).

Contexts describe under what circumstances we are testing, and scenarios are individual test cases. Note that scenarios may be testing the whole life-cycle of a request, not just each singular aspect of the feature under test, as feature specs take the longest.

```ruby
feature "Custom user fields" do
  context "when the user is able to create/edit new users" do
    scenario "save when a new user is created and update when said user is updated" do
      # actual test code here
    end
  end
end
```

### In feature specs, test the UI, not the database.

In the words of Capybara’s creator, “Capybara is written to best support use cases where you only test the UI, not the back-end.” ([source](https://bibwild.wordpress.com/2016/02/18/struggling-towards-reliable-capybara-javascript-testing/)). Calling out to the database in feature specs can cause threading issues. For example:

```ruby
scenario "Adding a Tag" do
  visit new_tag_path
  fill_in "Name", with: "My new Tag"
  click_button "Submit"

  # Good
  within "ul.all_tags" do
    expect(page).to have_content "My new Tag"
  end

  # Not so Good: can be flaky
  expect(Tag.find_by(name: "My new Tag")).to be_present
end
```

### Don't stub methods on the class being tested.

[Read why](https://robots.thoughtbot.com/don-t-stub-the-system-under-test).

### Avoid stubbing and mocking in integration tests.

For example, if a feature must be enabled, call `company.enable_feature!` instead of stubbing `allow(company).to receive(:has_feature?) { true }`. The isolation enabled by stubs and mocks is great in unit tests, but runs counter to the purpose of integration tests, which is to test the entire system end-to-end.

### Avoid calling methods in specs that aren't the method currently being tested.

Use factories instead. If you set up your spec with a method used elsewhere and that method is later changed, your spec may no longer be testing what you intend, and we'll never know.

```ruby
# not so good
before :each do
  lesson.assign_to!(user)
end

# so good!
before :each do
  create(:assignment, assignable: lesson, assignee: user)
end
```

### Always specify attributes that a test depends on.

If a spec depends on something being `nil` and it's `nil` by default, it should still specify that fact both for obviousness and so if someone changes the default value, the spec will continue testing what it means to.

``` ruby
# Not so good: breaks unnecessarily if we add a default description
it "is invalid without a description" do
  lesson = build(:lesson)
  expect(lesson).to be_invalid
end

# So good!
it "is invalid without a description" do
  lesson = build(:lesson, description: nil)
  expect(lesson).to be_invalid
end
```

### Write your specs with performance in mind.

Do these things to minimize contributions to the slowness of our test suite:

#### Default to using `build_stubbed` instead of `create`

  In general, it works in most places where you just need objects to exist without them interacting with the database (like for creating associations).

  From [this post from Factory Bot](https://robots.thoughtbot.com/use-factory-girls-build-stubbed-for-a-faster-test):

  > build_stubbed ... instantiates and assigns attributes just like build ... It makes objects look like they’ve been persisted, creates associations with the build_stubbed strategy ... and stubs out a handful of methods that interact with the database and raises if you call them. This leads to much faster tests and reduces your test dependency on a database.

#### Stub and mock external services and classes.

  This not only speeds up the tests but also enhances separation of concerns. If your code is written in a way that makes writing tests like this difficult, that might be a sign that your code could be refactored.

#### If you are adding tests to an existing file, check for code that can be reused.

  Creating a new `user` when the same type of `user` has already been created in the file creates an unnecessary test object to add to memory.

### Use the `request_setup` helper in your controller specs.

Controller specs won't work without it! Its definition can be found in `spec/support/controller_helper.rb`. It accepts `request`, `controller`, `current_user`, and `current_company` as arguments, but you can substitute other objects for those as needed.

```ruby
context "when no user is logged in" do
  let(:company) { create(:company) }

  before(:each) { request_setup(request, controller, nil, company) }

  it "redirects to the login page" do
    # some code...
  end
end
```

### JS Helpers

In feature specs that use JavaScript to return data or render a component, there is a delay between clicking something and an action taking place. Prefer to use Capybara methods that wait for an element to appear before acting on them with other methods that don't wait. Here's a [handy guide](http://stefan.haflidason.com/testing-with-rails-and-capybara-methods-that-wait-method-that-wont/) on which Capybara methods wait for an element or selector to appear, and which will attempt to run immediately.

However, sometimes that doesn't work well (or can be flaky). If you are running into that kind of issue, check out /spec/support/javascript_helpers.rb.

In that file, there are a number of methods to help with unique JS issues that often come up in feature specs. Many of these methods are titled `wait_and_[do_something]` and address timing issues that come up when a request is made before a JS request has fully processed.

## RSpec

### Use `and_call_original` to call the actual implementation of a mocked method

Why might you need this?

- To "unmock" a method that was previously mocked
- To set a default response that can be overridden with specific arguments
- To make sure that mocked services called in job specs actually carry out their task

```ruby
# Stub getting a value from the session, but make sure calls to get unrelated values from the session remain unstubbed
allow(controller.session).to receive(:[]).and_call_original
allow(controller.session).to receive(:[]).with("gradebook_user_ids") { [user.id] }
```

### Use `shared_contexts` and `shared_examples` to keep code DRY

Shared examples are tests that test common behavior and are written in such a way that they can be used in several settings. A good example is our `"lesson_content"` example that is used to test the behavior of several models that are included as content in lessons.

Shared contexts are common setup code that can be used to prepare tests in different settings. A common `shared_context` we use is the `logged in as a #{role}` context, which autocreates a `user` object of the desired role and a `company` that the user is associated with. Shared contexts are defined in `spec/support/shared_contexts.rb`. [Note: this specific example is defined with Capybara and is only usable with feature specs.]

### RSpec Syntax

- Use `specify` instead of `it` when it improves readability. This is common in our jobs specs, where `context` is used to refer to subclasses, and our model specs, where we test validations.

Example:

```ruby
# reads weirdly...
describe DoStuffJob do

  context DoStuffJob::Subclass do

    it "method does this thing" do
      # some code
    end
  end
end

# reads so smoothly!
describe DoStuffJob do

  context DoStuffJob::Subclass do

    specify "method does this thing" do
      # some code
    end
  end
end
```

- Include a blank line around `describe`/`feature` blocks, `it`/`scenario` blocks, `before`/`background` blocks, and `context` blocks.
- Within `it`/`scenario` blocks, separate setup and expectations with a blank line.
- Do not include a blank line above `let` blocks, in order to visually group them with their appropriate `describe`/`feature` or `context` block.
- Do not include blank lines between successive `end`s.

For example:

```ruby
describe Model do
  let(:model) { create(:model) }

  it "works" do
    expect(model).to be_working
  end

  context "when sabotaged" do
    let(:saboteur) { create(:user) }

    before :each do
      saboteur.sabotage!(model)
    end

    it "explodes on use" do
      model.use!

      expect(model).to be_exploded
    end
  end
end
```
