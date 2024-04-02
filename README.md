# JSON Server

![](https://media.dev.to/cdn-cgi/image/width=1000,height=400,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fufu6exnzkz80auwf205h.png)

*JSON Server is a simple npm package that allows you to quickly set up a RESTful API server with just a JSON file as your database.*

*It's a handy tool for prototyping, mocking APIs, or even building simple demo servers.*

*With JSON Server, you can easily define endpoints and manipulate data using simple conventions.* 

---

### Setup
Install JSON Server via npm:

```
npm install json-server
```

<p>Create a db.json file in your project directory with your desired data structure.</p>

<p>For example:</p>

```
{
  "products": [
    { "id": 101, "name": "Product A", "price": 50,"categoryId": 1 },
    { "id": 102, "name": "Product B", "price": 100, "categoryId": 2 },
    { "id": 103, "name": "Product C", "price": 150, "categoryId": 1 }
  ],
  "categories": [
    { "id": 1, "name": "Category A" },
    { "id": 2, "name": "Category B" }
  ]
}
```

---

### Start the JSON Server with your db.json file:

```
npm run serve-json` or `json-server --watch db.json
```

<p>Now you can access your mock API endpoints.</p>

<p>For example:</p>


### Fetching Specific Data
To fetch a specific product with ID 101, you can use:

*Use . to access deep properties*

```
http://localhost:3000/products?id=101
```

---

### Sorting Data
To sort products by price in ascending order:

```
http://localhost:3000/products?_sort=price&_order=asc
```

For multiple fields, use the following format:

```
GET /posts?_sort=user,views&_order=desc,asc
```

---

### Descending order:
```
http://localhost:3000/products?_sort=price&_order=desc
```

---

### Pagination
Use _page and optionally _limit to paginate returned data.

In the Link header you'll get first, prev, next and last links.

10 items are returned by default

Add _start and _end or _limit (an X-Total-Count header is included in the response)


```
http://localhost:3000/products?_page=1&_limit=2
```

```
http://localhost:3000/products?_start=0&_end=10
```

```
http://localhost:3000/products?_start=0&_limit=5
```

---

### Filtering Data
Filter products based on price range:

Add _gte or _lte for getting a range


```
http://localhost:3000/products?price_gte=100&price_lte=500
```

---

### Excluding Specific Data
Exclude products with ID 101:

Add _ne to exclude a value


```
http://localhost:3000/products?id_ne=101
```

---

### Using Operators
Perform operations like string matching:

Add _like to filter (RegExp supported)

```
http://localhost:3000/products?name_like=^S
```

---

### Full Text Search
Search for products containing the term "jeans":

```
http://localhost:3000/products?q=jeans
```
---

### Handling Relationships
Retrieve categories with embedded products:

To include children resources, add `_embed`

```
GET /posts?_embed=comments
GET /posts/1?_embed=comments
```

To include parent resource, add `_expand`

```
GET /comments?_expand=post
GET /comments/1?_expand=post
```

```
http://localhost:3000/categories?_embed=products
```


### Database

```
GET /db
```

### Homepage

Returns default index file or serves `./public` directory

```
GET /
```

## Extras

### Static file server

You can use JSON Server to serve your HTML, JS and CSS, simply create a `./public` directory
or use `--static` to set a different static files directory.

```bash
mkdir public
echo 'hello world' > public/index.html
json-server db.json
```

```bash
json-server db.json --static ./some-other-dir
```

### Alternative port

You can start JSON Server on other ports with the `--port` flag:

```bash
$ json-server --watch db.json --port 3004
```

### Access from anywhere

You can access your fake API from anywhere using CORS and JSONP.

### Remote schema

You can load remote schemas.

```bash
$ json-server http://example.com/file.json
$ json-server http://jsonplaceholder.typicode.com/db
```

### Generate random data

Using JS instead of a JSON file, you can create data programmatically.

```javascript
// index.js
module.exports = () => {
  const data = { users: [] }
  // Create 1000 users
  for (let i = 0; i < 1000; i++) {
    data.users.push({ id: i, name: `user${i}` })
  }
  return data
}
```

```bash
$ json-server index.js
```

__Tip__ use modules like [Faker](https://github.com/faker-js/faker), [Casual](https://github.com/boo1ean/casual), [Chance](https://github.com/victorquinn/chancejs) or [JSON Schema Faker](https://github.com/json-schema-faker/json-schema-faker).

### HTTPS

There are many ways to set up SSL in development. One simple way is to use [hotel](https://github.com/typicode/hotel).

### Add custom routes

Create a `routes.json` file. Pay attention to start every route with `/`.

```json
{
  "/api/*": "/$1",
  "/:resource/:id/show": "/:resource/:id",
  "/posts/:category": "/posts?category=:category",
  "/articles?id=:id": "/posts/:id"
}
```

Start JSON Server with `--routes` option.

```bash
json-server db.json --routes routes.json
```

Now you can access resources using additional routes.

```sh
/api/posts # → /posts
/api/posts/1  # → /posts/1
/posts/1/show # → /posts/1
/posts/javascript # → /posts?category=javascript
/articles?id=1 # → /posts/1
```

### Add middlewares

You can add your middlewares from the CLI using `--middlewares` option:

```js
// hello.js
module.exports = (req, res, next) => {
  res.header('X-Hello', 'World')
  next()
}
```

```bash
json-server db.json --middlewares ./hello.js
json-server db.json --middlewares ./first.js ./second.js
```

### CLI usage

```
json-server [options] <source>

Options:
  --config, -c       Path to config file           [default: "json-server.json"]
  --port, -p         Set port                                    [default: 3000]
  --host, -H         Set host                             [default: "localhost"]
  --watch, -w        Watch file(s)                                     [boolean]
  --routes, -r       Path to routes file
  --middlewares, -m  Paths to middleware files                           [array]
  --static, -s       Set static files directory
  --read-only, --ro  Allow only GET requests                           [boolean]
  --no-cors, --nc    Disable Cross-Origin Resource Sharing             [boolean]
  --no-gzip, --ng    Disable GZIP Content-Encoding                     [boolean]
  --snapshots, -S    Set snapshots directory                      [default: "."]
  --delay, -d        Add delay to responses (ms)
  --id, -i           Set database id property (e.g. _id)         [default: "id"]
  --foreignKeySuffix, --fks  Set foreign key suffix, (e.g. _id as in post_id)
                                                                 [default: "Id"]
  --quiet, -q        Suppress log messages from output                 [boolean]
  --help, -h         Show help                                         [boolean]
  --version, -v      Show version number                               [boolean]

Examples:
  json-server db.json
  json-server file.js
  json-server http://example.com/db.json

https://github.com/typicode/json-server
```

You can also set options in a `json-server.json` configuration file.

```json
{
  "port": 3000
}
```

### Module

If you need to add authentication, validation, or __any behavior__, you can use the project as a module in combination with other Express middlewares.

#### Simple example

```sh
$ npm install json-server --save-dev
```

```js
// server.js
const jsonServer = require('json-server')
const server = jsonServer.create()
const router = jsonServer.router('db.json')
const middlewares = jsonServer.defaults()

server.use(middlewares)
server.use(router)
server.listen(3000, () => {
  console.log('JSON Server is running')
})
```

```sh
$ node server.js
```

The path you provide to the `jsonServer.router` function  is relative to the directory from where you launch your node process. If you run the above code from another directory, it’s better to use an absolute path:

```js
const path = require('path')
const router = jsonServer.router(path.join(__dirname, 'db.json'))
```

For an in-memory database, simply pass an object to `jsonServer.router()`.

To add custom options (eg. `foreginKeySuffix`) pass in an object as the second argument to `jsonServer.router('db.json', { foreginKeySuffix: '_id' })`.

Please note also that `jsonServer.router()` can be used in existing Express projects.

#### Custom routes example

Let's say you want a route that echoes query parameters and another one that set a timestamp on every resource created.

```js
const jsonServer = require('json-server')
const server = jsonServer.create()
const router = jsonServer.router('db.json')
const middlewares = jsonServer.defaults()

// Set default middlewares (logger, static, cors and no-cache)
server.use(middlewares)

// Add custom routes before JSON Server router
server.get('/echo', (req, res) => {
  res.jsonp(req.query)
})

// To handle POST, PUT and PATCH you need to use a body-parser
// You can use the one used by JSON Server
server.use(jsonServer.bodyParser)
server.use((req, res, next) => {
  if (req.method === 'POST') {
    req.body.createdAt = Date.now()
  }
  // Continue to JSON Server router
  next()
})

// Use default router
server.use(router)
server.listen(3000, () => {
  console.log('JSON Server is running')
})
```

#### Access control example

```js
const jsonServer = require('json-server')
const server = jsonServer.create()
const router = jsonServer.router('db.json')
const middlewares = jsonServer.defaults()

server.use(middlewares)
server.use((req, res, next) => {
 if (isAuthorized(req)) { // add your authorization logic here
   next() // continue to JSON Server router
 } else {
   res.sendStatus(401)
 }
})
server.use(router)
server.listen(3000, () => {
  console.log('JSON Server is running')
})
```
#### Custom output example

To modify responses, overwrite `router.render` method:

```javascript
// In this example, returned resources will be wrapped in a body property
router.render = (req, res) => {
  res.jsonp({
    body: res.locals.data
  })
}
```

You can set your own status code for the response:


```javascript
// In this example we simulate a server side error response
router.render = (req, res) => {
  res.status(500).jsonp({
    error: "error message here"
  })
}
```

#### Rewriter example

To add rewrite rules, use `jsonServer.rewriter()`:

```javascript
// Add this before server.use(router)
server.use(jsonServer.rewriter({
  '/api/*': '/$1',
  '/blog/:resource/:id/show': '/:resource/:id'
}))
```

#### Mounting JSON Server on another endpoint example

Alternatively, you can also mount the router on `/api`.

```javascript
server.use('/api', router)
```

#### API

__`jsonServer.create()`__

### Returns an Express server.

__`jsonServer.defaults([options])`__



### Returns middlewares used by JSON Server.

* options
  * `static` path to static files
  * `logger` enable logger middleware (default: true)
  * `bodyParser` enable body-parser middleware (default: true)
  * `noCors` disable CORS (default: false)
  * `readOnly` accept only GET requests (default: false)

__`jsonServer.router([path|object], [options])`__

Returns JSON Server router.

* options (see [CLI usage](#cli-usage))