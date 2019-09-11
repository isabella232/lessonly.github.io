---
layout: styleguide
title: Job Pattern
main: false
---

## Jobs

We use jobs (found in `/jobs`) to perform actions in the background. We have a simple naming convention: `VerbNoun(s)Job`. For example, a job that sends an assignment notification may be called `SendAssignmentNotificationJob` or a job that creates a set of tags may be called `CreateTagsJob`.

Each job should `include Sidekiq::Worker` and define a `perform` method which either performs the work or calls methods that perform the work. Here is an example:

```ruby
class EditThingJob
  include Sidekiq::Worker

  def perform(thing_id, attributes = {})
    thing = Thing.find(thing_id)
    thing.update(attributes)
  end
end
```

A more complex job would define private methods to keep `perform` simple and easy to read.

The job will be called via `perform_async`, which will eventually call `perform`. Please note that any arguments passed to `perform_async` will be serialized and, as such, should be simple. Keyword arguments are not available to the `perform` method due to this constraint.
### Jobs should be idempotent

Per Sidekiq’s [Best Practices](https://github.com/mperham/sidekiq/wiki/Best-Practices#2-make-your-job-idempotent-and-transactional), assume that every job can be retried at any point in its execution. If a job raises an error at any point, it will be retried by default. Besides that, when Sidekiq is restarted (which [Heroku does every 24 hours](https://devcenter.heroku.com/articles/dynos#restarting)) all active jobs that do not finish in time are stopped and re-enqueued.

What idempotency looks like will depend on the context, but for example: when a job creates data, handle the case where the data was already created; when a job deletes data, handle the case where the data is already gone. Idempotency isn't always possible (especially with external side-effects such as sending email), but in such cases we can perform those side-effects at the end of the job instead of the middle, so that if an error is raised halfway through, those side-effects won't happen until the job is successful.

In short, as much as possible write jobs such that running them twice in a row will have the same effect as running them once.

### Jobs that spawn more jobs

In some cases, it makes sense to run a job that simply runs many smaller jobs. For example, every morning we send assignment notifications to every user who has an incomplete assignment due in one week, due in one day, one day overdue, or one week overdue. In these cases, the job that performs an action on a single object should follow the normal naming convention. The job that spawns more jobs should be named `VerbAllNounsJob`. Each job class should be independent of each other; neither should be namespaced by the other. For example:

```ruby
# /jobs/send_assignment_reminder_job.rb
class SendAssignmentReminderJob
  include Sidekiq::Worker

  def perform(assignment_id)
    # Do work
  end
end

# /jobs/send_all_assignment_reminders_job.rb
class SendAllAssignmentRemindersJob
  include Sidekiq::Worker

  def perform
    get_all_one_day_overdue_assignment_ids.each do |assignment_id|
      SendAssignmentReminderJob.perform_async(assignment_id)
    end
  end
end
```

The above example is very much simplified, but demonstrates the desired pattern.

### Changing a job's name

On occasion, it may be necessary to change a job's name. This can cause some problems when several instances of that job are scheduled to be run in the future when the rename is deployed. We can mitigate this by defining a new class with the old job's name that simply extends the new job.

```ruby
class NewJob
  def perform
    ...
  end
end

class OldJob < NewJob
end
```

In this case, if `OldJob` is already queued, it will still run as it extends `NewJob`.

### Scheduling a job

Lessonly uses clockwork to schedule background jobs. The job will need to be added to `lib/clock.rb`.

To kick-off the background job from the command line:
```
bundle exec clockwork lib/clock.rb
```

Useful links:
 - [Clockwork gem](https://rubygems.org/gems/clockwork/versions/1.3.1)
 - [Clockwork gem documentation](https://www.rubydoc.info/gems/clockwork/1.3.1)
 - [Gem for testing clockwork](https://github.com/kevin-j-m/clockwork-test)

Note:
 - May need to restart the server before running the command
 - At some point we may get rid of clockwork and replace with sidekiq scheduled jobs
