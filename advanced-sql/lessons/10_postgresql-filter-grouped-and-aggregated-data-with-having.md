# Filter Grouped and Aggregated Data with Having

[Video link](https://www.egghead.io/lessons/postgresql-filter-grouped-and-aggregated-data-with-having)

Instructor: [0:00] We have a table called purchases, and it keeps track of all the orders that take place by our users. As we can see, we have a date, a user handle, a SKU, and the quantity. Each row inside of this table is going to represent each checkout per item.

[0:15] I'm going to insert in some data that we can play around with. These first three inserts are transactions for the same user, but they're going to be for different SKUs or products and for different quantities of those unique products.

[0:29] The last one is going to be a different user than the previous, with again a different SKU, so all of them are different products. This one has a quantity of three for that one item.

[0:40] With that data in place, let's go ahead and write select user handle, the sum of quantity, as total from purchases where that sum of quantity is going to be greater than five grouping by user handles.

[1:00] Let's take a look at this query. What I ultimately want to return is all of the users that have purchased more than five total items. It doesn't have to be five different products, but I care about the total amount of items purchased. It has to be greater than five.

[1:16] Thinking back to our answers, we're looking for that first users that I inserted through rows data for, not the second user. We are trying to filter using the WHERE clause, where the sum of quantities is greater than five. However, our error here is telling us that we cannot filter aggregates by the WHERE. We actually need to use the HAVING clause.

[1:36] I'll remove the WHERE clause, and then after the GROUP BY we add the HAVING clause for the same sum greater than five expression. As you can see, we're retuning that first user that has a quantity of six total items purchased.

[1:51] Some takeaway item of using this HAVING clause, you often use a HAVING clause in conjunction with the GROUP BY clause to filter grouped rows that did not satisfy a specific condition.

[2:02] In our case, after we grouped the rows together by user handles, then we filter the rows. On the other side, the WHERE clause filters out rows before we group our data together. This is the biggest difference between WHERE and HAVING.

[2:16] It's possible to use the HAVING clause without the GROUP BY clause. In those situations, we'll treat all the rows, though, as one big group. This is pretty and commonly used because HAVING end SELECT will need to both use aggregate functions, and if the expression was true, you'd only get one row and none if it was false.
