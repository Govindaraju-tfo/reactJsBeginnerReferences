# HTML Visual studio plugins - https://marketplace.visualstudio.com/
      Auto Rename Tag 
      Bracket Pair Colorizer 2
      HTML Snippets
      Prettier - Code formatter
      Live Server

## HTML Block and Inline Elements
https://www.w3schools.com/html/html_blocks.asp


# reactJsBeginnerReferences
reference link to begin with nodejs, reactjs

## Download node js
https://nodejs.org/en/

## Node.js Global Objects
https://www.geeksforgeeks.org/node-js-global-objects/


## Require keyword https://flexiple.com/javascript-require-vs-import/
https://www.geeksforgeeks.org/node-js-npm-node-package-manager/

  - NPM (Node Package Manager) is the default package manager for Node.js and is written entirely in Javascript. 
  - The required packages and modules in Node project are installed using NPM.

## serve
https://www.npmjs.com/package/serve
    Assuming you would like to serve a static site, single page application or just a static file (no matter if on your device or on the local network), this package is just the right choice for you.

    Once it's time to push your site to production, we recommend using Vercel.

    In general, serve also provides a neat interface for listing the directory's contents:



## How is require() different from import()

### 1) require()
In NodeJS, require() is a built-in function to include external modules that exist in separate files. require() statement basically reads a JavaScript file, executes it, and then proceeds to return the export object. require() statement not only allows to add built-in core NodeJS modules but also community-based and local modules.

### 2) import()
  import() & export() statements are used to refer to an ES module. Other modules with file types such as .json cannot be imported with these statements. They are permitted to be used only in ES modules and the specifier of this statement can either be a URL-style relative path or a package name. Also, the import statement cannot be used in embedded scripts unless such script has a type="module". A dynamic import can be used for scripts whose type is not “module”


## How do I create a HTTP server?
https://nodejs.org/en/knowledge/HTTP/servers/how-to-create-a-HTTP-server/
    const http = require('http');

    const requestListener = function (req, res) {
      res.writeHead(200);
      res.end('Hello, World!');
    }

    const server = http.createServer(requestListener);
    
    //(or)
    //const server = http.creatServer((req,res)=> {} )
    
    server.listen(8080);
    
   
## What are Streams?
https://www.tutorialspoint.com/nodejs/nodejs_streams.htm

## express js
https://expressjs.com/en/5x/api.html

## express js Routing
https://expressjs.com/en/guide/routing.html

## Express.js express.json() Function
https://www.geeksforgeeks.org/express-js-express-json-function/

## Model-Routes-Controllers-Services Directory Structure
https://sodocumentation.net/node-js/topic/10785/route-controller-service-structure-for-expressjs

## serve-index
http://expressjs.com/en/resources/middleware/serve-index.html

## DDOS prevention/ Save API's from DDoS Attack
https://www.npmjs.com/package/express-rate-limit

## Generating Errors using HTTP-errors module in Node.js
https://www.geeksforgeeks.org/generating-errors-using-http-errors-module-in-node-js/

# Express.js File Upload
https://www.javatpoint.com/expressjs-file-upload
# NODEMAILER
https://nodemailer.com/about/


# How to read environment variables from Node.js
https://nodejs.dev/learn/how-to-read-environment-variables-from-nodejs

## How to Validate Data using joi Module in Node.js ?
https://www.geeksforgeeks.org/how-to-validate-data-using-hapi-joi-module-in-node-js/

## get-port
https://www.npmjs.com/package/get-port

# mongoose Middleware
https://mongoosejs.com/docs/middleware.html
Middleware (also called pre and post hooks) are functions which are passed control during execution of asynchronous functions. Middleware is specified on the schema level and is useful for writing plugins.

## Store Passwords In MongoDB With Node.js, Mongoose, & Bcrypt
https://coderrocketfuel.com/article/store-passwords-in-mongodb-with-node-js-mongoose-and-bcrypt


# node-jsonwebtoken
https://github.com/auth0/node-jsonwebtoken

# RS256 key
### Online RSA Key Generator
https://travistidwell.com/jsencrypt/demo/
https://randomkeygen.com/

### Node-RSA
https://www.npmjs.com/package/node-rsa

## node js crypto
  var crypto = require('crypto');

  var prime_length = 60;
  var diffHell = crypto.createDiffieHellman(prime_length);

  diffHell.generateKeys('base64');
  console.log("Public Key : " ,diffHell.getPublicKey('base64'));
  console.log("Private Key : " ,diffHell.getPrivateKey('base64'));

  console.log("Public Key : " ,diffHell.getPublicKey('hex'));
  console.log("Private Key : " ,diffHell.getPrivateKey('hex'));


# redis-commander
https://www.npmjs.com/package/redis-commander
  Redis web management tool written in node.js

