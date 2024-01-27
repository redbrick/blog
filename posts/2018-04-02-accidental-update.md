# Accidental updates - Redbrick Blog
The Preamble
------------

Redbrick runs a service called [HackMD](https://md.redbrick.dcu.ie/). It is web-based markdown editor. At the start of the year HackMD version one came out and in early February we decided to update it.

Unlike most updates in Redbrick, this isn’t really a big thing as it runs inside Docker container, with its only external dependency being PostgreSQL 9. We don’t actually have a central a central PostgreSQL database in Redbrick, a problem for another day, just a MySQL. So inside another container, we run PostgreSQL.

The update process was meant to be simple update the `Dockerfile`, run `docker-compose up --build` and wait…

What Actually Went Wrong
------------------------

And we waited but HackMD didn’t come back. One tail of the logs later and the problem was obvious PostgreSQL had restarted. But not only that it was trying and failing to upgrade itself to PostgreSQL 10.

The Problem was Docker had pulled the latest version of PostgreSQL. This was fine when HackMD was first installed the year earlier when all the docker tags pointed to PostgreSQL 9 but weren’t so great after.

The Fix
-------

The fix was pretty simple to add a version tag to for the database image to the `docker-compose.yml`. A quick `vim` and `docker-compose up` later and HackMD was back with lots of new bells and whistles.

So we went through and changed all the other compose files to have tagged versions of dependencies.

Problem Solved. Clean our hands and move on. Well unfortunately not.

The Solution
------------

Over the next couple of months, a couple problems were mentioned with HackMD but no one really looked into them. That was until it affected an admin. We couldn’t publish our roadmap for in coming admins.

So back to the logs and yep database issue. Seems that PostgreSQL cant find some of the keys. An initial thought was that we missed a database migration way back when we upgraded. So we `exec`ed into the container ran the migrations script and …nothing, there wasn’t any.

Back to the drawing board. We start reviewing configs and double checking against the repo. But everything seems right. Next, we decide to go to the heart of the problem the database itself. One docker run and we have a PostgreSQL shell. Start digging through tables, trying to find the missing key when we get a duplicate key error and an alias.

BINGO

The odd thing was when you looked up the alias there was only one entry for it. We couldn’t delete the duplicate entry as it didn’t exist and couldn’t modify the table entries as we got duplicate key errors.

Bit of googling later and we had a solution. Copy the table, delete it and restore. Is this the Database version of turn it off and on again?

```
hackmd=#  SELECT DISTINCT * INTO notes from "Notes";
SELECT 268
hackmd=# DROP TABLE "Notes";
DROP TABLE
hackmd=#  ALTER TABLE notes rename to "Notes";
ALTER TABLE
hackmd=# REINDEX DATABASE hackmd;
REINDEX
hackmd=# VACUUM(FULL, ANALYZE, VERBOSE);

```


What it turns out is the table’s index was wrong. While PostgreSQL’s attempt to update itself to 10 had failed it had modified the indexes for some of the tables and reverting the container didn’t magically fix the database inside.

So the TL;DR.

*   Always lock your container version
*   containers don’t magically fix things
*   And validate your database after modifying it