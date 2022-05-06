# Docker

Install ack-nestjs-boilerplate-mongoose with docker.

> Recommend version is LTS Version for every tool and package.

* [Docker](https://docs.docker.com)
* [Docker Compose](https://docs.docker.com/compose)

## Check Packages and tools

Check docker is successful installed.

```bash
docker --version

# will return 
# Docker version 20.x
```

Check docker compose is successful installed.

```bash
docker-compose --version

# will return
# Docker Compose version v2.x
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
APP_NAME=ack
APP_ENV=development
APP_MODE=simple
APP_LANGUAGE=en
APP_TZ=Asia/Jakarta

APP_HOST=0.0.0.0
APP_PORT= 3000
APP_VERSIONING=false
APP_DEBUG=false

APP_HTTP_ON=true
APP_TASK_ON=false

DATABASE_HOST=mongodb://mongodb:27017
DATABASE_NAME=ack
DATABASE_USER=root
DATABASE_PASSWORD=123456
DATABASE_DEBUG=false
DATABASE_OPTIONS=authSource=admin

AUTH_JWT_ACCESS_TOKEN_SECRET_KEY=123456
AUTH_JWT_REFRESH_TOKEN_SECRET_KEY=01001231

AUTH_BASIC_TOKEN_CLIENT_ID=asdzxc
AUTH_BASIC_TOKEN_CLIENT_SECRET=1234567890

AWS_CREDENTIAL_KEY=
AWS_CREDENTIAL_SECRET=
AWS_S3_REGION=us-east-2
AWS_S3_BUCKET=acks3
```

For detail information about environment, please read this section below

<button-jump-to name="Jump To Features" link="/#/documentation/readme"></button-jump-to>

## Run Project

> If mongodb version < 5, [please read section for adjust mongoose setting.](/getting-started/adjust-mongoose-setting)

Run project docker compose.

```bash
docker-compose up -d
```
