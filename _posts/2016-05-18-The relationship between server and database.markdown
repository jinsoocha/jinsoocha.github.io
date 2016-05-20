---
layout: post
title:  "Understanding the relationship between server and database using Node.js and MySQL"
date:   2016-05-17 16:31:38 -0700
categories: backend 
---

In this post, I would like to provide the big picture of how client, server and database interact with each other. First of all, what is client and sever? In computing, a server refers to a program or device that provides certain functionalities to a client, which refers to another program or device that interacts with the users. When a client sends a request to the server, the server performs the required action to satisfy the request and sends a response back to the client. 

However, if there are server and client only, the data, which the client sends to and receives from the server, gets removed whenever the server restarts. For this reason, database is necessary to maintain the data persistence beyond the program's current life cycle. With a database in place, when the client sends the data, the server receives the data and stores the data in the database. Likewise, when the client requests to get the data, the server receives the request and finds the data from the database and sends back to the client. This way, the database stores the data even after the server stops.      

Let's talk about this relationship between client, server and database in terms of the available tools to actually see what it is like to build server and database. Node.js is a tool that creates Web servers using JavaScript. Assuming you already have installed Node.js with your package manager, this is how you use Node.js to create the server using its framework, express:

{% highlight javascript %}
//set up node express
var express = require('express');
var app = express();
app.set('port', 3000);

app.listen(app.get('port'));
console.log('Listening on', app.get('port'));
{% endhighlight %}

To create a database, you need to use a database management system (DBMS) such as MySQL, MongoDB and etc. You can consider DBMS a software to interact with the actual database. Assuming you already have created the database using MySQL, this is how you connect the database with the server:     

{% highlight javascript %}
//assuming the database has been already created,
//connect to the database using mysql 
var mysql = require('mysql');

var db = mysql.createConnection({
	host: 'localhost',
	user: 'your username',
    password: 'your password',
    database: 'your database'
});

db.connect();
{% endhighlight %}

At this point, you might wonder, "If Node.js is just a tool to create a server and MySQL is just a tool to interact with the database, where is the actual server and database?"

The actual server and database get created in your local computer for now. However, when you deploy your app, you will be using cloud hosting services such as Heroku, Digital ocean or Amazon Web Services. These companies have servers that interact with multiple clients along with gigantic databases. By using their services, you have access to their servers, which host the client and server files that you provide. You can also rent some portion of their databases. 

The next step is to make the server interact with the client and the database. Node express get and post method enables the server to handle the client requests. After receiving the client requests via these methods, you need to use MySQL query method to tell the server to reach out to the database to find or store the data the client has inquired about. After MySQL finished performing the required action, the server ends the response by sending the client the retreived data or an acknowledgement that the data has been stored successfully. Take a moment to go through the example codes:

{% highlight javascript %}
//serve client files such as html and css
app.use(express.static(yourdirectorypath + '/../client'));

var headers = {
    'access-control-allow-origin': '*',
    'access-control-allow-methods': 'GET, POST, PUT, DELETE, OPTIONS',
    'access-control-allow-headers': 'content-type, accept',
    'access-control-max-age': 10 // Seconds.
};

//the server receives a 'GET' request
app.get('/', function(req, res) {
    res.writeHead(200, headers);
    //find the requested data from the database using mysql query method
    db.query('SELECT * FROM tablename', function(err, data) {
        if (err) {
        	console.log(err);
        } else {
        	//send the data to the client
        	res.end(JSON.stringify(data));
    	}
    });
});    

//the server receives a 'POST' request
app.post('/', function(req, res) {
    res.writeHead(201, headers);
    //insert the data to the database using mysql query method
    //req.body is the object that contains the data, which needs to be inserted 
    db.query('INSERT INTO tablename (columnname) VALUES ("' + req.body.data + '")', 
    function(err, result) {
        if (err) {
        	console.log(err);
        } else {
        	res.end(result);
        }	
    });
});   
{% endhighlight %}

