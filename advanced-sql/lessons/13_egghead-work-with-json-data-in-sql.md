# Work with JSON Data in SQL

[Video link](https://www.egghead.io/lessons/egghead-work-with-json-data-in-sql)

Instructor: [0:00] Let's start by creating a new table called Items. It's going to have two columns, an id -- that's a UUID type -- as a primary key, then info column -- that's JSON -- and it's going to be not null.

[0:11] Now, let's insert some data into our table. We'll give it a random UUID for the id. Then we'll insert some JSON that will make up about this item.

[0:20] We'll say color is blue, the weight is 2, tags are going to be an object with Department of Electronics and section of computer. We'll close off our parentheses and add a semicolon. Perfect. That's in our table now.

[0:36] If you're new to JSON, JSON is an open-centered format that consists of key value pairs. You'll see it most commonly used to transfer data between the Web and a server. As you can see, Postgres accepts JSON as a data type.

[0:48] In order to insert JSON into our table, we have to use parentheses, single quotes, then open up with curly brackets. Next, we add whatever data we want and then make sure we close our curlies and parentheses at the end. To select out JSON from a table, you very simply just say SELECT info FROM items. You'll see here, what we have is our JSON that we inserted.

[1:12] Postgres gives us two native operators to help us query JSON data. First stop is the -> which returns JSON object filled by key. Here we're saying return the value of the color key as color which in our only row of data is blue. You'll notice that our value's in quotes here. That's because it returns the value in JSON format.

[1:35] The second native Postgres operator is ->>. This returns the value from our key as text. In order to reach nested object values like we have with tags, we have to change this back to JSON format with the single > and then work with tags. Then we can use either operator to pull out the nested value key pair. Perfect.

[1:56] Now that we understand how to access our JSON data, we can use these operators with another clauses in our query. For example, we can filter out our rows depending on their JSON values.

[2:10] Another cool thing I'm working with JSON is we can use all of the aggregate functions built in the Postgres, as long as we cast it the right type the aggregate function needs. As we can see here, we're using the MEAN aggregate function. First, we're pulling out the weight value out in text format then casting it into an integer which the MEAN function can then work with.

[2:32] If you're wondering, we have used the text operator here because Postgres can't cast JSON to a different type. Again, you can use any other aggregate functions like MAX, SUM, and AVERAGE as long as you cast it first to the right type.

[2:49] Finally, Postgres gives us some functions to help process JSON data. Here we see json_each which allows us to expand the outermost JSON object into a set of key value pairs.

[3:02] There's another function we can use. If we wanted to get a set of keys in the outermost JSON object, we use json_object_keys. As we can see here, we get an expanded out view of the keys within out tags JSON object.
