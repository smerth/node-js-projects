

# How to use the app

## Install app

```bash
npm install
```

Start ws webserver

```bash
node ws
```

## Run app:

```bash
npm start
```

## Use app:

Open ```index.html``` in two separate browsers and begin messaging back  and forth.

---

# Build the app

## What is Socket.IO

If you create websockets with native JavaScript in the browser, they'll work in most modern browsers. But the support for them in older browsers is not good.

Socket.IO is a module that will help us build websockets, that has its own client and its own server JavaScript. Socket.IO fails back to long polling when websockets aren't supported.

So, if websockets aren't supported in a browser, Socket.IO will still most likely work. 

If websockets aren't supported at your hosting company, you can actually build a websocket application that uses entirely long polling, so that you can have a socket application work where websockets are not supported. 

## Build

In the project folder 

Initialize npm

```bash
npm init
```

Install Socket.IO

```bash
npm install socket.io --save
```



Now that I have Socket.IO installed, I'm actually going to use Socket.IO with an express application. So I'll also want to install Express. 

```bash
npm install express --save
```





Create SIO-server.js file

```bash
touch SIO-server.js
```



This is where we're going to program the Socket.IO server. 

Create public folder.

```bash 
mkdir public
```



### Add files to public folder

```bash
 touch styles.css
```

edit:

```css
body {
    background-color: black;
    color: #0F0;
    font-family: verdana;
}

body input,
body label {
    display: block;
}

h1 {
    text-align: center;
}


input {
    outline: none;
    border: none;
    background-color: black;
    color: #0F0;
    padding: 1em .5em;
    display: block;
    font-size: 1.5em;
    -webkit-box-flex: 1;
    -webkit-flex-grow: 1;
    -ms-flex-positive: 1;
    flex-grow: 1;
}

label {
    display: block;
    padding: 1em .5em;
    font-size: 1.5em;
}

div.messages {
    margin-left: 1em;
}

form {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
}
```



Add 

```bash
touch index.html
```

Edit

```html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="minimum-scale=1.0, width=device-width, maximum-scale=1.0, user-scalable=no"/>
    <meta charset="utf-8">
    <title>Websocket</title>
    <link rel="stylesheet" href="style.css"/>
</head>
<body>
<h1>Websocket</h1>
<div class="messages"></div>
<form action="javascript:void(0)">
    <label for="message">&gt;</label>
    <input type="text" id="message" required autofocus />
</form>
<script src="ws-client.js"></script>
</body>
</html>
```

```div.messages``` is a placeholder for data

```action="javascript:void(0)"``` negates the default form behavior so we can handle it in javascript.

We build the app in ```<script src="ws-client.js"></script>``` 



Add

```bash
touch ws-client.js
```

Edit

```javascript
document.forms[0].onsubmit = function () {
    var input = document.getElementById('message');
    input.value = '';
};

function setTitle(title) {
    document.querySelector('h1').innerHTML = title;
}

function printMessage(message) {
    var p = document.createElement('p');
    p.innerText = message;
    document.querySelector('div.messages').appendChild(p);
}
```



### Send a Message from the WS server to Client

*We need a TCP socket server and HTTP proxy server*

```bash

```

This is where we build the socket server

Make a constructor for the websocket server

```javascript
var WebSocketServer = require('ws').Server;
```

Make a new instance of the websocket server that runs on its own protocol. 

```javascript
var wss = new WebSocketServer({ port:3000 });
```

We can connect to it at ```ws://``` instead of ```http://```

Set up a listener for new connections. The new connection ```ws``` is handled by a callback function. 

```javascript
wss.on("connection", function(ws){
  
});
```

Each web socket is a connected end-point.  We have one for each client connected to the ```wss``` server. 

Send a message back ```.on()``` each connection to each client that connects.

```javascript
wss.on("connection", function(ws){
  ws.send("Welcometo Cyberchat.")
});
```



#### Edit @ ws-client.js

Make a new websocket instance using the websockets constructor and point it to our web sockets server

```javascript
var ws = new WebSocket("ws://localhost:3000");
```

Use this instance to wire up event handlers

```javascript
ws.onopen = function() {
  setTitle("Connected to Cyberchat")
};
```

```javascript
ws.onclose = function() {
  setTitle("DISCONNECTED")
};
```

When the web socket ```ws``` sends a message and this client gets the message the message content will be part of the object which is sent to this function.  We'll call it payload.

```javascript
ws.onmessage = function(payload) {
  
};
```

we are just going to add the message in the payload to the DOM

```javascript
ws.onmessage = function(payload) {
  printMessage(payload.data);
};
```



#### Test run the ws server

```bash

```

Double click index.html to open it in a browser.  You don't need to set it up on a server.

 WS server sends welcome and disconnect messages to server. 



### Broadcast Messages Back To Clients



Now how to get messages from clientsent to the ws server and broadcast them back to all connected clients

On submitting a message form send the input value back to the ws server

```javascript
document.forms[0].onsubmit = function () {
    var input = document.getElementById('message');
    ws.send(input.value);
    input.value = '';
};
```



back in ws.js the ws is passed as an argument into the callback function for every socket connection made from a client so we can add listeners here

```javascript
  ws.on("message", function(message){
	  if (message === "exit") {
	  	ws.close();
		} else {
	  	wss.clients.forEach(function(client){
  			client.send(message);
		  });
	  }
	});
```

was.clients is an array of all connected sockets so we can use javascript's ```forEach()``` to loop through the array and send the recieved message out again to every client.



♻︎ This project was built entirely with re-cycled electrons!



