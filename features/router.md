# Router

> All service must as [independent service](/tips/readme?id=independent-service)

Routes will put in `src/router/*` . Every route will have difference prefix. Endpoints or Controller that added in router will follow router prefix. List of prefix in `src/app/app.module.ts`

```txt
src
  └── router
      ├── *
      └── *
```

## Background

Why we use RouterModule and why we don't just use controller prefix ?
Well sometimes the controller prefix can't resolve prefix problem for complex path. [You can read this article for more information](https://docs.nestjs.com/recipes/router-module)

## Example

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
    }
]),
```
