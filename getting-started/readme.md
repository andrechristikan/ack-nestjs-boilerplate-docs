# Installation

Before we start, we need to install some packages and tools.

> Recommend version is LTS Version for every tool and package.

* [NodeJs](https://nodejs.org)
* [MongoDB as Replication](https://docs.mongodb.com/manual/replication/)
* [Yarn](https://yarnpkg.com)
* [Git](https://git-scm.com)

## Check Packages and tools

Check that NodeJs has been installed successfully.

```bash
node --version

# will return 
# v17.x
```

Check MongoDB has been successfully installed.

```bash
mongod --version

# will return 
# db version v5.x
```

Check package manager `yarn` or `npm` is successfully installed.

<!-- tabs:start -->

#### **Yarn**

```bash
yarn --version

# will return 
# 1.x
```

#### **NPM**

```bash
npm --version

# will return 
# 8.x
```
<!-- tabs:end -->

## Clone Repo

Clone ack-nestjs-boilerplate-mongoose with git.

```bash
git clone https://github.com/andrechristikan/ack-nestjs-boilerplate-mongoose
```

## Install Dependencies

> Make sure we on root dir of ack-nestjs-boilerplate-mongoose

To run this project, we must install app dependencies.

<!-- tabs:start -->

#### **Yarn**

```bash
yarn
```

#### **NPM**

```bash
npm i
```

<!-- tabs:end -->

## Environment

Copy from `.env.example`

```bash
cp .env.example .env
```

Then Adjust.

```txt
APP_NAME=ack
APP_ENV=development
APP_MODE=simple
APP_LANGUAGE=en
APP_TZ=Asia/Jakarta

APP_HOST=0.0.0.0
APP_PORT= 3000
APP_VERSIONING=false
APP_DEBUG=false

DATABASE_HOST=mongodb://localhost:27017
DATABASE_NAME=ack
DATABASE_USER=
DATABASE_PASSWORD=
DATABASE_DEBUG=false
DATABASE_OPTIONS=

AUTH_JWT_ACCESS_TOKEN_SECRET_KEY=123456
AUTH_JWT_REFRESH_TOKEN_SECRET_KEY=01001231

AUTH_BASIC_TOKEN_CLIENT_ID=asdzxc
AUTH_BASIC_TOKEN_CLIENT_SECRET=1234567890

AWS_CREDENTIAL_KEY=
AWS_CREDENTIAL_SECRET=
AWS_S3_REGION=us-east-2
AWS_S3_BUCKET=acks3
```

For detailed information about the environment, please read this section below

<button-jump-to name="Jump To Usage Documentation" link="/#/usage/readme"></button-jump-to>

## Database

!> If you want to to implement `transaction`, you must to install `Mongodb Replication Set`.

First, you need to run `mongodb`. There are have so many options, you can do by your self with search on google.

Then, create database `ack` from our App.
If you don't know how to create mongodb database, [please follow official mongodb instructions](https://www.mongodb.com/basics/create-database).

### Database Migration

!> Only for initial purpose.

Database migration ack-nestjs-boilerplate-mongoose used [NestJs-Command](https://gitlab.com/aa900031/nestjs-command) to do that.&#x20;

For migrate.

<!-- tabs:start -->

#### **Yarn**

```bash
yarn migrate
```

#### **NPM**

```bash
npm run migrate
```

<!-- tabs:end -->

For rollback.


<!-- tabs:start -->

#### **Yarn**

```bash
yarn rollback
```

#### **NPM**

```bash
npm run rollback
```

<!-- tabs:end -->

## Test

ack-nestjs-boilerplate-mongoose provide 3 automation testing `unit testing`, `integration testing`, and `e2e testing`. ack-nestjs-boilerplate-mongoose use [Jest as testing framework](https://jestjs.io/docs/getting-started).

Just by type this command on our console.

### Unit Testing

<!-- tabs:start -->

#### **Yarn**

```bash
yarn test
```

#### **NPM**

```bash
npm run test
```

<!-- tabs:end -->

### Integration Testing

<!-- tabs:start -->

#### **Yarn**

```bash
yarn test:integration
```

#### **NPM**

```bash
npm run test:integration
```

<!-- tabs:end -->

### E2E Testing

<!-- tabs:start -->

#### **Yarn**

```bash
yarn test:e2e
```

#### **NPM**

```bash
npm run test:e2e
```

<!-- tabs:end -->

## Run Project

> If mongodb version < 5, [please read section for adjust mongoose setting.](/getting-started/adjust-mongoose-setting)

Finally, Cheers 🍻🍻 !!! we passed all steps.

Now we can run ack-nestjs-boilerplate-mongoose and use all of its features.

<!-- tabs:start -->

#### **Yarn**

```bash
yarn start:dev
```

#### **NPM**

```bash
npm run start:dev
```
<!-- tabs:end -->