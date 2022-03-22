# Installation

Before we start, we need to install some packages and tools.

> Recommend version is LTS Version for every tool and package.

* [NodeJs](https://nodejs.org)
* [MongoDB as Replication](https://docs.mongodb.com/manual/replication/)
* [Yarn](https://yarnpkg.com)
* [Git](https://git-scm.com)

## Check Packages and tools

Check NodeJs is successful installed.

```bash
node --version

# will return 
# v17.3.0
```

Check MongoDB is successful installed.

```bash
mongod --version

# will return 
# db version v5.0.4
```

Check package manager `yarn` or `npm` is successful installed.

<!-- tabs:start -->

#### **Yarn**

```bash
yarn --version

# will return 
# 1.22.17
```

#### **NPM**

```bash
npm --version

# will return 
# 8.3.0
```
<!-- tabs:end -->

## Clone Repo

Clone ack-nestjs-boilerplate-mongoose with git.

```bash
git clone https://github.com/andrechristikan/ack-nestjs-boilerplate-mongoose
```

## Install Dependencies

> Make sure we on root dir of ack-nestjs-boilerplate-mongoose

We must to install app dependencies to run this project

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
APP_ENV=development
APP_HOST=localhost
APP_PORT= 3000
APP_LANGUAGE=en
APP_VERSIONING=false
APP_DEBUG=false
APP_TZ=Asia/Jakarta

DATABASE_HOST=localhost:27017
DATABASE_NAME=ack
DATABASE_USER=
DATABASE_PASSWORD=
DATABASE_ADMIN=false
DATABASE_SRV=false
DATABASE_DEBUG=false
DATABASE_SSL=false
DATABASE_OPTIONS=

AUTH_JWT_ACCESS_TOKEN_SECRET_KEY=123456
AUTH_JWT_REFRESH_TOKEN_SECRET_KEY=01001231

AUTH_BASIC_TOKEN_CLIENT_ID=asdzxc
AUTH_BASIC_TOKEN_CLIENT_SECRET=1234567890

AWS_BUCKET_CREATE_FROM_INIT=false
AWS_CREDENTIAL_KEY=
AWS_CREDENTIAL_SECRET=
AWS_S3_REGION=us-east-2
AWS_S3_BUCKET=acks3
```

For detail information about environment, please read this section below

<button-jump-to name="Jump To Usage Documentation" link="/#/usage/readme"></button-jump-to>

## Database

!> Mongodb instance must with replication set, cause we need to implement `mongoose transaction.`

First create database `ack` from our App.
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

ack-nestjs-boilerplate-mongoose only provide `e2e testing`, and use [Jest as testing framework](https://jestjs.io/docs/getting-started).

Just by type this command on our console.

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

And the test will passed 100%, if all steps correct.

## Run Project

> If mongodb version < 5, [please read section for adjust mongoose setting.](/getting-started/adjust-mongoose-setting)

Finally, Cheers 🍻🍻 !!! we passed all steps.\
Now we can run ack-nestjs-boilerplate-mongoose with yarn and use all features.

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