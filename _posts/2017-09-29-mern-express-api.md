---
layout: post
title:  "MERN Stack Application - Part 3"
description: "Database Service - This post will describe the steps involving the Express API service for our ToDo application."
date:   2017-09-29
tags: [nodejs,express,es6]
publish: true
---

<p class="intro"><span class="dropcap">T</span>his post will describe the steps involving the Express API service for our ToDo application.</p>

> MongoDB + Express + ReactJS + NodeJS with Swagger.

We will be using NodeJs express api to create our backend routes and also serve the React application. Lets start by creating an express instance and adding some common middleware for our API's.

```js
// create express instance
const express = require('express');
const app = express();

// we will use these later in the post
app.set('host', 'localhost');
app.set('port', 8080);
app.set('ports', 8443);

// enable cors
const cors = require('cors');
app.use(cors({'origin': '*', 'methods': 'GET,HEAD,PUT,PATCH,POST,DELETE',
  'preflightContinue': false, 'optionsSuccessStatus': 204}));

// add middlewares
const bodyParser = require('body-parser');
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

// set the path from which to serve static content
app.use(express.static(path.join(__dirname, '/build')));
```

In the above snippet we have added two middleware services for handling Cross-Origin-Requests and a body parser to parse JSON payloads.

<p class="note">Use cors when the API needs to accessed out side the application.</p>

Now lets start creating our API routes, for this we will use an express router instance.

```js
const router = express.Router();
const UsersService = require('../db').UsersService;

router.post('/login', (req, res) => {
  const {username, password} = req.body;
  if (username === 'user@email.com' && password === 'password') {
    // respond with auth token
    res.json({username: req.body.username, token: token});
  } else {
    res.status(400).json(errors.INVALID_CREDENTIALS);
  }
});

router.get('/users', (req, res) => {
  UsersService.getUsers()
    .then(result => res.status(200).json(result))
    .catch(e => res.status(404).json(e));
});

// return 404 when route not found
router.use('*', (req, res) => res.status(404)
  .json({message: "Unknown API route"}));

// use the router for all routes prefixed /api
app.use('/api', router);
```

In the above snippet we have created a router for all routes preceded by `/api`, by using the router we can segregate the api routes from the routes described in our React app.

Now we will add a error handler.

```js
// add error handler after all routes
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
```

Lastly we will serve the react application `index.html` when no matching route is found.

```js
// add spa last to handle any unknown route to redirect to public/index
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '/build/index.html'));
  res.end();
});
```

<p class="note">Express routes follow a waterfall model, wherein the routes will be matched down the chain. So always serve the react application <code>index.html</code> at the end.</p>

Now finally lets start the http server. The following setup is for creating a secure `https` server and a `http` server that will redirect to `https`.

```js
const fs = require('fs');
const options = {
  key: fs.readFileSync('./keys/key.pem'),
  cert: fs.readFileSync('./keys/cert.pem')
};
https.createServer(options, app).listen(app.get('ports'), () => {
  console.info('Express server https//localhost:' + app.get('ports'));
});

// Redirect from http port 80 to https
http.createServer(function(req, res) {
  const secureUrl = req.headers['host'].replace(/:\d+/, ':8443');
  res.writeHead(301, {"Location": `https://${secureUrl}${req.url}`});
  res.end();
}).listen(app.get('port'), () => {
  console.info('Express server http//localhost:' + app.get('port'));
});
```

Now that we managed to create a simple backend service for the ToDo application, in Part-4 we shall create the React application.

---

##### Further Reading
[Express Guide](http://expressjs.com/en/guide/routing.html)

[Express Security Practices](http://expressjs.com/en/advanced/best-practice-security.html)
