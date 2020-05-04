# Conditional returns with case / then

[Video link](https://www.egghead.io/lessons/postgresql-conditional-returns-with-case-then)

Instructor: [0:00] The SQL CASE expression is a generic conditional expression. It's similar to the IF ELTs statements you'll find in other programming languages. As you see here, we're doing SELECT first name, and then we say CASE when status is NULL, THEN member ELT status. END from our user's table.

[0:21] Again, just like when using an IF ELTs block in another language, this CASE expression allows us to conditionally return something else, depending on the expression we tell it. For the CASE syntax to be correct, we need CASE WHEN, then our expression. Next is this THEN keyword, the results, and then END here.

[0:43] Just like IF statements in C# or JavaScript, we could continue to add additional WHENs and ELTs to continue checking expressions and changing what actually gets returned. As you can see from our results, down below, most of our users had NULL for their status column.

[0:59] Let's find someone that has a status that is not NULL. As we can see, Lucy is an instructor. Our case will obviously return the current status, and not member, because of our WHERE that's filtering them out.

[1:10] CASE clauses can be used wherever an expression is valid. Let's look at another example where we'll use the CASE condition inside of a WHERE clause. WHERE CASE, when email is not NULL, then start date needs to be greater than January 1st 2019.

[1:29] An expression is required after this WHERE so a CASE clause can live here. All these individuals have emails, which puts us into this THEN, and all of their start dates are greater than our date.

[1:40] Notice we don't have an ELTs like the previous example we looked at, because it's optional. When there isn't an ELTs, then it returns NULL. Our clause is just WHERE NULL, which doesn't return any rows IF any individual's emails were NULL.
