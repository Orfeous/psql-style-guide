PSQL Style Guide
================

A Style Guide for writing clean and readable PostgreSQL queries.

* Based on the output of `pg_dump`.
* Focus on the readability and maintainability of queries.

## Table of Contents
* [Spacing and Casing](https://github.com/Hacker0x01/psql-style-guide#spacing-and-casing)
* [Aliasing](https://github.com/Hacker0x01/psql-style-guide#aliasing)
* [Join Conditions and Where Clauses](https://github.com/Hacker0x01/psql-style-guide#join-conditions-and-where-clauses)
* [Using raw SQL in Ruby](https://github.com/Hacker0x01/psql-style-guide#using-raw-sql-in-ruby)

## Spacing and Casing
* Distinct elements, such as operations, should be on a seperate line.
  * Nested elements should be indented.
  * Aside from this, no additional (blank) lines should be added.
* All SQL keywords and built-in functions should be written in upper case.


Good:

```SQL
SELECT
  COALESCE(firstname, username) AS name,
  email_address
FROM
  users
```

Bad:

```SQL
/* Bad: this should be on a seperate line. */
SELECT COALESCE(firstname, username) AS name,
  email_address

FROM /* Bad: there is a blank line above. */
  users
```

## Aliasing
* Always use an alias when doing a join that is not obvious.
* When aliasing, always use the keyword `AS`.
* Naturally, the alias should be descriptive.

Good:

```SQL
SELECT
  username
FROM
  users
JOIN
  posts AS posts_in_interval
    ON
      posts.user_id = users.id
      AND
        posts.timestamp <: period_end
```

Bad:

```SQL
SELECT
  username
FROM
  users
JOIN
  /* Bad: this is an obvious condition and does not require
     further clarification by using an alias. */
  posts AS posts_by_user ON posts.user_id = users.id
```

Bad:

```SQL
SELECT
  username
FROM
  users
JOIN
  posts /* Bad: missing `AS`. */
    ON
      posts.user_id = users.id
      AND
        posts.timestamp < :period_end
```


## Join Conditions and Where Clauses
* In general, only use `WHERE` clauses for the table specified with `FROM`.
  * Conditions for tables being joined should be specified by using `ON cond1 AND cond2`.
* Use the most straightforward condition in the `ON ...`.
  * Usually this looks like `table1.id = table2.table1_id`

Good:


```SQL
SELECT
  users.username
FROM
  users
JOIN
  posts AS posts_in_period
  ON
    posts_in_period.user_id = users.id
    AND
      posts_in_period.timestamp < :period_end
WHERE
  users.is_public
```


Bad:


```SQL
SELECT
  users.username
FROM
  users
JOIN
  posts AS posts_in_period ON posts_in_period.user_id = users.id
WHERE
  users.is_public
  AND
  /* Bad: this condition should be part of the ON clause */
  posts_in_period.timestamp < :period_end
```


## Using raw SQL in Ruby
* Use a multiline string inside heredoc markers.
  * Indent the query by one level.
* Always use sanitation when inserting variables.
* Inside Rails, call `strip_heredoc`.
* Variables should be simple: operations should be done in Ruby.
  * Keep the amount of bindings to a minimum.
* Define the variable bindings before the query.
* Use `:keywords` rather than `?` when using more than a single variable.
  * But using `:keywords` even when there is only one variable is fine.
* Try to use the same variable names in both Ruby and SQL.

Good:

```RUBY
def number_of_public_active_users(period_start, period_duration)
  sql_bindings = {
    period_end: period_start + period_duration,
  }

  query = <<-SQL.strip_heredoc
    SELECT
      users.username
    FROM
      users
    JOIN
      posts AS posts_in_period
      ON
        posts_in_period.user_id = users.id
        AND
          posts_in_period.timestamp < :period_end
    WHERE
      users.is_public
  SQL

  User.count_by_sql([query, sql_bindings])
end
```
