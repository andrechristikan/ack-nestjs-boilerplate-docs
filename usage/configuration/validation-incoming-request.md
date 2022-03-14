# Validation Incoming Request

ack-nestjs-boilerplate-mongoose combine `class-validator` with `NestJs Pipe` for create `RequestValidationPipe`. `RequestValidationPipe` is pipe validator for incoming request. Location in `src/request/pipe/request.validation.pipe.ts`

## Usage

> We can use `RequestValidationPipe` with `@Body` and `@Query` from `@nestjs/common.`

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

Then put `ClassValidator` as type in `Controller`, and don't forget to put `RequestValidationPipe` too.

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

## Interface

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
