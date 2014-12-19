PSQL Style Guide
================

A Style Guide for writing clean and readable PostgreSQL queries.

## Table of Contents
* [Spacing and casing](https://github.com/Hacker0x01/psql-style-guide#spacing-and-casing)
* [Aliasing](https://github.com/Hacker0x01/psql-style-guide#aliasing)
* [Join Conditions and Where Clauses](https://github.com/Hacker0x01/psql-style-guide#join-conditions-and-where-clauses)

## Spacing and casing
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
* Always use `AS`, and always use a descriptive name for your aliases.

Good:

```SQL
SELECT
  username
FROM
  users
JOIN
  posts AS posts_by_user ON posts.user_id = users.id
```

Bad:

```SQL
SELECT
  username
FROM
  users
JOIN
  posts posts_by_user ON posts.user_id = users.id /* Bad: missing `AS`. */
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
