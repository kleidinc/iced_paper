# Adding a Postgres Database to your Iced Application

Any serious application will have either an embedded database, or a database connection to somewhere.
In this experiment we will be working with a local Postgres database. But you can also connect to any
database in the cloud.

<!-- mtoc-start -->

* [SQLX?](#sqlx)
  * [The Why](#the-why)
  * [Add SQLX to your Cargo.toml](#add-sqlx-to-your-cargotoml)
  * [Pattern to build a database with SQLX](#pattern-to-build-a-database-with-sqlx)

<!-- mtoc-end -->

This is part of the [Master Iced Paper]().

# SQLX?

## The Why

- The pattern on how to use SQLX enables fast iterations as you are modeling your database, from the cli.
- Once your application is pushed to production you can automatically manage changes to the structure of the database, through sqlx migrate.
- Sqlx is async

## Add SQLX to your Cargo.toml

```bash
cargo add sqlx --features postgres,runtime-tokio,uuid,time,migrate,macros,bigdecimal
```

## Pattern to build a database with SQLX

1. Export the DATABASE_URL in your shell

```bash
export DATABASE_URL=postgres://alex:1234@localhost/icedformsexperiment
```

2. Delete the database (if it exists).

```bash
sqlx database drop
```

3. Create the database

```bash
sqlx database create
```

4. Create the tables you need in the migrations folder through sqlx

```bash
sqlx migrate add <table name>
```

5. Edit the files in the in the migrations folder.
   The reason why you should do it that way, is that once you really start building and running your application, you will be able
   to use version control to automatically upgrade tables through sqlx.

6. Read the SQL files in the migrations folder
   `sqlx migrate run`
