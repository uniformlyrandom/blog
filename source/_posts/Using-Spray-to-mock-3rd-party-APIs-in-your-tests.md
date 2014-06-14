title: 'Using Spray to mock 3rd party APIs in your tests'
date: 2014-06-14 20:26:44
tags: ["scala","spray","testing"]
---

If you need to test/mock 3rd party API's / a crawler / external web service, and you dont want to use some fancy DI framework (I hate the magic in DI frameworks, I use constructor DI instead..), anyway, here's a helper class I put togather to that end

If you haven't got a chance to try out Spray, you totally should, its a high performance web server built on top of Netty and Akka

Spray has a very nice HTTP API. Few things I liked:
 - HTTP method mapping `spray.http.HttpMethods` - `GET`, `POST` etc.. 
 - `HttpRequest` and `HttpResopnse` are basically wrappers around data you would expect to have in a request and response data
 - `spray.http.Uri` wrapper class around what you would expect to find in the URI
 - it really is just an Akka `Actor`, with just the right amount of control exposed and the rest well hidden behind its nice APIs

So, for testing/mocking all I really need is: 

 - HTTP method
 - URI
 - body(content) 

### Here's an implementation of the helper class

```scala
import akka.actor._
import akka.io.IO
import java.util.concurrent.atomic.AtomicInteger
import spray.can.Http
import spray.http.{Uri, HttpMethod, HttpResponse, HttpRequest}
 
object Helpers {
 	
	lazy implicit val testSystem = ActorSystem("helpers-test-system")
	// used to track the last assigned port
	val lastPort = new AtomicInteger(20000)
 	// a single expected triplet of method+uri+body
	type Replay = (HttpMethod,Uri.Path,String)
 
	// factory method to get a replater to play around with
	def webReplayer(replay : List[Replay]) : (ActorRef,Int) = {
		// everytime the factory is used a new port is assigned, 
		// so many instances can co-exist and tests can run in parallel without getting
		// on eachother's toes
		val newPort = lastPort.addAndGet(1)
		// instantiate a new Spray server. Yes, its that simple!
		val newServer = testSystem.actorOf(Props(new Helpers.ReplayerActor(replay)))
		IO(Http) ! Http.Bind(newServer, interface = "127.0.0.1", port = newPort)
		// return a pair of the server instance and the assigned port
		// the newPort should be used by client code to construct the remote URI with
		(newServer,newPort)
	}
 
	// object to use to tell the server to shutdown..
	object ShutTestServer
 
	// a concrete implementation for a Spray Actor to listen to incoming HttpRequest(..) messages
	// provided a list of messages to be matched against arrived and content to be sent back
	class ReplayerActor(var replay :List[Replay]) extends Actor {
		var ioServer : ActorRef = null
		override def receive: Receive = {
			case _ : Http.Connected =>
				sender ! Http.Register(self)
				ioServer = sender()
			// this is basically a "catch all" HttpRequest pattern
			case HttpRequest(method, uri,_,_,_) =>
				// a test to see that this is indeed expected to happen at this point
				if (!replay.isEmpty && replay.head._1 == method && uri.path == replay.head._2){
					sender ! HttpResponse(entity = replay.head._3)
					replay = replay.tail
				} else {
					sender ! HttpResponse(status = 400, entity = "was expecting a different request at this point")
				}
			case ShutTestServer => ioServer ! PoisonPill
			case _ => //ignore
		}
	}
}
```

### And now a cocrete client code example to use this tool

```scala
// some random content I would like to receive in this scenario (or test..)
val content =
s"""
  |<html>
  |<head>
  |<title>sometitle</title>
  |</head>
  |<body>some body</body>
  |</html>
""".stripMargin
 
// we use the factory method to get an instance of "replayer" to play around with
val (_testWebServer,port) = Helpers.webReplayer(
	List(
		(GET,Uri.Path("/somesite"),content)
	)
)
 
// and where ever in your actual code, use the port we got back above and have it "hit" the server
val whatEver = YourHTTPGetter.fetch(s"http://127.0.0.1:$port/somesite")
assert(whatEver == content)
```
Profit!
