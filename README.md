# Kekexpress (кекекспрес)

Naive express-like library to create http server application.
Uses json parser by default.

### Installation
```
npm i kekexpress
```

##  Simple Usage Example
```JS
const { Server } = require("kexpress");

// Creating server
const app = new Server();

// Applying global middlewares for application
app.middleware((req, res, next) => {
  console.log(req, res);
  
  // You can call next just like in express library
  next();
});

// Applying spesific hanler for url path
// Also supports 'put', 'patch', 'delete' and 'post' methods
app.get("/", (req, res) => {
  res.status(200).json({ message: "Hello world" })
});

// Special handler for unknown routes
app.notFound((req, res) => {
  res.status(404).json({ message: "Route not found" })
});

// Everytime when app throws Error inside route handlers
// it will be passed here
app.error((req, res, error) => {
  res.status(500).json({ message: error.message })
});

// Starting server on port
app.listen(3000, () => {  
  console.log("Server has been started on port 3000");  
});
```

## Usage with multiple routers
```JS
const { Server, Router } = require("kexpress");

const app = new Server();
...
// Argument is the prefix for all routes
const router = new Router("/user"); 

// The same api as for Server class
// middleware, get, put, etc.
router.get('/', ...);

// Applying router for server
app.use(router);

// You can also create nested routers
const subRouter = new Router("/new");
router.use(subRouter);

...
app.listen(3000, () => {  
  console.log("Server has been started on port 3000");  
});
```

## Route params
```JS
...
// You can specify route params using {name} syntax
router.get("/{id}", (req, res) => {
  const { id } = req.params;
  res.status(200).json({ message: "Hello world " + id })
});
...
```

## Additinal handlers for routes
```JS
...
// You can throw error here and then handle it inside
// global app 'error' handler
const validateParam = (req, res, next) => {
  if (!req.params.id) {
    throw new ValidationError('Id is not specifiend')
  }
}
const validateBody = ...;

// You can apply additional handler to validate request
// or to do some extra work before each route
router.get('/', validateParam, validateBody, handler);
...
app.error(req, res, error) => {
  if (error instanceof ValidationError) {
    res.status(400).json({ message: error.message });
  } else {
    res.status(500).json({ message: error.message });
  }
});
```

## API

### Request
- `url` - request url;
- `method` - request method;
- `params` - parsed params from route url (**/{name}** - **name** is a parameter);
- `query` - parsed query params from route url (**/user?id=2&test=3** - **id** and **test** are query params);
- `body` - parsed JSON body. Server accepts **only JSON** format;
- `httpRequest` - base request object from node.js **http** module.

### Response
- `status(statusCode)` - sets response status code;
- `send(message)` - sends respose with some text back to user;
- `json(data)`  - sends respose with data in JSON format;
- `html(htmlData)` - sends respose with html;
- `setHeader(headerKey, headerValue)` - sets header to reponse;
- `isFinished` - check if response was already sended. Not needed to trigger by you, but maybe there will be some edge cases;
- `httpResponse` - base response object from node.js **http** module.

### Server
- `httpServer` - base server object from node.js **http** module;
- `listen(port, callback)` - starts server on port;
- `close(callback)` - stops server;
- `|get, post, put, patch, delete|(url, ...handlers)` - adds handlers by url and selected method;
- `notFound(callback)` - runs callback when route is not found;
- `error(callback)` - runs callback when error was thrown inside route handler;
- `middleware(...handlers)` - adds middleware to the server.

### Router
- `constructor(prefix)` - creates router with prefix;
- `use(router)` - adds nested router into current router;
- `|get, post, put, patch, delete|(url, ...handlers)` - adds handlers by url and selected method;
- `middleware(...handlers)` - adds middleware to all routes inside router.

## Release History

* 0.2.0 - Added support of next() inside route handlers
* 0.1.2 - Package renaming 
* 0.1.1 - Added example of usage
* 0.1.0 - Initial release, working functionality