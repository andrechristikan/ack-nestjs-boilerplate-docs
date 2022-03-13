# Server

Tips for store ack-nestjs-boilerplate-mongoose to server.

## Containerization

We can read many article in google about advantage and disadvantage about containerization, you can read by yourself.

> **Keywords** : `Advantages of containerized applications`

Popular tools for do this thing is `Docker` , and we recommend to use this tools. &#x20;

> **Keywords** : `Docker advantage`

### Tips about docker

We will give some tips while using docker

#### Reduce image file

We provide dockerfile that can reduce our container image in production. Location in `prod/dockerfile`&#x20;

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

This because apline is smallest size image `node:lts-alpine`.

```docker
# always use alpine image
# FROM node:lts-alpine as builder
FROM node:lts-alpine as builder
```

#### Always set specific version about image base

We must to specifict version about our base image  for reduce chance error cause breaking changes. We can replace `node:lts-alpine` with `node:lts-alpine3.15`

```docker
# set specific version about node:lts-apline
# FROM node:lts-alpine as builder
FROM node:lts-alpine3.15 as builder
```

#### Always seperate container for test and production

In production we don't need dev dependencies

```docker
# seperate build 

# for test
FROM node:lts-alpine  as builder

# for production
FROM node:lts-alpine as main
```

## CI / CD

If we have so many application, it will waste so many time to update one by one, test and deploy (to staging and production). `So we highly recommend to use CI/CD`

What is CI/CD ? you can read and search advantage is google. Go ahead.

> Keyword : `Advantage of CI CD`

Popular tools that we choose is `Jenkins`

### Jenkins Pipeline

We already provide jenkins script pipeline that can use for production, that already combine with `Docker` and `Automation Test`

> Location dir in `prod/jenkis`

How to adjust that pipeline script ? Don't change or remove code in `stage`. We just need to change value in `environment`

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
        APP_PORT = 3000 // the application will serve in port 0.0.0.0:3000
        
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
        GIT_URL = 'https://github.com/andrechristikan/ack-nestjs-mongoose.git' // git url
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

#### Jenkins Stage Pipeline

Description for what jenkins do in every stage

| Stage   | Description                                                                 |
| ------- | --------------------------------------------------------------------------- |
| Prepare | Check dependencies, and pull base image                                     |
| Clone   | Clone git                                                                   |
| Build   | Build image for test and production                                         |
| Test    | Build container and test image with automation testing                      |
| Push    | Push Production image, if test stage passed                                 |
| Deploy  | SSH to server production, and deploy production image into docker container |
| Clean   | Clean unnessasary docker images in production server and in jenkins server  |
