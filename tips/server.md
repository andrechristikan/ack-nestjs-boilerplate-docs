# Server

Tips for store ack-nestjs-boilerplate-mongoose to server.

## Containerization

Many articles about the benefits and drawbacks of containerization can be found on Google, which you can read for yourself.

> **Keywords**: `Advantages of containerized applications`

We recommend using `Docker`, which is a popular tool for doing this.

> **Keywords**: `Docker advantage`

### Tips about docker

We will provide some tips for using Docker.

#### Reduce image file

We provide a Dockerfile that can be used to reduce the size of our container image in production. Location in `prod/dockerfile`

```docker
FROM node:lts-alpine as builder
LABEL maintainer "ack@baibay.com"

WORKDIR /app
COPY package.json yarn.lock ./

RUN set -x && yarn --production=false

COPY . .

RUN yarn build

FROM node:lts-alpine as main
LABEL maintainer "ack@baibay.com"

ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

WORKDIR /app
EXPOSE 3000

COPY package.json yarn.lock ./
RUN touch .env .env.share

RUN set -x && yarn --production=true

COPY --from=builder /app/dist ./dist

CMD ["yarn", "start:prod"]
```

#### Always use alpine image

This is because the alpine is the `smallest size` image of a`node`.

```docker
# always use alpine image
# FROM node:lts-alpine as builder
FROM node:lts-alpine as builder
```

#### Always set specific version of image base

We must use a specific version of our base image to reduce the possibility of errors causing breaking changes. We can replace `node:lts-alpine` with `node:lts-alpine3.15`

```docker
# set specific version about node:lts-apline
# FROM node:lts-alpine as builder
FROM node:lts-alpine3.15 as builder
```

#### Always separate containers for test and production

We don't need dev dependencies in production. Our `dockerfile` creates a separate runner for docker. 
The first is the `builder` for the `test,` and the second is the `main` for `production.`

```docker
# for test
FROM node:lts-alpine as builder

# for production
FROM node:lts-alpine as main
```

## CI / CD

If we have multiple applications, it will take a long time to update, test, and deploy each one (to staging and also production too). `As a result, we strongly recommend using CI/CD.`

What is CI/CD? you can read and search advantage in google. Go ahead.

> Keyword: `Advantage of CI-CD`

The popular tool that we choose is `Github Action` or `Jenkins`. You can choose what ever you want. Our recommendation to use `Github Action if you have some $` or `Jenkins for free`.

### Github Action

We provide CI, CD, and others automation script in separate file.

> Location in `.github/workflows/*`.

You can adjust some `environment`, and `secret` with change directly or from `account settings` or `organization settings`. Ahh! forgot something, do not forget to adjust `triggers` too.

#### Github Description

> Location dir in `.github/workflows/*`

Description of what github workflow do per-files.

| File          | Description                                          |
| ------------- | ---------------------------------------------------- |
| cd.yml        | Deploy our docker image into server target           |
| ci.yml        | Create image and push into docker hub     |
| linter.yml    | Check linter                                         |
| release.yml   | Release base on version from `package.json` file     |
| test.yml      | Automation Unit Test, Integration Test, and E2E Test |

### Jenkins Pipeline

> Location dir in `prod/jenkis`

We already provide a Jenkins script pipeline that can use for production, that is already combined with `Docker` and `Automation Test`

How to adjust that pipeline script? Don't change or remove code in `stage`. We only need to change the value in `environment`

```groovy
pipeline {
    agent any
    options {
        skipDefaultCheckout true
    }
    environment {
        BASE_IMAGE='node:lts-alpine' // change this with same version of container image

        APP_NAME = 'ack' // this will be container name
        APP_NETWORK = 'app-network' // this network name
        APP_PORT = 3000 // this project will serve in port 0.0.0.0:3000
        
        NODE_JS = 'lts' // depends with our jenkins setting 

        HOST_IP = 'xx.xx.xx.xx' // this server ip for your production server
        HOST_CREDENTIAL = '2637b88f-8dc8-4395-bd6b-0c6127720a89' // depends with our credentials jenkins
        
        DOCKER_CREDENTIAL = 'ef108994-1241-4614-aab5-2aadd7a72284' // depends with our credentials jenkins
        DOCKER_FILE= './prod/dockerfile'
        DOCKER_USERNAME = 'ack' // docker hub username
        DOCKER_REGISTRY = 'https://index.docker.io/v1/' 

        GIT = 'Default' // depends with our jenkins setting
        GIT_BRANCH = 'main' // git branch
        GIT_CREDENTIAL = '86535ad6-5d74-48c0-9852-bddbe1fbaff6' // depends with our credentials jenkins 
    }
    tools {
        nodejs NODE_JS
        git GIT
    }
    stages {
        ...
        ...
        ...
    }
}
```

#### Jenkins Description

Description of what Jenkins do in every stage

| Stage    | Description                                                    |
| -------- | -------------------------------------------------------------- |
| Prepare  | `Check dependencies`, and `pull base image` from docker hub    |
| Clone    | Clone git                                                      |
| Build    | `Build test image`                                             |
| Test     | Unit Test                                                      |
| Integration Test     | Integration Test                                   |
| E2E Test | E2E Test                                                       |
| Push     | `Build production image` and `push image into docker hub registry` |
| Deploy   | Deploy image into server target                                |
| Clean    | Clean unnecessary docker images and containers from server target and server jenkins |

&nbsp;
