---
title: "Mongoose and me"
description: "“BSON” — is a portmanteau(a blend) of the words “binary” and “JSON”. Think of BSON as a binary representation of JSON (JavaScript Object Notation) documents and that is how mongoDB stores everything…"
date: "2017-07-13T04:18:51.752Z"
categories: 
  - Mongodb
  - Mongoose
  - Schema
  - Database

published: true
canonical_link: https://medium.com/@jsj14/mongoose-and-me-e57df0c613a0
redirect_from:
  - /mongoose-and-me-e57df0c613a0
---

There came a time when I had to not just store things but start searching for it too.

“BSON” — is a portmanteau(a blend) of the words “binary” and “JSON”. Think of BSON as a binary representation of JSON (JavaScript Object Notation) documents and that is how mongoDB stores everything, making searching through thousands of records a piece of cake. 😛

You can search for deeeply nested objects (maybe even the depths of [Tartarus](http://riordan.wikia.com/wiki/Tartarus) !) 😆 for eg:

```
db.students.find({MidTerm.LastTest.Optional.Math.marks {$gte: 55}})
```

Obviously, those are made up fields… but you get the idea. Mongoose doesn’t mind how far down that ‘marks’ field is stored, if it satisfies the given condition, it will return an array of all details of such students. Pretty neat eh.

Some other cool things I like about mongoose would be:

1.  **Projections**: See what you want to. 🤓

```
db.students.find( { semester: 1, grades: { $gte: 85 } },
                  { "grades.$": 1 } )
```

This would not only reduce our output to semester and grades, but also ensures that all grades returned are greater than 85.

2\. **Operators**: 👷 You know what these are for. $and, $or, $not, $nor, (and $in, $eq, $ne, $gte, $lte), … just KIM(keep-in-mind 😜)everything inside an $and or $or must be an object. for eg:

```
db.inventory.find( { $and: [ { price: { $ne: 1.99 } }, 
                             { price: { $exists: true } }
                            ] 
} )
```

3\. For the above **$or / $and** you may feel like adding on to the array, but remember each one you add would increase the time spend in querying. So add only those that you feel are needed at the time of the query. (Think big! When you have a lot of records to go through, and each document has several fields to compare against…)

```
db.inventory.find( { $and: [ { price: { $ne: 1.99 } }, 
                             { price: { $exists: true } }
                            ]} )
```

This will take maybe 0.001 sec more than

```
db.inventory.find( { $and: [ { price: { $ne: 1.99 } }]} )
```

4\. **BulkWrite**: life-saver when you have to modify old data

**CAUTION: ☠️ Test this on dummy db/collections first. Do NOT use it directly.**

```
db.getCollection(‘Students’).bulkWrite([{updateMany: {filter: {StudentId: {$exists: true}}, update: {$set: {‘MidTerm.totalMarks’: ’Final term’.totalMarks’ }}, upsert: false}}])
```

Things to note ☝️

-   how **$set** is used to assign one field value with another field’s value. It can be used accordingly otherwise also.
-   how \`**upsert: false**\` is used to ensure no new docs are created in this process. Contrary can served with 'true’.
-   how **filter** is an option that can be extensively used to update only what you like, just fill in the conditions.

Well mongoose and mongodb and Robomongo have several tricks up their sleeve. These are just a few of my favourites 😘 (most frequently used). 👈

More on its way, coz, They say it right when they say, learning never ends! 😄 🤓

Edit:

If you have saved Schema as strict for mongoose AND used `type: Date` then please remember that **_if you try to save a string, Mongo will type cast it to ISODat_**e and what happens when ISODate is formed from different timezones is beyond the context of this story 😅 😉 Trust me, you don’t want to leave this onus on the shoulders on Mongod alone. Make sure you send an **_ISOString_** itself, if you have to send strings.

If you ever have to insert 100s of documents at one go via terminal, and keep failing to do so for all, note that their may be the `writeConcern` causing an issue.

The following piece can do the trick

```
mongo = new Mongo('myServeraddr');
mongo.setWriteConcern(WriteConcern.SAFE);
db = mongo.getDB("myDBname");

db.collectionName.insert([100s of docs]);
```

Remember the deeply nested objects — well it turns out theres a limit!

> MongoDB supports no more than 100 levels of nesting for [BSON documents](https://docs.mongodb.com/manual/reference/glossary/#term-document). and

The maximum BSON document size is 16 megabytes.

Check out the [Limits and restrictions](https://docs.mongodb.com/manual/reference/limits/) page on its docs.
