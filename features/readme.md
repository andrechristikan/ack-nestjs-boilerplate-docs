# Environment

ack-nestjs-boilerplate-mongoose use `.env` file with `dotenv` package.

## Environment value

By default, ack-nestjs-boilerplate-mongoose has 2 `Environment values`

### Production

* DebuggerModule will not write into the console even if `APP_DEBUG` is set to `true`.
* DebuggerModule will always write into log file.
* Mongoose debug will not write into the console even if `DATABASE_DEBUG` is set to `true`.
* Cors `origin` will allow setting on `src/config/config.middleware.ts`.

### Development

* DebuggerModule (info/error) will write into the console.
* DebuggerModule will always write into log file.
* Mongoose debug will write into the console if `DATABASE_DEBUG` is `true`.
* Cors `origin` will always set to `*`.

## Example environment

Environment location `.env.example`. By default, the environment will look like this.

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

AWS_CREDENTIAL_KEY=
AWS_CREDENTIAL_SECRET=
AWS_S3_REGION=us-east-2
AWS_S3_BUCKET=acks3
```

## Description

Description for every environment variable

<!-- tabs:start -->

#### **APP Environment**

| Key | Type | Value | Description |
| ---- | ---- | ---- | ---- |
| APP\_ENV | `string` | <ul><li>production</li><li>development</li><li>testing</li></ul> | Application environment, please read [Getting Started Section](/getting-started/readme) for to know difference between value. |
| APP\_HOST | `string` | localhost or correct ip | Address that serve our app. |
| APP\_PORT | `number` | Available port in our system | Port that serve our app. |
| APP\_LANGUAGE | `string` | Enum languages | Default language, we can see enum in [Usage Documentation Section Centralize language and Message.](/features/language) |
| APP\_VERSIONING | `boolean` | Boolean value | If `true`, our url will replace from `api/` to `/api/v1/` |
| APP\_DEBUG | `boolean` | Boolean value | If true, `Debugger Module` and `Http Debugger Module` will write into file. |
| APP\_TZ | `string` | Timezone name | `Set default timezone` for our app. |

#### **Database Environment**

| Key | Type | Value | Description |
| ---- | ---- | ---- | ----|
| DATABASE\_HOST | `string` | `localhost:27017` or `localhost:27017,localhost:27018,localhost:27019` | Database url, representative mongoose url |
| DATABASE\_NAME | `string` | Database name | Database name |
| DATABASE\_USER | `string` | Database user | Our user for accessing the database |
| DATABASE\_PASSWORD | `string` | Database user password | User Password |
| DATABASE\_ADMIN | `boolean` | Boolean value | Mongoose admin option |
| DATABASE\_SRV | `boolean` | Boolean value | `Mongoose url will add with srv in beginning` by default if value is `true` |
| DATABASE\_DEBUG | `boolean` | Boolean value | Mongoose debug mode |
| DATABASE\_SSL | `boolean` | Boolean value | Mongoose SSL option |
| DATABASE\_OPTIONS | `string` | Mongoose options value | Other mongoose options |

#### **Auth Environment**

| Key | Type | Value | Description |
| ---- | ---- | ---- | ---- |
| AUTH\_JWT\_ACCESS\_TOKEN\_SECRET\_KEY  | `string` | Free text | Secret key for JWT Access Token  |
| AUTH\_JWT\_REFRESH\_TOKEN\_SECRET\_KEY | `string` | Free tex  | Secret key for JWT Refresh Token |

#### **Basic Environment**

| Key | Type | Value | Description |
| ---- | ---- | ---- | ---- |
| AUTH\_BASIC\_TOKEN\_CLIENT\_ID  | `string` | Free text | Basic ID  |
| AUTH\_BASIC\_TOKEN\_CLIENT\_SECRET | `string` | Free tex  | Secret key for Basic Token |

#### **AWS Environment**

| Key | Type | Value | Description |
| ---- | ---- | ---- | ---- |
| AWS\_CREDENTIAL\_KEY | `string` | Free text | AWS account credential key |
| AWS\_CREDENTIAL\_SECRET | `string` | Free text | AWS account credential secret |
| AWS\_S3\_REGION | `string` | AWS S3 region | AWS S3 Region |
| AWS\_S3\_BUCKET | `string` | AWS S3 bucket | AWS S3 Name of Bucket |

<!-- tabs:end -->