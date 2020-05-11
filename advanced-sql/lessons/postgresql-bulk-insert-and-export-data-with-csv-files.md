# Bulk insert and export data with csv files

[Video link](https://www.egghead.io/lessons/postgresql-bulk-insert-and-export-data-with-csv-files)

Instructor: [0:00] Importing and exporting data from some type of file source like a CSV within SQL is super common. Luckily for us, Postgres has an answer for it. As we can see, our users table is currently empty.

```postgres
postgres=# select count(*) from users;
  count
---------
      0
(1 row)
```

[0:12] I have a CSV here that lives locally on my machine. I want to bulk add every row straight into my users database. Back in the terminal, I'm going to write copy users user_handle, first_name, last_name, email from a file path on my machine delimiter, it's going to be a coma, CSV, and header.

```postgres
postgres=# copy users (user_handle, first_name, last_name, email) from '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users.csv' DELIMITER ',' CSV HEADER;
COPY 500
```

[0:38] When this command runs correctly, we will bulk insert all the rows into our table. As you can see, we added 500 rows. The keyword that makes this possible is this copy command. We begin by saying copy, then the table we want to copy into so, in our case, the users table.

[0:55] Next, we could optionally define the columns we want to add data to from our CSV. Our CSV shows that the columns are in the same order as our users table. This command will work just fine without listing these out. Next stop, we say `from` and then the file location of the CSV on your machine.

[1:13] The ending of the `copy` command is how we define options. First off, the `copy` command works with a couple of different formats. The default is a tab character in text format. Ours is a CSV, so we want to deliminate the different column values by commas.

[1:29] These two are a pair. You can think of it like a key value pair. Unlike how these two work to defining an option, CSV and header are individual options. First of all, `CSV` defines the format option. Other format options are text or binary. As you can see, you don't separate these options with commas.

[1:51] Finally, `HEADER`, again, its on option and it's a Boolean that when present represents true, in absence is false. This is telling the copy command that the first row in our CSV is a header row, and not to copy those values over to the table.

[2:06] Also, the copy command is pretty cool, and the fact that it has to use cases. In our first example, we are copying data from a CSV to our database. By simply changing this `from` to a `to`, we could do the inverse. 

```postgres
postgres=# copy users (user_handle, first_name, last_name, email) to '/Users/tylerclark/Documents/projects/sql-advanced/sql-advanced-users-copy.csv' DELIMITER ',' CSV HEADER;
COPY 500
```

With all the same options, we can create a CSV export of our data. Now, I have a `sql-advanced-users-copy.csv` in this project with the same data as the original that was used to populate it.
