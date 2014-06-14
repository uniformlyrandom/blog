---
title: "Bootstrapping A New Project"
categories: ["design"]
tags: []
date : 2012/02/07
---

### My take on: How to give your new project a good starting point

So, you have a new shiny project on your lap, so now what?  
Today everyone is talking about "agile", and how its better then the old "waterfall" methodology of doing things

#### Waterfall
The "Waterfall" approach is notorious in the software community, some highlights of why this is the case:

 * It assumes that the initial requirements will not change
 * There is only one version delivered to the stakeholders, at the end of the process
 * All the planning and "thinking" happen only in the beginning of the project

#### Agile
Everyone likes "Agile", its the "cool" way to manage a project, it asks for less paperwork, less work upfront, built from the ground up to support changes, to name a few conceptions about it.
While I call it "Agile" here, in reality it is a world of sub cultures, some of which are: "XP" (eXtreme Programming), "scrum", "Kanban" to name a few.
The difference between specific cultures is not always clear, we don't necessarily have to stick to one, we can mix and match, like I am doing here, thus from this point onward I will just ignorantly call it "Agile" with out specifying the specific methodology.
The main strengths going for the Agile methodology are

 * Short upfront work phase (requirements, design ..)
 * Iterations - project is broken down to deliverables, which is translated to real value delivered very early in the project lifetime
 * Changes are welcome and expected and are introduced into following iterations (bugs found in previous deliverables, design "inadequacies")

Doing things in small pieces is great, just ask a WEB developer, how there's nothing quite like writing a bit of code, refreshing the page and seeing the changes reflected, especially when working on a big complex project. This gives you a feeling of progress and achievement, even though the end goal is far far away..

Some Agile approaches talk beyond writing business code and going into proactive bug searching using TDD (Test Driven Development), which turns out to be a good fit to the overall process as it secures quality of delivered pieces of logic.
BDD (Behavior Driven Development) is very similar to TDD, but instead of going after "test coverage" it takes on a higher level, business specification driven approach, where the business embodied user stories are part of the actual test suite.

#### My recipe for success

 * User Stories
 * Mock-ups (in the case of an app that has UI)
 * HLD (High Level -system component- Design)
 * Process Sequence diagram / Workflow diagram (possibly atop HLD)

#### Tooling

Tools that keep me productive:

 * Google Document (User Stories)
 * Google Drawing (mockups, diagrams, HLD)
 * Lucidchart (diagrams)

In addition, I use a light weight web based project management tool as a central place to keep reference to important docs and user stories, and see my progress (and others working on the project). A great tool for the job is [Trello.com](http://www.trello.com) - which free and super simple!

#### REATED UPDATES:
[User Stories](/code/2012/02/25/user-stories/)
