# Aggregate Inline filters in SQL

[Video link](https://www.egghead.io/lessons/postgresql-aggregate-inline-filters-in-sql)

Instructor: [0:00] Working with our purchases table, which has a row of data for each individual order, let's go ahead and select out the sum of all of our quantities of orders that had a quantity greater than five. As reference the sum without filtering is 38 because there are two orders that did not purchase over the five-quantity threshold.

[0:19] We're tasked to select out the first order in our purchases table. As we can see from our data, the earliest date is January 1st, 2019. Let's add min date to our query and pull that out. We're returning February 2nd instead. If you caught it, then good for you. Our where clause is filtering out the data our min aggregate function is using to calculate this earliest date.

[0:42] Notice that our January 1st date has a quantity of three, so it's being filtered out. Remember, our goal here is the earliest row date and the sum of all of our orders that have a quantity over five. We're going to have to re-architect our query here in order to get it to work. Instead, let's use the inline filter here.

[1:01] What this means is we can remove the where at the end of our query and then add a filter where clause right after our sum aggregate function. Notice that now we get the correct min date across our entire data set as well as the same sum of data with our filter still working.

[1:18] The filter clause extends aggregate functions by adding an additional WHERE clause that applies to that aggregate function itself only. If you're in a situation where you do not want to affect the returned rows of data which then affect other selected columns and so forth, use this inline filter.

[1:37] The syntax is quite simple, as you can see. It can only be used after an aggregate function. We say, "Filter," then where in parentheses, and then our filter clause. A common misconception is that we can reference the aggregate data this filter applies to.

[1:52] This isn't true. It's affecting the rows of data used to create the aggregated data. In other words, we couldn't group by user handles in our query then reference this sum within the filter. Again, we can only use the columns in our table as is.
