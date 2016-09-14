

# Express.js dictionary app — A Simple dictionary app served by express.js

> App.js defines an express.js server which serves a dictionary of terms from a json object.  App.js can be run on any node.js enabled server.  New terms can be added via a form.  Terms can be deleted by double clicking on them.  
>
> Adding and deleting terms updates the json array held in memory on the server.  When the server is rest the original terms are read again from the json object in app.js.
>
> This is a project from Lynda.com's "[Node.js Essential Training](https://www.lynda.com/Node-js-tutorials/Node-js-Essential-Training/417077-2.html)"

**Read the notebook** [link to notebook](link to notebook),  |  no live demo for this project.

## Usage

Use project as a quick way to get to work on some code requiring a server for some static json data.

### Install

```bash
npm install
```

### Start the app server

```bash
node app
```
The app server serves your application files from the public folder on port 3000.

### Start the app server with node-dev

```bash
node-dev app.js
```

node-dev will reload the express server any time you make changes to app-server.js. (N.B. - it doesn't restart the browser, just the server.)

The app server serves your application files from the public folder on port 3000.

### N.B.

The server is just a simple express.js server so you can modify it any way you want.

🌐 [express.js](http://expressjs.com)



## Build

### Install some modules

```bash
npm install express cors body-parser --save
```

### Add a file to define the express server

```bash
touch app.js
```



## Add directories for the application front-end

```bash
mkdir public && mkdir public/css && mkdir public/js
```



## Add application front-end files

```bash
touch public/css/styles.css && touch public/js/dictionary.js
```





Add pre-baked index, css, jquery files - that's trivial.

### Dictionary.js

```javascript
$(document).ready(function () {

    $.getJSON('/dictionary-api', printTerms);
    $('form').submit(function (e) {
        e.preventDefault();
        $.post('/dictionary-api', {term: $('#term').val(), defined: $('#defined').val()}, printTerms);
        this.reset();
    });

});

function printTerms(terms) {
    $('body>dl').empty();
    $.each(terms, function () {
        $('<dt>').text(this.term).appendTo('body>dl');
        $('<dd>').text(this.defined).appendTo('body>dl');
    });
    $('dt').off('dblclick').dblclick(function() {
        $.ajax({
            url: '/dictionary-api/' + $(this).text(),
            type: 'DELETE',
            success: printTerms
        });
    });
}
```

### Build App

```javascript
var express = require("express");

var app = express();

app.use(express.static("./public"));

app.listen(8000);

console.log("Express JSON server up and running on port: 3000);

module.exports = app;
```



###  Add some middle-ware to our Express app server

```javascript
appserver.use(function(request, response, next){

});
```



Log some info about the incoming request and then send the response on to the next function. (note the back-ticks in this snippet mess up some code highlighters.)

```javascript
appserver.use(function(request, response, next){
	console.log(`${req.method} request for '${req.url}'`);
	next();
});
```



So we can see the request coming in from the app on port:8000

```bash
Your express application server is up and running on port: 8000
GET request for '/'
GET request for '/css/style.css'
GET request for '/js/dictionary.js'
GET request for '/js/jquery.min.js'
GET request for '/dictionary-api'
GET request for '/js/jquery.min.map'
```



###  Express Routing and CORS

Add some json data directly to the app just to get going

```json
var skierTerms = [
    {
        term: "Rip",
        defined: "To move at a high rate of speed"
    },
    {
        term: "Huck",
        defined: "To throw your body off of something, usually a natural feature like a cliff"
    },
    {
        term: "Chowder",
        defined: "Powder after it has been sufficiently skied"
    }
];
```



### Add an express route to handle a GET request 

```javascript
appserver.get("/dictionary-api", function(req, res) {
	res.json(skierTerms);
});
```



At this point the app is consuming data from its own domain to make things more "real-world" we can add CORS middle-ware to our data-server so it will serve across domains.

```javascript
var cors = require("cors");
```



Other apps should be able to hit our-site/dictionary-api and get the json terms in the app.

### Add body-parser

```javascript
var bodyParser = require("body-parser");
```

Body parser will take input from post and delete requests and parse it into variables that we can access easily.

Add above ```app.use()```

```javascript
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

### Add an express route to handle a POST request

```javascript
app.post("/dictionary-api", function(req, res) {    
  skierTerms.push(req.body);
  res.json(skierTerms);
});
```

### Add an express route to handle a DELETE request

```javascript
app.delete("/dictionary-api/:term", function(req, res) {
    skierTerms = skierTerms.filter(function(definition) {
        return definition.term.toLowerCase() !== req.params.term.toLowerCase();
    });
    res.json(skierTerms);
});
```



## As an application this demo has a lot of design issues

- To delete items double click on them. That's not an obvious interface.

- The JSON data is loaded into the app and held in memory as long as the server is running. When you add or delete, an operation is performed on the JSON array in memory. - How to perform the crud operations on the the json data object in the app file instead of in memory?


- Instead of using a json object in app.js, how to load data from an external son file.


- Style is an after-thought.





♻︎ This project was built entirely with re-cycled electrons!