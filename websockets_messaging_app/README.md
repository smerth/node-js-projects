# 9. WebSockets

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

## Creating a WebSocket server

Web Sockets allow for a true two way connection between the client and the server. Web Sockets use their own protocol to send and receive messages from a TCP server. 

Until recently, we had no way push information from the server to the browser. The browser had to constantly check the server API by making a request to see if the state of the server has changed. We call this polling or long polling.

A long poll consist of making a request of a server and leaving it open for a longer period of time. We let that request time out when information hasn't changed. When you have a long poll, if information does change on the server, we can immediately receive a response with the changed information. So you can think of long polling as just a more efficient way of polling. 

Now with Web Sockets available we can connect to the server and leave the connection open so that we can send and receive data.

Web Sockets are not limited to the browser. With Web Sockets, clients can connect to the server, including native applications, and leave a two way connection open. Through this connection, clients can send data that are easily broadcasted to every open connection. So the server is able to push data changes to the client using Web Sockets. 

Instead of HTTP, Web Sockets use their own protocol. Settinng up a Web Socket from scratch on your web application can be a little tricky You need a TCP Socket server and a HTTP proxy.

Fortunately, there are node modules that will help us with building our Web Sockets. 

**In this lesson, we are going to build our Web Socket Server using the WS module.** 

In the project folder 

Initialize npm

```bash
npm init
```

Install the ws module

```bash
npm install ws --save
```

Create app entry point

```bash
touch ws.js
```

Create a variable called WebSocketServer, this is going to be a constructor that we will get from the WS module. 

Chain on the server Because the WebSocketServer is going to be the server key of the WS Module. 

```javascript
var WebSocketServer = require("ws").Server;
```

Create an instance of a Web Socket server using this constructor.

Call that instance, WSS and then using the "new" key word, invoke a new WebSocketServer and give it a port of 3000.  This will create a new WebSocketServer that runs on its own protocol, And we can connect it with WS:// as opposed to HTTP:// 

```javascript
var wss = new WebSocketServer({ port: 3000 });
```



