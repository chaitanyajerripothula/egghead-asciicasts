# Pattern Matching and Regular Expressions in SQL

[Video link](https://www.egghead.io/lessons/postgresql-pattern-matching-and-regular-expressions-in-sql)

Instructor: [0:00] Looking at our users tables, we see that we have a user_handle, first_name, last_name, email, and a status. Let's go ahead and insert into this table a random UUID, Tyler, Clark and null and null for email and status. Let's look at some ways we can pattern match and pull that rows data out.

[0:20] As we can see here, I'm selecting out all the rows filtered by LIKE "Tyler". The LIKE expression is used to pattern match if a string matches the supplied pattern. If our pattern does not contain percent signs or underscores, which we'll talk about here next, then the pattern only represents the string itself.

[0:40] You can think of this like the = operator as we're trying to evaluate the first_name string = this pattern string. It's important to note so it's worth saying it again, the LIKE pattern matching always covers the entire string. If you want to match a sequence anywhere within the string, we need to add additional characters to that pattern.

[1:00] One of those additional characters is the underscore. Underscore represents any single character. Here, I'm saying that the pattern I want to match up has the letter T and can then be followed by any four other characters after that, which is Tyler.

[1:16] Another is the percent sign. Unlike the underscore, the percent sign means any sequence of  or more characters. Our pattern is the letter T followed by any number of characters after that.

[1:30] We also have the ability to combine these together. Here, my pattern is one character, then the letter y, then anything after that is fine. If you're curious, you can do the inverse of the pattern by using the NOT LIKE expression instead of the LIKE expression.

[1:46] Postgres only has the ILIKE operator, which handles case insensitive patterns. Here we can switch out LIKE with ILIKE, and that will still return my row even with the capital Y because ILIKE is case insensitive.

[2:01] To recap, the LIKE and NOT LIKE is how we can pattern match within SQL. These expressions will match the entire string unless we use the underscore or the percent sign.

[2:11] Now let's talk about regular expressions and how we can use those with SIMILAR TO in Postgres and SQL. As we can see here, the SIMILAR TO operator returns true or false depending on whether its pattern matches the given string, similar to LIKE except that it interprets the pattern using the SQL standard definition of regular expressions.

[2:33] SQL regular expressions are a cross between LIKE notation and common regular expression notation. SIMILAR TO can use the underscore and percent like we saw with LIKE. Though, it has additional characters as we can see here that can be used to match the entire string. The pipe operator here is stating either/or.

[2:54] I'm saying the first character can be the letter T or the letter M. The parenthesis is used to group items into a singular item. Other characters includes the star or the asterisk, which is a repetition of the previous items here or more times, the plus, which is the previous item one or more times, and the question mark, which is the previous item zero or one time.

[3:19] Finally, I want to briefly touch on POSIX. This provides an even more powerful way for pattern matching than we're going to get by using like and the similar to operators. POSIX uses match operators. Here, we're just going to use the tilde and to match up with our string. Notice we get the same result as LIKE and SIMILAR TO.

[3:39] We can combine to be more flexible with this by adding other match operators to the tilde. For example, if we add an asterisk, this is now matching on a regular expression and the asterisk makes it case insensitive. The bang tilde does not match regular expression case sensitive. The bang tilde asterisk does not match with case insensitive.
