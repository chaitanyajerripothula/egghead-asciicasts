In our code editor, we're working on a file, `getURLSlug.js`, and we'd like to know who last modified line number one. We want to know why they added this export default. What we can do is let's go back to our command line, and let's run the `git blame` command with the name of our file, which was `getURLSlug.js`.

When we run the `git blame` command, it shows us the details of how each line was last modified inside of the file that we passed in. In this case, for the `getURLSlug.js` file, we have five lines in the file. For each line, we have the commit ID of when the line was last modified, as well as who last modified it, and when that change was made.

In this case, we know that the `export default` line change was made on July 2nd, and it was made by Trevor. To get more context around this change, I like to copy the commit ID, and then use other tools to find out more information.

![git blame command](../images/tools-practical-git-show-who-changed-a-line-last-with-git-blame-git-blame-command.png)

For example, let's use the `git log` command to see more information about the rest of the changes in this commit. Now let's paste in the ID of the commit that we copied. Let's also use the patch option so that we see the diff, `git log 461eg073 -p`.

Now we have the full context behind this commit. We see that the message was to upgrade to `ES2015 modules`. We can see that there were other changes from the word `function` to `export default`, and that's been done throughout this commit on multiple files.