# Configuration

## Centralize Configuration

### Config Module

> Store into Global Variable

ack-nestjs-boilerplate-mongoose used `@nestjs/config` module to manage all configs and set in `src/config/*`.

We don't need to import ConfigModule for every modules, that because stored in `global variable`. Import into our `CoreModule`

```typescript
import { ConfigModule } from '@nestjs/config';

@Module({
    controllers: [],
    providers: [],
    imports: [
        MiddlewareModule,
        ConfigModule.forRoot({
            load: Configs,
            ignoreEnvFile: false,
            isGlobal: true, // <--- Import as global
            cache: true,
            envFilePath: ['.env', '.env.share'],
        }),
        
        ...
        ...
        ...
    ],
})
export class CoreModule {}

```

### Usage

#### Set default value

> We recommend to set default value in `configs file` in `src/config/*`, and don't set default value in Controller, or Service.

We can use `.env file` as default value of our config, or we can set directly. Let see our `AppConfig`

```typescript
import { registerAs } from '@nestjs/config';

// we can call our env just by using process.env
export default registerAs(
    'app',
    (): Record<string, any> => ({
        env: process.env.APP_ENV || 'development',
        language: process.env.APP_LANGUAGE || 'en',
        versioning: process.env.APP_VERSIONING === 'true' || false,
        http: {
            host: process.env.APP_HOST || 'localhost',
            port: parseInt(process.env.APP_PORT) || 3000,
        },
        
        ...
        ...
        ...
    })
);

```

#### Add new config

Let add `AppConfig` into `config index`, so we can call `AppConfig` anywhere.

```typescript
import AppConfig from 'src/config/app.config';

export default [
    AppConfig, // <--- add to here
    
    ...
    ...
    ...
];

```

#### Use Config in Controller or Service

Configs can access by call `ConfigService` . Just put it tu constructor of function and don't forget to use `@Injectable` decorator too.

```typescript
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AppService {
    private readonly env: string;

    constructor(
        private readonly configService: ConfigService
    ) {
        this.env = this.configService.get<string>(
            'app.env'
        );
    }
    
    ...
    ...
    ...
}
```

---

## Centralize Language and Message

> Store into Global Variable

ack-nestjs-boilerplate-mongoose use `I18nModule` and wrap into `MessageModule`. All languages will manage in MessageModule and store into `Global variable` same as `ConfigModule`. It's because we will use it often. Location in `src/message/message.module.ts`

`MessageModule` will look like this.

```typescript
import { Global } from '@nestjs/common';
import { MessageService } from 'src/message/message.service';

@Global() // <--- will store in global
@Module({
    providers: [MessageService],
    exports: [MessageService],
    imports: [],
    controllers: [],
})
export class MessageModule {}
```

and then, we import into our `CoreModule`

```typescript
import { MessageModule } from 'src/message/message.module';

@Module({
    controllers: [],
    providers: [],
    imports: [
        ...
        ...
        ...
        
        MessageModule, // <--- add to here
        PaginationModule,
        DebuggerModule,

        ...
        ...
        ...
    ],
})
export class CoreModule {}
```

### Language

ack-nestjs-boilerplate-mongoose already provide 2 languages (`English` and `Indonesia`). Check out in `src/message/languages/*`

And this our enum

```typescript
export enum ENUM_MESSAGE_LANGUAGE {
    en = 'en',
    id = 'id',
}
```

#### Default language

> The default of language is `en`

We can see or change default from `.env` file

```txt
APP_ENV=development
APP_HOST=localhost
APP_PORT= 3000
APP_LANGUAGE=en # <-- change this value
APP_VERSIONING=false
APP_DEBUG=false
APP_TZ=Asia/Jakarta

...
...
...
```

#### Dynamic language

We don't need any extra configuration, we just need to add `x-custom-lang` into `Header Request`. Example we will change language to `id` .

```json
{
    "headers": {
        "x-custom-lang": "id",
        
        ...
        ...
        ...
    }
}
```

#### Multi Language

> Delimiter is coma (,)

