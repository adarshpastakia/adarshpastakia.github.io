---
layout: post
title:  "MERN Stack Application - Part 2"
description: "Database Service - This post will describe the steps for creating a database service for our ToDo application."
date:   2017-09-25
tags: [nodejs,mongodb,es6]
publish: true
---

<p class="intro"><span class="dropcap">T</span>his post will describe the steps for creating a database service for our ToDo application.</p>

> MongoDB + Express + ReactJS + NodeJS with Swagger.

Im not a backend dev, so will just give a brief on creating a MongoDB service to use for our ToDo application.
We shall start by creating a base class for connecting to the database.

```js
// MongoDB Service
const MongoClient = require('mongodb').MongoClient;

class DbService {
  constructor(collection, indexes) {
    this.collection = collection;

    if (indexes)
      openConnection().then(db => {
        db.collection(this.collection)
          .createIndexes(indexes)
          .then(() => db.close());
      }).catch(e => e);
  }

  queryAll(query = {}) {
    return new Promise((resolve, reject) => {
      openConnection().then(db => {
        db.collection(this.collection)
          .find(query)
          .toArray((error, result) => {
            db.close();
            if (error) throw error;

            resolve(result);
          });
      }).catch(e => reject(e));
    });
  }

  queryOne(query = {}) ...

  insert(record) ...

  update(query, record) ...
}
```

Use the base class for our database models, the following example creates a `UserModel`.

```js
const DbService = require('./dbservice');

class UsersModel extends DbService {

  constructor() {
    // Create an index on the username property
    super('users', [
      {
        key: {
          username: 1
        },
        name: 'idx_users_username',
        unique: true
      }
    ]);
  }

  getUsers() {
    return this.queryAll();
  }

  createUser(user) {
    return new Promise((resolve, reject) => {
      let validation;

      if ((validation = validateUser(user)) !== null)
        return reject({message:'Invalid model'});

      return this.insert(user)
        .then(result => resolve(result))
        .catch(e => reject(e));
    });
  }
}
```

To use a MongoDB cloud service try [mLab](https://mlab.com), they provide free databases as well as paid service.

In Part-3 we will look at creating the express api service for the ToDo application

---


##### Further Reading

[MongoDB Getting Started](https://mongodb.github.io/node-mongodb-native/api-articles/nodekoarticle1.html)

[MongoDB Docs](http://mongodb.github.io/node-mongodb-native/2.2/api/)

[MongoDB on W3Schools](https://www.w3schools.com/nodejs/nodejs_mongodb.asp)
