# Perform Multiple Steps in One with Transactions

[Video link](https://www.egghead.io/lessons/postgresql-perform-multiple-steps-in-one-with-transactions)

Instructor: [0:00] Imagine you're a developer for a bank, and you're in charge of the code that handles transferring money from one account to another.

[0:06] How can you write code that will guarantee that while extracting money out of one account no hiccups happen or your server doesn't go down at that exact moment and the next step of the code never runs, the part where you're supposed to insert or update the money going into?

[0:22] This is where `transaction` blocks come into play. Transactions bundle multiple steps with a single all-or-nothing operation. 

```postgres
postgres=# start transaction;
START TRANSACTION
postgres=# insert into purchases values ('2019-05-20', 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11', uuid_generate_v4(), 1);
INSERT 0 1
postgres=# update purchases set quantity = 5 where date = '2019-05-20';
UPDATE 1
postgres=# commit;
COMMIT
```

The steps that happen in between here are not accessible to other concurrent actions. If there's an error anywhere inside of this transaction block, then everything will be rolled back, which basically makes it a no op.

[0:40] In order to start a transaction block, we need to use this `start transaction` and end it with a `;`. Every command that happens after this start transaction, like our `insert` and our `update`, is not going to happen unless we end it with a `commit`.

[0:55] If you we were to look up these changes inside of our transaction block, look up for this insert and this update, we will see it inside of this session. However, if we don't commit this, once we close this session, it doesn't apply to our table.

[1:10] Let's look at another example. We're going to start with `begin` this time. We're going to say `insert into purchases values`. Next, we're going to say `savepoint insert_save_point`. Now we're going to do another `insert into purchases` with different `values`.

[1:25] Before we go on, we're going to add a `rollback` to our savepoint that we previously made. Then we're going to `update purchases`, `set` all of their quantities to 8, and then `commit` this.

```postgres
postgres=# begin;
BEGIN
postgres=# insert into purchases values ('2019-10-10', 'f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11' uuid_generate_v4(), 1);
INSERT 0 1
postgres=# savepoint insert_save_point;
SAVEPOINT
postgres=# insert into purchases values ('2019-05-01;, '28e87413-2750-4c16-8e95-0c67f0dae2fa', uuid_generate_v4(), 1);
INSERT 01
postgres-# rollback to insert_save_point;
ROLLBACK
postgres=# update purchases set quantity = 8;
UPDATE 6
postgres=# commit;
COMMIT
```

[1:38] Let's talk about what's going on here. First off, you notice that we use `begin` here instead of `start transaction` like in the last example. Both are valid and they do the same thing.

[1:49] Next up, we `insert` our values values, then create what's called a `savepoint`. savepoints, as you can tell from the name and how we used it with the rollback, are used to put a bookmark in your transaction, which basically tells the rollback what actions to undo up until the designated savepoint.

[2:06] In our example, our second `insert` happens here within our session. However, it's immediately rolled back to the next line. Meaning when we `commit` our code at the bottom, we're not going to see this second `insert` performed. We'll double check this here in a minute.

[2:20] savepoints and rollbacks are useful when you're using a back-end language and you want to check some outside effects while running your transaction. Depending on outside factors, you could add this rollback code or not, if/else and template strings. Next step is another `update` setting all quantities to 8 and then we `commit`, making our changes official.

[2:41] Let's look at our Purchases table and see what was added. If we slack start from Purchases where `user_handle` and we'll grab this first one, we'll see that we did add this to the Purchases table. All the values are the same. 

```postgres
postgres=# select * from purchases where user_handle = 'f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
    date     |                user_handle           |                  sku                 | quantity 
-------------+--------------------------------------+--------------------------------------+----------
 2019-10-10  | f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11 | 039bc9ea-5cda-455f-91fd-1977c758dfd7 |    8 
(1 row)
```

Let's look for the second one. Our move this user_handle and add this. You'll notice we didn't add this one because of the rollback and savepoint.

```postgres
postgres=# select * from purchases where user_handle = 'f0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11';
  date | user_handle | sku | quantity 
-------+-------------+-----+----------
(0 rows)
```

[3:02] If we `select * from purchases` notice how all the quantities are 8, so the rollback and the savepoint that insert didn't happen, but the first insert and that third update did happen in our transaction block.