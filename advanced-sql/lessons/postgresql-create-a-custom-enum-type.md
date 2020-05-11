# Create a Custom Enum Type 

[Video link](https://www.egghead.io/lessons/postgresql-create-a-custom-enum-type)

Instructor: [0:00] Data integrity is something you should always have in mind. It just makes writing and debugging queries so much easier. We ensure integrity by adding constraints to our tables like not null, unique, adding primary keys, and also using the correct types for our columns.

[0:16] One powerful feature of SQL is the ability to add custom types. Let's write `create type user_status as enum`, then we're going to give it three string values -- `member`, `instructor`, and `developer`. Once you've run this, you'll see it is as `CREATE TYPE`.

```postgres
postgres=# create type user_status as enum ('member', 'instructor', 'developer');
CREATE TYPE
```

[0:32] Let's break this down. We're creating a custom type and calling it `user_status`. Next up, we're defining the data type of this custom type. Here, we're using an `enum`. There are other custom data types you can create with composites, enumerated, range, base, and array.

[0:49] We can spend an hour going over this, but for this video we're just going to stick to using enums. For the enum values we want to use, we're going to pass those through here as strings. We only want to use `members`, `instructors`, and `developers`.

[1:02] Let's think of our product like egghead.io. We need to differentiate users. Especially when they access the page we need to know if they're a member, instructor, or developer.

[1:12] Looking at our `users` table again, we've got four columns here -- user handle, first name, last name, and email. 

```postgres
postgres=# select * from users limit 1;
              user_handle             | first_name | last_name |         email          
--------------------------------------+------------+-----------+-----------------------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Smith     | Lucie-Clark@gmail.com 
```

Let's go ahead and add another column to this table that's going to have the enum that we just created.

[1:25] We'll call that column the status and it's going to have the user `status`. By default it's going to be null because we're not giving it any value. You see that Lucy Smith doesn't have a status.

```postgres
postgres=# alter table users add column status user_status;
postgres=# select * from users limit 1;
              user_handle             | first_name | last_name |         email         | status 
--------------------------------------+------------+-----------+-----------------------+--------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Smith     | Lucie-Clark@gmail.com | 
```

[1:35] We can easily do this by just updated her row. Let's say `update users set status = 'instructor' where first_name = 'Lucie';`. Now we go and try and find her again we'll see that we successfully updated her status to an instructor.

```postgres
postgres=# update users set status = 'instructor' where first_name = 'Lucie';
postgres=# select * from users limit 1;
              user_handle             | first_name | last_name |         email         | status 
--------------------------------------+------------+-----------+-----------------------+--------
57bd72d4-7115-11e9-a923-1681be663d3e  | Lucie      | Smith     | Lucie-Clark@gmail.com | instructor
```

[1:53] Now, if you're like me and a terrible speller, this enum is going to save us from misspelling. If we misspell instructor, we're going to see an arrow thrown that's going to catch it and save us, thus helping our data integrity of our table.

```postgres
postgres=# update users set status = 'instruct' where first_name = 'Lucie';
ERROR: invalid input for enum user_status: "instruct"
LINE 1: update users set status = 'instruct' where first_name = 'Luc...
```

[2:07] To review, creating custom types is super easy. The syntax is just `create type`. Then you're going to give it a name of this new type and then you're going to define the data type that you want your custom type to reference.

[2:20] In our case we used an ENUM so it took open and closed parens. 

```postgres
postgres=# create type "name" as "data type" ();
```

Again, check the notes of this video for other options for other SQL languages and other data types you can use when creating custom types.
