---
title: "Reasons to create Unit Tests"
date: 2019-06-14T18:00:00+01:00
publishdate: 2019-06-14T18:00:00+01:00
tags: ["testing"]
comments: false
summary: Writing unit tests for API's is a waste of time. While developing an API it looks so. Today I need to rewrite some calls and all my tests fail. I need to update all the unit tests. If I did not have to write tests, my code was already finished. But that is a mistake. 
---

Writing unit tests for API's is a waste of time. A waste of time? Yes, I am developing an API. The API is not stable yet. Today I need to rewrite some calls and now all my tests fail. I need to update all those unit tests. If I did not have to write tests, my code was already finished.

Yes, it is. By changing an interface or API, you need to update the existing tests. It is possible that many tests needs an update. Updating tests takes some time and costs something. Does this mean that writing unit tests is a waste of time?

### Unit Testing is like walking.

Unit testing is like trying to do a long distance walk. You want to walk 100 km in 24 hours. You start to train. Each weekend you go for a walk. It goes well in the beginning. You can walk 10 km, a few weeks later you walk 20 km. Then you are training to go more kilometres. After 25 km, you feel pain in your feet. You are thinking: "Why am I doing this? Why am I hurting myself?"  That pains stays several weeks.

By the time it is getting better, there is something else that has more priority. Some people have their birthday. The party is on a day that you planned to go for a walk. You skip. The week after that something else interferes with your walking plans. After a few weeks without any walk you decide to go again. Ambitious as you are, you start with 30 kilometres. But everything hurts after 20 kilometres. You ask yourself again why you are doing it?

While you walk, you see other walkers. They never had problems with pain and go much faster as you. They also go twice the distance as you are doing. How do they do that? But they do not have as many friends as you have. And their friends never celebrate their birthdays. They have much more time to walk.

You have two options. The first option is to quit. You conclude that you do not have anything in common with them. In the end you conclude that a long distance walk is nothing for you. You never walk again.  The second option is to start over and try until you manage to walk the distance that you want to walk. The second option is the hardest, but in the end it was worth the effort.

### Safety net

Unit testing is not easy. You can start to create unit tests and create each day more unit tests. In the end you have many unit tests. No, that was not our initial problem. The problem was that the developer needs to update too many unit tests.

{{< figure src="api_overview.jpg" title="Drawing" >}}

You have a system with an API. The API does probably have more than one call. If you do not write any tests for the API's, none of them have any tests. What happens if you update a function that also has some impact on an other function? What happens if you as developer make a mistake and the other API's are failing? Do you want to know while you are developing that your other interfaces are failing? I suppose you do. If you do not have any unit tests on API level or in another part, your do not know if something else in the system is failing. Unit tests are the safety net for the existing functionality that you do not touch.

### Design

Unit testing has more advantages. By creating unit tests on an interface, you are thinking about how you should use that interface (or API). Maybe the API is not that useful when you use it. The design of the API was not good. The usage of the API via the unit tests proves that.

### Stable

Unit tests test a small piece of code. That means that the tests are more stable than an automated user interface test. User interface tests are more flaky. They tend to fail without any reason. They also have problems with variations on the network speed. They are more complex to debug when they fail. The code that they cover is bigger. In case a unit test fails, the developer does not have to debug in most cases. He knows which part of the code a test covers.

### Documentation

Write some documentation. Your other colleagues do also need to know how your code looks like. Unit test are ideal for documenting code. A developer can look at a unit test to see how to call some part of the code. You do not have to document the usage anymore. It is in the set of unit tests.

### Speed

The fastest tests are unit tests. The execution of one tests is in the range of milliseconds. In the same time as you run one end to end test, you can do a lot of unit tests. End to end tests are slow because they imitate  a user. Because of the fast execution times there is a fast feedback. You as developer can run the tests during the development process. You will check if the system is still performing like it was before your changes. Faster feedback is cheaper, everybody knows it by now. You will find bugs earlier in the development cycle by the unit tests. The cost of a bug is much lower.

### Continuous Integration Server

Writing unit tests is a very good idea. It is better to run them in an automated way on a continuous integration server. Then the server executes the tests as soon as you put the new code in version control. Everybody notices it when the tests are failing on the continuous integration server. This is an advantage. Developers will fix the failing tests much faster. Nobody likes a failing build.

### Wrap up

Writing unit tests is a good way of working. In the beginning it is hard. It is worth the effort. The end user will be much happier with a product that has less bugs.