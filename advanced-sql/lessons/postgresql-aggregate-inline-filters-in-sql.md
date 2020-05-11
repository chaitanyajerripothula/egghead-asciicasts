Instructor: [0:00] Working with our `purchases` table, which has a row of data for each individual order, let's go ahead and `select` out the `sum` of all of our quantities of orders that had a quantity greater than five. As reference the sum without filtering is 38 because there are two orders that did not purchase over the five-quantity threshold.

```postgres
postgres=# select sum(quantity) from purchases where quantity > 5;
 sum
-----
 32
(1 row)
postgres=# select sum(quantity) from purchases;
 sum
-----
 38
(1 row)
postgres=# select * from purchases;
    date    |            user_handle               |                    sku               | quantity
 -----------+--------------------------------------+--------------------------------------+----------
 1019-02-02 | a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | 786a75b5-ed2b-4a0b-abb2-baf7b80f086b |        8
 1019-02-03 | a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | aa12b5ec-97cd-480d-8953-7b3cae3b9c5d |        8
 1019-02-04 | a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | 6734953a-8221-4ba5-b37b-34f6597c583d |        8
 1019-04-20 | cebfb6c1-7e85-44b1-8408-4084d38f87dd | e00f8e69-6c7e-46f9-b1a5-640ab11dead7 |        8
 1019-10-10 | f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | 039bc9ea-5cda-455f-91fd-1877c758dfd7 |        3
 1019-01-01 | a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | fc811183-28b9-4599-8d6a-261d949a4c41 |        3
```

[0:19] We're tasked to select out the first order in our purchases table. As we can see from our data, the earliest date is January 1st, 2019. Let's add `min(date)` to our query and pull that out. 

```postgres
postgres=# select min(date, sum(quantity) from purchases where quantity > 5;
    min    | sum
-----------+-----
2019-02-02 | 32
```

We're returning February 2nd instead. If you caught it, then good for you. Our `where` clause is filtering out the data our min aggregate function is using to calculate this earliest date.

[0:42] Notice that our January 1st date has a quantity of three, so it's being filtered out. Remember, our goal here is the earliest row date and the sum of all of our orders that have a quantity over five. We're going to have to re-architect our query here in order to get it to work. Instead, let's use the inline `filter`.

[1:01] What this means is we can remove the `where` at the end of our query and then add a `filter` where clause right after our `sum` aggregate function. 

```postgres
postgres=# select min(date, sum(quantity) filter (where quantity > 5) from purchases;
    min    | sum
-----------+-----
2019-01-01 | 32
```

Notice that now we get the correct min date across our entire data set as well as the same sum of data with our filter still working.

[1:18] The filter clause extends aggregate functions by adding an additional WHERE clause that applies to that aggregate function itself only. If you're in a situation where you do not want to affect the returned rows of data which then affect other selected columns and so forth, use this inline filter.

[1:37] The syntax is quite simple, as you can see. It can only be used after an aggregate function. We say, `filter`, then `where` in parentheses, and then our filter clause. A common misconception is that we can reference the aggregate data this filter applies to.

```postgres
postgres=# select min(date, sum(quantity) filter (where sum > 5) from purchases;
ERROR: column "sum" does not exist
LINE 1: select min(date, sum(quantity) filter (where sum > 5) from ...
                                                    ^
```

[1:52] This isn't true. It's affecting the rows of data used to create the aggregated data. In other words, we couldn't group by user handles in our query then reference this sum within the filter. Again, we can only use the columns in our table as is.
