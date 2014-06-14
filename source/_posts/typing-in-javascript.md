---
title: "Typing in JavaScript?"
categories : ['code']
tags: ["nodejs","javascript"]
date: 2012/09/22
---

"Strong typing is for people with weak memories" - not sure who originally said that, but you have got to love the enthusiasm people have for dynamic languges. What is there not to love? powerful basic language constructs such as closure, prototypal inheritance, objects as first class citizens and anonymous functions, mainly, with which you can simulate almost anything static languages have to offer!

Proof you say? no problem! here are few implementations that are worthy of noting:

**Compile time type checking** https://developers.google.com/closure/compiler/docs/js-for-compiler#types

**Classic OO inheritance** http://mootools.net/docs/core/Class/Class & http://coffeescript.org/#classes

(if you have other interesting examples let me know!)

### Convention over configuration

The combination of CoffeeScript's "classical" Class like system and Backbone.JS's MVC style structure, give (IMHO) the best value in terms of complexity and pure fun writing apps. The challenges I come across while building large complex applications is the inherited nature of a true dynamic language  "type_lessness_".

The ugly monster, that is -a large application written in JavaScript, pushed me into struggling with these two main challenges:

 * Many-to-many PubSub communication
 * 3rd party API's

### Many-to-many PubSub communication

While the PubSub (also known as observer pattern) in JavaScript is a powerful tool to decouple your code, having multiple callers and listeners on the same event makes it really difficult to keep track of the actual common API for these events. Lats try to reflect on this while looking at an actual implementation.

(please excuse my CoffeeScript)


**Pub/Sub implementation in Backbone/Underscore.JS**

```coffee
pubsub = _.extend {}, Backbone.Events
pubsub.on 'myEvent', (param1, param2)->
	x1 = param1 + param2
	# do something with x1

# in some other obscure location in the code
pubsub.on 'myEvent', (param1)->
	x2 = param1
	# do something with x2

# ...
pubsub.trigger 'myEvent', 5
# ...
pubsub.trigger 'myEvent', 5,10
```

The "pubsub.on(event name, callback)" API is very common amongst different JS pub/sub frameworks. here's an example of how a generic implementation might look like:


```coffee
pubsub = {
	_listeners : {}
	on : (channel, callback)->
		@_listeners[ channel ] ?= []
		@_listeners[ channel ].push callback

	trigger : (channel, params...)->
		@_listeners[ channel ]?.forEach (cb)-> cb.apply(null, params)
}

pubsub.on 'myEvent', (param1, param2)->
	x1 = param1 + param2
	# ... do something with x1..

# in some other obscure location in the code
pubsub.on 'myEvent', (param1)->
	x2 = param1
	# .... do something with x2

# ...
pubsub.trigger 'myEvent', 5
# ...
pubsub.trigger 'myEvent', 5,10
```


If your using the very popular Socket.IO library, for realtime client/server communication, you are using the exact same API, in which case it makes it even more complicated to keep track of, since the ambiguity is manifested on both the client and the server.

### 3rd party API's

This is how a type related bug might look like in a dynamic language

```coffee
a = "42" # returned from some API
a == 42
=> false
```

## Enter "Contractor" & "Backbone-typed"

Contractor
https://github.com/romansky/Contractor

Backbone-typed
https://github.com/romansky/backbone-typed

I'm introducing a convention here, that helps keep the API consistent across the application, the approach here is to create wrapper functions, that are used as both the API documentation and run-time validation facility for required and optional arguments.

Example:

```coffee
# declare the events and their APIs
Messages = {
	LoginEvent : Contractor.Create( "LoginEvent", Contractor.Required("user ID"), Contractor.Optional("additional info") )
	AppErrorEvent : Contractor.Create( "AppErrorEvent", Contractor.Required("user ID"), Contractor.Required("error description"), Contractor.Optional("exception") )
}

# register on event API
pubsub.on Messages.LoginEvent, (userID, info)-> #... do stuff here
pubsub.on Messages.AppErrorEvent, (userID, description, exception)-> #... do stuff here

# trigger the event
pubsub.apply(pubsub, Messages.LoginEvent(112233, "mobile"))
pubsub.apply(pubsub, Messages.LoginEvent(112233))

pubsub.apply(pubsub, Messages.AppErrorEvent(112233, "user did not have access to resource", e))
pubsub.apply(pubsub, Messages.AppErrorEvent(112233)) # since we did not provide the second required argument, this will log an error and return null
```

With NodeJS, I am using the same code on the front and back end, which makes it really easy to manage my Socket.IO communication.

If you have been paying attention, there's still the type issue. Since I work allot with Backbone, it made a lot of sense to write "backbone-typed" which adds optional typing to Backbone-Models.
Example:

```coffee
Wearing = {
	"bracelet" : "bracelet"
	"watch" : "watch"
}

class User extends TypedModel
	defaults: {
		name: null
		email: null
		lotteryNumber: null
		isAwesome: null
	}

	types: {
		name: Types.String
		email: Types.String
		lotteryNumber: Types.Integer
		isAwesome: Types.Boolean
		wearing : Types.Enum(Wearing)
	}

user1 = new User({name: "foo", email: "foo@bar.com", lotteryNumber: 12345, isAwesome: true, wearing: Wearing.watch})
user1.toJSON() #=> {name: "foo", email: "foo@bar.com", lotteryNumber: 12345, isAwesome: true, wearing: "watch"} - nothing special going on here..

user2 = new User({name: "foo", email: "foo@bar.com", lotteryNumber: "54321", isAwesome: "false", wearing: "thong"})
user2.toJSON() #=> {name: "bar", email: "bar@foo.com", lotteryNumber: 54321, isAwesome: false, wearing: null} - shit happens!

user2.set({wearing: Wearing.bracelet})

if user2.get("lotteryNumber") == 54321 and user2.get("wearing") == Wearing.bracelet then user2.set({isAwesome: "true"})
user2.toJSON() #=> {name: "bar", email: "bar@foo.com", lotteryNumber: 54321, isAwesome: true, wearing: "bracelet"} - awesome for sure..
```

If you want to know a little more about how these work, please visit the respective Git repositories, and let me know what you think!
