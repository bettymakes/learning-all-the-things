---
topic: Mode Reports
tags: "Mode, SQL"
date: 2019-04-03
---

# 💁 Mode reports with Joy

## Getting started

- When you first start with your report, you want to make sure you've selected the appropriate `Data Warehouse`
  - You can select the appropriate Data Warehouse by expanding this field :point_down:
    ![Data Warehouse Dropdown](https://screenshot.click/2019-29-03-12hkv-8cvpj.jpg)
  - Our team uses `Presto`

## Mode pro-tips

- Double-clicking the name of the table in the sidebar will insert the table name auto-magically for you within your editor
- Pin the tables you're looking at most frequent to prevent excessive scrolling

## SQL

### Basics

- When creating a query, we want to break it down in three parts:

  - What do we want to display?
  - Where do we want to get the data from?
  - What criteria do we want to apply to the subset of data we're looking at?

  ```SQL
    -- What do we want to display?
      SELECT p.id, p.title, COUNT(membership.role) AS num_champions, p_team.name AS team_name

   -- Where do we want to get it from?
      FROM projects p
      INNER JOIN projects_users membership ON p.id = membership.project_id
      INNER JOIN teams p_team ON p.team_id = p_team.id

    -- Our filters (what criteria do we want to apply to the subset of data we're looking at)
      WHERE p.phase NOT IN ('Done', 'Inactive')
      AND p.deleted_at IS NULL
      AND membership.role = 'Champion'
      AND membership.removed_at IS NULL
      GROUP BY 1, 2, 4
      HAVING COUNT(membership.role) > 1
      ORDER BY team_name DESC
  ```

### Aliasing

- You can alias tables and columns by using the `AS` keyword
  ```SQL
  -- Alias the title field on the Projects table to `proj_title`
  SELECT  projects.title AS proj_title
  ```
- Joy tip: We favour snake-casing over strings with white space characters. However, if you wanted to alias the field to a space-seperated-string ...
  - :warning: Presto SQL expects space-separated-string field names to be wrapped in double quotes `"` :warning:
  ```SQL
  -- Spring separated field name must be declared with double quotes
  -- You CAN NOT use a single quote to denote a field name in Presto SQL
  SELECT  projects.title AS "proj title"
  ```
- You can also alias table names
  ```SQL
  -- Alias the Projects table to `pr`
  -- Notice that declaration order doesn't matter
  SELECT pr.title
  FROM projects pr
  ```

### Equality

- The `<>` operator means `not equal`
  - Think of it as `!=`, it can only compare against _one value_
    ```SQL
    -- Filters out any Project records where the phase is `Done`
    SELECT projects.title
    FROM projects
    WHERE projects.phase <> 'Done'
    ```
- When comparing more than one value, use the `NOT IN` keywords
  ```SQL
  SELECT projects.title
  FROM projects
  WHERE projects.phase NOT IN ('Done', 'Inactive')
  ```
- Use `=` when checking whether a value equals to another value
  ```SQL
  SELECT projects.title
  FROM projects
  WHERE projects.phase NOT IN ('Done', 'Inactive')
  AND projects.role = 'Champion'
  ```

### GROUP BY arguments

- `GROUP BY` can accept two types of arguments:

  - The field name

    ```SQL
    SELECT p.id, p.title, COUNT(membership.role) AS num_champions, p_team.name AS team_name
    FROM projects p
    INNER JOIN projects_users membership ON p.id = membership.project_id
    INNER JOIN teams p_team ON p.team_id = p_team.id
    WHERE p.phase NOT IN ('Done', 'Inactive')
    AND membership.role = 'Champion'

    -- Notice that we cannot use team_name, the alias cannot be resolved when the query runs
    GROUP BY p.id, p.title, p_team.name
    ```

  - Alternatively, we can indicate the fields to `GROUP BY` by indicating the "index" position of the fields denoted within the `SELECT` clause (Note: SQL "index" starts counting at `1`)

    ```SQL
    SELECT p.id, p.title, COUNT(membership.role) AS num_champions, p_team.name AS team_name
    FROM projects p
    INNER JOIN projects_users membership ON p.id = membership.project_id
    INNER JOIN teams p_team ON p.team_id = p_team.id
    WHERE p.phase NOT IN ('Done', 'Inactive')
    AND membership.role = 'Champion'

    -- These numbers map to the "argument" position within the SELECT clause
    GROUP BY 1, 2, 4
    ```

### Aggregate functions

- When we want to summarize our data (for example through grouping or counting), we can use [`Aggregate Functions`](https://mode.com/sql-tutorial/sql-aggregate-functions/)

### Subqueries

- If we want to perform a query "on top" of another query, we can use [subqueries](https://mode.com/sql-tutorial/sql-subqueries/)

## Additional learning resources:

- [Mode SQL Tutorial](https://mode.com/sql-tutorial/)
