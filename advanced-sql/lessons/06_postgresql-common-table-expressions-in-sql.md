# Common Table Expressions in SQL

[Video link](https://www.egghead.io/lessons/postgresql-common-table-expressions-in-sql)

Instructor: [0:00] Common Table Expressions, also referred to as a CTE for short, is created by using the WITH keyword, assigning it to a value, and then adding a query within parentheses.

[0:12] For our first example, let's say WITH dates as a SELECT NOW() as date and then we'll say SELECT * FROM dates. Let's look at this query again. We need to start it off with a WITH keyword. The date is going to be the alias we are assigning the returned value to. Inside of the parentheses, we run a SELECT. Inside this SELECT, we're returning NOW() as a date.

[0:36] Notice that we didn't end this line with a semicolon. If we did, we would terminate the CTE because they only last within the query, unlike with temp tables that persist for the entire connection to database.

[0:49] Next stop, we SELECT * from our alias CTE dates and we see that we get date and the current time. The biggest use case I've seen CTEs be used for is the ability to break out complex queries into smaller pieces. Really, almost any time you need to write a subquery, you can potentially break that out and put it at the top of your query and then reference it down below.

[1:11] My members table is intended to show the history of all of my users. The primary key here is not going to be just user_handle, it's going to be some combination of either user_handle, the current_time or my start_date. This is useful within an app that wants to show changes to a users profile over time, for example, a UI that shows that they've changed their name or their email.

[1:33] This however can be tricky when trying to determine the current state of a user. How can you know the earliest start_date row and also the current email row when there could be in number of rows for one user?

[1:47] I've inserted two rows here. They're both for the same user, as you can see by the duplicate user_handles. However, their email has changed. There are also different start_dates. The earlier one has an end_date, meaning, they canceled their account but obviously came back here. When they did, it was with this new email.

[2:05] My goal here is to show to the user the earliest that they signed up, as well as the most current email. In order to get the information out, we need to join the members table onto itself. We're going to do that by using a subquery that will distinct down the user_handles.

[2:23] Our original SELECT up top is going to pull out the email from the subquery of members, which returns the most recent record for the user and then use the main aggregate function to pull out the earliest start_date.

[2:37] Don't worry so much about what this query's doing to get this data. The point that I want you to take away from this is imagine what this query will look like as we join with other tables or possibly needed to add additional subqueries.

[2:51] We could easily break out this subquery part and alias it to a CTE. I've already put that all together here, so I'll paste it. I'm just using the WITH most_recent_membership. That's what I'm going to call it. Inside of it is just our subquery, as you saw on the previous screen.

[3:07] Inside of our LEFT header JOIN, we can reference this most_recent_membership_CTE. We're going to alias it, keep it as MR just like in the previous query, so that we can group by email and pull out that newest email. We're going to get the same results.

[3:23] As you can see, it's a little bit cleaner. You can see what's happening. We're breaking down the subquery nesting that's going to happen to all of our queries.

[3:32] To close, I mentioned when introducing CTEs that we could use a SELECT or DELETE query inside of the parentheses. That's because we could be using the returning clause when deleting items, which would return all of the items that were deleted.

[3:47] In this scenario, we're creating a new table. Within our CTE, we're deleting all of the rows within the original purchases table. Then we can insert these deleted rows assigned to the CTE all into the purchases_copy table.
