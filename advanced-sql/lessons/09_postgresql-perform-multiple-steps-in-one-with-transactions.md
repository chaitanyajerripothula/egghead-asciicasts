# Perform Multiple Steps in One with Transactions

[Video link](https://www.egghead.io/lessons/postgresql-perform-multiple-steps-in-one-with-transactions)

Instructor: [0:00] Imagine you're a developer for a bank, and you're in charge of the code that handles transferring money from one account to another.

[0:06] How can you write code that will guarantee that while extracting money out of one account no hiccups happen or your server doesn't go down at that exact moment and the next step of the code never runs, the part where you're supposed to insert or update the money going into?

[0:22] This is where transactions come into play. Transactions bundle multiple steps with a single all-or-nothing operation. The steps that happen in between here are not accessible to other concurrent actions. If there's an error anywhere inside of this transaction block, then everything will be rolled back, which basically makes it a no op.

[0:40] In order to start a transaction block, we need to use this START TRANSACTION and end it with a semicolon. Every command that happens after this START TRANSACTION, like our INSERT and our UPDATE, is not going to happen unless we end it with a COMMIT.

[0:55] If you we were to look up these changes inside of our transaction block, look up for this INSERT and this UPDATE, we will see it inside of this session. However, if we don't COMMIT this, once we close this session, it doesn't apply to our table.

[1:10] Let's look at another example. We're going to start with BEGIN this time. We're going to say INSERT INTO Purchases these values. Next, we're going to say SAVEPOINT insert_savepoint. Now we're going to do another INSERT INTO Purchases with different values.

[1:25] Before we go on, we're going to add a ROLLBACK to our SAVEPOINT that we previously made. Then we're going to UPDATE Purchases, set everything, all of their quantities to 8, and then COMMIT this.

[1:38] Let's talk about what's going on here. First off, you notice that we use BEGIN here instead of START TRANSACTION like in the last example. Both are valid and they do the same thing.

[1:49] Next up, we INSERT these values, then create what's called a SAVEPOINT. SAVEPOINTs, as you can tell from the name and how we used it with the ROLLBACK, are used to put a bookmark in your transaction, which basically tells the ROLLBACK what actions to undo up until the designated SAVEPOINT.

[2:06] In our example, this next INSERT happens here within our session. However, it's immediately rolled back to the next line. Meaning when we COMMIT our code here at the bottom, we're not going to see this INSERT performed. We'll double check this here in a minute.

[2:20] SAVEPOINTs and ROLLBACKs are useful when you're using a back-end language and you want to check some outside effects while running your transaction. Depending on outside factors, you could add this ROLLBACK code or not, [inaudible] in template strings. Next step is another UPDATE setting all quantities to 8 and then we COMMIT, making our changes official.

[2:41] Let's look at our Purchases table and see what was added. If we slack start from Purchases where user_handle and we'll grab this first one, we'll see that we did add this to the Purchases table. All the values are the same. Let's look for the second one. Our move this user_handle and add this. You'll notice we didn't add this one because of the ROLLBACK and SAVEPOINT.

[3:02] If we SELECT * notice how all the quantities are 8, so the ROLLBACK and the SAVEPOINT that INSERT didn't happen, but the first INSERT and that third UPDATE did happen in our transaction block.
