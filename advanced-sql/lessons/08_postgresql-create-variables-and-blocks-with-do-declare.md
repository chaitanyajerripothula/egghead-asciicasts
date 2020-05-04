# Create Variables and Blocks with Do / Declare

[Video link](https://www.egghead.io/lessons/postgresql-create-variables-and-blocks-with-do-declare)

Instructor: [0:00] We have a member's table that has a start date, end date, user handle, first name, and email. Start date and end date are date types, user handle's a UID, and first name and email are text. To get started we're going to do a DO $$ DECLARE.

[0:14] We're going to say handle is a UID equal to this UID. We're going to say begin and then enter it into members values now null handle Stacy and for the email it'll be stacy@Gmail.com. We're going to say END $$. Don't forget our : .

[0:32] Then let's go and look up in the member's table to see if we added Stacy correctly. We'll pull her out. Her first name is Stacy and you can see that we did insert into our member's table. Now, let's slow down and break down what's happening here.

[0:45] Starting at the top, we see we have DO and then this $$. In Postgres, DO is highly defined in anonymous code block. Comparing that to a language like JavaScript, you can think of this DO as the opening of a curly bracket.

[0:58] The $$ is defining the procedural language code that's going to be used. This is required with the DO block. We're not going to go too much into this. You almost always use a $$ sign, it always what's recommended.

[1:11] You notice we also end with the $$ at the bottom here, which represents the end of this DO block. Next up is DECLARE, which is declaring the declaration section within our DO block. Basically, it's how we say we're going to declare variables. Without this DECLARE, Postgres would not know what we're trying to do here on this next line.

[1:32] Here is where we actually define a variable. The first is the variable name, in our case handle, next we define its type, which is contrary to Typescript, Java, C#, where the type is defined first. After that, we do :=, which is how we assign a value to a variable, closing this statement with a ;.

[1:52] In our case, this is the UUID, and this is the value UUID type. Now, we can reference this variable anywhere within our DO block. Next stop we say BEGIN. BEGIN initiates a transaction block, which means that all statement after a BEGIN command will be executed in a similar transaction.

[2:11] We'll close this transaction with END here. The END command in Postgres is an extension that is equivalent to command. Finally, inside of our insert into members, you can see we're referring that handle variable here.

[2:24] To recap, we successfully created an anonymous code block with DO. We have this declaration section, all within a transaction block, we're making an insert that's referring our variable.

[2:36] Let's look at another example. I'm currently a member within the member's table, and as you can see here, this is my info that I have for myself. I've already put together a really similar situation of a DO DECLARE block that we saw with Stacy.

[2:50] However, I'm doing one additional variable. My goal here is to insert Melisa into our member's table. I want her to have exact same start date as me. Again, the DO DECLARE, $$, BEGIN, and END are all the same set up as before.

[3:04] However, notice I don't give start date here a value. Just a name and title. Then I do a select into our stated variable here. This is new. This will assign a value to our variable that we declared in the DECLARE block. Notice we use my userHandle variable to find the right start date, then we insert Melisa while using my start date value here.

[3:29] Let's look at our members table for Melisa and see if it matches mine. Look it up and, yeah. Her start date is April 1st, and that's the same as mine. Let's look at another example of what we can do with our variables, specifically the start date variable that received it's value from the selected DO statement.

[3:49] We can add an IF check that states only add Melisa at my start date is not null. Notice we have an if and then a condition. Next is a then, and then we do our inserts, and we close this IF block with this END F. Something else that's pretty cool is we can use built in functions when creating our variables.

[4:09] As you can see, a createDate variable is going to be whatever is smaller between this variable and this hard-coded date. Then we use this variable and reference it instead of insert statement. Finally, I want to point out some additional functionality you can do with variable and blocks

[4:28] It's a complex topic, so I'm going to reference the Postgres documentation. As you can see, variable can be reassigned from within the BEGIN block, and as it states in the paragraph, and this statement section of a block can also be sub block.

[4:43] Within a BEGIN, we can declare again, create additional variables, and then open and close another transaction with BEGIN and END, which creates this sub block.
