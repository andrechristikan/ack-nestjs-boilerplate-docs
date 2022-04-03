# Validation

> `RequestValidationPipe` will validates all incoming requests based on `ClassValidator`

ack-nestjs-boilerplate-mongoose combine `class-validator` with `NestJs Pipe` for create `RequestValidationPipe`. Location in `src/utils/request/pipe/request.validation.pipe.ts`

## Request Validation Pipe

To use `RequestValidationPipe` we need to create `ClassValidator` as a validator for our request.

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

Then we need to add `ClassValidator` to the whitelist of `RequestValidationPipe` .

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

`RequestValidationPipe` will pass error into `ErrorHttpFilter`, so the response will look like

```json
{
    "statusCode": 422,
    "message": "Request validation errors",
    "errors": [
        {
            // "message": "{property} should be a type of email",
            "message": "Email field should be a type of email",
            "property": "email"
        }
    ]
}
```

### Interface of errors

```typescript
export interface IErrors {
    readonly message: string;
    readonly property: string;
}
```
