# postgit

**Declarative version control for PostgreSQL databases**

## how it works

Forget about defining your database in function of migrations that apply on
top of the older version of the database. Instead write your changes like you
normally would in any other project; and then commit the detected changes to
your database.

**Examples:**

### Initialize a new database repo

Empty database:
```
postgit init
```

From connection URL:
```
postgit init --url postgres://user:password@host:port/database
```

### Create a new table

Create a new script wherever you want in the repository with your table
definition:

```
CREATE TABLE example (
  id serial PRIMARY KEY,
  name varchar(255) NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
)
```

Check database status. This command pulls the current state of the database,
using a cached version when possible. We keep metadata on the db to now when
the latest version was pulled so we now when we need to refresh (only detects
changes apply by postgit, if you need to force a refresh use the `--refresh`
flag).

```
postgit status

+ TABLE: example
```

We can generate a migration script easily, this compares the working index
against the current db layout, and generates a script that will apply _all_
changes for us in a **single transaction**.

```
postgit generate
```

Review script manually in `./migrations` then proceed to

```
postgit apply
```

That's it!

### Adding a column

Open your table definition script and add a column:

```
CREATE TABLE example (
  id serial PRIMARY KEY,
  name varchar(255) NOT NULL,
  content text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
)
```

Run the migration in one command:

```
postgit apply
```

This runs the same process as in the previous example, detects a new column,
generates the necessary migration, and applies it.

## planned features

**Support for following database objects:**

- Schemas
- Tables
- Functions
- RLS Policies
- Roles and Users
- Rules
- Triggers
- Views

**Database initialization:**

Scrape the database for all objects and create an initial source repository.

**Automatic dependency tracking:**

Create a table in one file and reference it in another file. We make sure
they are created (or deleted) in the correct order.

Should also work for:

- Function references
- Role references
- Etc