Set up is a listener for new connections ```wss.on("connection") will fire a callback function when we have a new socket connected. And that individual WebSocket will be passed to this function as an argument which we will call "ws". 

```javascript
wss.on("connection", function(ws) {

});
```



So you can think of Web Socket as a connected end-point. This means that we have one client connection and every client that connects is going to cause this callback function to fire and then we can edit their individual connection using this ws variable. 

What we are going to do is send a message back to each connected socket.

```ws.send("")```, will send a message back to the client, and we will send this message back after they've made a connection.  We will send the message, "Welcome to cyber chat." 

```javascript
wss.on("connection", function(ws) {

	ws.send("Welcome to cyber chat");

});
```



With the server setup we next need to write the client. 

```bash
touch index.html
```

@ ```index.html```

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



We have a div with a class of "messages" to hold messages coming from the server and a form for adding new messages.

note: 

```html
<form action="javascript:void(0)">
```



There is no button for form submission.  

Clicking enter will actually collect our new message and run our client JavaScript. 

Create client side code:

@ terminal

```bash
touch ws-client.js
```

Edit: 

@ ws-client.js

```javascript
document.forms[0].onsubmit = function () {
    var input = document.getElementById('message');
    ws.send(input.value);
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

Start with an on submit function which grabs the element ```#message``` and assigns it to a variable called input.  Then we use the ```ws.send()``` function (from ws module) to the send the value of the variable ```input``` using ```input.value```. Then we reset the value of the ```var input``` back to an empty stiring, ready for new input from the form.

Two other  function set the value of the title element and then message element.

Every time we print a message, we can use the print message function and this will create a new paragraph, set the text of the paragraph to our message, and add our message to the dom. 



Use native JavaScript to create a Web Socket instance using the new Web Socket constructor.

And when we use this constructor, we have to point out where our Web Socket constructor is running. So Web Socket use their own protocol, so this server is running at ws, localhost port 3000. 

@ ws-client.js

```javascript
var ws = new WebSocket("ws://localhost:3000");
```





Now, I'm actually going to use this instance to wire up event handlers. For instance, when the Web Socket opens, I want this function to be invoked. And when our socket opens, I'm going to go ahead and set the title of the page connected to cyber chat.

There we go. When the Web Socket closes, I will use this function. So, when our web socket closes, we will also set the title to disconnected, and when we receive a message from the Web Socket. So when this Web Socket sends a message, this function will fire. And that message will be a part of the object that is sent to this function. I'm going to call it pay load. So, when we connect to this Web Socket, we are going to get sent a message, a welcome message to the Cyber Chat, and every time we get a message, we are simply going to add it to the dump.

So, I'm going to call print message, then send the payload.data to the dump. So whatever message, data that I receive, we will actually print that message on the dump. So let me go ahead and save this

@ ws-client.js

```javascript
ws.onopen = function() {
  setTitle("Connected to Cyberchat")
};

ws.onclose = function() {
  setTitle("DISCONNECTED")
};

ws.onmessage = function(payload) {
  printMessage(payload.data);
};
```









Start the package server. 

```bash
node ws
```

The socket server is running, which should have maybe given us a message to let us know that the Socket Server is running (but we haven't set that up in ws.js…), but seeing this cursor flash right here, tells us that the server is in fact running. (The cursor doesn't flash in my build, but the app doesn run...)



Now, this is the neat part about Web Sockets. We actually do not need to set up the index HTML on a server to work work with a Web Sockets. I can simply double click and open this file in a browser and you can see using the file API, that I have connected to the Web Socket, and it says, "Welcome to cyber chat." So, not only have I connected to the Socket, but I've received my first message from the Socket Server.

Now if I go out into the terminal and I stop our web server

```bash 
ctl c
```

we will see that our socket has disconnected. So the message at the top of the page has changed from "connected to cyber chat", and it is now saying, "disconnected" because we have stopped our Web Sockets Server. 



## Broadcasting messages with WebSockets

In the last lesson, we constructed a websocket server and then connected to it from client side javascript using the native websocket constructor. In this lesson, we want to be able to send and broadcast chat messages to every connected socket. So, I'm starting out in the ws client js file that is loaded in the index.html page that runs in the browser. Whenever a user enters a new message, the document forms on summit handler will fire, and this is where we can actually collect the new message from a user.

So, if I enter a new message into the browser page, what I need to do is send this message back to the socket server. So, I'm going to use my ws instance, and I am going to send the input.value back to the socket server. So, the input.value is what the user has entered into the form before they have submitted it. 

```javascript
document.forms[0].onsubmit = function () {
    var input = document.getElementById('message');
    ws.send(input.value);
    input.value = '';
};
```





So, now that we are sending the messages back to the socket server, we can go ahead and navigate to our socket server code. I'm going to go ahead and open up my files in the finder, and I am going to find my ws.js file, this is the websocket server.

So, I can go ahead and open up that. So, in this file every new connection we send the message, Welcome to cyber chat. Inside of this callback function, the websocket that is newly connected is passed as an argument. 

So, we can actually add listeners to the websocket here. What we want to listen for, are any messages that are sent from the browser to the socket server. So, ws.on message will cause this event handler to be fired. The message that is passed from the client to the server will be added as an agrument to this event handler.

So, what we want to say is, if the message is equal to exit, then we are going to disconnect this client. I can disconnect this socket by calling ws.close. This leaves my socket server running, but it closes this client's connection. Otherwise, if the client has typed anything else, we want to broadcast that to all of the clients. WSS, is our websocket server instance. .clients is an array of all the connected websockets.

Because it's a javascript array, I can use the forEach function. The forEach function takes in a callback that will be invoked once for every one of the clients that are in that array. So, I'm going to pass the individual client to this function. So, now we have nice loop, looping through all of the clients, and to broadcast the chat message back, I'm just going to invoke a client.send. This is how we can send the message back to the client. It's actually, the exact same send function that we are using on line 20.

```javascript
wss.on("connection", function(ws){

  ws.on("message", function(message){
	  if (message === "exit") {
	  	ws.close();
		} else {
	  	wss.clients.forEach(function(client){
  			client.send(message);
		  });
	  }
	});

	ws.send("Welcometo cyber-chat.")

});
```





Great. So, let's go ahead and save this. Now, let's go out to our terminal. So, now let's start our websocket server. 

```bash
node ws
```



Node ws will start our server. Now, I can navigate to my files, and all I need to do is open up the index file. I actually don't need to run this in a browser and chrome for websockets to work. So, here we have one of our chat rooms connected. I'm going to go ahead and copy this URL to the file, in the file api, and open up a second browser window and paste it in there.

Now, I have two connected clients. So, I'm going to place both of these clients side by side so that I can test the chat feature. So, let's see if we say hello and send it, we can see hello shows up in our other client. If I go over here and say how is it going, we can see how is it going show up on this client. This is because we are able to send this message to the server, and then the message is then broadcast to all clients.

So, this is how we can implement websockets natively in the browser, using the ws module.