Yup, as you guess. We just need to change value from `.env` or `x-custom-lang` on `Header Request`.

```json
{
    "headers": {
        "x-custom-lang": "en,id",
        
        ...
        ...
        ...
    }
}
```

### Usage

We can use `MessageModule` with 3 way.

* [@Response()](#response) or [@ResponsePaging()](#responsepaging)
* [ErrorHttpFilter](#centralize-exception)
* [Direct](#direct)

#### Response Decorator

We just need to pass the path of language to be param of response decorator.

```typescript
import { Response } from 'src/utils/response/response.decorator';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {

    @Response('test.hello') // <--- path of language
    @Get('/hello')
    async hello(): Promise<void> {
        return;
    }
}
```

#### Error Http Filter

Same as response decorator, we just need to pass the path of language.

```typescript
import { InternalServerErrorException } from '@nestjs/common';
import { ENUM_STATUS_CODE_ERROR } from 'src/utils/error/error.constant';
import { Response } from 'src/utils/response/response.decorator';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        throw new InternalServerErrorException(
            'http.serverError.internalServerError', // <--- path of language
        );
    }
}
```

#### Direct

Or we want to direct use, here example for that

```typescript
import { Message } from 'src/message/message.decorator';
import { MessageService } from 'src/message/message.service';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    constructor(
        private readonly messageService: MessageService, // <--- describe the service
    ) {}

    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        return this.messageService.get('test.hello'); // <--- direct use
    }
}

```

---

## Centralize Response

> `Response Decorator` will restructure success response.

ack-nestjs-boilerplate-mongoose use `Response Decorator`. `Response Decorator` consume `interceptor` from `@nestjs/common` and wrap into decorator. Location in `src/utils/response/interceptor/*`. Response decorator have purpose to centralize and restructure success response. That can be easy to maintain or if we have to change.

Response decorator divide become 2 decorators.

* `@Response` for default usage.
* `@ResponsePaging` for paging usage.

### @Response

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

#### Usage

!> Don't forget to set return type of promise

##### Default usage

`@Response` without data.

```typescript
import { Response } from 'src/utils/response/response.decorator';

@Response('test.hello') // <-- we can use like this
@Get('/hello')
async hello(): Promise<void> {
    return;
}
```

##### With Data

`@Response` also work with data, just by give return value.

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

#### Interface

```typescript
export type IResponse = Record<string, any>;
```

### @ResponsePaging

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

#### Usage

!> Don't forget to set return type of promise

`@ResponsePaging` return type will be use for server side paging.

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

#### Interface

```typescript
export interface IResponsePaging {
    totalData: number;
    totalPage: number;
    currentPage: number;
    perPage: number;
    data: Record<string, any>[];
}
```

### Custom Status Error

By default `StatusCode` will same as HttpStatus. But both of `@Reponse` and `@ResponsePaging` can make custom for StatusCode. Just by put `number or string value` in 2nd param.

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

---

## Centralize Exception

> `ErrorHttpFilter` only catch `HttpException` and only restructure error response.

ack-nestjs-boilerplate-mongoose used `Exception Filter` module from `@nestjs/common` and the location is in `src/utils/error/error.filter.ts`.

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

As long as we use [`@Response()`](#centralize-response) or [`@ResponsePaging()`](#centralize-response) Decorator, `ErrorHttpFilter` will also imported. Cause `ErrorHttpFilter` and `Response Decorator` wrap into same group decorator.

```typescript
import { ResponsePagingInterceptor } from './interceptor/response.paging.interceptor';

export function ResponsePaging(messagePath: string, statusCode?: number): any {
    return applyDecorators(
        UseInterceptors(ResponsePagingInterceptor(messagePath, statusCode)),
        UseFilters(ErrorHttpFilter) // <--- here
    );
}
```

### Usage

Example create user, but got error exist.

#### Default Usage

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

#### With errors

with errors.

```typescript
throw new BadRequestException({
    message: 'user.error.exist',
    errors: IErrors[]
})
```

#### With data

```typescript
throw new BadRequestException({
    message: 'user.error.exist',
    data: Record<string, any>
});
```

#### Custom status code

```typescript
throw new BadRequestException({
    statusCode: ENUM_USER_STATUS_CODE_ERROR.USER_EXISTS_ERROR,
    message: 'user.error.exist',
});
```

#### Custom status code and with data

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

---

## Centralize Router

> All service must as [independent service](/tips/readme?id=independent-service)

Routes will put in `src/router/*` . Every route will have difference prefix. Endpoints or Controller that added in router will follow router prefix. List of prefix in `src/app/app.module.ts`

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

### Reason

Why we use RouterModule and why we don't just use controller prefix ?
Well sometimes the controller prefix can't resolve prefix problem for complex path. [You can read this article for more information](https://docs.nestjs.com/recipes/router-module)

### List of routes

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

---

## Versioning

> Versioning is important thing to do in production. [You can read our reference](https://restfulapi.net/versioning/).

In NestJs there have 3 versioning types.

* URL Versioning
* Header Versioning
* Media Type Versioning

ack-nestjs-boilerplate-mongoose only use `Url Versioning`. To use versioning, simply we just need to add other param in `@Controller`

### Usage

> Versioning can on / off, by switch environment variable

```typescript
@Controller({
    version: '1', // <--- version 1
    path: 'user',
})
export class UserAdminController {}
```

After that, if environment variable `APP_VERSIONING` is `true` then our url will look like `v1/admin/user/*`, but if `APP_VERSIONING` is `false` then  our url look like `admin/user/*`

---

## Validation Incoming Request

> `RequestValidationPipe` will validate all incoming request based on `ClassValidator`

ack-nestjs-boilerplate-mongoose combine `class-validator` with `NestJs Pipe` for create `RequestValidationPipe`. `RequestValidationPipe` is pipe validator for incoming request. Location in `src/utils/request/pipe/request.validation.pipe.ts`

### Usage

To use `RequestValidationPipe` we need to create `ClassValidator` as validator for our request.

```typescript
import { Type } from 'class-transformer';
import {
    IsNotEmpty,
    IsEmail,
    MaxLength,
    IsBoolean,
    IsOptional,
    ValidateIf,
    IsString,
} from 'class-validator';

export class AuthLoginValidation {
    @IsEmail()
    @IsNotEmpty()
    @MaxLength(100)
    readonly email: string;

    @IsOptional()
    @IsBoolean()
    @ValidateIf((e) => e.rememberMe !== '')
    readonly rememberMe?: boolean;

    @IsNotEmpty()
    @Type(() => String)
    @IsString()
    readonly password: string;
}

```

Then we need to add `ClassValidator` into `RequestValidationPipe` whitelist.

```typescript
export class RequestValidationPipe implements PipeTransform {

    ...
    ...
    ...
    
    private toValidate(metatype: Record<string, any>): boolean {
        const types: Record<string, any>[] = [
            AuthLoginValidation // <--- add into here
        ];
        return types.includes(metatype);
    }
}
```

Then put `ClassValidator` as type of `@Body`/`@Query` in `Controller`, and don't forget to put `RequestValidationPipe` too as pipe.

```typescript
@Controller({
    version: '1',
})
export class AuthController {

    ...
    ...
    ...
    
    @Response('auth.login', ENUM_AUTH_STATUS_CODE_SUCCESS.AUTH_LOGIN_SUCCESS)
    @HttpCode(HttpStatus.OK)
    @Post('/login')
    async login( 
        @Body(RequestValidationPipe) body: AuthLoginValidation // <--- use like this
    ): Promise<IResponse> {
    
    ...
    ...
    ...
    
    }
}
```

if error response will look like

```json
{
    "statusCode": 422,
    "message": "Request validation errors",
    "errors": [
        {
            "message": "{property} should be a type of email",
            "property": "email"
        }
    ]
}
```

### Interface

```typescript
export interface IErrorHttpException {
    statusCode: number;
    message: string;
    errors: IErrors[];
}

export interface IErrors {
    readonly message: string;
    readonly property: string;
}
```
