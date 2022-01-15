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
