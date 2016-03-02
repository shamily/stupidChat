# Stupid Chat

## Overview

This is a simple single room multiuser chat. The solution is based on:

* ``Node.js`` (https://nodejs.org/) and ``Express`` (http://expressjs.com/)  
* ``socket.io`` (http://socket.io/)

The goal was to create a simple test for WebSockets technology.

## How to run the app

Do the following steps (unless you have some software pre-installed)

* Install Node.js
```bash
$ sudo apt-get install node
$ sudo apt-get install npm
```

* Get the repository
```bash
$ sudo apt-get install git
$ git clone https://github.com/shamily/stupidChat
$ cd stupidChat
```

* Update the packages required for the project
```
$ npm install
```

* Run the service
```
$ node ./bin/www
```

Node should start listening at port 3000. After that run the app in a browser: http://localhost:3000. You should run a few tabs - which will "chat" between themselves. Server doesn't store the messages, only transmits to all connected clients.

## Steps to generate the app

I've used ``yeoman`` (http://yeoman.io/) for scaffolding, which explains why there are some not needed stuff in the files.

```bash
sudo npm install -g yo
sudo npm install -g express-generator
yo
```

I've selected:

1. Express generator
2. Basic version (not MVC)
3. ``EJS`` view engine (Found it hard to use Jade without practice)
4. ``Node-Sass`` preprocessor (doesn't matter for this example)
5. ``Grunt`` build tool

Then I needed to install socket.io:

```bash
npm install socket.io -S
```

## Code changes

### ``bin/www``

Added at the end of the file:

```javascript
var io = require('socket.io')(server);

io.on('connection', function (socket) {
  socket.on('message', function (data) {
    console.log(data);
    io.emit('message', { name: data.name, message: data.message });
  });
});
```

### ``views/index.ejs``

Added at the body:

```html
<script src="/socket.io/socket.io.js"></script>
<script>
var socket = io(window.location.protocol + "//" + window.location.host);

socket.on('message', function (data) {
    console.log(data);
    document.getElementById('chat').innerHTML = data.name + ": " + data.message + "<br>" + document.getElementById('chat').innerHTML;
});

function sendMessage() {
    socket.emit('message', { name: document.getElementById('name').value, message: document.getElementById('message').value });
}
</script>

<input id="name" type="text" value="nickname" /> nickname <br>
<input id="message" type="text" value="type here" /> <input type="button" onclick="sendMessage()" value="Message"/>
<hr>

<div id="chat"> &nbsp;</div>

```
