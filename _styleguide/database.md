---
layout: styleguide
title: Database Style Guide
main: true
---

## Defer to the PostgreSQL wiki about what not to do.

The PostgreSQL Wiki has a handy [“Don’t Do This”](https://wiki.postgresql.org/wiki/Don't_Do_This) page. While there are exceptions when most of these discouraged practices may be safe or even necessary, we should avoid them unless we’re sure we’re the exception.

## Enforce data integrity with unique indexes and not-null constraints

Bugs resulting from invalid data can be tricky to detect and it can be time-consuming to clean up—best to prevent the possibility of it in the first place. PostgreSQL gives us a range of tools to ensure invalid data isn’t just unlikely but impossible:

- Use `UNIQUE` `CONSTRAINT`s/`INDEX`es (`unique: true` in migrations) to prohibit “duplicate” data. For instance, if it doesn’t make sense for a Lesson to be tagged with “Sales” _twice_, we want a unique composite index on the `tag` and `taggable`.
- Use `NOT NULL` (`null: false` in migrations) to guarantee necessary relationships (e.g. make `company_id` non-nullable on tables that must be associated with a company) or attributes (if a `title` is required, make it `NOT NULL`).
- However, avoid using `FOREIGN KEY` constraints, as we’ve run into performance issues deleting from large tables like `users` that have very many of them. If there is an overriding concern about data fidelity to justify them, be mindful of performance when adding foreign key constraints: see [this migration](https://github.com/lessonly/lessonly/pull/5390/files#diff-bf0c0f95fbacafc133567b5e36e2f289R20) for an illustration of the challenges and a way around them. Rails’ `dependent: :destroy` and `dependent: :nullify` callbacks do not contribute to database-level slowness with deleting records, so prefer them to foreign-key constraints for maintaining data fidelity.

## Avoid nullable booleans.

We expect boolean values typically to be either `true` or `false`. Make them `NOT NULL` with a default value when we don’t intend to make use of the default third `null` state.

## Use transactions only when necessary.

Transactions are sometimes essential for ensuring data integrity, but when we include operations within the transaction that don't need to be atomic, it makes transactions take longer and increases the likelihood of locks, deadlocks, and other performance issues resulting from slow queries ([example](https://app.clubhouse.io/lessonly/story/27117/postgres-is-spending-too-much-time-waiting-for-transactions-to-complete)).
