# Authorization

> Inspired by [CASL Package](https://casl.js.org/v5/en/) and [NestJs Authorization](https://docs.nestjs.com/security/authorization) but we make it more simple.

We guard endpoints with specific permissions simply by using `AuthAdminJwtGuard` or `AuthPublicJwtGuard`. When endpoint was called, then`AuthAdminJwtGuard` or `AuthPublicJwtGuard` will check for the active user, active role, and active permission. Otherwise will give a response Unauthorized or Forbidden Exception.

## Example

Example we will use `AuthAdminJwtGuard` to get user with id

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
