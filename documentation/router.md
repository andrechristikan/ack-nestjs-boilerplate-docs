# Router

> All service must as [independent service](/tips/readme?id=independent-service)

Routes will be put in `src/router/*` . Every route will have a different prefix. Endpoints or Controller that are added to the router will use the router prefix. List of prefixes in `src/app/app.module.ts`

```txt
src
  └── router
      ├── *
      └── *
```

## Background

Why do we use RouterModule instead of just using the controller prefix? 
In some cases, the controller prefix is unable to resolve prefix issues for the complex path. [You can read this article for more information](https://docs.nestjs.com/recipes/router-module)

## Example

For example, we use `RouterAdminModule` with the prefix is `/admin`

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
    ],
})
export class RouterAdminModule {}
```

In `RoleAdminController` the controller prefix is `/role`. Normally will look like this.

* `/role/list`
* `/role/get/:role`

But if we use `RouterModule` will look like this.

* `/admin/role/list`
* `/admin/role/get/:role`

## List of routes

List of routes provided by ack-nestjs-boilerplate-mongoose

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
    }
]),
```
