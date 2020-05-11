# On conflict do something clause

Instructor: [0:00] Let's say that you have an eCommerce site and you allow the creation of an account to happen during the checkout process. Whenever a user signs up, we store some basic info about that person into our users table.

```postgres
postgres=# select * from users where email = 'Lucie-Jones@gmail.com';
                    user_handle       | first_name | last_name | email                 | status
--------------------------------------+------------+-----------+-----------------------+-------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Jones     | Lucie-Jones@gmail.com |
```

[0:12] When a user enters in an email to create a new account, we want to check to see if that email is already being saved for a different user because two different users should not have the same email for security purposes and account recovery.

[0:25] Before we create a new account, we want to select out of our users table to see if that email already exists. 

```postgres
postgres=# insert into user (user_handle, email) values (uuid_generate_v4(), 'Lucie-Jones@gmail.com');
ERROR: duplicate key value violates unique constraint "unique_email"
DETAIL: Key (email)=(Lucie-Jones@gmail.com) already exists.
```

To insert a new user, we'll have to run two separate queries, a `select` to see if it exists and then the `insert` if it does not.

[0:41] However, there is an easier way to do this. We can write `insert into users` listing out the column headers, then our values, a UUID, Lucy Jones with an email `Lucie-Jones@gmail.com`. Then we'll say `on conflict do nothing`.

```postgres
postgres=# insert into users (user_handle, first_name, last_name, email) values (uuid_generate_v4(), 'Lucie', 'Jones', 'Lucie-Jones@gmail.com') on conflict do nothing;
INSERT 0 0
```

[1:01] When we run this, we'll see that we are inserting nothing. We'll see `INSERT 0 0`. If we didn't add this `on conflict do nothing` clause, we would see an error printed to the page, rather than this `INSERT 0 0`.

[1:13] Another cool thing about swallowing these errors with this on conflict clause is we can instead of doing an insert or not doing anything, we can `update` a row. Let's say that our friend Lucy got married and she has a different last name, but she kept her original email. We see that we have Lucy Hawkins going into our users table, but the same email.

[1:33] We're going to say `on conflict (email) do update set first_name = excluded.frst_name, last_name = excluded.last_name;`. 

```postgres
postgres=# insert into users values (uuid_generate_v4(), 'Lucie', 'Hawkins', 'Lucie-Jones@gmail.com') on conflict (email) do update set first_name = excluded.first_name, last_name = excluded.last_name;
INSERT 0 1
```

We'll see that here we do get an INSERT. When we select that Lucy again by looking up her email, we'll see that her last name has been updated to Hawkins and that she still has the same email.

```postgres
postgres=# select * from users where email = 'Lucie-Jones@gmail.com';
                    user_handle       | first_name | last_name | email                 | status
--------------------------------------+------------+-----------+-----------------------+-------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Hawkins   | Lucie-Jones@gmail.com |
```

[1:58] Let's go back and look at our query again. When we want to do an update whenever there's a conflict with our insert statement, we have to specify the conflict. This is called the conflict target. In our case, the conflict is a duplicate value on the email column that I have previously set to be a unique constrain.

[2:16] Next up is the conflict action. In our case, we're doing an update, `do update`. We want to specifically update `first_name` and `last_name` to whatever values are coming in with the insert statement. In our case, that is these values here, `'Lucie'` and `'Hawkins'`.

[2:30] We reference them by this excluded terminology. By saying `first_name = excluded.first_name`, we're saying update the row that has the conflict with the excluded value or the value that's coming in on the insert statement.

[2:43] We're basically guarantying that we are doing either an insert or an update, which is also commonly known as an `upsert`. Taking this even one step further because it's cool and interesting to note that we can use a `where` clause within this update as well.

[2:59] Backing up, we can alias the users table with an U. Then we can say `where u.first_name` is not Lucy. 

```postgres
postgres=# insert into users as u values (uuid_generate_v4(), 'Lucie', 'Cook', 'Lucie-Jones@gmail.com') on conflict (email) do update set first_name = excluded.first_name, last_name = excluded.last_name where u.first_name <> 'Lucie';
INSERT 0 0
```

This has a couple of conditions now, right? If there's a conflict with email, do update setting these columns to the incoming data. Unless, the original row's first name is Lucy, then don't update the row.

```postgres
postgres=# select * from users where email = 'Lucie-Jones@gmail.com';
              user_handle             | first_name | last_name |         email         | status
--------------------------------------+------------+-----------+-----------------------+-------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Hawkins   | Lucie-Jones@gmail.com |
```
