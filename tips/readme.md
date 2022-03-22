# Application

Tips for use ack-nestjs-boilerplate-mongoose.

## Function Decorator Ordering

We realize for our decorator sometimes make the app broken. So for fix that issue, you can follow this tips.

```typescript
@Response()
@UserDeleteGuard()
@AuthAdminJwtGuard()
@HttpCode()
@Post()
```

## Independent Service

Make sure we create service as independent. That means the service don't need other services. Let make inheritance between service into controller. Checkout ack-nestjs-boilerplate-mongoose carefully for example.

## Response Type

In ack-nestjs-boilerplate-mongoose we use `@Response` decorator or `@ResponsePaging` decorator to return our response with/without data. But sometimes we forgot to set `Response Type`

```typescript
@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {

    // don't forget to set the type of Promise<void> or Promise<IResponse>
    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> { 
        return;
    }
}

```

## Guard as validation

Guard can be a validation too. With guard we can reuse our validation with other modules.

`User not found validation` that use in each endpoint that have `/:_id` param. Let imagine if we have 5 endpoint, we need to write 5 `User not found validation` for each endpoint. That will hard and take a lot time when we need to change, we must change 5 validation.

But let imagine if we wrap the `User not found validation` into Guard ? and then if there have new endpoint, we just use that guard. Simple right ?

```typescript
// User not found validation with guard
export class UserNotFoundGuard implements CanActivate {

    async canActivate(context: ExecutionContext): Promise<boolean> {
        const { __user } = context.switchToHttp().getRequest();

        if (!__user) {

            throw new NotFoundException({
                statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_NOT_FOUND_ERROR,
                message: 'user.error.notFound',
            });
        }

        return true;
    }
}


// Guard Group
export function UserGetGuard(): any {
    return applyDecorators(
        UseGuards(
            UserPutToRequestGuard, 
            UserNotFoundGuard
        )
    );
}

// We can use for other Group
export function UserDeleteGuard(): any {
    return applyDecorators(
        UseGuards(
            UserPutToRequestGuard, 
            UserNotFoundGuard
        )
    );
}

  
// This how to use in controller  
@Response('user.get')
@UserGetGuard()
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ)
@Get('get/:user')
async get(@GetUser() user: IUserDocument): Promise<IResponse> {
    return user;
}
```

## Debugger and Logger

`DebuggerModule` and `LoggerModule` has difference purpose.

### Debugger Module

!> Be careful while using docker. The docker will erase all container data while the container is down/remove. So if we don't mounting volume `/app/logs` in container with our `/logs` in host, we will loss the log file.

```typescript
export default registerAs(
    'app',
    (): Record<string, any> => ({
        ...
        ...
        ...

        debug: process.env.APP_DEBUG === 'true' || false,
        debugger: {
            http: {
                maxFiles: 5,
                maxSize: '2M',
            },
            system: {
                active: false, // <--- will write or not write logger into file
                maxFiles: '7d',
                maxSize: '2m',
            },
        },
    })
);
```

Actually, `DebuggerModule` divide become 2 modules

#### DebuggerModule

Like console but can write into file. This have purpose to debug every thing that we want to catch. Useful in debugger or track our data flow.

#### HttpDebuggerModule

A module that can catch every incoming request and response.

### Logger Module

Logger module have purpose to save log into database, and then the data will use by our data scientist.
