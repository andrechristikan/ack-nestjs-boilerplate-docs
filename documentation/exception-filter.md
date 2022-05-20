
# Exception Filter

> Store as global module

ack-nestjs-boilerplate-mongoose used `Exception Filter` module from `@nestjs/common` and the location is in `src/utils/error/error.filter.ts`.

```txt
src
  └── utils
      └── error 
          └── error.filter.ts
          └── error.module.ts
```

## Error Http Filter

> `ErrorHttpFilter` only catch `HttpException` and only restructure error response.

Exception Filter in ack-nestjs-boilerplate-mongoose will named as `ErrorHttpFilter`. `ErrorHttpFilter` only catch `HttpException` and only restructure error response.

* `BadRequestException`
* `UnauthorizedException`
* `NotFoundException`
* `ForbiddenException`
* `NotAcceptableException`
* `RequestTimeoutException`
* `ConflictException`
* `GoneException`
* `HttpVersionNotSupportedException`
* `PayloadTooLargeException`
* `UnsupportedMediaTypeException`
* `UnprocessableEntityException`
* `InternalServerErrorException`
* `NotImplementedException`
* `ImATeapotException`
* `MethodNotAllowedException`
* `BadGatewayException`
* `ServiceUnavailableException`
* `GatewayTimeoutException`
* `PreconditionFailedException`

`ErrorHttpFilter` will import into `ErrorModule`. Location in `src/utils/error/error.module.ts`

```typescript
@Module({
    controllers: [],
    providers: [
        {
            provide: APP_FILTER,
            inject: [MessageService],
            useFactory: (messageService: MessageService) => {
                return new ErrorHttpFilter(messageService);
            },
        },
    ],
    imports: [],
})
export class ErrorModule {}
```

## Usage

For example, try to create a user but got an error that one already exists.

### Default Usage

```typescript
import { BadRequestException } from '@nestjs/common';

@Response('user.create')
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ, ENUM_PERMISSIONS.USER_CREATE)
@Post('/create')
async create(
    @Body()
    body: UserCreateDto
): Promise<IResponse> {
    const checkExist: IUserCheckExist = await this.userService.checkExist(
        body.email,
        body.mobileNumber
    );

    if (checkExist.email && checkExist.mobileNumber) {
        this.debuggerService.error('create user exist', {
            class: 'UserController',
            function: 'create',
        });

        // with this we can throw error into ErrorHttpFilter
        // param is path of message language, and it will convert automatically
        // statusCode will same as HttpException
        throw new BadRequestException('user.error.exist');
    }
    
    ...
    ...
    ...
}
```

or we can also pass an object.

```typescript
throw new BadRequestException({
    message: 'user.error.exist',
})
```

### With errors

with errors.

```typescript
throw new BadRequestException({
    message: 'user.error.exist',
    errors: IErrors[]
})
```

### With data

```typescript
throw new BadRequestException({
    message: 'user.error.exist',
    data: Record<string, any>
});
```

### With properties

<button-jump-to name="Jump To Features Section Language" link="/#/documentation/language?id=usage-with-properties"></button-jump-to>

### Custom status code

```typescript
throw new BadRequestException({
    statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_EXISTS_ERROR,
    message: 'user.error.exist',
});
```

### Complex

```typescript
throw new BadRequestException({
    statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_EXISTS_ERROR,
    message: 'user.error.exist',
    data: Record<string, any>,
    properties: Record<string, string>
});

// or

throw new BadRequestException({
    statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_EXISTS_ERROR,
    message: 'user.error.exist',
    errors: IErrors[],
    properties: Record<string, string>
});

```

### Interface

```typescript
export interface IErrorHttpException {
    statusCode: number;
    message: string;
    errors?: IErrors[];
    data?: Record<string, any>;
    properties?: IMessageOptionsProperties;
}

export interface IErrors {
    readonly message: string;
    readonly property: string;
}
```

&nbsp;
