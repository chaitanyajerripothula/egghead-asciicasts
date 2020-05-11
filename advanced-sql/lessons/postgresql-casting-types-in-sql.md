# Casting Types in SQL

Instructor: [0:00] Whenever we create tables, we have to give each column a specific type. This enforces that all values that we'll insert into that column will keep that or will get an exception thrown.

```postgres
postgres=# \d users

                 Table "public.users"
    Column    | Type | Collation | Nullable | Default
  ------------+------+-----------+----------+--------
  user_handle | uuid |           | not null | 
  first_name  | text |           |          |
  last_name   | text |           |          | 
  email       | text |           |          | 
Indexes: 
    "users_pkey" PRIMARY KEY, btree (user_handle)
```

[0:10] With this in mind, sometimes you'll find yourself needing to coerce a type to another type, especially when you're joining tables and filtering. If we select out `now()`, we see that it gives us not only a date but also a time. This is a datetime type.

```postgres
postgres=# select now();
           now
----------------------------
2020-03-17-22:07:27.44598-06
```

[0:23] We can cast it into a different type by saying `cast` expression as our target type. 

```postgres
postgres=# select cast (now() as date);
   now
----------
2020-03-17
```

This causes our datetime now into just a date type. Another type of this is a string of a number into an integer. We'll see that we get one hundred.

```postgres
postgres=# select cast('100' as integer);
  int4
--------
  100
```

[0:41] Postgres is pretty cool and has a shorthand feature of this cast by using a ::. If you say `select now()::date`, is doing the same thing as the `select cast (now() as date)`. Let's look at a scenario. Let's create a `temporary` table. We'll call it `users_temp`.

[1:01] It's going to have a `create_date` column with a `date` type, a `user_handle uuid`, `first_name text`, same for the `last_name` and `email`. Then we're going to `insert` into it just one row and the values we'll give it is `now` we'll randomly generate a uuid, last name is `jones`, first name is `michelle`, and no email.

```postgres
postgres=# create temporary table users_temp (create_date date, user_handle uuid, first_name text, last_name text, email text);
CREATE TABLE 
postgres=# insert into user_temp values (now(), uuid_generate_v4(), 'jones', 'michelle');
INSERT 0 1
```

[1:25] Now, let's `select create_date from users_temp u inner join` on a subquery where we `select now() as date`. Then we're going to join `n on u.create_date = n.date;`. 

```postgres
postgres=# select create_date from user_temp u inner join (select now() as date) n on u.create_date = n.date;
  create_date
---------------
(0 rows)
```

Let's walk through this again. We have at temp table and we entered in one row in it.

[1:41] We're using `now()` to generate the date and time. Now what I want to do is join the temp table and the subquery, which both just have one row. We're trying to match on `create_date` and `date`.

[1:54] Both use `now`, so at first glance you might think this would work because it's the same value. The problem is that now is not actually the same value because the first time we used now is a datetime and time and now used the second time is a date.

[2:07] These types don't match, thus showing nothing. As you can imagine if we were to change the temp table to a datetime type it would match types, however, these go down to the millisecond, so it would still not match. Ideally, we want to cast our second type to a date so they match on the day and not the time.

[2:26] By using our handy `::` to cast our DATETIME into a DATE, we'll see that we do get our create date returned.

```postgres
postgres=# select create_date from user_temp u inner join (select now()::date as date) n on u.create_date = n.date;
  create_date
---------------
  2020-03-17
(1 rows)
```
