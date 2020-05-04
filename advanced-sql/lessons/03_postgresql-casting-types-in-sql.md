# Casting Types in SQL

[Video link](https://www.egghead.io/lessons/postgresql-casting-types-in-sql)

Instructor: [0:00] Whenever we create tables, we have to give each column a specific type. This enforces that all values that we'll insert into that column will keep that or will get an exception thrown.

[0:10] With this in mind, sometimes you'll find yourself needing to coerce a type to another type, especially when you're joining tables and filtering. If we select out NOW, we see that it gives us not only a date but also a time. This is a DATETIME type.

[0:23] We can cast it into a different type by saying CAST {} expression as our target type. This causes our DATETIME NOW into just a DATE type. Another type of this is a string of a number into an integer. We'll see that we get a hundred here.

[0:41] Postgres is pretty cool and has a shorthand feature of this cast by using a ::. If you say SELECT NOW::DATE, is doing the same thing as the SELECT CAST NOW as date. As you can see our now is a date type. Let's look at a scenario. Let's create a temp table. We'll call it users temp.

[1:01] It's going to have a create date column with a date type, a user handle UID, first name string text, same for the last name and email. Then we're going to insert into it just one row and the values we'll give it is now we'll randomly generate a UID, last name is Jones, first name is Michelle, and no email.

[1:25] Now, let's select create date from users temp you inter join on a subquery where we're selecting out now as date. Then we're going to join on create date and this date. Let's walk through this again. We have at temp table and we entered in one row in it.

[1:41] We're using NOW to generate the date and time. Now what I want to do is join the temp table and the subquery, which both just have one row. We're trying to match on create date here and date here.

[1:54] Both use NOW, so at first glance you might think this would work because it's the same value. The problem is that NOW is not actually the same value because NOW used here is a DATETIME and time and NOW used here is a DATE.

[2:07] These types don't match, thus showing nothing. As you can imagine if we were to change the temp table to a DATETIME type it would match types, however, these go down to the millisecond, so it would still not match. Ideally, we want to cast this type to a date so they match on the day and not the time.

[2:26] By using our handy :: to cast our DATETIME into a DATE, we'll see that we do get our create date returned.
