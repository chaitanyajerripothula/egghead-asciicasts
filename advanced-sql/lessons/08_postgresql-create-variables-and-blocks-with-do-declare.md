# Create Variables and Blocks with Do / Declare

Instructor: [0:00] We have a member's table that has a `start_date`, `end_date`, `user_handle`, `first_name`, and `email`. `start_date` and `end_date` are `date` types, `user_handle` is a `uuid`, and `first_name` and `email` are `text`. 

```postgres
postgres=# \d members
                 Table "public.members"
    Column    | Type | Collation | Nullable | Default
  ------------+------+-----------+----------+--------
  start_date  | date |           |          | 
  end_date    | date |           |          |
  user_handle | uuid |           |          | 
  first_name  | text |           |          | 
  email       | text |           |          |
```

To get started we're going to do a `do $$`, `declare`.

[0:14] We're going to say `handle uuid := '29587e31-b631-4b2a-a31f-9771a43db98a';`. We're going to say `begin` and then enter it into `members values` with `now()`, `null`, `handle`, `'Stacy'`, and for the email it'll be `stacy@gmail.com`. We're going to say `end $$`. Don't forget our `;`.

```postgres
postgres=# do $$
postgres$# declare 
postgres$# handle uuid := '29587e31-b631-4b2a-a31f-9771a43db98a';
postgres$# begin
postgres$# insert into members values (now(), null ,handle, 'Stacy', 'stacy@gmail.com');
postgres$# end $$
postgres-#;
DO
```

[0:32] Then let's go and look up in the member's table to see if we added Stacy correctly. We'll pull her out. Her first name is Stacy and you can see that we did insert into our member's table. 

```postgres
postgres=# select * from members where first_name = 'Stacy';
 start_ date | end_ date |            user_ handle              | first_name |      email
-----------------------------------------------------------------------------------------------
2020-04-01   |           | 29587e31-b631-4b2a-a31f-9771a43db98a | Stacy      | stacy@gmail.com
```

Now, let's slow down and break down what's happening here.

[0:45] Starting at the top, we see we have `do` and then this `$$.` In Postgres, do is highly defined in anonymous code block. Comparing that to a language like JavaScript, you can think of this DO as the opening of a curly bracket.

[0:58] The `$$` is defining the procedural language code that's going to be used. This is required with the do block. We're not going to go too much into this. You almost always use a $$ sign, it always what's recommended.

[1:11] You notice we also end with the $$ at the bottom here, which represents the end of this do block. Next up is `declare`, which is declaring the declaration section within our do block. Basically, it's how we say we're going to declare variables. Without this declare, Postgres would not know what we're trying to do here on this next line.

[1:32] Next is where we actually define a variable. The first is the variable name, in our case `handle`, next we define its type, which is contrary to Typescript, Java, C#, where the type is defined first. After that, we do `:=`, which is how we assign a value to a variable, closing this statement with a `;`.

[1:52] In our case, this is the `uuid`, and this is the value uuid type, our generated `user_handle`. Now, we can reference this variable anywhere within our DO block. Next stop we say `begin`. Begin initiates a transaction block, which means that all statements after a begin command will be executed in a similar transaction.

[2:11] We'll close this transaction with `end` here. The end command in Postgres is an extension that is equivalent to command. Finally, inside of our `insert into members`, you can see we're referring that `handle` variable.

[2:24] To recap, we successfully created an anonymous code block with `do`. We have this `declare` section, all within a transaction block, we're making an `insert` that's referring our variable.

[2:36] Let's look at another example. I'm currently a member within the member's table, and as you can see here, this is my info that I have for myself. 

```postgres
postgres=# select * from members where user_handle = '099ab71b-53ad-4fc6-823a-5f22aabc2d45';
 start_ date | end_ date |            user_ handle              | first_name |      email
-----------------------------------------------------------------------------------------------
 2020-04-01  |           | 099ab71b-53ad-4fc6-823a-5f22aabc2d45 | Tyler      | tyler@gmail.com
```

I've already put together a really similar situation of a `do/declare` block that we saw with Stacy.

```postgres
postgres=# do $$
postgres$# declare 
postgres$# handle uuid := '099ab71b-53ad-4fc6-823a-5f22aabc2d45';
postgres$# startDate date;
postgres$# 
postgres$# begin
postgres$# select start_date into startDate from members where user_handle = handle;
postgres$# insert into members values (startDate, null, uuid_generate_v4(), 'Melissa', mellisa@gmail.com');
postgres$# end
postgres$# $$;
```

[2:50] However, I'm doing one additional variable. My goal here is to insert `Melissa` into our member's table. I want her to have exact same start date as me. Again, the `do`, `declare`, `$$,` `begin`, and `end` are all the same set up as before.

[3:04] However, notice I don't give `startDate` a value. Just a name and title. Then I do a `select` into our stated variable here. This is new. This will assign a value to our variable that we declared in the `declare` block. Notice we use my `user_handle` variable to find the right start date, then we insert Melisa while using my `startDate` value at the start of our `insert`.

[3:29] Let's look at our members table for Melisa and see if it matches mine. 

```postgres
postgres=# select * from members where user_handle = '099ab71b-53ad-4fc6-823a-5f22aabc2d45';
 start_ date | end_ date |            user_ handle              | first_name |       email
-------------------------------------------------------------------------------------------------
 2020-04-01  |           | 82cb8717-3621-4373-8796-c3acc1699b86 | Melissa    | melissa@gmail.com
```

Look it up and, yeah. Her start date is April 1st, and that's the same as mine. Let's look at another example of what we can do with our variables, specifically the `startDate` variable that received its value from the selected `do` statement.

```postgres
postgres=# do $$
postgres$# declare 
postgres$# handle uuid := '099ab71b-53ad-4fc6-823a-5f22aabc2d45';
postgres$# startDate date;
postgres$# 
postgres$# begin
postgres$# select start_date into startDate from members where user_handle = handle;
postgres$# if (startDate is not null) then
postgres$# insert into members values (startDate, null, uuid_generate_v4(), 'Melissa', mellisa@gmail.com');
postgres$# end if;
postgres$# end $$;
DO
```

[3:49] We can add an `if` check that states only add Melisa at my start date is not null. Notice we have an if and then a condition. Next is a then, and then we do our inserts, and we close this if block with `end if`. Something else that's pretty cool is we can use built in functions when creating our variables.

[4:09] As you can see, a `createDate` variable is going to be whatever is smaller between this variable, `2019-01-12` and this hard-coded date, `someDate`. 

```postgres
postgres=# $$
postgres$# declare
postgres$#    handle uuid:= '3ebf30fd-f524-4ec6-b864-9cb2ae57f5ed';
postgres$#    someDate date := '2019-04-01';
postgres$#    createDAte date := least('2019-01-12', someDate);
postgres$# begin
postgres$#    insert into members values (createDate, null, handle, 'Jason', 'Anderson');
postgres$# end;
postgres$# $$
```

Then we use this variable, `createDate` and reference it instead of insert statement. Finally, I want to point out some additional functionality you can do with variable and blocks

[4:28] It's a complex topic, so I'm going to reference the Postgres documentation. As you can see, variable can be reassigned from within the begin block, and as it states in the paragraph, and this statement section of a block can also be sub block.

[4:43] Within a begin, we can declare again, create additional variables, and then open and close another transaction with begin and end, which creates this sub block.
