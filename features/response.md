# Response

> `Response Decorator` and `Response Paging Decorator` will restructure success response.

ack-nestjs-boilerplate-mongoose using `Response Decorator`. `Response Decorator` consumes `interceptor` from `@nestjs/common` and wraps it in the decorator. Location in `src/utils/response/interceptor/*`. The goal of the response decorator is to centralize and restructure the successful response. That can be easy to maintain or change if necessary.

```txt
src
  └── utils
      └── response 
          └── interceptor
                ├── response.default.interceptor.ts
                └── response.paging.interceptor.ts
```

Response decorator is divided into 2 decorators.

* `@Response` for default usage.
* `@ResponsePaging` for paging usage.

## Response Decorator

`@Response` is a group decorator that wrap `ResponseDefaultInterceptor`

```typescript
import { ResponseDefaultInterceptor } from './interceptor/response.default.interceptor';

export function Response(messagePath: string, statusCode?: number): any {
    return applyDecorators(
        UseInterceptors(ResponseDefaultInterceptor(messagePath, statusCode)),
        UseFilters(ErrorHttpFilter)
    );
}
```

### Usage

!> Don't forget to set the return type of promise

#### Default usage

`@Response` without data.

```typescript
import { Response } from 'src/utils/response/response.decorator';

@Response('test.hello') // <-- we can use like this
@Get('/hello')
async hello(): Promise<void> {
    return;
}
```

#### With Data

`@Response` also works with data, just by giving return value.

```typescript
import { Response } from 'src/utils/response/response.decorator';
import { IResponse } from 'src/utils/response/response.interface';

@Response('test.hello') 
@Get('/hello')
async hello(): Promise<IResponse> { // <--- don't forget to set type to IResponse

    // return some data
    return {
        "test": "test"
    };
}
```

### Interface

```typescript
export type IResponse = Record<string, any>;
```

## Response Paging Decorator

`@ResponsePaging` is a group decorator that wrap `ResponsePagingInterceptor`&#x20;

```typescript
import { ResponsePagingInterceptor } from './interceptor/response.paging.interceptor';

export function ResponsePaging(messagePath: string, statusCode?: number): any {
    return applyDecorators(
        UseInterceptors(ResponsePagingInterceptor(messagePath, statusCode)),
        UseFilters(ErrorHttpFilter)
    );
}

```

### Usage

!> Don't forget to set the return type of promise

`@ResponsePaging` return type will be used for server-side paging.

```typescript
import { ResponsePaging } from 'src/utils/response/response.decorator';
import { IResponsePaging } from 'src/utils/response/response.interface';

@ResponsePaging('permission.list')
@AuthAdminJwtGuard(ENUM_PERMISSIONS.PERMISSION_READ)
@Get('/list')
async list(
    @Query(RequestValidationPipe)
    { page, perPage, sort, search }: PermissionListValidation
): Promise<IResponsePaging> { // <--- don't forget to set to IResponsePaging

    return {
        totalData,
        totalPage,
        currentPage: page,
        perPage,
        data,
    };
}
```

### Interface

```typescript
export interface IResponsePaging {
    totalData: number;
    totalPage: number;
    currentPage: number;
    perPage: number;
    data: Record<string, any>[];
}
```

## Custom Status Error

By default, `StatusCode` is the same as HttpStatus. However, both of `@Reponse` and `@ResponsePaging` can specify a custom for StatusCode. Simply enter `number or string value` in the 2nd param.

```typescript
import { Response } from 'src/utils/response/response.decorator';
import { IResponse } from 'src/utils/response/response.interface';

@Response('test.hello', 123456) // <-- custom status code
@Get('/hello')
async hello(): Promise<IResponse> {
    return {
        "test": "test"
    };
}
```
