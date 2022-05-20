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

> Store as global module

API is important for our app. This can help we to make our app more secure, control the traffic, filter log, and dst.
[More information you can read this reference](https://cloud.google.com/endpoints/docs/openapi/when-why-api-key) and [two article](https://stackoverflow.com/questions/21465559/restrict-api-requests-to-only-my-own-mobile-app)

ack-nestjs-boilerplate-mongoose provide `ApiKeyGuard` for check `x-api-key` on request header is recorded or not in our database.

> Location in `src/auth/guard/api-key/*`

This guards will check based on `x-api-key` in request header.

1. `x-api-key` is empty or not
2. `IsActive` of API Key
3. Same schema is correct or not / this will check the decryption is correct or not too
4. Is `key` and `data.key` same (`data.key` is part of `IAuthApiRequestHashedData`)
5. Is timestamp in data match with `x-timestamp` or not
6. Valid API Key

### Usage

> Make sure you has run `yarn migrate` to migrate the api key data into database

If the `APP_MODE` is `secure` that will auto include the `ApiKeyGuard`.

#### Encryption Data

1. Hash use `sha256`. Hash the `key` and `secret`.

!> `secret` should be kept private.

```typescript
const hash = this.helperHashService.sha256(`${key}:${secret}`);
// e11a023bc0ccf713cb50de9baa5140e59d3d4c52ec8952d9ca60326e040eda54
```

Steps `sha256` function do

- hash with `sha-256`
- convert `encrypted data` into `hex`

2. Create data object base on `IAuthApiRequestHashedData` interface. Make sure the timestamp value must same with `x-timestamp` value header.

```typescript
const timestamp = helperDateService.timestamp(); 
// timestamp 1651607972429
const data: IAuthApiRequestHashedData = {
    "key": "qwertyuiop12345zxcvbnmkjh", // <--- example from migration
    timestamp,
    hash,
}
```

3. Encryption use `AES 256`. We can find `passphrase` and `encryptionKey` value in database on `authapis` collection. The passphrase and encryptionKey will difference between other apiKey.

!> These data `encryptionKey`, and `passphrase` should be kept private.

```typescript
const passphrase = 'cuwakimacojulawu'; // <--- IV for encrypt AES 256
const encryptionKey = 'opbUwdiS1FBsrDUoPgZdx';
const apiKeyEncryption = await authApiService.encryptApiKey(
    data,
    encryptionKey,
    passphrase
);
```

Steps `encryptApiKey` function do

- make the `data` into `string`
- encryption with `aes-256`
- convert `encrypted data` into format `OpenSSL`

4. Combine the `key` and `apiKeyEncryption`

```typescript
const xApiKey = `${apiKey}:${apiEncryption}`;
```

5. Send request. Put the `xApiKey` in request headers

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

#### Get API Key

To get the `apiKey`, we can use `ApiKey Decorator`

```typescript
@Response('auth.refresh')
@HttpCode(HttpStatus.OK)
@Post('/refresh')
async refresh(
        @ApiKey() apiKey: IAuthApiPayload // <--- use this decorator
    ){
    ...
    ...
    ...

    return apiKey;
}
```

#### Exclude API KEY

Simply, in controller use `AuthExcludeApiKey`

```typescript
@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingCommonController {
    @Response('test.hello')
    @AuthExcludeApiKey() // <--- add this in controller level
    @Get('/hello')
    async hello(
        @UserAgent() userAgent: IResult,
        @ApiKey() apiKey: IAuthApiPayload
    ): Promise<IResponse> {
        return { userAgent, apiKey };
    }
}
```

#### Interface

```typescript
export interface IAuthApiRequestHashedData {
    key: string;
    timestamp: number;
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

&nbsp;
