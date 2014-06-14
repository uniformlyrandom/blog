---
title: "Using Contractor With Child Processes"
category: ["code"] 
tags: ["nodejs"]
date: 2012/10/06
---

`Contractor` is a factory of contracts, that helps with documenting our APIs.
The child_process facility built into node is using `EventEmitter` (v1), and does not support callbacks, so it is a great fit for `Contractor`!
Since I needed it for a distributed worker framework I am working on, I added a helper facility called "Lawyer", it basically reads the contracts and routes them to the provided object.

__Let us take the parent and child example code from Node's official documentation (http://nodejs.org/api/child_process.html) and see how simple it would be to add some documentation.__

Parent

```javascript
var cp = require('child_process');

var n = cp.fork(__dirname + '/sub.js');

n.on('message', function(m) {
  console.log('PARENT got message:', m);
});

n.send({ hello: 'world' });
```

Child

```javascript
process.on('message', function(m) {
  console.log('CHILD got message:', m);
});

process.send({ foo: 'bar' });
```

__Node's child_process facility provied us with a very basic means of communication, we simply call `n.send({any: "thing"})` and it automagically recieved by the forked child.  Let's spice it up with a more RPC message passing style, with contracts and lawyers__

contracts.js

```javascript
var Contractor = require('contractor').Contractor;
exports.ChildPublish = {
	"GreetingResponse" : Contractor.Create("GreetingResponse", Contractor.Required("childs greeting response")).
	"StatusResponse" : Contractor.Create("StatusResponse", Contractor.Required("errors count"), Contractor.Required("number of completed jobs"))
}

exports.ChildSubscribe = {
	"Greeting": Contractor.Create("Greeting", Contractor.Required("greeting word")),
	"StatusQuery" : Contractor.Create("StatusQuery")
}

```


parent.js

```javascript
var contracts = require('./contracts');
var Contractor = require('contractor').Contractor;
var Lawyer = require('contractor').Lawyer;
var cp = require('child_process');

var n = cp.fork(__dirname + '/sub.js');

n.on('message', function(m) {
	Lawyer.Read(m, {
		"GreetingResponse" : function(childResponse){ console.log("child responded:" + childResponse) },
		"StatusResponse" : function(errorCount, numCompletedJobs){ console.log("childs status, errors:"+errorCount+" jobs done:" + numCompletedJobs) }
	});
});
n.send(contracts.ChildSubscribe.Greeting("this is your father!"));
n.send(contracts.ChildSubscribe.StatusQuery());
```

sub.js

```javascript
var contracts = require('./contracts');
var Contractor = require('contractor').Contractor;
var Lawyer = require('contractor').Lawyer;
process.on('message', function(m) {
  Lawyer.Read(m, {
	"Greeting" : function(parentGreeting){ process.send( contracts.ChildPublish.GreetingResponse("Hi "+parentGreeting+" can I have 10$?")) },
	"StatusQuery" : function(){ process.send( contracts.ChildPublish.StatusResponse(0, 42) ) }
 });
});
```

If we were to run these

```javascript
#> node parent.js
child responded:Hi this is your father! can I have 10$?
childs status, errors:0 jobs done:42
```

If you found Contractor / Lawyer interesting and/or useful, I would love to hear about it!
