# Authorization

[OAuth 2.0](https://oauth.net/2/) is the industry-standard protocol for authorization. This is used to describe the user. ack-nestjs-boilerplate-mongoose combine between `@nestjs/jwt` and `@nestjs/passport` to implement `OAuth2`.
As we know, OAuth has 2 token `accessToken` and `RefreshToken`.

The config of access token will put in `src/config/auth.config.ts`

```txt
src
  └── config
      ├── *
      └── auth.config.ts
```

and the value will look like

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

## Access Token

> Access token create in `login endpoint`

Access token will have `30 minutes` to active and then we must to refresh the access token. The access token will separate into 2 guard `AuthPublicJwtGuard` and `AuthAdminJwtGuard`.

These guard  will check based on `payload user`.

1. is_active user
2. is_active role
3. permission of user
4. is_active permission
5. password_expired of user
6. is_admin or not

### AuthPublicJwtGuard

> is_admin is false

#### Usage

Get profile user with `AuthPublicJwtGuard` as guard

```typescript
@Response('user.profile')
@AuthPublicJwtGuard()
@Get('/profile')
async profile(@GetUser() user: IUserDocument): Promise<IResponse> {
    return this.userService.mapProfile(user);
}
```

### AuthAdminJwtGuard

> is_admin is true

#### Usage

Get user endpoint with `AuthAdminJwtGuard` as guard

```typescript
@Response('user.get')
@AuthAdminJwtGuard()
@Get('get/:user')
async get(): Promise<string> {
    return 'user data';
}
```

## Refresh Token

> Refresh token create in `refresh endpoint`

Refresh token will able to use after access token expired or `30 minutes after access token created`. Refresh token have `7 days` to active or if `rememberMe` in login endpoint is `true` then refresh token will have `200 days` to active. Refresh Token will use for refresh the access token if access token expired, so you don't need to relogin.

Refresh token will put into guard `AuthRefreshJwtGuard`

### Usage

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
