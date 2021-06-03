# CIT281 Project 3

Purpose of this Project:
- To gain experience interpreting functional description and specifications to complete an assignment 
- Practice refacotring using modern JS syntax 
- Practice creating and using code modules 
- Gain expeirence using Fastify with the GET verb, routes, and query parameters
- Gain experience loading a file and using as a web page 

Overview:
- Create a "coinage" code module that is capable of calculating the total value of coin objects. 

### Project Tasks:

1. Valid Denomination(Coin)
- Returns true if the coin function parameter is a valid coin value of either 1,5,10,25,50, or 100
- Must use the array indexOf() Method and !== Equality operator 
```
function validDenomination(coin) {
  return [1, 5, 10, 25, 50, 100].indexOf(coin) !== -1;
}
```
2. ValueFromCoinObject(obj)
- Returns the calculated value of a single coin object from the obj function parameter
- Must use object deconstruction to create constant variables denom and count from the obj function parameter, using default object values of 0 for denom and count
```
function valueFromCoinObject(obj) {
  const { denom = 0, count = 0 } = obj;
  return validDenomination(denom) ? denom * count : 0;
}
```
3. valueFromArray(arr)
- iterates through an array of coin objects and returns the final calculated value of all coin objects
- Must use Array.reduce() method, and an arrow function with the Array.reduce() method
- Must call valueFromCoinObject()
```
function valueFromArray(arr) {
  return arr.reduce(
    (acc, val) =>
      Array.isArray(val) ? valueFromArray(val) : acc + valueFromCoinObject(val),
    0
  );
}


module.exports = {
  coinCount,
};

```

4. coinCOunt(...coinage)
- Calls and returns the result of valueFromArray() function, which will be the value of all coin objects with the coinage array function parameter
```
function coinCount(...coinage) {
  return valueFromArray(coinage);
}
console.log("{}", coinCount({denom: 5, count: 3}));
```

5. Web server code
```
const fs = require("fs");
const fastify = require("fastify")();
const { coinCount } = require("./p3-module.js");

fastify.get("/", (request, reply) => {
  fs.readFile(`${__dirname}/index.html`, (err, data) => {
    if (err) {
      reply
        .code(500)
        .header("Content-Type", "text/html; charset=utf-8")
        .send("<h1>Server error.</h1>");
    } else {
      reply
        .code(200)
        .header("Content-Type", "text/html; charset=utf-8")
        .send(data);
    }
  });
});
fastify.get("/coin", (request, reply) => {
  const { denom = 0, count = 0 } = request.query;
  const coinValue = coinCount({
    denom: parseInt(denom),
    count: parseInt(count),
  });
  reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(
      `<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`
    );
});
fastify.get("/coins", (request, reply) => {
  let coinValue = 0;
  const coins = [
    { denom: 25, count: 2 },
    { denom: 1, count: 7 },
  ];
const { option } = request.query;
  switch (option) {
    case "1":
      coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });
      break;
    case "2":
      coinValue = coinCount(...coins);
      break;
    case "3":
      coinValue = coinCount(coins);
      break;
  }
  reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(
      `<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`
    );
});

// Start server and listen to requests using Fastify
const listenIP = "localhost";
const listenPort = 8080;
fastify.listen(listenPort, listenIP, (err, address) => {
  if (err) {
    console.log(err);
    process.exit(1);
  }
  console.log(`Server listening on ${address}`);
});
```

### What I learned:
- Learned to use code module and update the "server" with my own code file using fastify package. the web page is able to test my own code. 
