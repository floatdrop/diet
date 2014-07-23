# **Diet**
Diet is a beautiful, minimalistic, extensible HTTP framework for node.js. When you're code is on diet, your apps become skinny, fast, organized & healthy!

## **What does diet do?**

 - Diet provides **Plugins** that extend your apps functionality.
 - Diet provides **HTTP Routing**.
 - Diet provides **HTTP Domain Controlling**.
 - Diet provides the **`$` signal argument** that is a combination of the request and response objects. The signal argument holds key informations about the request, and some default functions, like `$.end()`, `$.url`, `$.query`, `$.body`, `$.headers`
 - Diet ships with some powerful basic plugins: `sessions`,  `demands`, `diet-mysql`, `diet-accounts`, `diet-cookies`, `diet-mail`


## **Hello World!**
```js
$ = require('diet');
$.http('localhost', 8000);
$('GET /', function($){ $.end('Hello World!'); })
```
in just 3 lines.

## **Install**
`npm install diet `

# **Diet Plugins**
Plugins are middlewares that act as a bridge between modules and help write much more efficient Object Oriented code. Plugins are essentially regular node.js functions or modules that follow a standard based on diet's `$` *(signal)* argument.

#### **The 3 Types of Plugins**
Plugins may be all or at least one of these types:

- **Onload** plugin
- **Global** plugin
- **Local** plugin
	
## **Onload Plugins**
Onload plugins *run code right away after the plugin was initialized*. The use cases of onload plugins are very handy when you want to alter the signal property globally, preprocess data, update caches, schedule/execute background tasks etc.

**An Example Plugin:**
```js  
// project/example.js
module.exports.onload = function($){ 
    $.message = 'hello world!';
    $.return();
}
```
```js
// project/index.js
$ = require('diet');    
$.http('localhost', 8000);
$.plugin('example.js'); // pass a function as the second
                        // argument for an async callback
console.log($.message); // -> hello world!
```
## **Global Plugins**
Global plugins run on all incoming HTTP requests/routes. Global plugins can be handy when you need certain functionalities in all or a specific type of routes for example sessions & static file handling.

**Example Plugin:**
```js
// project/example.js
var path = require('path');
module.exports.global = function($){
    this.extension = path.extname($.url.href);
    $.return(this);
}
```
```js
// project/index.js
$ = require('diet');
$.http('localhost', 8000);
$.plugin('example.js');

$('get /', function($){
    $.end('Extension is ' + $.example.extension);
});

$('get /image.jpg', function($){
    $.end('Extension is ' + $.example.extension);
});
```
```
// terminal
curl 'http://localhost:8000/'
-> Extension is undefined

curl 'http://localhost:8000/image.jpg'
-> Extension is .jpg
```

## **Local Plugins**
Local plugins run on specified routes. Local plugins are handy for organizing your code for optimization, so each plugin is required only when it is actually needed.

**Example Local Plugin as a Module:**
```js
// project/example.js
module.exports.local = function($){
    this.name = 'Adam';
    this.age = 20;
    $.return(this);
}
```
```js
// project/index.js
$ = require('diet');
$.http('localhost', 8000);
var person = $.plugin('example.js');

$('get /', person, function($){
    $.end('Hi I am ' + $.person.name + ', '  + $.person.age + ' old.');
    // -> Hi I am Adam, 20 years old.
});
```

**Example Local Plugin as a Function:**
Local plugins can also be created as functions
```js
// project/index.js
$ = require('diet');
$.http('localhost', 8000);

function person($){
    this.name = 'Adam';
    this.age = 20;
    $.return(this);
}

$('GET /', person, function($){
    $.end('Hi I am, ' + $.person.name + ', '  + $.person.age + ' old');
    // -> Hi I am Adam, 20 years old.
});
```

# **$ Signal**
The signal is diet's namespace in node. There is a global signal variable used in the global context, and there is a signal argument which is used in the plugin's local context.

## **Example**
```js
    $ = require('diet');        // <-- global signal
    $.http('localhost', 8000);  // <-- using global signal in global scope
    $.plugin('plugin_name');    
    
    $('get / ', function($){  // <-- signal argument
        $.end('hello world'); // <-- using local signal in local scope
    });
```

## **Global Signal Attributes**
```js
    // include a plugin
    $.plugin('plugin_name', optionl_async_function);     
    
    // enable debug mode. `false` by default.
    $.debug = true; 
    
    // path of your application. `process.cwd()` by default
    $.path;
    
    // start accepting incoming http requests.
    $.http(hostname, port);
    
    // start accepting incoming https requests.
    $.https(hostname, port, certificates);
    
    // route http(s) requests
    $('get /path', pluginA, pluginB .., function($){
        ...
    });
    // first argument is the method and path. the method can be `get` or `post` followed by a space and the path.
    // 'get /page'
    // 'post /form'
    // the last argument is the ending function
    // any argument between the first and last is a local plugin
```
