# Features

This section will explain some features.

## Centralize Configuration

Changing configuration values can be a nightmare for some developers. Perhaps due to incorrect implementation, causing the app to break or error.

Of course, we don't want that to happen, then we need to reduce the mistake. Centralizing our configuration is a recommended solution approach. The configs will be put into one folder or be environment variables for easy maintenance.

### Configs

```txt
src
  └── config
```

### Languages

```txt
src
  └── message
      └── languages 
          ├── en
          └── id
```

<button-jump-to name="Click here to more information " link="/#/usage/configuration?id=centralize-configuration"></button-jump-to>

## OAuth2 as Authorization

[OAuth 2.0](https://oauth.net/2/) is the industry-standard protocol for authorization. This is used to describe the user.

ack-nestjs-boilerplate-mongoose use JsonWebToken to implement OAuth2, and provide 2 guards for Authorization for admin and for public.

```typescript
@AuthPublicJwtGuard()
@AuthAdminJwtGuard()
```

<button-jump-to name="Click here to more information " link="/#/usage/guard?id=jwt"></button-jump-to>

## User, Role and Permission Management

> Inspired by [CASL Package](https://casl.js.org/v5/en/) and [NestJs Authorization](https://docs.nestjs.com/security/authorization) but we make it more simple.

We can describe and guard endpoints with specific permissions simply by using `AuthAdminJwtGuard` or `AuthPublicJwtGuard.`

When endpoint was called, then`AuthAdminJwtGuard` or `AuthPublicJwtGuard` will check for the active user, active role, and active permission. Otherwise will give a response Unauthorized or Forbidden Exception.

Example get user with id

```typescript
// describe permission, only role with USER_READ permission can access
// with this decorator, we already check our payload
// - the active status user
// - the active status role
// - and the active status permission

@Response('user.get')
@UserGetGuard()
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ)
@Get('get/:user')
async get(@GetUser() user: IUserDocument): Promise<IResponse> {
    return this.userService.mapGet(user);
}
```

<button-jump-to name="Click here to more information " link="/#/usage/guard?id=role-and-permission-management"></button-jump-to>

## Advance Validate Income Request

After RnD, and tested some similar packages. Finally, we decided to choose [`class-validation`](reference.md) package to handle this.

```typescript
import {
  IsInt,
  Length,
  IsEmail,
  Min,
  Max,
} from 'class-validator';

export class Post {
  @Length(10, 20)
  title: string;

  @IsInt()
  @Min(0)
  @Max(10)
  rating: number;

  @IsEmail()
  email: string;
}
```

As we can see, it is easy to read right ? this also easy to change and of course, class validation has a similar implementation with NestJs. Because of that, we fall in love at first sight.

Then, how to use class-validator with ack-nestjs-boilerplate ? ack-nestjs-mongoose provide `RequestValidationPipe` and with this pipe, we can combine it like this.

```typescript
@Response('role.create')
@AuthAdminJwtGuard(ENUM_PERMISSIONS.ROLE_READ, ENUM_PERMISSIONS.ROLE_CREATE)
@Post('/create')
async create(
    // RoleCreateValidation is class-validation
    // Just by put RequestValidationPipe in @Body, or @Query
    // Every incoming request will validate
    @Body(RequestValidationPipe)
    { name, permissions, isAdmin }: RoleCreateValidation
): Promise<IResponse> {
    ...
    ...
    ...
}
```

## Advance Mongoose Implementation

[Mongoose](reference.md) is package integration between [NodeJs](reference.md) and [MongoDB](reference.md). Mongoose has an elegant way to write MongoDB object modeling for NodeJs. We can fully control our database by writing code with mongoose.

List of mongoose examples that ack-nestjs-mongoose used.

* Populate
* Deep Populate
* Mongoose Transaction
* Multi Database
* Database Migration

## Catch Every Activity

Catch every activity with logger. ack-nestjs-mongoose can catch every activity about users. By default, users' activities will be written into files. Activities in ack-nestjs-mongoose will divide into 3 types.

There have 3 different approaches while doing the implementation.

* `HttpDebuggerModule` can catch everything from the incoming requests.
* `DebuggerModule` is like `console.log` but can write into files.
* `LoggerModule` is a logger that can write into a database

## Etc

<button-jump-to name="Click here to more information " link="/#/usage/readme"></button-jump-to>
