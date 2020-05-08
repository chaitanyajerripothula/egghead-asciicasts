# Filter Grouped and Aggregated Data with Having

Instructor: [0:00] We have a table called `purchases`, and it keeps track of all the orders that take place by our users. As we can see, we have a `date`, a `user_handle`, a `sku`, and the `quantity`. Each row inside of this table is going to represent each checkout per item.

```postgres
postgre=# \d purchases;
                Table "public.purchases"
   Column   |   Type  | collation | Nullable | Default
------------+---------+-----------+----------+
date        | date    |           |          | 
user_handle | uuid    |           |          | 
sku         | uuid    |           |          | 
date        | integer |           |          | 
```

[0:15] I'm going to insert in some data that we can play around with. These first three inserts are transactions for the same user, but they're going to be for different SKUs or products and for different quantities of those unique products.

```postgres
postgres=# insert into purchases values ('2019-02-02', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
INSERT 0 1
postgres=# insert into purchases values ('2019-02-03', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 2);
INSERT 0 1
postgres=# insert into purchases values ('2019-02-04', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 3);
INSERT 0 1
```

[0:29] The last one is going to be a different user than the previous, with again a different sku, so all of them are different products. This one has a quantity of `3` for that one item.

```postgres
postgres=# insert into purchases values ('2019-02-02', 'cebfb6c1-7e85-44b1-8408-4084d38f87dd', uuid_generate_v4(), 3);
INSERT 0 1
```

[0:40] With that data in place, let's go ahead and write `select user_handle`, the `sum` of `quantity`, `as total from purchases where sum(quantity) > 5 group by user_handle;`.

```postgres
postgres=# select user_handle, sum(quantity) as total from purchases where sum(quantity) > 5 group by user_handle;
ERROR: aggregate functions are not allowed in WHERE
LINE 1: ...ndle, sum(quantity) as total from purchases where sum(quanti...)
```

[1:00] Let's take a look at this query. What I ultimately want to return is all of the users that have purchased more than five total items. It doesn't have to be five different products, but I care about the total amount of items purchased. It has to be greater than five.

[1:16] Thinking back to our answers, we're looking for that first user that I inserted through rows data for, not the second user. We are trying to filter using the `where` clause, where the sum of quantities is greater than five. However, our error here is telling us that we cannot filter aggregates by the WHERE. We actually need to use the having clause.

[1:36] I'll remove the `where` clause, and then after the `group by` we add the `having` clause for the same sum greater than five expressions. 

```postgres
postgre=# select user_handle, sum(quantity) as total from purchases group by user_handle having sum(quantity) > 5;
            user_handle              | total
-------------------------------------+-------
a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 |     6
(1 row)
```

As you can see, we're retuning that first user that has a quantity of six total items purchased.

[1:51] Some takeaway item of using this having clause, you often use a having clause in conjunction with the group by clause to filter grouped rows that did not satisfy a specific condition.

[2:02] **In our case, after we grouped the rows together by user handles, then we filter the rows. On the other side, the where clause filters out rows before we group our data together.** This is the biggest difference between where and having.

[2:16] It's possible to use the having clause without the group by clause. In those situations, we'll treat all the rows, though, as one big group. This is pretty and commonly used because having end select will need to both use aggregate functions, and if the expression was true, you'd only get one row and none if it was false.
