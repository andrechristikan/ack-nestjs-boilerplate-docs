# Authorization

We guard endpoints with specific permissions simply by using `AuthAdminJwtGuard` or `AuthPublicJwtGuard`.

## Usage

Example we will use `AuthAdminJwtGuard` to get user with id

```typescript
// describe permission, only role with USER_READ permission can access
// with this decorator, we already check our payload
// - the active status user
// - the active status role
// - and the active status permission

@Response('user.get')
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ) // <--- give permission that you want
@Get('get/:user')
async get(): Promise<IResponse> {
    return 'data';
}
```

## List of permission

List of permission located in `src/permission/permission.constant.ts`

```txt
src
  └── permission
      ├── *
      └── permission.constant.ts
```

and will look like

```typescript
export enum ENUM_PERMISSIONS {
    USER_CREATE = 'USER_CREATE',
    USER_UPDATE = 'USER_UPDATE',
    USER_READ = 'USER_READ',
    USER_DELETE = 'USER_DELETE',
    ROLE_CREATE = 'ROLE_CREATE',
    ROLE_UPDATE = 'ROLE_UPDATE',
    ROLE_READ = 'ROLE_READ',
    ROLE_DELETE = 'ROLE_DELETE',
    PERMISSION_READ = 'PERMISSION_READ',
    PERMISSION_UPDATE = 'PERMISSION_READ',
}
```
