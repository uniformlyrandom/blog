---
title: "HLD High Level Design"
categories: ['design']
tags: ["design"]
date: 2012/03/07
---


This post is a follow-up on ["Bootstrapping A New Project"](/code/2012/02/07/bootstrapping-a-new-project/)

#### Previously...

I've written about ["User Stories"](/code/2012/02/25/user-stories/), a tool that helps with expectation setting when working with (mostly) non-technical stakeholders.

#### Designing Toward

 - **database** (schema, tables, indexes..)
 - **application layout**
 - **deployment**
 - **modules**
 - **internal and external APIs**
 - **application infra** (frameworks, tools etc..)

#### Design Tools At Your Disposal

 - **system architecture** ([example-Lucidchart](http://www.lucidchart.com/publicSegments/view/4f6e3aaf-0798-4bce-97ca-386a0a5a8951/image.png)) - big pieces that make up the system
 - **activity diagram** ([more info](http://www.agilemodeling.com/artifacts/activityDiagram.htm)) - communication between components in the system
 - **sequence diagram** ([example-Lucidchart](http://www.lucidchart.com/publicSegments/view/4f6e3ae9-8544-46f2-a469-1daf0ac9c29f/image.png), [more info](http://www.agilemodeling.com/artifacts/sequenceDiagram.htm)) - algorithms / processes high level implementation
 - **state diagram** ([more info](http://www.agilemodeling.com/artifacts/stateMachineDiagram.htm)) - state minded processes / algorithms
 - **mock ups / wire frames** ([example-Google Draw](https://docs.google.com/drawings/pub?id=1y-GFJZeIcqSIk2hFcf9SWjWnpemvaBU-ffdPjv1u4hs&w=960&h=720)) - for projects with GUIs

#### Audiences

 - **System Architect** - mostly interested in the software stack, deployment and the database design
 - **Team Members** - mostly interested in the more lower level stuff like modules and API's
 - **Other Teams** - mostly interested in API's

Feedback on design is great for everyone involved, setup meetings with relevant parties to do DR (design review)

#### UML and Other Vegetables

UML is complex, you don't need to know it perfectly, start with what makes sense, and slowly pick up tutorials. Remember, its a visual tool that helps you communicate, not everyone who will look at your designs has the time (or cares for) to read throughly a book about UML and its specifics. In Einstein's words "Make everything as simple as possible, but not simpler." 

#### Disclosure

Design does *not* last. Nobody is going to update the design as the system grows and evolves. Having said that, can still be used a good reference.






