# Create and Safely Hande Nulls in SQL

Instructor: [0:00] Let's create a table called `posts`. This table is going to hold some blog posts of ours to be viewed later. As you can see, we're going to have an `id`, a `title`, an `excerpt`, a `body`. Then, let's add some dates like `created_at` and `updated_at`. Let's enter some data into our posts table, and then, let's `select` and see what we've got to work with here.

```postgres
postgres=# create table posts (
postgres(# id serial primary key,
postgres(# title text,
postgres(# excerpt text,
postgres(# body text,
postgres(# created_at timestamp default current_timestamp,
postgres(# updated_at timestamp
postgres(# )
CREATE TABLE
postgres=# INSERT INTO posts (title, excerpt, body)
postgres-# VALUES
postgres-#      ('test post 1', 'test post excerpt 1', 'test post body 1'),
postgres-#      ('test post 2', '', 'test post body 2'),
postgres-#      ('test post 3', null, 'test post body 3'),
INSERT 0 3
postgres=# select * from posts; 
 id |    title    |       excerpt       |       body       |        created_at          | updated_at
----+-------------+---------------------+------------------+----------------------------+------------
  1 | text post 1 | test post excerpt 1 | test post body 1 | 2020-04-09 21:41:16.913069
  2 | text post 2 |                     | test post body 2 | 2020-04-09 21:41:16.913069
  3 | text post 3 |                     | test post body 3 | 2020-04-09 21:41:16.913069
```

[0:24] We have three blog posts. They all have titles. One has an excerpt with text that we can read. The second one is not null, it's an empty text and you can see that on the line here where we're inserting it into with an empty string. This last one is null, as you can see where we are inserting that one as well. They all have body and a created_at date.

[0:45] Our goal is to display a post overview page that's going to show the title and the excerpt of each post. In the case that the excerpt is not provided, we default to the first 40 characters of the post body.

[0:57] No one likes to deal with nulls. When pulling out this post, let's `coalesce` the nulls into the first characters of the body. 

```postgres
postgres=# select title, coalesce(excerpt, left(body, 40)) from posts;
    title    |       coalesce
-------------+---------------------
 test post 1 | test post excerpt 1
 test post 2 | 
 test post 3 | test post body 3
(3 rows)
```

The coalesce function accepts an unlimited number of arguments. It returns the first argument that's not null. If they're all null, then it will return null.

[1:14] The `left` function returns the first 'n' number characters from a string. With that in mind, looking at our results, you can see that because test post three had a null excerpt, we return the first 40 characters of the body. However, empty string did not return those characters of the body.

[1:31] In order to solve this, we can force empty strings to be nulls by using the `nullif` function.

```postgres
postgres=# select title, coalesce(nullif(excerpt, ''), left(body, 40)) from posts;
    title    |       coalesce
-------------+---------------------
 test post 1 | test post excerpt 1
 test post 2 | test post body 2
 test post 3 | test post body 3
(3 rows)
```

Then our coalesce will return the body characters now that we have a null here. As you can imagine, the nullify function returns a null value if argument one is equal to argument two. Otherwise, it will return argument one.

[1:55] Because our post two had an empty string, this matched here within a nullif. This is going to be turned a null which makes the coalesce return the first 40 characters of the body for our test post two.
