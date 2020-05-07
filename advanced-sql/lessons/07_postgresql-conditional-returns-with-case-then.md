# Conditional returns with case / then

[Video link](https://www.egghead.io/lessons/postgresql-conditional-returns-with-case-then)

Instructor: [0:00] The SQL `case` expression is a generic conditional expression. It's similar to the if/else statements you'll find in other programming languages. As you see here, we're doing SELECT first name, and then we say `case when status is null then 'member' else status end` from our user's table.

```postgres
postgres=# select first_name
postgres=# case when status is null then 'member' else status end
first_name | status
--------------------
Lucie      | member
Nancy      | member
Rebekah    | member
Maria      | member
Jamie      | member
Ashley     | member
...
```

[0:21] Again, just like when using an if/else block in another language, this `case` expression allows us to conditionally return something else, depending on the expression we tell it. For the `case` syntax to be correct, we need `case when`, then our expression. Next is this `then` keyword, the results, and then `end`.

[0:43] Just like `if` statements in C# or JavaScript, we could continue to add additional when's and else's to continue checking expressions and changing what actually gets returned. As you can see from our results, down below, most of our users had NULL for their status column.

[0:59] Let's find someone that has a status that is not `null`. 

```postgres
postgres=# select first_name
postgres=# case when status is null then 'member' else status end
postgres=# from users
postgres=# where status is not null;

first_name |   status
------------------------
Lucie      | instructor
```

As we can see, Lucy is an instructor. Our case will obviously return the current status, and not member, because of our `where` that's filtering them out.

[1:10] CASE clauses can be used wherever an expression is valid. Let's look at another example where we'll use the `case` condition inside of a `where` clause. `where case when email is not null then start_date > '2019-01-01' end;`.

```postgres
postgres=# select first_name, start_date
postgres=# from members
postgres=# where case when email is not null then start_date > '2019-01-01' end;
first_name | start_date
--------------------
joe        | 2019-02-20
tyler      | 2019-02-20
Stacy      | 2019-04-01
Tyler      | 2019-04-01
Melissa    | 2019-04-01
Melissa    | 2019-04-01
(6 rows)
```

[1:29] An expression is required after this `where` so a `case` clause can live here. All these individuals have emails, which puts us into this `then`, and all of their start dates are greater than our date.

[1:40] Notice we don't have an else like the previous example we looked at, because it's optional. When there isn't an else, then it returns null. Our clause is just `where null`, which doesn't return any rows IF any individual's emails were NULL.
