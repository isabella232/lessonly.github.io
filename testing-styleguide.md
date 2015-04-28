---
layout: page
title: Testing Style Guide
permalink: /testing/
---

## Rule #1

Write tests for your code, please.

## House Rules

- In RSpec tests, denote instance methods with "#" and class methods with "::", e.g.
  
      describe Thing do
        describe "::class_method" do # ...
        describe "#instance_method" do # ...
      end

- When stubbing methods, use a block instead of `and_return`, because `expect().and_return()` sounds like you're expecting the method to return something, when in fact you're *forcing* it to. For example:

      expect(UserUpdate).to receive(:new).with(anything) { user_update }

- Unless you have a reason otherwise, prefer specifying the number of times an expected message should be received, in order to catch bugs where a message is improperly sent twice, e.g.

      expect(progress).to receive(:restart!).once

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
