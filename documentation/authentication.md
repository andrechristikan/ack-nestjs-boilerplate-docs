# Authentication

List of authentication

## OAuth2 with JWT

[OAuth 2.0](https://oauth.net/2/) is the industry-standard protocol for authorization. This is used to describe the user. ack-nestjs-boilerplate-mongoose combine between `@nestjs/jwt` and `@nestjs/passport` to implement `OAuth2`.

The config of access token will be put in `src/config/auth.config.ts`

```txt
src
  └── config
      ├── *
      └── auth.config.ts
```

and the value will look like this

```typescript
import { registerAs } from '@nestjs/config';

export default registerAs(
    'auth',
    (): Record<string, any> => ({
        jwt: {
            accessToken: {
                secretKey:
                    process.env.AUTH_JWT_ACCESS_TOKEN_SECRET_KEY || '123456',
                expirationTime: '30m', // recommendation for production is 30m
                notBeforeExpirationTime: '0', // keep it in zero value
            },

            refreshToken: {
                secretKey:
                    process.env.AUTH_JWT_REFRESH_TOKEN_SECRET_KEY ||
                    '123456000',
                expirationTime: '7d', // recommendation for production is 7d
                expirationTimeRememberMe: '30d', // recommendation for production is 30d
                notBeforeExpirationTime: '30m', // recommendation for production is 30m
            },
        },
);
```

### Access Token

> Access token create in `login endpoint` or `refresh endpoint`

The access token will have `30 minutes` to active and then we must refresh the access token. The access token will check if we use `AuthPublicJwtGuard` and `AuthAdminJwtGuard`.

These guards will check based on `payload user`.

1. isActive user
2. isActive role
3. permission of user
4. isActive permission
5. passwordExpired of user
6. isAdmin or not

#### AuthPublicJwtGuard

> isAdmin is false

##### Usage

Get profile user with `AuthPublicJwtGuard` as a guard

```typescript
@Response('user.profile')
@AuthPublicJwtGuard()
@Get('/profile')
async profile(@GetUser() user: IUserDocument): Promise<IResponse> {
    return this.userService.mapProfile(user);
}
```

#### AuthAdminJwtGuard

> isAdmin is true

##### Usage

Get user endpoint with `AuthAdminJwtGuard` as a guard

```typescript
@Response('user.get')
@AuthAdminJwtGuard()
@Get('get/:user')
async get(): Promise<string> {
    return 'user data';
}
```

### Refresh Token

> Refresh token create in `login endpoint` or `refresh endpoint`

The refresh token can be used after the access token has expired or `30 minutes after the access token was created`, the refresh token will be valid for `7 days` or if `rememberMe` in the login endpoint is set to `true` then the refresh token will be valid for `30 days`. Refresh Token will be used to refresh the access token if it expired.

Refresh token will check if we use `AuthRefreshJwtGuard`

#### Usage

```typescript
@Response('auth.refresh')
@AuthRefreshJwtGuard()
@HttpCode(HttpStatus.OK)
@Post('/refresh')
async refresh(){
    ...
    ...
    ...

    return 'data';
}
```

## API Key

> Location in `src/auth/guard/api-key/*`

API is important for our app. This can help we to make our app more secure, control the traffic, filter log, and dst.
[More information you can read this reference](https://cloud.google.com/endpoints/docs/openapi/when-why-api-key) and [two article](https://stackoverflow.com/questions/21465559/restrict-api-requests-to-only-my-own-mobile-app)

ack-nestjs-boilerplate-mongoose provide `ApiKeyGuard` for check `x-api-key` on request header is recorded or not in our database.

> `x-api-key` is string of `key` and `encryption data`.

This guards will check based on `x-api-key` in request header.

1. `x-api-key` is empty or not
2. Is schema (before encryption) is correct or not
3. Is timestamp in data match with `x-timestamp` or not
4. Check API Key in database
5. IsActive of API Key
6. Valid API Key

### Usage

If the `APP_MODE` is `complex` and `*JwtGuard` is use on Guard some API, that will auto include.

```typescript
export function AuthJwtGuard(): any {
    return applyDecorators(
        UseGuards(
            ApiKeyGuard, // <--- here
            JwtGuard, 
            AuthPayloadDefaultGuard
        )
    );
}
```

### Encryption Data

> Make sure you has run `yarn migrate` to migrate the api key into database

1. Create data object base in `IAuthApiRequestHashedData` interface. The timestamp value must same with `x-timestamp`. Also the schema of object must be same with interface.

```typescript
const timestamp = helperDateService.timestamp(); // 1651607972429
const data = {
    "key": "qwertyuiop12345zxcvbnmkjh",
    "timestamp": 1651607972429,
    "secret": "5124512412412asdasdasdasdasdASDASDASD",
    "hash": "e11a023bc0ccf713cb50de9baa5140e59d3d4c52ec8952d9ca60326e040eda54",
}
```

2. Encryption the data. You can find `passphrase` value in database on `authapis` collection.

```typescript
const passphrase = 'cuwakimacojulawu';
const apiKeyEncryption = await authApiService.encryptApiKey(
    data,
    passphrase

);
```

3. Combine the `data.key` and `apiKeyEncryption`

```typescript
const xApiKey = `${apiKey}:${apiEncryption}`;
```

4. Then put the `xApiKey` in request headers

```json
{
    "headers": {
        "x-api-key": "${xApiKey}",

        ...
        ...
        ...
    }
}
```

### Interface

```typescript
export interface IAuthApiRequestHashedData {
    key: string;
    timestamp: number;
    secret: string;
    hash: string;
}
```


## Basic Token

!> This purpose is not recommendation because is not secure

Basically, this authorization method just encryption the key and secret into base64 encode. [More information](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

### Usage

```typescript
@Response('auth.refresh')
@AuthBasicGuard()
@HttpCode(HttpStatus.OK)
@Post('/refresh')
async refresh(){
    ...
    ...
    ...

    return 'data';
}
```
