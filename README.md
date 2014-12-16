PSQL Style Guide
================

A Style Guide for writing clean and readable PostgreSQL queries.

## Table of Contents

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
SELECT COALESCE(firstname, username) AS name, /* Bad: this should be on a seperate line. */
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
