---
layout: post
title: Full Stack Development (Connecting a Webserver to a Database)
date: 2020-09-08 13:39
category: Full Stack
author: Benjamin Makowsky
tags: [express, cors, mysql, react, node, javascript, typescript]
summary: In this post we are going to learn how to connect a webserver to a database and enable the ability handle GET requets to send data to another application.
---

# Intro to Full Stack Development

What is meant by the phrase "Full Stack"? What is a full stack developer? __Full Stack__ simply is the term meaning the entire software development stack from front-end (Web Page / UI) to the back-end (Server). The term full stack developer is a buzz word used by recruiters or companies to mean an individual who is involved in the entire development stack. There is no such thing as a full stack developer vs not because in any position you can be required to work on either side of development and are not limited to simply just front-end or back-end work. Every individual should do their best to learn both front and back end development so that when an issue arises in the pipeline, they can have an idea of where to look and how to fix it. With that, lets begin by creating the back-end server for our front-end client to connect with. [Get the code here][codeLink]

# The Server
In this example we will be creating our server in JavaScript and using ___express___ to handle the GET requests to control the flow information. A __GET__ request is exactly what it sounds like. It is a request to GET information from a server. In our example we will get use GET to access a database and return the data that is inside.

## Step 1: Setup
To begin create a folder to store your project. From the command line navigate to the directory and install the following:
```
npm install express cors mysql
```
This will install the libraries we will be using to manage our server. Next create a new JavaScript file and name it whatever you, I chose SparkiServer.js. Now open the file and begin with importing the required libraries:

## Step 2: Writing the Code
```javascript
//Allows us to  perform routing and respond to various http request methods (GET, POST, PUT, DELETE)
const express = require('express');
const app = express();

/* Cross-Origin-Resource-Sharing
This is what allows our front-end react app to connect with our server and fetch the data being presented.
Without cors() it would fail and nothing would happen. */
const cors = require('cors');
app.use(cors());        //Without this line, when we try to fetch the data from react. Nothing would happen

//Allows us to connect to our database
const mysql = require('mysql');
```
Here ___express___ will be used to create routes and manages the requests to our webserver. ___Cors___ allows our React app that we will be creating in the next post to connect to our server and display the information in our web app. ___Mysql___ allows us to communicate with our database. Now we need to connect to the database:
```javascript
/*Creating the database connection*/
const con = mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "password",
    database: "SPARKI"
});

con.connect(function(err) {
    if (err) throw err;
});
```
If you have read my other posts I went into more detail on this section of code, but this is section is pretty standard whenever you connect to a mysql data from javascript. It simply creates an instance of connect to a server called sparki with the password and then connects. Now for the important parts:
```javascript
//Respond to a GET request at the designated path displaying all values in the stats table
app.get('/stats', (req, res) => {
    con.query("SELECT * FROM stats", (err, result) => {
        if (err) {return res.send(err)}
        else {return res.json({data: result})}
    });
});

//Respond to GET with design to retrieve individual columns
//Format localhost:PORT/stats/get?cell=CELL_NAME
app.get('/stats/get', (req, res) => {
    const {cell} = req.query; //gets the name of the cell from: ?cell=CELL_NAME
    const query = `SELECT ${cell} FROM stats`; //Creates the query selecting the desired cell from stats table
    con.query(query, (err, result) => {
        if (err) {
            return res.send(err);
        } else {
            return res.json({data: result});
        }
    });
});
```
Here we begin using express by using the __.get(path, callback function)__ method. The first parameter is the path that tells our server which web path to perform an action on. In this example whenever someone goes to _localhost:4000/stats_ our server will respond with the associated callback function we define. In the first example it returns all the cells in our table. 

The second route is a little more complicated. Here we set up a system where the the server can retrieve individual cells from our database. This is possible because the second line `const {cell} = req.query;` reads input after a ? and stores the values as defined in the path. For example if someone sent a GET request to "localhost:4000/stats/get?cell=example" req.query returns a variable __CELL__ with the value example. If instead they put ___?foo=bar___ then req.query would return a variable called foo with the value bar. Using _const {cell} = req.query;_ we store the desired cell name into our variable and pass it to our database query string as follows: _const query = \`SELECT ${cell} FROM stats\`_. From there we define the port we want the server to operate on and start the server:
```javascript
const PORT = 4000;      //The port to listen on
app.listen(PORT, () => {
    console.log(`listening and started on PORT: ${PORT}`)
});
```
# Step 3
And thats it. We have created the server our react app will connect to and are ready to create the server itself.

[codeLink]: https://github.com/benjaminmakowsky/Sparki/blob/master/Node.js/SparkiServer.js