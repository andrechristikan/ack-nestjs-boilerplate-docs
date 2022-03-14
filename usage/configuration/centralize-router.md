# Centralize Router

> All service must as [independent service](https://app.gitbook.com/s/AgPk2YQXEyb83NSMYDo1/)

Routes will put in `src/router/*` . Every route will have difference prefix. Endpoints or Controller that added in router will follow router the prefix. List of prefix in `src/app/app.module.ts`

For example we use `RouterAdminModule`, and the prefix is `/admin`

```typescript
import { RouterAdminModule } from 'src/router/router.admin.module';
import { RouterModule } from '@nestjs/core';

@Module({
    controllers: [],
    providers: [],
    imports: [
        ...
        ...
        ...
        
        // Router
        RouterAdminModule,
        RouterModule.register([
            {
                path: '/admin',
                module: RouterAdminModule,
            }
        ]),
    ],
})
export class AppModule {}
```

Then, add `RoleAdminController` into `RouterAdminModule`.

```typescript
@Module({
    controllers: [
        RoleAdminController
    ],
    providers: [],
    exports: [],
    imports: [
        RoleModule,
        PermissionModule,
        PaginationModule,
    ],
})
export class RouterAdminModule {}
```

In `RoleAdminController` will have prefix as `/role`.

* `/role/list`
* `/role/get/:role`

So url path will look like this

* `/admin/role/list`
* `/admin/role/get/:role`

## Reason

With `ControllerPrefix` sometimes can't resolve problem for complex path. Thats why `RouterModule` comes. [Please see our reference](centralize-router.md#reference)

## List of routes

List of routes that ack-nestjs-boilerplate-mongoose provide

```typescript
RouterModule.register([
    {
        path: '/',
        module: RouterCommonModule,
    },
    {
        path: '/test',
        module: RouterTestModule,
    },
    {
        path: '/enum',
        module: RouterEnumModule,
    },
    {
        path: '/admin',
        module: RouterAdminModule,
    },
    {
        path: '/public',
        module: RouterPublicModule,
    },
    {
        path: '/callback',
        module: RouterCallbackModule,
    },
]),
```
