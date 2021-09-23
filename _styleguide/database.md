---
layout: styleguide
title: Database Style Guide
main: true
---

## Defer to the PostgreSQL wiki about what not to do.

The PostgreSQL Wiki has a handy [“Don’t Do This”](https://wiki.postgresql.org/wiki/Don't_Do_This) page. While there are exceptions when most of these discouraged practices may be safe or even necessary, we should avoid them unless we’re sure we’re the exception.

## Enforce data integrity with unique indexes and not-null constraints

PostgreSQL gives us a range of tools to ensure invalid data isn’t just unlikely but impossible:

- Use `UNIQUE` `CONSTRAINT`s/`INDEX`es (`unique: true` in migrations) to prohibit “duplicate” data. For instance, if it doesn’t make sense for a Lesson to be tagged with “Sales” _twice_, we want a unique composite index on the `tag` and `taggable`.
- Use `NOT NULL` (`null: false` in migrations) to guarantee necessary relationships (e.g. make `company_id` non-nullable on tables that must be associated with a company) or attributes (if a `title` is required, make it `NOT NULL`).
- **Update:** we no longer recommend adding `FOREIGN KEY` constraints, as we’ve run into performance issues on tables like `users` that have very many of them. So avoid adding foreign-key constraints unless there is an overriding concern about data fidelity to justify them. (If so, be mindful of performance when adding foreign key constraints: see [this migration](https://github.com/lessonly/lessonly/pull/5390/files#diff-bf0c0f95fbacafc133567b5e36e2f289R20) for an illustration of the challenges and a way around them.)

Bugs resulting from invalid data can be tricky to detect and it can be time-consuming to clean up—best to prevent the possibility of it in the first place.

## Avoid nullable booleans.

We expect boolean values typically to be either `true` or `false`. Make them `NOT NULL` with a default value when we don’t intend to make use of the default third `null` state.

## Use transactions only when necessary.

Transactions are sometimes essential for ensuring data integrity, but when we include operations within the transaction that don't need to be atomic, it makes transactions take longer and increases the likelihood of locks, deadlocks, and other performance issues resulting from slow queries ([example](https://app.clubhouse.io/lessonly/story/27117/postgres-is-spending-too-much-time-waiting-for-transactions-to-complete)).

## Identify all rows with a required `company_uuid` column on all tables

In code and reporting, we often need to know the company/tenant to whom data belongs. This can be difficult (and slow) when we need to join on a series of tables to find it. And it can be ambiguous when there are multiple pathways to a company which should in theory agree but might not. [Denormalizing](https://en.wikipedia.org/wiki/Denormalization) a reference to the company’s UUID on to all tables will help in both cases. Do not add a forign-key constraint, however, as too many of those will slow down writes to the `companies` table. The supporting Rails associations will require two extra options:

```ruby
# app/models/widget.rb
belongs_to :company, primary_key: :uuid, foreign_key: :company_uuid


# app/models/company.rb
has_many :widgets, primary_key: :uuid, foreign_key: :company_uuid
```
