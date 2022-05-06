# Overview

ack-nestjs-boilerplate-mongoose is a [NestJs](http://nestjs.com) Boilerplate with [Mongoose](https://mongoosejs.com) and [MongoDB](https://docs.mongodb.com) as Database.

Made with following
- [nodejs-best-practice](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net)
- NestJs Habit.

## Important

If you change env value of `APP_MODE` to `complex` that will trigger more validation.

1. `x-timestamp`, tolerant 5 minutes of request.
2. `user-agent`, whitelist of user agent.
3. `x-api-key`, check api key.
4. check cors origin

You can see our `e2e testing file` or read the documentation on [section environment](/documentation/readme.md).

## Build with

Describes which version of the main packages and main tools.

| Name       | Version  |
| ---------- | -------- |
| NestJs     | v8.x     |
| NodeJs     | v17.x    |
| Typescript | v4.x     |
| Mongoose   | v6.x     |
| MongoDB    | v5.x     |
| Yarn       | v1.x     |
| NPM        | v8.x     |
| Docker     | v20.x    |
| Docker Compose | v2.x |

## Objective

ack-nestjs-boilerplate-mongoose have some objective.

- Simple, scalable and secure
- Avoid spaghetti code
- Component based
- Reusable component
- Easy to maintenance
- Support for all microservice patterns

## Features

- NestJs v8.x 🥳
- Typescript 🚀
- Authentication and Authorization (OAuth2, API Key, Basic Auth) 💪
- Mongodb integrate by using Mongoose Package 🎉
- Database Migration
- Integrate with AWS
- Server Side Pagination
- Url Versioning
- Request Validation Pipe
- Custom error status code 🤫
- Logger and Debugger 📝
- Centralize Configuration 🤖
- Centralize Exception Filter
- Multi-language (i18n)
- Dynamic Setting from Database 🗿
- Maintenance Mode on / off
- Support Docker Installation
- Support Docker Installation
- Support CI/CD with Github Action or Jenkins
- Husky GitHook For Check Source Code, and Run Test Before Commit 🐶
- Linter with EsLint for Typescript

## Prerequisites

We assume that everyone who comes here is _**`programmer with intermediate knowledge`**_ and we also need to understand more before we begin in order to reduce the knowledge gap.

1. Understand [NestJs Fundamental](http://nestjs.com), Main Framework. NodeJs Framework with support fully TypeScript.
2. Understand[Typescript Fundamental](https://www.typescriptlang.org), Programming Language. It will help us to write and read the code.
3. Understand [ExpressJs Fundamental](https://nodejs.org), NodeJs Base Framework. It will help us in understanding how the NestJs Framework works.
4. Understand what NoSql is and how it works as a database, especially [MongoDB.](https://docs.mongodb.com)
