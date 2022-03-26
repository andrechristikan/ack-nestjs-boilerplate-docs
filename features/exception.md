
# Exception

ack-nestjs-boilerplate-mongoose used `Exception Filter` module from `@nestjs/common` and the location is in `src/utils/error/error.filter.ts`.

```txt
src
  └── utils
      └── error 
          └── error.filter.ts
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

As long as we use [`Response Decorator`](/features/response?id=response-decorator) or [`Response Paging Decorator`](/features/response?id=response-paging-decorator) Decorator, `ErrorHttpFilter` will also imported. Cause `ErrorHttpFilter` and `Response Decorator` wrap into same group decorator.

```typescript
import { ResponsePagingInterceptor } from './interceptor/response.paging.interceptor';

export function ResponsePaging(messagePath: string, statusCode?: number): any {
    return applyDecorators(
        UseInterceptors(ResponsePagingInterceptor(messagePath, statusCode)),
        UseFilters(ErrorHttpFilter) // <--- here
    );
}
```

## Usage

Example create user, but got error exist.

### Default Usage

```typescript
import { BadRequestException } from '@nestjs/common';

@Response('user.create')
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ, ENUM_PERMISSIONS.USER_CREATE)
@Post('/create')
async create(
    @Body(RequestValidationPipe)
    body: UserCreateValidation
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

or also we can pass object too

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
    data: Record<string, any>
});

// or

throw new BadRequestException({
    statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_EXISTS_ERROR,
    message: 'user.error.exist',
    errors: IErrors[]
});

```

### Interface

```typescript
export interface IErrorHttpException {
    statusCode: number;
    message: string;
    errors?: IErrors[];
    data?: Record<string, any>;
}

export interface IErrors {
    readonly message: string;
    readonly property: string;
}
```
