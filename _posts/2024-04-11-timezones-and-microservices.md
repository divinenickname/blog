---
layout: post
title:  "Microservices and timezones"
date:   2024-04-12
tags: [ development, microservices, timezone ] 
---
When we start developing a new microservice many things may fade into background and sometimes completely forgotten. Many of them we may consider constant which never changing due to him inexpierence. The longer we postpone fixing this tech debt, the more painful it will be to do in future.

One of the those disgusting things in apps is the timezone.

We forget about it at the start of developing and then we have to write difficult database migrations or make business-logic more complexity to accommodate unexpected timezone behavior.

This problem is more complex than it seems at first glance. For example if we're living in Berlin with GMT+2 timezone and our microservice is also in Berlin, there are no risks with using LocalDateTime or OffsetDateTime because the server timezone and user timezone are the same.

However what might go wrong?

Let's say we hire a new developer or we simply decide to use `now()` in database migration. What will happen? As long as we still have same timezones - everything will be okay. However the microservice may be deployed in different region where timezone ***might*** be different.

We could also have distributed system based in different data centers across various regions each with its own timezone. Imagine the amount of work required to ensure consistency in datetime formats without causing collisions in business logic? If we decide to fix it early it will be fine. In most cases we realize the importance of this when money has been wasted due incorrect datetime logic.

Some senior developers try to predict different timezone behavior and hardcoding the timezone in configuration or using argument at the start of application. But this is not a great solution.

> The timezone will still be different if we forget about all these settings

My position is clearly simple: a developer should write code, he shouldn't worry about environment, datacenters and other non-coding things. We have such high levels of abstraction that we nedd not think about local issues. Of course we could leave this to QA, but let me ask you one question. Why should developers, devops, QA engineers bother remembering all these parameters? It's not just one settings in one place, this is a time bomb placed in code, configs, deployment settings and local environment. As a team lead I don't want to increase the application runtime risks due to obscure configurations. Fortunately this problem can be solved.

Based on my experience I have made a solution which is quite simple to maintain and I no longer think about datacenters' timezones and deployment settings.

> I convert all timestamps into GMT+0, and prohibit the use of `now()` in database migrations.

This solution provides several advantages:

- It is a systemic solution set up only once;
- Our applications are configured just once with a uniform timezone GMT+0;
- Testing is simplified as we compare the same time between users and in the database;
- Database always contains correct time values and it is irrelevant how the devops configured the db cluster;
- Strict rules allow for enforcement through linting tools or SonarQube.

When developing I always prefer to use the most systemactic once-configurable solutions instead of patching up local issues. Microservices have to be independent and deployable anywhere without affecting business-logic.