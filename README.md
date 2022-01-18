# reactJsBeginnerReferences
reference link to begin with nodejs, reactjs

## Download node js
https://nodejs.org/en/

## Node.js Global Objects
https://www.geeksforgeeks.org/node-js-global-objects/


## Node.js | NPM (Node Package Manager)
https://www.geeksforgeeks.org/node-js-npm-node-package-manager/

  - NPM (Node Package Manager) is the default package manager for Node.js and is written entirely in Javascript. 
  - The required packages and modules in Node project are installed using NPM.


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
