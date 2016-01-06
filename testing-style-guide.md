---
layout: page
title: Testing Style Guide
permalink: /testing/
---

## Rule #1

Write tests for your code, please.

## House Rules

- In RSpec tests, denote instance methods with "#" and class methods with "::", e.g.
  
```ruby
describe Thing do
  describe "::class_method" do # ...
  describe "#instance_method" do # ...
end
```

- When stubbing methods, use a block instead of `and_return`, because `expect().and_return()` sounds like you're expecting the method to return something, when in fact you're *forcing* it to. For example:

    expect(UserUpdate).to receive(:new).with(anything) { user_update }

- Unless you have a reason otherwise, prefer specifying the number of times an expected message should be received, in order to catch bugs where a message is improperly sent twice, e.g.

    expect(progress).to receive(:restart!).once
  
- When writing feature specs, the `feature` description is generally the *thing* under test (a noun). Contexts describe under what circumstances we are testing, and scenarios are individual test cases. Note that scenarios may be testing the whole life-cycle of a request, not just each singular aspect of the feature under test, as feature specs take the longest.

```ruby
feature "Custom user fields" do
  context "when the user is able to create/edit new users" do
    scenario "save when a new user is created and update when said user is updated" do
      # actual test code here
    end
  end
end
```
      
  We also have the ability to query the database directly from the feature specs, so a feature test can change something via the UI, but verify it with actual database calls.
  
  Beware, pseudo code ahead!
```
  visit edit_user_path(user)
  fill_in 'Name', with: 'John Doe'
  click_button 'Save'
  expect(User.find_by(name: 'John Doe').name).to eq 'John Doe'
```

## Guidelines

- [Don't stub methods on the class being tested.](https://robots.thoughtbot.com/don-t-stub-the-system-under-test)
- Avoid stubbing and mocking in integration tests. For example, if a feature must be enabled, call `company.enable_feature!` instead of stubbing `allow(company).to receive(:has_feature?) { true }`. The isolation enabled by stubs and mocks is great in unit tests, but runs counter to the purpose of integration tests, which is to test the entire system end-to-end.
- Avoid calling methods in specs that aren't the method currently being tested: use factories instead. If you set up your spec with a method used elsewhere and that method is later changed, your spec may no longer be testing what you intend, and we'll never know.

      # not so good
      before :each do
        lesson.assign_to!(user)
      end
      
      # so good!
      before :each do
        create(:assignment, assignable: lesson, assignee: user)
      end

## RSpec Syntax

- Include a blank line around `describe`/`feature` blocks, `it`/`scenario` blocks, `before`/`background` blocks, and `context` blocks.
- Within `it`/`scenario` blocks, separate setup and expectations with a blank line.
- Do not include a blank line above `let` blocks, in order to visually group them with their appropriate `describe`/`feature` or `context` block.
- Do not include blank lines between successive `end`s.

For example:
 
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
