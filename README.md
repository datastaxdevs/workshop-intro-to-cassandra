🎓🔥 Welcome to the Intro to Cassandra for Developers using DataStax Astra 🔥🎓
======================================================

In this repository, you'll find everything you need for this workshop:
- Materials used during presentations
- Hands-on exercises

## Table of Contents

| Title  | Description
|---|---|
| **Slide deck** | [Slide deck for the workshop](slides/Presentation.pdf) |
| **1. Create your Astra instance** | [Create your Astra instance](#1-create-your-astra-instance) |
| **2. 2. Create tables and run simple CRUD ops** | [Create tables and run simple CRUD ops](#2-create-tables-and-run-simple-crud-ops) |


## 1. Create your Astra instance

`ASTRA` service is available at url [https://astra.datastax.com](https://astra.datastax.com/)

**✅ Step 1a.Register (if needed) and Sign In to Astra** : You can use your `Github`, `Google` accounts or register with an `email`

- [Registration Page](https://astra.datastax.com/register)

![Registration Image](images/astra-create-register.png?raw=true)

- [Authentication Page](https://astra.datastax.com/)

![Login Image](images/astra-create-login.png?raw=true)


### Step 1b.Fill the Create New Database Form

As you don't have have any instances the login will route through the instance creation form. You will find below which values to enter for each field.

![Database Form](images/astra-create-2.png?raw=true)


- **Set the Compute Size**: For the work we are doing please use `Free tier`. You instance will be there forever, free of charge

- **Select the region**: This is the region where your database will reside physically (choose one close to you or your users). For people in EMEA please use `europe-west-1` idea here is to reduce latency.

- **Fill in the database name** - `killrvideocluster.` While Astra allows you to fill in these fields with values of your own choosing, please follow our reccomendations to make the rest of the exercises easier to follow. If you don't, you are on your own! :)

- **Fill in the keyspace name** - `killrvideo`. It's really important that you use the name killrvideo (with no 'e' in "killr") here in order for all the exercises to work well. We realize you want to be creative, but please just roll with this one today.

- **Fill in the Database User name** - `KVUser`. Note the user name is case-sensitive. Please use the case we suggest here.

- **Fill in the password** - `KVPassword`. Fill in both the password and the confirmation fields. Note that the password is also case-sensitive. Please use the case we suggest here.

- **Launch the database**. Review all the fields to make sure they are as shown, and click the Launch Database button.


![Launch Database](images/astra-create-3.png?raw=true)


### Step 1c.View your Database and connect

View your database. It may take 2-3 minutes for your database to spin up. You will receive an email at that point. But, go ahead and continue with the rest of the exercise now.

![View Database](images/astra-create-4.png?raw=true)


Once Database is ready you should see the following home page:

![Home Page](images/astra-create-5.png?raw=true)


Let’s review the database you have configured. In the box on the left side of the window, you can see the database and keyspace name metadata. The box on the right describes the size and location of your database along with your estimated cost. Once Astra initializes the database completely, the left box will have connection details. Once you are at this point you have a database ready to go. We'll start using it in the next section.


[🏠 Back to Table of Contents](#table-of-contents)


## 2. Create tables and run simple CRUD ops

```
// User credentials, keyed by email address so we can authenticate
CREATE TABLE IF NOT EXISTS user_credentials (
    email     text,
    password  text,
    userid    uuid,
    PRIMARY KEY (email));

// Users keyed by id
CREATE TABLE IF NOT EXISTS users (
    userid     uuid,
    firstname  text,
    lastname   text,
    email      text,
    created_date timestamp,
    PRIMARY KEY (userid));

```


example insert statements
```
INSERT INTO killrvideo.users (userid, created_date, firstname, lastname, email)
  VALUES(77777777-7777-7777-7777-777777777777, toTimestamp(now()), 'Aleks', 'volochnev', 'av@datastax.com');
INSERT INTO killrvideo.user_credentials (userid, email, password)
  VALUES(77777777-7777-7777-7777-777777777777, 'av@datastax.com', 'C@ss@ndr@3v3rywh3r3');

INSERT INTO killrvideo.users (userid, created_date, firstname, lastname, email)
  VALUES(44444444-4444-4444-4444-444444444444, toTimestamp(now()), 'David', 'Gilardi', 'dg@datastax.com');
INSERT INTO killrvideo.user_credentials (userid, email, password)
  VALUES(44444444-4444-4444-4444-444444444444, 'dg@datastax.com', 'H@t$0ff2C@ss@ndr@');

INSERT INTO killrvideo.users (userid, created_date, firstname, lastname, email)
  VALUES(22222222-2222-2222-2222-222222222222, toTimestamp(now()), 'Eric', 'Zietlow', 'ez@datastax.com');
INSERT INTO killrvideo.user_credentials (userid, email, password)
  VALUES(22222222-2222-2222-2222-222222222222, 'ez@datastax.com', 'C@ss@ndr@R0ck$');

INSERT INTO killrvideo.users (userid, created_date, firstname, lastname, email)
  VALUES(33333333-3333-3333-3333-333333333333, toTimestamp(now()), 'Cedrick', 'Lunven', 'cl@datastax.com');
INSERT INTO killrvideo.user_credentials (userid, email, password)
  VALUES(33333333-3333-3333-3333-333333333333, 'cl@datastax.com', 'Fr@nc3L0v3$C@ss@ndr@');
```
