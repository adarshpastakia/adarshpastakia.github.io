---
layout: post
title:  "MERN Stack Application - Part 1"
description: "Pre-Requisites and Tools - This post will describe the steps for setting up and deploying a MERN stack application."
date:   2017-09-23
tags: [nodejs,reactjs,es6]

mongo-install: https://docs.mongodb.com/manual/installation/
express-security: https://expressjs.com/en/advanced/best-practice-security.html
---

<p class="intro"><span class="dropcap">T</span>his post will describe the steps for setting up and deploying a MERN stack application.</p>

> MongoDB + Express + ReactJS + NodeJS with Swagger.

To begin with lets go through each of the players involved.

* __MongoDB__: A popular NoSQL database. [Read More](https://www.mongodb.com)
* __Express__: Web-Application framework for NodeJS. [Read More](http://expressjs.com)
* __ReactJS__: A Frontend Framework for building Single-Page-Applications. [Read More](http://reactjs.com)
* __NodeJS__: A Server Framework built using JavaScript. [Read More](https://nodejs.org/en/)

<p class="note">All above modules will require NodeJS to be installed. Follow the instructions <a href="https://nodejs.org/en/download/">here</a> to install NodeJs.</p>

<p class="note">To use MongoDB locally follow the installation instructions <a href="https://docs.mongodb.com/manual/installation/#tutorials" target="_blank">here</a>. Alternatively use <a href="https://mlab.com" target="_blank">mLab</a> Database-as-a-Service provider for MongoDB.</p>

---

### Create a MERN stack ToDo Application

* Install the following node dependencies

```shell
# install create-react-app cli
yarn global add create-react-app

# create a react application
create-react-app mern-todo

cd mern-todo

# install other npm dependencies

## express api router
yarn add express cors body-parser

## mongodb driver
yarn add mongodb

## swagger json generator
yarn add swagger-jsdoc
```

* Add `main.js` node entry-point file

```js
// @file: mern-todo/main.js

// imports
const path = require('path');
const http = require('http');

// create express instance
const express = require('express');
const app = express();

// enable cors
const cors = require('cors');
app.use(cors({
  'origin': '*',
  'methods': 'GET,HEAD,PUT,PATCH,POST,DELETE',
  'preflightContinue': false,
  'optionsSuccessStatus': 204
}));

// add middlewares
const bodyParser = require('body-parser');
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

// server static files from react /build folder
app.use(express.static(path.join(__dirname, '/build')));

// add the routes
app.get('/api/test', (req,res) => res.send('It works!'));
// return 404 when api route not found
app.use('/api/*', (req, res) => res.status(404).send());

// add error handler for api errors
const isDev = true;
app.use((err, req, res, next) => {
  res.status(500).json({
    error: 500,
    message: 'Internal Server Error!',
    stack: isDev
      ? err.stack
      : null
  })
});

// handle any unhandled route
// redirect to build/index
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '/build/index.html'));
  res.end();
});


// start the http server
app.listen('8080', () => {
  console.info('Server listening @ http://localhost:8080');
});
```

* Finally build the react application and run the server

```shell
# build the react application
npm run build

# start the http server
node main.js
```

Lets test our apis routes
* `curl -X GET http://localhost:8080/api/test`
  - Result 'It Works!'
* `curl -X GET http://localhost:8080/api/login`
  - Result 404:Not Found

Now we have the server running we can access the react application at `http://localhost:8080/`

In Part-2 we will look at creating a database service for the ToDo application
