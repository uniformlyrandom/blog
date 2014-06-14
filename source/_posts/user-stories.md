---
title: "User Stories"
categories: ["design"]
tags: ["design"]
date: 2012/02/25
---

This post is a follow-up on ["Bootstrapping A New Project"](/code/2012/02/07/bootstrapping-a-new-project/)

"User Story" is an important communication tool, it is used between you (the developer) and who ever is making the requirements (the stakeholder).
A "contract" which (unlike one written by a lawyer) is clear, understood and agreed upon.
On our side (development) user stories embody a work unit that can be further broken down and estimated.

Key value points:

 * Language - its key that user stories are written in a language everyone can understand (stakeholders, developers etc..)
 * Perspective - user stories need to be written from the perspective of the "feature user", so they represent real world value
 * Estimation - to help you get better and more precise estimation for the project overall
 * Prioritization - what needs to be delivered soon/later

#### Lets imagine we just got a call from the product manager, where he is telling us about this new survey tool:

 1. It needs to collect answers from users via a link sent to our users by e-mail
 2. It needs to be summarized and sent to ... once a month on the 1st
 3. Managers need to be able to see past surveys

Hmm.. OK, thats pretty clear, but suppose this can be written clearer, lats try using the following template: (not my invention;))
``As a [role], I want [Requirement / Feature], So that [Justigication/ Reason]``

The requirements now look like this:

 1. As an end user, I will be able to follow a link I got via e-mail, so that I can fill a survey and express my opinion
 2. As a manager,  I will receive an e-mail with a digest of surveys, so that I can evaluate performance
 3. As a manager, I will be able to access old surveys, so that I can compare and evaluate performance over time

By reading the above I can clearly understand the required functionality and the **motivation** behind writing it, this gives me a good **starting point**.
Since I am the one to implement this, I will need to provide estimates, so that product can plan ahead and communicate the product road map onward.
Unfortunately, "The Devil is In The Details" as they say, so giving estimates based on points 1,2,3 is still difficult.
Points 2 & 3 are "huge" stories, also known as "epics", we need to further break down these stories, while still using the same language and practices:

 1. As an end user, I will receive an email with a link as a result of some communication with a company, so that I can follow it and fill a survey
 2. As an end user, I get a set of questions to answer, so that my opinion is collected
 3. As a manager,  I will receive an e-mail with a digest of surveys, so that I can evaluate companies performance
 4. As a manager, I will have a survey management portal, so that I can overview previous results and compare to
 5. As a manager, I can select a specific survey, so that I can extract meaningful actionable information

The functionality expected of deliverables from the points above is clear, and now I feel more comfortable estimating the time it will take me to complete each.
Along with the estimation, these can now be prioritized against each other and against other ongoing projects..

### Summery
The beginning of a new project is the perfect time to introduce user stories, it will help with getting the conversation going, further clarifying the scope and requirements.
While the project scope might change, user stories are (optimally) modular, decoupled and encapsulated, this should help with managing changes and their effect on the overall project.


