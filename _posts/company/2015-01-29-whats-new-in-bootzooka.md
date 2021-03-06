---
title: What's new in Bootzooka?
description: Let's take a closer look at most important changes made in our web application scaffolding project.
author: Marcin Kubala
author_login: marcin_kubala
categories:
- scala
- company
layout: simple_post
---

In this blog post I would like to put a spotlight on most important changes we have recently
made in [Bootzooka](https://github.com/softwaremill/bootzooka).

If you've never heard about Bootzooka before - it's an open-source scaffolding project for web applications
built on top of [Scalatra](http://www.scalatra.org/) and [AngularJS](https://angularjs.org/) frameworks.

The live demo is available at [bootzooka.softwaremill.com](http://bootzooka.softwaremill.com).

The project is available in our [GitHub repository](https://github.com/softwaremill/bootzooka) under Apache 2.0 License
which means you can freely use any part of it.

Bootzooka has been actively developed all the time, but in the last few weeks we introduced changes that have
noticeable impact on the whole project. Let's take a look on it:

## Flattened project structure
The number of SBT sub-projects was decreased by pulling backend-related modules into a common project called bootzooka-backend.
This way we achieved clearer separation between server- and client-side components.

## We dropped MongoDB
While MongoDB is a really great document store, it has a limited scope of application kinds, which may be problematic when
you're developing the scaffold.
With such project you cannot predict where it will be used and have absolutely no guarantee that NoSQL will fit
particular project's needs.

Thus we decided to replace it with a traditional SQL database which has a wider scope of application, thereby making
Bootzooka more universal.

We chose [H2 SQL database](http://www.h2database.com/html/main.html) because it's lightweight and doesn't require any
3rd party software to be running (e.g. `mongod`).
This simplifies both development on a local machine and a Continuous Integration (CI) process.

Since we're in the SQL's eco-system, you're able to replace H2 with any other _'production'_ RDBMS (e.g. MySQL, PostgreSQL or Oracle)
without any pain.

[Rogue](https://github.com/foursquare/rogue) was replaced with [Slick](http://slick.typesafe.com/) - the library which
allows to access & query SQL database in a type safe manner.

## Painless database schema evolution
Explicit schema comes with the need of applying DDL scripts whenever it evolves.
Often such scripts are applied manually during deployment, which makes the whole process more complex (you have to check
which scripts are already applied at the particular environment and which one you have to apply next)
and error-prone (e.g. you may forget to apply one of the scripts or apply them in wrong order).

Bootzooka has been integrated with [Flyway](http://flywaydb.org/) - a tool that can automatically apply appropriate
DDL & DML scripts.
You only need to put your scripts under `db/migration/` directory (which must be present in the classpath) and obey the
[Flyway's naming convention](http://flywaydb.org/documentation/migration/sql.html).

Bootzooka stores its schema evolution SQLs under `bootzooka-backend/src/main/resources/db/migration/`.

Migration will be performed automatically on an application start.

Also, by extending `FlatSpecWithSql` you can bring Flyway to your integration tests and catch any problem with scripts
during regular CI (which usually is done right after problematic change was pushed to the repository).

## Interactive REST API documentation
APIs of our REST services are documented using [Swagger](http://swagger.io/).
We also included [Swagger-UI](https://github.com/swagger-api/swagger-ui) - a tool that generates interactive
documentation from Swagger-compliant API.

The interactive API docs are available at `<Bootzooka URL>/api-docs`.
The raw JSON produced by Swagger lives at `<Bootzooka URL>/rest/api-docs`.

Don't hesitate yourself to play with our demo instance's interactive API browser, available at
[bootzooka.softwaremill.com/api-docs](http://bootzooka.softwaremill.com/api-docs) :)

That's all about interesting changes in the backend. Let's check what has changed at the client-side!

## New views' routing
We decided to replace plain old uri-oriented Angular's ng-router with modern, states-oriented
[ui-router](https://github.com/angular-ui/ui-router).
The new mechanism is more flexible and supports parallel & nested views, which makes master-detail
views development a piece of cake :)

## Brand new user notification service and directive
The old notification mechanism had a big disadvantage - it could display only one message of particular type
(error or info) at the same time.

It has been replaced with dedicated `NotificationsService` (available in `smlBootzooka.notifications` module)
that aggregates notifications and `<bs-notifications />` directive which dynamically creates
pop-ups for each notification incoming from the given source.

[Michał Ostruszka](https://twitter.com/mostruszka) came out with a brilliant idea of
[decoupling directive from messages provider](http://michalostruszka.pl/blog/2015/01/18/angular-directives-di/).
Such separation allows us to include many notification directives displaying different kind of content on the
same page. Reusability has been increased :)

---------

That's all of noticeable changes. We also updated Scala, Scalatra, AngularJS
and other dependencies to the most recent stable versions, to keep Bootzooka up to date.

Any kind of suggestions, ideas and pull requests are welcomed!
