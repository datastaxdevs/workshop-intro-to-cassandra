## 🎓🔥 Intro to Cassandra for Developers using DataStax Astra DB 🔥🎓

Welcome to the 'Intro to Cassandra for Developers' workshop! In this two-hour workshop, the Developer Advocate team of DataStax shows the most important fundamentals and basics of the powerful distributed NoSQL database Apache Cassandra. Using Astra DB, the cloud based Cassandra-as-a-Service platform delivered by DataStax, we will cover the very first steps for every developer who wants to try to learn a new database: creating tables and CRUD operations. 

It doesn't matter if you join our workshop live or you prefer to do at your own pace, we have you covered. In this repository, you'll find everything you need for this workshop:

- Materials used during presentations
- Hands-on exercises
- [Workshop video](https://www.youtube.com/watch?v=VaLwHqAHNtE)
- [Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)

## Homework

To complete the workshop and get verified badge, follow these simple steps:

1. Watch the workshop live or recorded.
2. Complete the workshop practice as described below and make the screenshot of the last step (result of the `DELETE` in "Execute CRUD", see [here](#homework-note)).
3. Complete the following short courses: [Cassandra Data Modeling](https://www.datastax.com/node/3272) and [Cassandra Query Language](https://www.datastax.com/dev/scenario/try-it-out-cassandra-query-language-cql). Take screenshots of the "Congratulations" page from each course.
4. [Submit the Homework](https://github.com/datastaxdevs/workshop-intro-to-cassandra/issues/new?assignees=HadesArchitect&labels=homework%2C+wait+for+review&template=homework.md&title=%5BHW%5D+%3CNAME%3E) attaching the screenshots.

## Table of Contents

| Title  | Description
|---|---|
| **Slide deck** | [Slide deck for the workshop](slides/Presentation.pdf) |
| **1. Create your Astra Db instance** | [Create your Astra Db instance](#1-create-your-astra-db-instance) |
| **2. Create a table** | [Create a table](#2-create-a-table) |
| **3. Execute CRUD (Create, Read, Update, Delete) operations** | [Execute CRUD operations](#3-execute-crud-operations) |


## 1. Create your Astra DB instance

_**`ASTRA DB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, $25.00 USD credit every month, roughly 5M writes, 30M reads, 40GB storage monthly - sufficient to run small production workloads._

✅ Register (if needed) and Sign In to Astra DB [https://astra.datastax.com](https://astra.dev/12-15): You can use your `Github`, `Google` accounts or register with an `email`.

_Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character_

✅ Choose "Start Free Now"

Choose the "Start Free Now" plan, then "Get Started" to work in the free tier.

You will have plenty of free initial credit (renewed each month!), roughly corresponding
to 80 GB of storage and 20M read/write operations.

> If this is not enough for you, congratulations! You are most likely running a mid- to large-sized business! In that case you should switch to a paid plan.

(You can follow this [guide](https://docs.datastax.com/en/astra/docs/creating-your-astra-database.html) to set up your free-tier database with the $25 monthly credit.)

![astra-db-signup](images/tutorials/astra_signup.gif)

To create the database:

- **For the database name** - `workshops`. While Astra DB allows you to fill in these fields with values of your own choosing, please follow our recommendations to ensure the application runs properly.

- **For the keyspace name** - `chatsandra`. Please stick to this name, it will make the following steps much easier (you have to customize here and there otherwise). In short:

| Parameter | Value 
|---|---|
| Database name | workshops  |
| Keyspace name | chatsandra |

- **For provider and region**: Choose and provider (either GCP, AWS or Azure). Region is where your database will reside physically (choose one close to you or your users).

- **Create the database**. Review all the fields to make sure they are as shown, and click the `Create Database` button.

You will see your new database `pending` in the Dashboard.

![db-pending-state](https://github.com/datastaxdevs/shared-assets/blob/master/astra/dashboard-pending-1000-update.png?raw=true)

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

**👁️ Walkthrough**

![db-creation-walkthrough](images/tutorials/astra-create-db.gif?raw=true)

## 2. Create a table
Ok, now that you have a database created the next step is to create tables to work with. 

> _General Methodology Note_: We'll work with a (rather simplified) "chat application" called **ChatSandra**: users, identified by a unique ID, write posts in several "rooms".
> Rooms are also uniquely identified by their name, such as `#gardening`. The design of our application is such
> that we need to be able to (a) retrieve all posts by a given user, sorted by descending date,
> and (b) retrieve all posts for a given room, sorted by descending date.
> As dictated by the best practices of data modeling with Cassandra, this requirement is satisfied by creating _two_ very similar tables,
> as you'll see momentarily: they will contain the same posts, but stored (a.k.a. partitioned) in two different ways;
> and it will be our (or the application's) responsibility to maintain them aligned.

**✅ Step 2a. Navigate to the CQL Console and login to the database**

In the Summary screen for your database, select **_CQL Console_** from the top menu in the main window. This will take you to the CQL Console and automatically log you in.

<details>
    <summary>Show me! </summary>
    <img src="images/astra-cql-console.gif" />
</details>

> _Note_: if you are working with your own Cassandra cluster (other than Astra DB), you will reach the CQL Console differently.
> Moreover, in that case you have to manually create the keyspace once in the CQL Console: this is done with a command similar to
> `CREATE KEYSPACE chatsandra WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'replication_factor': 3};`.
> See the Cassandra documentation for more details on this.

**✅ Step 2b. Describe keyspaces and USE one of them**

Ok, now we're ready to rock. Creating tables is quite easy, but before we create one we need to tell the database which keyspace we are working with.

First, let's **_DESCRIBE_** all of the keyspaces that are in the database. This will give us a list of the available keyspaces.

📘 **Command to execute**
```
DESC KEYSPACES;
```
_"desc" is short for "describe", either is valid._

> CQL commands usually end with a semicolon `;`. If you hit Enter, nothing happens and you don't even get your prompt back, most likely it's because you have not closed the command with `;`. If in trouble, you can always get back to the prompt with `Ctrl-C` and start typing the command anew.

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 13 54 55" src="https://user-images.githubusercontent.com/20337262/94687725-8cbf8600-0324-11eb-83b0-fbd3d7fbdadc.png">

Depending on your setup you might see a different set of keyspaces than in the image. The one we care about for now is **_chatsandra_**. From here, execute the **_USE_** command with the **_chatsandra_** keyspace to tell the database our context is within **_chatsandra_**.

> Take advantage of the TAB-completion in the CQL Console. Try typing `use kill` and then pressing TAB, for example.

📘 **Command to execute**
```
USE chatsandra;
```

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 13 55 56" src="https://user-images.githubusercontent.com/20337262/94687832-b082cc00-0324-11eb-885a-d44e127cf9be.png">

Notice how the prompt displays ```<username>@cqlsh:chatsandra>``` informing us we are **using** the **_chatsandra_** keyspace. Now we are ready to create our table.

**✅ Step 2c. Create the posts_by_user table**

At this point we can execute a command to create the **posts_by_user** table.
Just copy/paste the following command into your CQL console at the prompt.
Try to identify the primary key and the partition key for this table in the
command:

📘 **Command to execute**

```
CREATE TABLE IF NOT EXISTS posts_by_user ( 
  user_id     UUID, 
  post_id     TIMEUUID,
  room_id     TEXT, 
  text        TEXT,
  PRIMARY KEY ((user_id), post_id)
) WITH CLUSTERING ORDER BY (post_id DESC);
```

Then **_DESCRIBE_** your keyspace tables to ensure it is there.

📘 **Command to execute**

```
DESC TABLES;
```

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 13 57 32" src="https://user-images.githubusercontent.com/20337262/94687995-e88a0f00-0324-11eb-8c7a-08c3dee00eaf.png">

Aaaand **BOOM**, you created a table in your database. That's it. You may wonder, how did we arrive at this particular structure for the table? The answer lies in the methodology for data modeling
with Cassandra, which, at its very core, states: _first look at the application's needs, determine the required workflows, then map them to a number of queries, finally design a table around each query_.

[🏠 Back to Table of Contents](#table-of-contents)

## 3. Execute CRUD operations
CRUD operations stand for **create, read, update, and delete**. Simply put, they are the basic types of commands you need to work with ANY database in order to maintain data for your applications.

**✅ Step 3a. Create another table**

We started by creating the **_posts_by_user_** table earlier, which allows to get all posts from a given user.
But now we need to create another table to support a different query: "get all posts for a given _room_".
As you have seen in the presentation, data modeling with Cassandra generally requires different tables for different queries.

Let's go ahead then and create the table **_posts_by_room_**. Execute the following statements to create our tables.

📘 **Commands to execute**

```
CREATE TABLE IF NOT EXISTS posts_by_room ( 
  user_id     UUID, 
  post_id     TIMEUUID,
  room_id     TEXT, 
  text        TEXT,
  PRIMARY KEY ((room_id), post_id)
) WITH CLUSTERING ORDER BY (post_id DESC);
```

Then **_DESCRIBE_** your keyspace tables, to ensure both tables are there.

📘 **Command to execute**

```
DESC TABLES;
```

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 13 59 50" src="https://user-images.githubusercontent.com/20337262/94688257-3bfc5d00-0325-11eb-9ec6-40d2596fb71e.png">

**✅ Step 3b. (C)RUD = create = insert data**

Our tables are in place so let's put some data in them. This is done with the **INSERT** statement. We'll start by inserting data into the **_posts_by_user_** table.
_(Once you have carefully examined the first of the following **INSERT** statements below, you can simply copy/paste the others which are very similar.)_

> _Note_: in the following, we are using "timeuuids" crafted by hand to make things easier to visualize. In a real application, you would generate them at application
> level or, in some cases, using the `NOW()` primitive offered by CQL. In the values below, you can just look at the first octet of hex digits.

> _Note_ that we have three users in this example: "111...", "555..." and "999...", which are having a pleasant conversation. In a real application, you would probably
> generate user IDs at the application level or with the `UUID()` primitive offered by CQL.
> See the [documentation](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_reference/timeuuid_functions_r.html) for more details on time/uuid-related CQL functions.

📘 **Commands to execute**

```
// Insert some data in the "posts_by_user" table

INSERT INTO posts_by_user (
  user_id,  // UUID: unique id for a user
  post_id,  // TIMEUUID: unique uuid + timestamp
  room_id,  // TEXT: id of a chat room
  text      // TEXT: the post content itself
)
VALUES (
  11111111-1111-1111-1111-111111111111,
  22222222-5cff-11ec-be16-1fedb0dfd057,
  '#hiking',
  'I climbed Mt. Gumbo yesterday ...'
);

INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  77777777-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'Who likes marathons here?'
);
INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  aaaaaaaa-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', '... and Mt. Gumbo was easy!!!'
);
INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  55555555-5555-5555-5555-555555555555,
  bbbbbbbb-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', 'For us humans Gumbo is a tough one...!'
);
INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  99999999-9999-9999-9999-999999999999,
  cccccccc-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'I just love marathons.'
);
INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  eeeeeeee-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'Same here!'
);
INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
  55555555-5555-5555-5555-555555555555,
  ffffffff-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', 'I have to buy new boots.'
);
```

Ok, we have a lovely bunch of posts in our chat application.
But **wait**: data is denormalized and the very same posts have to be inserted
in table **_posts_by_room_** as well! Let's do it with the following command
(please note that the `INSERT` statements are exactly the same as above,
with only the table name changed):

📘 **Commands to execute**

```
// Insert some data in the "posts_by_room" table

INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  22222222-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', 'I climbed Mt. Gumbo yesterday ...'
);

INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  77777777-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'Who likes marathons here?'
);
INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  aaaaaaaa-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', '... and Mt. Gumbo was easy!!!'
);
INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  55555555-5555-5555-5555-555555555555,
  bbbbbbbb-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', 'For us humans Gumbo is a tough one...!'
);
INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  99999999-9999-9999-9999-999999999999,
  cccccccc-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'I just love marathons.'
);
INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  11111111-1111-1111-1111-111111111111,
  eeeeeeee-5cff-11ec-be16-1fedb0dfd057,
  '#running', 'Same here!'
);
INSERT INTO posts_by_room (user_id, post_id, room_id, text) VALUES (
  55555555-5555-5555-5555-555555555555,
  ffffffff-5cff-11ec-be16-1fedb0dfd057,
  '#hiking', 'I have to buy new boots.'
);
```

**✅ Step 3c. C(R)UD = read = read data**

Now that we've inserted a set of data, let's take a look at how to read that data back out. This is done with a **SELECT** statement. In its simplest form we could just execute a statement like the following **_**cough_** **_**cough_**:
```
// Read all rows from "posts_by_user" table (careful with this ...)
SELECT * FROM posts_by_user;
```

You may have noticed my coughing fit a moment ago. Even though you can execute a **SELECT** statement with no partition key definied this is NOT something you should do when using Apache Cassandra. We are doing it here for illustration purposes only and because our dataset only has a handful of values. Given the data we inserted earlier a more proper statement would be something like (while we are at it, we also explicitly specify which columns we want back):
```
// Read (some columns of) rows in a certain partition of "posts_by_user" table
SELECT post_id, room_id, text FROM posts_by_user
  WHERE user_id = 11111111-1111-1111-1111-111111111111;
```

The key is to ensure we are **always selecting by some partition key** at a minimum, so to avoid the dreaded _full-cluster scans_ which yield performances that are generally unacceptable in production.

Ok, with that out of the way we can **READ** the data from the other table as well - remember we **INSERT**ed on both tables?

📘 **Commands to execute**

```
// Read the whole "posts_by_room" table
// (warning: not suitable for large tables in production)
SELECT * FROM posts_by_room;

// Read (some columns of) posts from a certain room (= a certain partition)
SELECT user_id, text FROM posts_by_room WHERE room_id = '#hiking';
```

(again, in the second **SELECT** we specify some columns - it is something we may want to do in most cases).

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 14 03 18" src="https://user-images.githubusercontent.com/20337262/94688606-bb8a2c00-0325-11eb-8124-5c4d9ac0d4fc.png">

Once you execute the above **SELECT** statements you should see something like the expected output above. We have now **READ** the data we **INSERTED** earlier. Awesome job!

_BTW, just a little extra for those who are interested. Since we used a [TIMEUUID](https://docs.datastax.com/en/cql-oss/3.3/cql/cql_reference/timeuuid_functions_r.html) type for our **post_id** field we can use the **dateOf()** function to determine the timestamp from the value. Check it out._

```
// Read all data from the posts_by_room table, 
// convert post_id into a timestamp, and label the column "post_date"
SELECT user_id, dateOf(post_id) AS post_date, text FROM posts_by_room
  WHERE room_id = '#hiking';
```

**✅ Step 3d. CR(U)D = update = update data**

At this point we've **_CREATED_** and **_READ_** some data, but what happens when you want to change some existing data to some new value? That's where **UPDATE** comes into play.
_The use case is as follows: in our chat app, users are allowed to edit their previous posts._

Let's take one of the records we created earlier and modify it. Recall that we **_INSERTED_** the following record in the **_posts_by_user_** table.
```
      // ** Just for reference: **
      //  INSERT INTO posts_by_user (user_id, post_id, room_id, text) VALUES (
      //    11111111-1111-1111-1111-111111111111,
      //    aaaaaaaa-5cff-11ec-be16-1fedb0dfd057,
      //    '#hiking', '... and Mt. Gumbo was easy!!!'
      //  );
```

Let's also take a look at how the **_posts_by_user_** table was created. In order to **UPDATE** an existing record, indeed, we need to know the primary key we defined when we **CREATE**d the table.
```
      // ** Just for reference: **
      // CREATE TABLE IF NOT EXISTS posts_by_user ( 
      //   user_id     UUID, 
      //   post_id     TIMEUUID,
      //   room_id     TEXT, 
      //   text        TEXT,
      //   PRIMARY KEY ((user_id), post_id)
      // ) WITH CLUSTERING ORDER BY (post_id DESC);
```

> Let's say that user "111..." has noticed the remark by "555..." and, perhaps a bit ashamed by their own boasting, wants to correct their assessment on the hike difficulty!

Looking at ```PRIMARY KEY ((user_id), post_id)```, we know that both **user_id** and **post_id** are used to define uniqueness of the row.
We'll need both to update our record (plus, of course, some of the data columns, otherwise we are not changing anything in that row!).

_You may remember that we used hardcoded values for **post_id** when we created these records (a real application would generate them live, one way or the other).
Imagine the UX for editing an existing post: when the user clicks the "edit" button, both **user_id** and **post_id** are known and can be provided to
the backend, where they ultimately become part of an **UPDATE** statement._

So we can run the following **UPDATE** statement and help user "111..." fix their post on table **_posts_by_user_**
(we also subsequently read back the data as a check):

📘 **Commands to execute**

```
UPDATE posts_by_user 
  SET text = '... and Mt. Gumbo was NOT SO easy!!!' 
    WHERE user_id = 11111111-1111-1111-1111-111111111111
    AND   post_id = aaaaaaaa-5cff-11ec-be16-1fedb0dfd057;

SELECT post_id, room_id, text FROM posts_by_user
  WHERE user_id = 11111111-1111-1111-1111-111111111111;
```

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 14 05 21" src="https://user-images.githubusercontent.com/20337262/94688803-0015c780-0326-11eb-96e3-b76fb59a9d11.png">

But **wait**: data, again, is denormalized! This means that we have to make sure
such an edit is performed on table **_posts_by_room_** as well.
Since the primary key of that table is given as `PRIMARY KEY ((room_id), post_id)`,
these are the fields to provide, along with `text` itself, to the **UPDATE** statement.

And we _could_ run an **UPDATE**. But, lo and behold, in Cassandra **UPDATE**s
and **INSERT**s are (almost) the same, as a consequence of its architecture and
the way storage and write logic are structured. We can then update the row with
an **INSERT** statement like the following (note we provide primary key,
any field that we want to modify, and leave out the other, unchanged fields):

📘 **Commands to execute**
```
INSERT INTO posts_by_room (room_id, post_id, text) VALUES (
  '#hiking',
  aaaaaaaa-5cff-11ec-be16-1fedb0dfd057,
  '... and Mt. Gumbo was NOT SO easy!!!'
);

SELECT post_id, user_id, text FROM posts_by_room WHERE room_id = '#hiking';
```

That's it, we successfully edited a post (on both tables).
All that's left now is to **DELETE** some data.

**✅ Step 3e. CRU(D) = delete = remove data**

The final operation from our **CRUD** acronym is **DELETE**. This is the operation we use when we want to remove data from the database.
In Apache Cassandra you can **DELETE** from the cell level all the way up to the partition
_(meaning I could remove a single column in a single row or I could remove a whole partition)_ using the same **DELETE** command.

_Generally speaking, it's best to perform as few delete operations as possible on the largest amount of data. Think of it this way, if you want to delete ALL data in a table, don't delete each individual cell, just **TRUNCATE** the table. If you need to delete all the rows in a partition, don't delete each row, **DELETE** the partition and so on._

> User "555..." notices the post by "111..." being edited and wants to remove their snarky remark. Let's help them!

When deleting a row on a given table, we have to specify the values of the primary key for that table. And don't forget
that a post appears as two separate rows in the two tables, so we have to perform
two different **DELETE** operations!

📘 **Commands to execute**

```
SELECT post_id, room_id, text FROM posts_by_user
  WHERE user_id = 55555555-5555-5555-5555-555555555555;

SELECT post_id, user_id, text FROM posts_by_room WHERE room_id = '#hiking';

DELETE FROM posts_by_user
  WHERE user_id = 55555555-5555-5555-5555-555555555555
  AND post_id = bbbbbbbb-5cff-11ec-be16-1fedb0dfd057;

DELETE FROM posts_by_room
  WHERE room_id = '#hiking'
  AND post_id = bbbbbbbb-5cff-11ec-be16-1fedb0dfd057;

SELECT post_id, room_id, text FROM posts_by_user
  WHERE user_id = 55555555-5555-5555-5555-555555555555;

SELECT post_id, user_id, text FROM posts_by_room WHERE room_id = '#hiking';
```

(Notice in the above, for your convenience, we read the tables, then delete the rows, then read them again).

📗 **Expected output**

<img width="1000" alt="Screenshot 2020-09-30 at 14 07 05" src="https://user-images.githubusercontent.com/20337262/94689019-3eab8200-0326-11eb-86b9-010c130a49c3.png">

Notice the rows are now removed from both tables: it is as simple as that.

### Homework note

To submit the **homework**, please take a screenshot of the CQL Console showing the rows in tables
`posts_by_user` and `posts_by_room` before _and_ after executing the DELETE statements.

## 4. Wrapping up
We've just scratched the surface of what you can do using Astra DB, built on Apache Cassandra.
Go take a look at [DataStax for Developers](https://www.datastax.com/dev) to see what else is possible.
There's plenty to dig into!

# Done?

Congratulations: you made to the end of today's workshop.

Don't forget to submit your homework and be awarded a nice verified badge!

![Badge](images/badge/intro-to-cassandra.png)
