# Centralize Response

ack-nestjs-boilerplate-mongoose use `Response Decorator`. `Response Decorator` consume `interceptor` from `@nestjs/common` and wrap into decorator. Location in `src/response/interceptor/*`. Response decorator have purpose to centralize and restructure success response. That can be easy to maintain or if we have to change.

Response decorator divide become 2 decorators.

* `@Response` for default usage.
* `@ResponsePaging` for paging usage.

## @Response

`@Response` is a group decorator that wrap `ResponseDefaultInterceptor`&#x20;

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

#### Default usage

`@Response` without data.

!> Don't forget to set type of Promise to `void`

```typescript
import { Response } from 'src/response/response.decorator';

@Response('test.hello') // <-- we can use like this
@Get('/hello')
async hello(): Promise<void> {
    return;
}
```

#### With Data

`@Response` also work with data, just by give return value.

!> Don't forget to set type of Promise to `IResponse`

```typescript
import { Response } from 'src/response/response.decorator';
import { IResponse } from 'src/response/response.interface';

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

## @ResponsePaging

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

`@ResponsePaging` return type will be use for server side paging.

!> Don't forget to set type of Promise to `IResponsePaging`

```typescript
import { ResponsePaging } from 'src/response/response.decorator';
import { IResponsePaging } from 'src/response/response.interface';

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

By default `StatusCode` will same as HttpStatus. But both of `@Reponse` and `@ResponsePaging` can make custom for StatusCode. Just by put `number or string value` in 2nd param.

```typescript
import { Response } from 'src/response/response.decorator';
import { IResponse } from 'src/response/response.interface';

@Response('test.hello', 123456) // <-- custom status code
@Get('/hello')
async hello(): Promise<IResponse> {
    return {
        "test": "test"
    };
}
```
