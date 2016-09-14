# Node.js Essential Training



> These notes accompany my builds of the projects from [Node.js Essential Training](https://www.lynda.com/Node-js-tutorials/Node-js-Essential-Training/417077-2.html) on Lynda.com.  My repo for these projects can be found [here]().

## node_js_http_module

This folder contains projects to illustrate the basic functionality of Node.js built in http module.

### make_a_request

To run the server

```bash
node request.js
```

Executes

```javascript
var https = require("https");
var fs = require("fs");

var options = {
	hostname: "en.wikipedia.org",
	port: 443,
	path: "/wiki/George_Washington",
	method: "GET"
};

var req = https.request(options, function(res) {

	var responseBody = "";

	console.log("Response from server started.");
	console.log(`Server Status: ${res.statusCode} `);
	console.log("Response Headers: %j", res.headers);

	res.setEncoding("UTF-8");

	res.once("data", function(chunk) {
		console.log(chunk);
	});

	res.on("data", function(chunk) {
		console.log(`--chunk-- ${chunk.length}`);
		responseBody += chunk;
	});

	res.on("end", function() {
		fs.writeFile("george-washington.html", responseBody, function(err) {
			if (err) {
				throw err;
			}
			console.log("File Downloaded");
		});
	});

});

req.on("error", function(err) {
	console.log(`problem with request: ${err.message}`);
});

req.end();


```



### simple_server

To run the server

```bash
node server.js
```

server.js

```javascript
var http = require("http");

var server = http.createServer(function(req, res) {

	res.writeHead(200, {"Content-Type": "text/html"});

	res.end(`
		<!DOCTYPE html>
		<html>
			<head>
				<title>HTML Response</title>
			</head>
			<body>
				<h1>Serving HTML Text</h1>
				<p>${req.url}</p>
				<p>${req.method}</p>
			</body>
		</html>
	`);

});

server.listen(3000);

console.log("Server listening on port 3000");
```

The page is served and two variables available to the node.js are printed to the screen.

```javascript
<p>${req.url}</p>
<p>${req.method}</p>
```



### simple_file_server

To run the server

```bash
node fileserver.js
```

a simple fileserver

```javascript
var http = require("http");
var fs = require("fs");
var path = require("path");

http.createServer(function(req, res) {

	console.log(`${req.method} request for ${req.url}`);

	if (req.url === "/") {
		fs.readFile("./public/index.html", "UTF-8", function(err, html) {
			res.writeHead(200, {"Content-Type": "text/html"});
			res.end(html);
		});

	} else if (req.url.match(/.css$/)) {

		var cssPath = path.join(__dirname, 'public', req.url);
		var fileStream = fs.createReadStream(cssPath, "UTF-8");

		res.writeHead(200, {"Content-Type": "text/css"});

		fileStream.pipe(res);

	} else if (req.url.match(/.jpg$/)) {

		var imgPath = path.join(__dirname, 'public', req.url);
		var imgStream = fs.createReadStream(imgPath);

		res.writeHead(200, {"Content-Type": "image/jpeg"});

		imgStream.pipe(res);

	} else {
		res.writeHead(404, {"Content-Type": "text/plain"});
		res.end("404 File Not Found");
	}

}).listen(3000);


console.log("File server running on port 3000");

```



### simple_json_server

To run the server

```bash
node api.js
```

A simple json api

```javascript
var http = require("http");

var data = require("./data/inventory");

http.createServer(function(req, res) {

	if (req.url === "/") {
		res.writeHead(200, {"Content-Type": "text/json"});
	    res.end(JSON.stringify(data));
	} else if (req.url === "/instock") {
		listInStock(res);
	} else if (req.url === "/onorder") {
		listOnBackOrder(res);
	} else {
		res.writeHead(404, {"Content-Type": "text/plain"});
		res.end("Whoops... Data not found");
	}

	

}).listen(3000);

console.log("Server listening on port 3000");


function listInStock(res) {

	var inStock = data.filter(function(item) {
		return item.avail === "In stock";
	});

	res.end(JSON.stringify(inStock));

}

function listOnBackOrder(res) {

	var onOrder = data.filter(function(item) {
		return item.avail === "On back order";
	});

	res.end(JSON.stringify(onOrder));

}
```



### collect_POST_data

To run the server

```bash
node formserver.js
```

Handle a POST request

```javascript
var http = require("http");
var fs = require("fs");

http.createServer(function(req, res) {

	if (req.method === "GET") {
		res.writeHead(200, {"Content-Type": "text/html"});
	    fs.createReadStream("./public/form.html", "UTF-8").pipe(res);
	} else if (req.method === "POST") {

		var body = "";

		req.on("data", function(chunk) {
			body += chunk;
		});

		req.on("end", function() {

			res.writeHead(200, {"Content-Type": "text/html"});

			res.end(`

				<!DOCTYPE html>
				<html>
					<head>
						<title>Form Results</title>
					</head>
					<body>
						<h1>Your Form Results</h1>
						<p>${body}</p>
					</body>
				</html>

			`);


		});


	}


}).listen(3000);

console.log("Form server listening on port 3000");
```



## httpster_file_server

Httpster is a web server that will serve static content.

Install Httpster

```bash
npm install - g httpster
```

Run httpster and give it a port flag and give it a directory to serve.

```bash
httpster -p 8080 -d ./public
```



#### Running node modules: global vs local

Try this:

Run

```bash
which httpster
```

returns

```bash
/usr/local/bin/httpster
```

This is where the httpster executable files is located when it is installed globally. When a node module is installed globally the executable file is linked automatically so that terminal is aware of its location.  So to run the executable you can simply call it by name.

```bash
httpster -p 8080 -d ./public
```



Now, remove httpster global install.

```bash
npm remove -g httpster
```

Now install it locally

```bash
npm install httpster
```

This creates a node_modules folder with httpster installed there.  

Try to run httpster

```bash
httpster -p 8080 -d ./public
```

The httpster executable cannot be found.

```bash
-bash: /usr/local/bin/httpster: No such file or directory
```

To run it you need to specify the executable's location.  

```bash
./node_modules/httpster/bin/httpster -p 8080 -d ./public
```

This applies to all node modules

## express_js_term_dictionary

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

See the readme file in the app folder...

## websockets_messaging_app

See project folder for detailed readme...

## sockets_io_messaging_app

See project folder for detailed readme...





