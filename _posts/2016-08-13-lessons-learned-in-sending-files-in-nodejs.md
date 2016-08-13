---
layout: post
title:  ' Lessons Learned in Sending files in NodeJS '
categories: [redirect, sendFile, express, node]
---

The normal way to server webpages in `NodeJS/ExpressJS` is to use a view engine, like `HandleBars`. But what is a light-weight webpage serve approach? I tried three different ways.

**Approach 1: sendFile function.**

index.html:

```
<html>
<body>
    <p> I love NodeJS! </p>
</body>
</html>
```

server.js:

```
var express = require("express");
var app     = express();

app.get('/', function(req, res) {
    res.sendFile(__dirname+"/index.html");
});

app.listen(9876);
```

It works as expected. You got the "index.html" successfully.
But one concern arises: waht if my webpage need include an external javascript file? For example:

index.html:

```
<html>
<body>
    <p> I love NodeJS! </p>
    <script src="index.js"> </script>      <!-- newly added -->
</body>
</html>
```

"sendFile" wont work this time because "index.js" is not served to client. OK. Fair. Can I send the "index.html" followed by sending "index.js"?

```
var express = require("express");
var app     = express();

app.get('/', function(req, res) {
    res.sendFile(__dirname+"/index.html");
    res.sendFile(__dirname+"/index.js");   <!-- newly added -->
});

app.listen(9876);
```

You got an error: "index.js 404 Not Found". Why I got "404" this time?  Isn't "index.js" served? The answer is NO. "index.js" is not served even it seems sent by "res.sendFile(__dirname+"/index.js");". The reason is because "sendFile" finishes a response, which means all the followed information or files will be ignored and thus client side wont receive any information or files. In our case, the second "sendFile" is ingored.

Then I tried "redirect",

**Approach 2: redirect function.**

index.html:

```
<html>
<body>
    <p> I love NodeJS! </p>
</body>
</html>
```

server.js:

```
var express = require("express");
var app     = express();

app.get('/', function(req, res) {
    res.redirect("index.html");
    //res.redirect("http://google.com"); this redirection can work!
});

app.listen(9876);
```

Unfortunately, it cannot work and you got "Error: Can't set headers after they are sent.". To understand why, we need understand what "redirect" exactly means. "redirect" means it will forward you to a designated webpage/path which has to exist. So before "redirect" is used, the designated webpage/path should be there. In our case, "index.html" is not existing. To make it work, we need add a route to serve the redirection.

```
var express = require("express");
var app     = express();

app.get('/', function(req, res) {
    res.redirect("/index"); //make it a path instead of "index.html"
});

app.get('/index', function(req, res) {
    res.sendFile(__dir_name+"/index.html");
});
app.listen(9876);
```

Comparing to the first approach, We are creating an another step to send a webpage. Not a good choice. 

Finally, the perfect solution comes:

**Approach 3: static middleware in Express.**

```
<html>
<body>
    <p> I love NodeJS! </p>
    <script src="index.js"> </script>      <!-- newly added -->
</body>
</html>
```

```
var express = require("express");
var app     = express();

app.use(express.static(__dirname));

app.listen(9876);
```

Both html and javascrits files are served. What an elegant solution! Enjoy.

So the golden rule is to use `static` from `ExpressJS` as much as possible.

