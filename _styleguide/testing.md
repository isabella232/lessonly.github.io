---
layout: styleguide
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

### When stubbing methods, prefer to use `and_return` instead of block syntax.

`expect().and_return(return_value)` is easier to understand and keeps code reading like documentation compared to `expect() { return_value }`. Additionally, using `.and_return` keeps code consistent for other similar methods like `expect().and_yield()`.

```ruby
# Not recommended
expect(UserUpdate).to receive(:new).with(anything) { user_update }

# Recommended
expect(UserUpdate).to receive(:new).with(anything).and_return(user_update)
```

### Prefer specifying the number of times an expected message should be received.

Unless you have a reason not to, this helps catch bugs where a message is improperly sent twice, e.g.

```ruby
expect(progress).to receive(:restart!).once
```

Use `once` or `twice` instead of `1` or `2` when specifying the number of times it should be received.

```ruby
# bad
expect(foo).to receive(:bar).exactly(1).times
expect(foo).to receive(:bar).exactly(2).times
expect(foo).to receive(:bar).at_least(1).times
expect(foo).to receive(:bar).at_least(2).times
expect(foo).to receive(:bar).at_most(1).times
expect(foo).to receive(:bar).at_most(2).times

# good
expect(foo).to receive(:bar).once
expect(foo).to receive(:bar).twice
expect(foo).to receive(:bar).at_least(:once)
expect(foo).to receive(:bar).at_least(:twice)
expect(foo).to receive(:bar).at_most(:once)
expect(foo).to receive(:bar).at_most(:twice).times
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

Here's how you can help keep our test suite running fast:

#### Default to using `build_stubbed` instead of `create`

In general, it works in most places where you just need objects to exist without them interacting with the database (like for creating associations).

From [this post from Factory Bot](https://robots.thoughtbot.com/use-factory-girls-build-stubbed-for-a-faster-test):

> `build_stubbed` ... instantiates and assigns attributes just like `build` ... It makes objects look like they’ve been persisted, creates associations with the `build_stubbed` strategy ... and stubs out a handful of methods that interact with the database and raises if you call them. This leads to much faster tests and reduces your test dependency on a database.

#### Stub and mock external services and classes.

This not only speeds up the tests but also enhances separation of concerns. If your code is written in a way that makes writing tests like this difficult, that might be a sign that your code could be refactored.

#### If you are adding tests to an existing file, check for code that can be reused.

Creating a new `user` when the same type of `user` has already been created in the file creates an unnecessary test object to add to memory.

### Prefer Request or Feature specs over Controller Specs

As of Rails 5, controller specs are [no longer recommended by either RSpec or Rails](http://rspec.info/blog/2016/07/rspec-3-5-has-been-released/#rails-support-for-rails-5). Instead, prefer request specs (see `spec/requests/*` for examples) for testing HTTP endpoints directly, and/or feature specs (see `spec/features/*`) for testing browser interactions end-to-end.

### Use the `request_setup` helper in your controller specs.

(_Note: [as noted above](#prefer-request-or-feature-specs-over-controller-specs), we want to avoid introducing new controller specs, but this recommendation remains applicable to existing controller specs until we completely migrate away._)

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

However, sometimes that doesn't work well (or can be flaky). If you are running into that kind of issue, check out `spec/support/javascript_helper.rb`.

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


Prefer the more-concise `it` when declaring test scenarios about specific things (e.g. classes, methods), but consider `specify` when more grammatical flexibility is required.

```ruby
describe CookiePolicy do
  # Ehh...
  it "admins are permitted eat cookies" do
   # ...
  end

  # That's better!
  specify "admins are permitted eat cookies" do
   # ...
  end
end
```

Though keep in mind that specs using `specify` can typically be written more concisely using `it`, e.g.

```ruby
describe CookiePolicy do
  specify "admins are permitted eat cookies" do
   # ...
  end

# More consise!
  it "permits admins to eat cookies" do
   # ...
  end
end
 ```

- Include a blank line around `describe`/`feature` blocks, `it`/`scenario` blocks, `before`/`background` blocks, and `context` blocks.
- Within `it`/`scenario` blocks, separate setup and expectations with a blank line.
- Do not include a blank line above `let` blocks, in order to visually group them with their appropriate `describe`/`feature` or `context` block.
- Include a blank line below the last 'let' in the block.
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

### Testing negative expectations

RSpec supports both `to_not` and 'not_to`, but `to_not` is commonly considered ungrammatical as a "split infinitive". In prose one might at times prefer to split an infinitive for stylistic reasons, but in specs consistency and precision are paramount. Therefore, use `not_to` when writing specs that test the negative expectation.

```ruby
# bad
it "..." do
  expect(false).to_not be_true
end

# good
it "..." do
  expect(false).not_to be_true
end
```

### Write 'expect(...)' using actual value, not literal value

The `expect(...)` should contain the actual value and not the literal value you are testing.

```ruby
# bad
expect(5).to eq(price)
expect(/foo/).to eq(pattern)
expect("John").to eq(name)

# good
expect(price).to eq(5)
expect(pattern).to eq(/foo/)
expect(name).to eq("John")
```

### Use create_list for creating multiple records

```ruby
# bad
3.times do
  create(:lesson_section, lesson: lesson)
end

# good
create_list(:lesson_section, 3, lesson: lesson)
```

### Do not use 'should' when describing your tests.

Also, do not repeat `it` in the description of the test.

```ruby
# bad
it "should find nothing" do
end

it "it creates an answer with the description as the answer" do
end

# good
it "finds nothing" do
end

it "creates an answer with the description as the answer" do
end
```

### Include an empty line after an example group
```ruby
# bad
RSpec.describe Foo do
  describe '#bar' do
  end
  describe '#baz' do
  end
end

# good
RSpec.describe Foo do
  describe '#bar' do
  end

  describe '#baz' do
  end
end
```

### Include an empty line after hook blocks
```ruby
# bad
before { do_something }
it { does_something }

# bad
after { do_something }
it { does_something }

# bad
around { |test| test.run }
it { does_something }

# good
before { do_something }

it { does_something }

# good
after { do_something }

it { does_something }

# good
around { |test| test.run }

it { does_something }
```

### When using the change matcher, pass in an object and attribute as arguments
```ruby
# bad
expect(run).to change { Foo.bar }
expect(run).to change { foo.baz }

# good
expect(run).to change(Foo, :bar)
expect(run).to change(foo, :baz)
# also good when there are arguments or chained method calls
expect(run).to change { Foo.bar(:count) }
expect(run).to change { user.reload.name }
```

### Use local variables or 'let' instead of instance variables
```ruby
# bad
describe MyClass do
  before { @foo = [] }
  it { expect(@foo).to be_empty }
end

# good
describe MyClass do
  let(:foo) { [] }
  it { expect(foo).to be_empty }
end
```

### Use 'all' matcher instead of iterating over an array
```ruby
# bad
it 'validates users' do
  [user1, user2, user3].each { |user| expect(user).to be_valid }
end

# good
it 'validates users' do
  expect([user1, user2, user3]).to all(be_valid)
end
```

### 'let' statements should come before examples
```ruby
# Bad
let(:foo) { bar }

it 'checks what foo does' do
  expect(foo).to be
end

let(:some) { other }

it 'checks what some does' do
  expect(some).to be
end

# Good
let(:foo) { bar }
let(:some) { other }

it 'checks what foo does' do
  expect(foo).to be
end

it 'checks what some does' do
  expect(some).to be
end
```
