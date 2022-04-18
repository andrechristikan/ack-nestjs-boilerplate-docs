# Application

Tips for use ack-nestjs-boilerplate-mongoose.

## Incoming Request

Now ack-nestjs-boilerplate-mongoose have `required` request headers.

1. `x-timestamp`, tolerant 5 minutes of request.
2. `user-agent`, whitelist of user agent.

You can see our `e2e testing file`.

## Decorator Ordering

We recognize that our decorator occasionally causes the app to malfunction. So, to resolve that issue, you can use the following suggestions.

```typescript
@Response()
@UserDeleteGuard()
@RequestParamGuard()
@AuthAdminJwtGuard()
@HttpCode()
@Post()
```

## Validation Params

In previous, we recognize param as `_id` sometimes can make our application crash because we do not validate it. ~~Actually, we forgot xD~~. So we provide example for that, named as `RequestParamGuard`.

```typescript
export function RequestParamGuard(classValidation: ClassConstructor<any>): any {
    return applyDecorators(UseGuards(ParamGuard(classValidation)));
}
```

## Abstract Class Pagination

Always implement abstract class for pagination

## Independent Service

Make sure that we create service as a stand-alone. This means that the service does not require any additional services.
Let us make inheritance between services into the controller. Take a look at ack-nestjs-boilerplate-mongoose, for example.

## Response Type

In ack-nestjs-boilerplate-mongoose we use `Response Decorator` or `Response Paging Decorator` to return our response with/without data. But sometimes we forgot to set `Response Type`

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

Guard can also be used as a form of validation. We can reuse our validation with other modules using guard.

`User not found validation` is used in each endpoint with the '/: id' parameter. 
Assume we have 5 endpoints and need to write 5 'User not found validation' for each one. When we need to change, we must change 5 validation, which will be difficult and time-consuming.

But what if we wrapped the `User not found validation` in Guard? If there is a new endpoint, we simply use that guard. Isn't it simple?

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

`DebuggerModule` and `LoggerModule` have a different purpose.

### Debugger Module

Catch every thing is app.

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

Actually, `DebuggerModule` divides into 2 modules

#### DebuggerModule

Like `console.log`, but with the ability to write into a file. This serves the purpose of debugging everything that we want. This is useful when tracking our data flow in debug mode.

#### HttpDebuggerModule

A module that can catch all incoming requests and responses.

### Logger Module

The logger module's purpose is to save logs into a database, which will then be used by our data scientist.
