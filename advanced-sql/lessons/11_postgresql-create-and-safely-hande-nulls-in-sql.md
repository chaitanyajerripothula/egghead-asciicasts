# Create and Safely Hande Nulls in SQL

[Video link](https://www.egghead.io/lessons/postgresql-create-and-safely-hande-nulls-in-sql)

Instructor: [0:00] Let's create a table called posts. This table is going to hold some blog posts of ours to be viewed later. As you can see, we're going to have an id, a title, an excerpt, a body. Then, let's add some dates like created_at and updated_at. Let's enter some data into our posts table, and then, let's select and see what we've got to work with here.

[0:24] We have three blog posts. They all have titles. One has an excerpt with text that we can read. This one is not null, it's an empty text and you can see that on this line here where we're inserting it into with an empty string. This last one is null, as you can see here as well. They all have body and a created_at date.

[0:45] Our goal is to display a post overview page that's going to show the title and the excerpt of each post. In the case that the excerpt is not provided, we default to the first 40 characters of the post body.

[0:57] No one likes to deal with nulls. When pulling out this post, let's COALESCE the nulls into the first characters of the body. The COALESCE function accepts an unlimited number of arguments. It returns the first argument that's not null. If they're all null, then it will return null.

[1:14] The LEFT function returns the first in [inaudible] characters from a string. With that in mind, looking at our results, you can see that because test post three had a null excerpt, we return the first 40 characters of the body. However, empty string did not return those characters of the body.

[1:31] In order to solve this, we can force empty strings to be nulls by using the NULLIF function. Then our COALESCE will return the body characters now that we have a null here. As you can imagine, the NULLIF function returns a null value if argument one is equal to argument two. Otherwise, it will return argument one.

[1:55] Because our post two had an empty string, this matched here within a NULLIF. This is going to be turned a null which makes the COALESCE return the first 40 characters of the body for our test post two.
