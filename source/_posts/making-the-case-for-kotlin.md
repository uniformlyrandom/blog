---
title: "Making The Case For Kotlin"
categories: ["code"]
tags: ["kotlin","scala"]
date : 2012/11/14
---

The Kotlin project page has this to say about Kotlin: "is a statically typed programming language that compiles to JVM byte codes and JavaScript." [project page](http://kotlin.jetbrains.org/)

Kotlin is a very interesting project, I wanted to check it out, so I decided to write this blog post exploring some design aspects behind Kotlin, and specifically how it compares to the leading JVM "hacker" language - Scala.

## Who Is Behind Kotlin?

Even though there are several decent free IDE's available today (Eclipse, NetBeans, Visual Studio Express..), it (could be) surprising that JetBrains is able to rack in between 199$ for a personal license and up to 699$ for a commercial license (for companies) including 1 year update subscription (I have indeed bought one personal license for my development needs). If you are developing on top of VisualStudio you are (most) probably familiar with the 250$+ plugin ReSharper.

I would argue that this makes them a good candidate, with insight into good and bad language features, but lets not get ahead of ourselves and set the expectations too high.

## Why Another JVM Language?

JVM is a well known platform, its weaknesses and scalability strategies are available and documented well on the web, its being used as classic Java run-time VM and for some other popular languages like Scala, Groovy, Clojure and others. Many modules have been already written in Java, normally JVM run languages are compatible with the existing Java ecosystem. 

## Why Static Typing?

In the old (and still ongoing) battle of dynamic and static languages, the consensus is that dynamic languages are faster and easier way to get things done, on the static languages side they allow programmers harness the power of intelligent IDEs to scale up and maintain their code. Type-inferred languages try to find a middle ground approach so that you don't need to write so much boilerplate code (cue JAVA) to support the static typing, while having the benefits of a full compile time type enforcement.

## Finally, Kotlin Vs. Scala - Fight!

Scala today is the fastest growing JVM language and is probably competing for the same crowd as Kotlin, Kotlin put up a comparison page [link](http://confluence.jetbrains.net/display/Kotlin/Comparison+to+Scala) so we will simply go over the omissions part and try to make sense of it.

### OMITTED - Implicit conversions, parameters

*What is it?* Scala implicits is (IMHO) a single biggest weakness in the language, once they are used, along with the ability to boast symbols for syntax, it quickly stops being human readable and more like a DSL. Which requires many trips to the docs or even worse - the source code.

`SBT` (Scala Build Tool) is the Maven/Ant of the Scala world [link](http://www.scala-sbt.org/release/docs/Examples/Full-Configuration-Example.html), its very difficult to get things done with it without many trips to google/documentation

```scala
lazy val pricing = Project (
    "pricing",
    file ("cdap2-pricing"),
    settings = buildSettings ++ Seq (libraryDependencies ++= pricingDeps)
  ) dependsOn (common, compact, server)
```

`Lift` Is a popular web framework, here's a snippet I took from one of its documentation pages [link](http://cookbook.liftweb.net/Plain+old+form+processing.html) (see the `"#result *" #> x` part)

```scala
def render = inputParam match {
  case Full(x) => 
    println("Input is: "+x)
    "#result *" #> x
      
  case _ =>  
    println("No input present! Rendering input form HTML")
    PassThru  
}
```


*Thoughts - * Indeed for many people this is the part that makes Kotlin a better "scalable" language, by not allowing programmers and libraries hinder the code unreadable without an IDE

### OMITTED - Overridable Type Members

*What is it?* Type members in Scala allow you to manage local generic types by assign a type to a class/abstract class/trait member

```scala
abstract class HasTypedMember {
  type typeMe
}
```

An extending class will provide a specific type for this type type member

```scala
class ExtendingClass extends HasTypedMember {
  type typeMe = Int
}
```

*Thoughts - * Frankly, its not really clear why Kotlin decided to omit having typed members, I could be missing something.. Never the less Kotlin usually takes the minimalistic and simplistic route, my guess is that if this is indeed useful, the door is open to add it later..

### OMITTED - Path-dependent types

*What is it?* Pointing to a class under another class in Java would mean a specific type (Parent.Child), well not so in Scala. Scala makes the nested class of two same class instances different, much like you would expect two members of two different instances of a single class. Confused? here is some code to explain this better

Java:

```java
public class Outer {
  public class Inner {}
  public void useInner(Outer.Inner inner){...}
}
//.. somewhere in the code
o1 = new Outer()
o2 = new Outer()
i1 = new o1.inner()
i2 = new o2.inner()
// this would work fine
o1.useInner(i2) // works
o2.useInner(i1) // works
```

Scala:
```scala
class Outer {
  class Inner {}
  def useInner(inner : Inner){...}
}
//.. somewhere in the code..
val o1 = new Outer
val o2 = new Outer
val i1 = new o1.Inner
val i2 = new o2.Inner
o1.useInner(i1) // works fine
o2.useInner(i2) // works fine
o1.useInner(i2) // THROWS type mismatch exception!
```

*Thoughts - * It makes some sense that the path to a type that can only exist in an instance is enforced as such.. It would be interesting to know why Kotlin decided to implement this feature in a similar way to Java (conservative?)

### OMITTED - Existential types

*What is it?* This is yet another trade-off for Scala to be able to inter-op with Java
Let's see an example:

```scala
// Create a case class with generic type
case class Z[T]( blah:T )
// create an instance and cast it to a type created with existential type annotation
Seq( Z[Int]( 5 ), Z[String]("blah") ) : Seq[Z[X] forSome { type X >: String with Int }]
res1 = Seq[Z[_ >: String with Int]] = List(Z(5), Z(blah))
```

*Thoughts - * Scala supports type variance to describe generic type bounds, Scala designer (Odersky) chose to add existential types support due to three main reasons [link](http://www.artima.com/scalazine/articles/scalas_type_system.html): erasure, raw types (lack of generics) and Java's "wildcard" types.

Kotlin took the more "purist" approach (also known as reified types) of dropping support for erasure, raw types, wildcard types and finally existential types.

### OMITTED - Complicated logic for initialization of traits

*What is it?* Traits are similar to abstract classes, except that you may use several traits on the same class at the same time (like interfaces).

In Scala, traits are constructed before the extended classes

```scala
trait Averager {
  val first : Int
  val second : Int
  val avg : Int = first / second
}
// if we were to naively construct a class like so:
class Naive extends Averager {
  val first = 20
  val second = 10
}
// create instance
> new Naive()
java.lang.ArithmeticException: / by zero
// what happened was that our val "avg" was computed at construction time before seeing the values we put in Naive
```

Alternatively the trait can be used with a pre-initialized anonymous class `val res = new { val first = 20 ; val second = 10 } with Averager`, also the val which depends on other vals (avg in our case) can be made "lazy" with the (obvious) lazy keyword `lazy val avg : Int = first / second`.

As a side note, using def for "avg" could work, but it would mean evaluating whatever is defined under "avg" every-time this method is called.

*Thoughts - * Traits in Scala can have state which could be made tricky. If you read this blog post [link](http://blog.jetbrains.com/kotlin/2011/08/multiple-inheritance-part-2-possible-directions/) you will see Kotlin developers thoughts and a nice insight into what went into designing the inheritance in Kotlin. (don't forget to read the comments!)

### *OMITTED* - Custom symbolic operations

*What is it?* Scala does not have a built in handling of symbols (like =,+,/ etc), they are actually method calls, as you can have symbols used for method names.

*Thoughts - * This may sound useful at first, but can and (arguably) will create very confusing looking code, like we saw before in the `Lift` and `SBT` examples.

### *OMITTED* - Built-in XML

*What is it?* Exactly as it sounds, Scala built in XML native support, so you can do stuff like this

```scala
val holdsXml = <outer><inner>some text</inner></outer>
```

*Thoughts - * Having a built in support for XML as a language feature is interesting, and sometimes useful. An interview [link](http://www.techworld.com.au/article/397692/a-z_programming_languages_from_pizza_scala/) with Odersky reveals that he would consider removing the native XML support in Scala if had written Scala now.


## Bottom Line

Kotlin is an idiomatic language that draws from the many good features in todays leading languages, specifically from Scala which is a leading language on the JVM platform today. We've looked into omissions on Kotlin's part versus Scala

I've very much enjoyed writing this blog post and learning about the evolution that the design of Java/Scala/Kotlin languages, and the choices the respective authors made along the way.

To get a better feeling of Kotlin I might pick it up for a small project, at which point I will surely blog about it some more..
