# Installation with docker

Install ack-nestjs-boilerplate-mongoose with docker. 

> Recommend version is LTS Version for every tool and package.

## Check Packages and tools

Check docker is successful installed.

```bash
docker --version

# will return 
# Docker version 20.10.12, build e91ed5707e
```

Check docker compose is successful installed.

```bash
docker-compose --version

# will return
# docker-compose version 1.27.4, build 40524192
```

## Clone Repo

Clone ack-nestjs-boilerplate-mongoose with git.

```bash
git clone https://github.com/andrechristikan/ack-nestjs-boilerplate-mongoose
```

## Environment

Copy from `.env.docker`

```bash
cp .env.docker .env
```

Then adjust.

```txt
APP_ENV=development
APP_HOST=0.0.0.0
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

<button-jump-to name="Usage Document section Environment" link="/usage/environment.md"></button-jump-to>

## Run Project

Run project docker compose.

```bash
docker-compose up
```
