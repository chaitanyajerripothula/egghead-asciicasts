# Pattern Matching and Regular Expressions in SQL

Instructor: [0:00] Looking at our `users` tables, we see that we have a `user_handle`, `first_name`, `last_name`, `email`, and a `status`. 

```postgres
postgres=# \d users
Table "public.users"
    Column   |    Type     | Collation | Nullable | Default
-------------+-------------+-----------+----------+--------
 user_handle | uuid        |           |          | 
 first_name  | text        |           |          |
 last_name   | text        |           |          | 
 email       | text        |           |          | 
 status      | user_status |           |          |
```

Let's go ahead and insert into this table a random uuid, `tyler`, `clark` and `null` and `null` for email and status. Let's look at some ways we can pattern match and pull that rows data out.

```postgres
postgres=# insert into users values (uuid_generate_v4(), 'tyler', 'clark', null, null);
INSERT 0 1
postgres=# select * from users where first_name like 'tyler';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
``` 

[0:20] As we can see here, I'm selecting out all the rows filtered by `like` the string `'tyler'`. The like expression is used to pattern match if a string matches the supplied pattern. If our pattern does not contain percent signs or underscores, which we'll talk about here next, then the pattern only represents the string itself.

[0:40] You can think of this like the `=` operator as we're trying to evaluate the `first_name` string = this pattern string. It's important to note so it's worth saying it again, the LIKE pattern matching always covers the entire string. If you want to match a sequence anywhere within the string, we need to add additional characters to that pattern.

```postgres
postgres=# select * from users where first_name like 't____';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
``` 

[1:00] One of those additional characters is the underscore. Underscore represents any single character. Here, I'm saying that the pattern I want to match up has the letter T and can then be followed by any four other characters after that, which is Tyler.

[1:16] Another is the `%` sign. Unlike the underscore, the percent sign means any sequence of  or more characters. Our pattern is the letter T followed by any number of characters after that.

```postgres
postgres=# select * from users where first_name like 't%';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
``` 

[1:30] We also have the ability to combine these together. I'll make a pattern that is is one character, then the letter y, then anything after that is fine. If you're curious, you can do the inverse of the pattern by using the `not like` expression instead of the LIKE expression.

```postgres
postgres=# select * from users where first_name like '_y%';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
(1 row)
postgres=# select * from users where first_name not like '_y%';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
(0 rows)
``` 

[1:46] Postgres only has the `ilike` operator, which handles case insensitive patterns. Here we can switch out `like` with `ilike`, and that will still return my row even with the capital Y because ilike is case insensitive.

```postgres
postgres=# select * from users where first_name ilike '_Y%';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
``` 

[2:01] To recap, the `like` and `not like` is how we can pattern match within SQL. These expressions will match the entire string unless we use the underscore or the percent sign.

[2:11] Now let's talk about regular expressions and how we can use those with `similar to` in Postgres and SQL. 

```postgres
postgres=# select * from users where first_name similar to '(t|m)%';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
``` 

As we can see, the similar to operator returns true or false depending on whether its pattern matches the given string, similar to `like` except that it interprets the pattern using the SQL standard definition of regular expressions.

[2:33] SQL regular expressions are a cross between like notation and common regular expression notation. similar to can use the underscore and percent like we saw with like. Though, it has additional characters as we can see here that can be used to match the entire string. The pipe operator is stating either/or.

[2:54] I'm saying the first character can be the letter `t` or the letter `m`. The parenthesis is used to group items into a singular item. Other characters includes the star or the `*`, which is a repetition of the previous items here or more times, the `+`, which is the previous item one or more times, and the `?`, which is the previous item zero or one time.

[3:19] Finally, I want to briefly touch on `POSIX`. This provides an even more powerful way for pattern matching than we're going to get by using like and the similar to operators. POSIX uses match operators. Here, we're just going to use the tilde and to match up with our string. 

```postgres
postgres=# select * from users where first_name ~ 'tyler';
            user_handle              | first_name | last_name | email | status 
-------------------------------------+------------+-----------+-------+--------
03c18455-8187-42b3-b52b-718a921de477 | tyler      | clark     |       |
```

Notice we get the same result as like and similar to.

[3:39] We can combine to be more flexible with this by adding other match operators to the tilde. For example, if we add an asterisk, `~*`, this is now matching on a regular expression and the asterisk makes it case insensitive. The `!~` does not match regular expression case sensitive. The bang tilde asterisk does not match with case insensitive.
